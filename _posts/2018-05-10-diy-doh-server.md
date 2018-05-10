---
layout: post
title: "DNS over HTTPS 서버를 직접 만들어 보자"
date: 2018-05-10 12:00:00 +0900
categories: IT
---

0. 호옥시나, 그럴일은 없겠지만, 정부가 무식하게 DoH 지원하는 Resolver들을 막아버리면(...) 어쩔까 하는 생각에 작성하는 글이다.    
1. 준비물: 
  - DNS 검열을 받지 않는 지역(일본이던, 미국이던, ...)에 존재하는 Linux 서버. 꼭 리눅스가 아니어도 되지만, 내가 설명하기 편하니까...
  - 이러한 지식에 대한 기초적인 이해 
2. 먼저, DoH 서버를 올릴 컴퓨터로 접속해서 `https://github.com/hardillb/dns-over-https` 를 클론 한다.
3. `cd dns-over-https` 후, `./mkCert.sh $(curl ipconfig.io)` 를 이용해 Self-Signed Certificate를 만든다. 
4. `npm install` 하던, `yarn install` 하던... 취향에 맞는 Package Manager로 Dependency들을 설치한다.
5. `npm start`, `yarn start` 등으로 서버를 실행하면 된다. DoH는 443을 이용해 통신하므로, 방화벽에서 443 포트를 당연히 열어줘야지.
6. 근데 이렇게만 만들어 놓으면 터미널 세션을 나가버리면 서버가 꺼지니까, systemd를 이용해 데몬화 시켜주자.
  1. [Systemd Script](/static/doh-server.service)를 다운받아서 `/lib/systemd/system` 에 저장한다. 귀찮으면 걍 `cd /lib/systemd/system; sudo wget https://blog.r3mark.xyz/static/doh-server.service` 로 해결.
  2. `sudo vi /lib/systemd/system/doh-server.service` 로 열어서, `WorkingDirectory=` 뒤의 경로를 Git Repo가 저장된 경로로 바꿔주고 `:wq`로 저장한다.
  3. `sudo systemctl daemon-reload` 후, `sudo service doh-server start` 하면 서비스가 실행된다. 
7. 서버 설정은 다 끝났으나, 아직 할 일이 남았다. 우리가 만든 TLS용 Cert는 Self-Signed이기 때문에, Client에서 바로 접속하려 하면 Cert가 올바르게 Sign되지 않았다고 에러를 뱉으므로... Cert를 미리 컴퓨터에 설치해야 한다. 
  - 먼저, 터미널에서 Repo 폴더에 저장된 `server.crt` 파일을 찾아 컴퓨터에 미리 저장해 두어야 한다.
  - Windows에서는
    1. Win+R키로 실행 창을 띄우고, mmc.exe를 입력 후 엔터를 친다.
    2. Ctrl+M을 누르고, 왼쪽의 Available Snap-ins에서 `Certificates (Local Computer)`를 찾아서 클릭 후 `Add >` 를 누른다.
    3. OK를 눌러 창을 닫는다.
    4. 이제 `Console Root` 밑에 `Certificates (Local Computer)` 가 추가되었다. 더블 클릭 후, `Trusted People > Certificates` 순으로 접근한다. 
    5. 우측의 빈 공간을 우클릭 후 `All Tasks > Import` 를 눌러 인증서를 추가한다.
    6. Next 후, `File to Import` 창이 나오면 다운받아 두었던 `server.crt` 를 선택하고, Next를 누른다.
    7. 다시 Next를 누르면 인증서가 불러와 져 있다.
8. 이제, 이전 게시글([Windows](/it/2018/05/04/install-cloudflare-doh-on-windows.html), [macOS](/it/2018/05/04/install-cloudflare-doh-on-macos.html))을 참조하여 DoH Client를 설정한다. 이때, Config.yml을 작성하는 부분에서 `https://1.1.1.1/dns-query` 대신 `https://(서버 IP)/query` 를 적어 준다.
9. 끝! 
