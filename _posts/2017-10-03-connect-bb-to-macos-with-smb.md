---
layout: post
title: "macOS에서 BlackBerry Link 없이 BB와 연결하기"
date: 2017-10-03 16:00:00 +0900
categories: IT
---
macOS High Sierra 업뎃 이후로 BB Blend/Link가 작동하지 않아 찾은 대체 방식이다. BB와 컴퓨터를 SMB로 연결하여 파일을 전송 가능하게 해준다.
다만 SMB 연결이라 속도가 상당히 느리다.

1. macOS의 System Pref. -> Network에서 BlackBerry가 연결되는지 확인한다(노란불 들어오면 OK).   

2. BB의 Settings -> Storage and Access로 접근하여 Access using Wi-Fi 옵션을 활성화해준다.   

3. 적절한 Password를 설정한다.

4. Password 설정 후, Identification on Network로 접근하여 본인의 BB의 이름과 Username을 확인한다.

5. macOS Finder에서 Cmd + K를 눌러 서버 접속 다이얼로그를 연 후, 주소창에 smb://<BB 이름>을 넣고 연결을 누른다.

6. 4에서 확인한 Username과 3에서 설정한 Password를 입력하고 연결을 누른다.

7. 마운트할 볼륨을 선택한다. media가 내부 저장소, removable_sdcard가 외부 저장소이다.

8. 파일을 확인할 수 있다.

- Troubleshooting
1. Network 패널에서 BB가 잡히지 않을 경우
[드라이버](https://github.com/thy2134/thy2134.github.io/blob/files/Drivers.zip?raw=true) 를 설치 후, 재시작해본다.
