---
layout: post
title: "Smart DNS 서버 구축하기"
date: 2017-11-20 18:00:00 +0900
categories: IT
---

이 글은 [해당 Github Repo](https://github.com/jamiees2/dnsproxy) 를 거의 대부분 참고하여 작성

미국 Amazon Prime Video 서비스에는 지역락이 걸려있어서, 미국을 제외한 지역에서는 시청할 수 없다. Netflix, Hulu, 기타 등등 수많은 VOD/Live TV 서비스들 역시 비슷한 방식의 지역락을 걸고 있다.   
보통 지역락을 걸기 위해서는, 유저가 서비스에 접속할 때 서버에 특정 요청을 보내 유저의 IP가 재생 가능 지역에 속해 있는지 아닌지를 서버가 판단하는 방식을 취한다.    
![Image](https://github.com/thy2134/thy2134.github.io/blob/master/static/images/DNS_3.png?raw=true)    
    
Amazon Prime Video의 예시. Amazon Prime Video는 `https://atv-ps.amazon.com/cdp/catalog/GetPlaybackResources` 에 POST 요청을 보내서, 영상의 정보와 지역락 여부를 가져온다.    
이러한 요청을 보내는 과정은 대략 네임서버(혹은 캐시)에서 IP를 땡겨옴->IP에 요청을 보냄->전달받음 인데, Smart DNS 서비스는 이 과정을 파고드는 서비스이다.    
자체 네임서버를 한 대 두고, 여러 스트리밍 서비스들의 인증 도메인에 연결된 원래 IP를 미국 내의 프록시 서버로 바꿔치기 하는 것이다.    
이렇게 되면, 브라우저는 원래 도메인 대신 프록시 서버에 동일한 요청을 보내고, 미국 내의 프록시 서버가 원래 인증 서버에 요청을 보내 그 결과를 사용자의 컴퓨터에 돌려주게 된다.    
이 과정에서, 인증 서버는 사용자의 컴퓨터가 한국에 있는 것이 아니라 미국에 있는 것으로 인식하여, 인증을 우회할 수 있는 것이다.
요약하자면   
1. 한국에서 일반 네임서버로 도메인의 IP를 질의 => 인증서버의 IP를 돌려줌    
2. 돌려준 인증 서버에 요청을 보냄 => 인증 서버에서는 요청이 미국 바깥에서 온 것으로 판단, 블락     
이었던 것이    

1. 한국에서 특수 네임서버로 도메인의 IP를 질의 => 인증서버가 아닌 프록시 서버의 IP를 돌려줌    
2. 프록시 서버에 요청을 보냄 => 요청을 받은 미국의 프록시 서버가 미국의 인증 서버에 요청을 보냄    
3. 미국의 프록시 서버에서는 요청이 미국 내 서버에서 온 것으로 판단, 허용 및 정보 리턴    
4. 리턴받은 정보를 프록시 서버가 다시 한국의 컴퓨터에게 돌려줌    

이렇게 되는 것.    

인증 구간 하나만 캡쳐해서 프록시 서버로 던져버리는거라 VPN에 비해 속도 문제가 거의 없기 때문에, 미국 VOD 서비스를 보기 위해 많이들 쓰는 방식이다.    
단점은... 비싸다는거 -_-    
대강 한달에 10불 정도 한다.    
프라임 끊느라 1년에 50불을 내는데 영상 보려고 10불을 또 낼수는 없으니, 남는 VPS에 실제로 구축해보기로 했다.       

- 준비물    
1. 프록시 서버 역할을 할, Ubuntu가 설치된 미국 내에 위치한 VPS. 사양은 512MB 램이면 충분하다.
2. Python2가 설치된 네임서버 역할을 할 컴퓨터. 라즈베리 파이 정도면 충분하다. DD-WRT가 설치된 공유기도 된다. 영상을 컴퓨터에서 보려면 굳이 없어도 된다. 
3. 기초적인 Linux / 네트워크 지식 

1. 작업을 할 컴퓨터에서 `https://github.com/jamiees2/dnsproxy` 레포지토리를 clone 한다.
2. proxies/proxies-us.json을 열어 해당 도메인이 포함된 Dictionary를 지운다. 응답하지 않는 도메인이므로, 그냥 놔두면 에러를 일으킨다.
```
token.dramafever.com
htmltvui-api.netflix.com
mvid.yql.yahoo.com
videoplayer.vevo.com
```
이후에, 레포의 config-sample.json을 config.json으로 변경 후 public_ip 항목을 VPS의 IP로 변경한다. 

4. 레포지토리로 이동 후, `python dnsproxy.py -o haproxy` 를 실행하여 설정 파일을 생성한다. 생성한 설정 파일은 output/ 폴더에 위치해 있다. 
5. 프록시 서버에 SSH로 연결한다.
6. 다음 명령을 실행하여 HAProxy를 설치한다.    
```
apt-get update -qq && apt-get install -y software-properties-common
add-apt-repository -y ppa:vbernat/haproxy-1.5
apt-get update -qq && apt-get install -y haproxy
```
7. /etc/haproxy/haproxy.cfg를 haproxy.cfg.old로 이름을 바꾼다.
8. dnsproxy 폴더 안의 output/haproxy.conf를 /etc/haproxy/haproxy.conf 에 저장한다. 
9. `service haproxy restart` 로 HAProxy를 재시작한다. 
10. 이제 프록시 서버 설정은 끝났다. 다시 작업 컴퓨터로 돌아오자.
11. 작업 컴퓨터에 DNSMasq를 설치한다. 맥은 [링크](https://gist.github.com/ogrrd/5831371)를 참고하여, Ubuntu는 `sudo apt install dnsmasq`를 입력하여 설치한다.
12. 맥은 `/usr/local/etc/dnsmasq.conf`, Ubuntu는 `/etc/dnsmasq.conf` 를 열어 안의 내용물을 dnsproxy 레포 내의 dnsmasq-haproxy.conf의 내용물로 교체한다.
13. DNSMasq를 재시작한다. 
14. 영상을 시청할 컴퓨터의 인터넷이 들어오는 NIC의 DNS를 방금 DNSMasq를 설치한 컴퓨터의 IP로 바꿔준다. 로컬에 설치했을 경우에는 127.0.0.1, 아니면 알아서 입력한다. 보조 DNS는 ISP의 DNS를 넣어준다. 넣어주지 않으면 인터넷이 안된다.
15. DNS Cache를 Flush 후 `nslookup atv-ps.amazon.com` 명령어를 실행하여 Address 부분이 Proxy Server의 IP Addr로 적용되었는지 체크한다.

![Image](https://github.com/thy2134/thy2134.github.io/blob/master/static/images/DNS_1.png?raw=true)    
적용 전 
![Image](https://github.com/thy2134/thy2134.github.io/blob/master/static/images/DNS_2.png?raw=true)       
적용 후    

영상이 재생된다. 만세!     

PS) 이걸 해보자고 결심한게 VPN을 통해 Youtube TV로 NCAA Football PO를 보고 나서인데, 정작 이걸 써도 Youtube TV는 작동하지 않는다. ㅡㅡ     
다른 방법 연구가 필요할 듯.    
PS2) XBox 360 / PS3 / Bravia TV및 BDP / 옛 삼성 TV의 경우에는 이 방식을 지원하지 않아, DNAT 방식으로 설정해야 한다고 한다. 설정방법은 해당 레포 Wiki에 있으니 알아서 잘...
