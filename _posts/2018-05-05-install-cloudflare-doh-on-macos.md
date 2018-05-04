---
layout: post
title: "macOS에 Cloudflare DNS와 DNS on HTTPS 연결을 해주는 Authoritative Name Server 설치하기"
date: 2018-05-05 01:00:00 +0900
categories: IT
---

2018년 5월 4일부로, 문화체육관광부가 DNS 쿼리 오염을 통한 불법 사이트 차단을 시작했다.   
```
$ nslookup bamtoki.com    
Server:  1dot1dot1dot1.cloudflare-dns.com    
Address:  1.1.1.1    
Name:    bamtoki.com    
Address:  121.189.57.82
```    

다음은 DNS 오염을 이용한 서버 차단의 예시이다. `nslookup` 명령어를 통해 대표적인 불법 웹툰 사이트인 `bamtoki.com`의 IP를 질의했더니, 원래 IP가 아닌 이상한 IP가 튀어나온다.   
그리고 해당 IP는,   
```
$ nslookup www.warning.or.kr    
Server:  localhost    
Address:  127.0.0.1    
Non-authoritative answer:    
Name:    www.warning.or.kr    
Address:  121.189.57.82
```       
짜잔. 무려 `warning.or.kr`에 엮인 IP다.    
정부가 특정 사이트에 대한 DNS 질의를 무단으로 가로채서 변조 후 전송한다는 뜻이다.   
하지만 방패가 있으면 창이 있으니, 얼마 전에 발표한 Cloudflare의 DNS와 함께 발표된 Argo Tunnel을 이용해 쉽게 DNS 연결을 암호화 할 수 있다.   

0. `brew install cloudflare/cloudflare/cloudflared` 로 cloudflared를 설치한다.
1. `cd ~/`로 유저 폴더로 이동한다.
2. `mkdir .cloudflared` 로 cloudflared 설정 파일이 들어갈 폴더를 생성한다.
3. `cd .cloudflared` 한 후, vi 등으로 `config.yml` 파일을 만든다. 내용은 다음과 같이 채워야 한다.
```    
proxy-dns: true    
proxy-dns-upstream:    
    - https://1.1.1.1/dns-query    
    - https://1.0.0.1/dns-query      
```
4. `sudo cloudflared service install` 로 cloudflared 서비스를 설치한다.
5. `sudo launchctl start com.cloudflare.cloudflared` 로 cloudflared 데몬을 실행한다.
6. `sudo lsof -i UDP:53` 명령어로 cloudflared 데몬이 제대로 돌아가고 있는지 확인한다. 
```
COMMAND     PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
cloudflar 96272 root    3u  IPv4 0xb8aaa9d10728bd67      0t0  UDP *:domain
```
7. 이런 식으로 나오면 성공.
8. `dig +short @127.0.0.1 bamtoki.com AAA` 명령어로 Query가 제대로 이루어지는지 체크한다.
9. macOS의 DNS를 127.0.0.1로 변경한다.
