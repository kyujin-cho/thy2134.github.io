---
layout: post
title: "Windows에 Cloudflare DNS와 DNS on HTTPS 연결을 해주는 Authoritative Name Server 설치하기"
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

0. 32-bit 컴퓨터는 [이곳](https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-windows-386.zip), 64-bit 컴퓨터는 [이곳](https://bin.equinox.io/c/VdrWdbjqyF/cloudflared-stable-windows-amd64.zip) 에서 cloudflared 실행 파일이 담긴 zip 파일을 다운로드 받는다. 
1. 다운로드 받은 cloudflared.exe를 적당한 곳에 압축 풀어준다. 이 예시에서는 C:\Cloudflared\cloudflared.exe로 하겠다.
2. CMD를 "관리자 권한"으로 열어준다.
3. CMD에서 파일이 저장된 폴더(여기서는 C:\Cloudflared) 로 이동한다.
4. `cloudflared service install` 을 실행한다. 정상적으로 서비스가 설치되었으면 `INFO[0000] Installing Argo Tunnel Windows service` 라는 메세지가 나온다.
5. 이제 탐색기를 열고, `C:\Windows\System32\config\systemprofile` 폴더로 이동한다.
6. `.cloudflared` 폴더를 생성한다.
7. 메모장을 열고, 다음 내용을 붙여넣는다.    
    ```
    proxy-dns: true    
    proxy-dns-upstream:     
        - https://1.1.1.1/dns-query    
        - https://1.0.0.1/dns-query
    ```    
8. 파일을 `config.yml` 이라는 이름으로 바탕 화면에 저장한다.
9. 저장한 파일을 방금 생성한 `.cloudflared` 폴더 안에 집어넣는다.
10. 시작+R 을 누르고 services.msc를 입력 후 엔터.
11. `Argo tunnel agent` 서비스를 찾아서 시작한다.
12. 컴퓨터의 DNS를 127.0.0.1로 변경한다.
13. CMD에서 `nslookup bamtoki.com` 을 입력해, 위의 결과가 아닌 다른 결과가 나오면 성공.    
예시:     
```
$ nslookup bamtoki.com
Server:  localhost
Address:  127.0.0.1

Non-authoritative answer:
Name:    bamtoki.com
Addresses:  2400:cb00:2048:1::6817:8513
          2400:cb00:2048:1::6817:8613
          104.23.133.19
          104.23.134.19
```