---
layout: post
title: "ASUS B150M-A 마더보드에 커피레이크 CPU 설치기 (진행중)"
date: 2018-07-05 10:30:00 +0900
categories: IT
---

[모 네이버 블로그](https://blog.naver.com/vivamus09/221300782204) 를 참고하여
진행하였다.

1.  준비물:

-   CFL.zip:
    <https://drive.google.com/file/d/16yNTMfTyBOujTuFKIQeJHQAidJ_GYnUg/edit>

-   Pcie-patcher: 출처의 patcher1.4.zip

-   HxD: <https://mh-nexus.de/en/hxd/>

-   마더보드 BIOS ROM: v11.7.x.1xxx 이하의 ME 버전이어야 함 17’ 10월 이전
    Release FW면 가능할듯.  
    난
    [v3402](http://dlcdnet.asus.com/pub/ASUS/mb/LGA1151/B150M-A/B150M-A-ASUS-3402.zip)로
    진행.

1.  압축 해제

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/3874fd0df88d898d1a2033d8a5ef2156.png)

이중 Patcher 1.4는 PCIe Patcher, Workspace는 임의 생성한 폴더(앞으로 작업물들이
저장될 공간)

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/6709c2a6c5a255d084d779c2b38c2cf7.png)

Workspace 폴더에 압축 해제한 BIOS FW 파일을 투하.

1.  VBIOS 업데이트

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/aad546befc07ff7a100e3324a54bc73b.png)

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/e9111853c8737f2ca01d2f043e6dae73.png)

-   UBU v1.69.11 fix 폴더에 MMTool.exe를 실행 -> Load Image를 누르고, Workspace
    폴더에서 압축 해제한 FW 파일을 선택.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/e39d97216bfec501e7bd7c3776c31f26.png)

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/b2adcdcc4674bef08a04b4ad6d852b9b.png)

-   파일 로드가 완료되면, Option ROM 탭을 선택 후 Browse를 누르고, vbios.dat 을
    File name에 입력 후 Open

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/292facbb79b57f553a2ba4f0d04e4770.png)

-   목록에서 Device ID가 406인 줄 선택, Option ROM 항목에서 Extract \~ 선택,
    Apply 누르면 VBIOS 추출 끝.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/7868f2623bb67ed81dc00f475b921b70.png)

-   MMTool 창을 그대로 유지한 채, 탐색기를 통해 Workspace 폴더로 이동하여
    vbios.dat 파일이 생성되었음을 체크.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/349a3669e88c4eb468fca9eaa3895e42.png)

-   CFL 폴더의 Intel BMP.exe를 실행. Ctrl-O를 눌러 파일 열기 창을 띄운 후,
    Binary Data File에는 방금 추출한 vbios.dat, BMP Script File은 `VBIOS and
    BSF/SKLKBL/1046/skl_1046.bsf` 파일 선택.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/00c6f6963ebb2645d81f73fef6559c19.png)

-   파일이 열리면, 상단 메뉴의 BIOS Settings-> Save all…을 누르고, Workspace
    폴더에 transfer라는 이름으로 저장.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/db0f8582585f7617e30025b2b9b77e7e.png)

-   저장된 파일을 텍스트 편집기로 열고, STRING \$SignOn 이라는 문구를 찾아서
    해당 문구로 시작하는 줄을 지운 후 저장.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/9ec6a15b9e43b56e21d648330c8f89d7.png)

-   BMP에서 다시 Ctrl-O로 파일 열기 창을 띄운 후, 이번에는 `VBIOS and
    BSF/SKLKBL/1046/skl_1046.dat`와 `VBIOS and
    BSF/SKLKBL/1046/skl_1046.bsf`를 선택.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/68a4ad64243e8a4c7a0767b5c44ca6f5.png)

-   BIOS Settings-> Apply All… 을 선택 후, 방금 수정한 transfer.ssf를 선택하여
    패치를 진행.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/bc735d3bc8ecafd08665ddcba9d6364b.png)

-   패치가 완료되면, File-> Save As…를 눌러 Workspace 폴더에 new_vbios.dat 파일로
    저장.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/1268bddf94466ae4014a0e730a574d60.png)

-   BMP를 닫고, UBU v1.69.11 fix 폴더의 UEFITool.exe를 열고, File-> Open으로 BIOS
    FW 파일을 오픈.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/00e743898d8812feb2e18264d28b404a.png)

-   Ctrl-F 를 눌러 Search 창을 띄우고, GUID 탭으로 이동 후, 아까 MMTool의 선택한
    줄에 있던 Section GUID를 입력하고 검색. 앞에 두 자리까지만 입력해도 잘
    찾아지는듯?

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/cc30f51dbc814e40f8e33e73e25351be.png)

-   입력한 GUID Pattern에 매치되는 항목이 있으면 밑에 메세지가 뜬다. 이 메세지를
    더블 클릭하여 해당 항목으로 이동.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/80c2bd6aa45d2bc6e46a576c8dad2285.png)

-   해당 항목을 선택하고, Ctrl-Shift-R을 눌러 아까 저장한 new_vbios.dat을 선택.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/c4c9f8f8d556b5c2ea3ffd2fb27b7bee.png)

-   이제 Ctrl-S를 눌러 after-vBIOS-patch.cap으로 저장.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/97e98f9a96f3be6ee2921e1e9c969e26.png)

-   생성한 파일을 열거냐는 메세지인데, No를 누르고 UEFITool을 닫아 버리자.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/30172418f7a3832f91ea5cce0640a569.png)

-   Workspace 폴더에 after-vBIOS-patch.cap 파일이 있는지 체크 후, 이 파일을 UBU
    v1.69.11 fix 폴더로 복사.

1.  BIOS 마이크로코드 패치

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/7d8913a79c5ad551799afb30fe0db8fb.png)

-   UBU 폴더의 UBU.bat 파일을 관리자 권한으로 실행하면, 똑똑하게도 폴더에 있는
    바이오스 파일을 자동으로 찾아서 분해해 준다. 밑의 멘트가 나올때까지
    기다리고, 모든 작업이 끝나면 아무 키나 눌러서 진행.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/0557515f8712a7a80cddb11d2168dfc8.png)

-   21 테크로 EFI GOP Driver 패치.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/92912e94338fe07cf075d8258e7408ae.png)

-   7을 눌러 마이크로코드 업데이트 모드로 진행.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/35814da6929629970939606051eb8426.png)

-   첫째는 72

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/1e9c2b2593e08d02ba38133ae88535f5.png)

-   둘째는 BE

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/7c011e51fa53495243f93b0a5a780cd0.png)

-   패치중…

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/fecc3fc969fd94710e848116921e1bc7.png)

-   패치끝.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/9c81ded24436a347720456c6a3f2f2b2.png)

-   01 테크로 파일 저장. 이후 엔터키 누르면 종료.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/8726182075982e5dcf81a9499592b511.png)

-   전에 없던 B15A4.cap이 생긴 걸 볼 수 있다. Workspace 폴더로 옮겨 주자.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/b5d7a8c770b84413c4656699a680d055.png)

-   Patcher 1.4 폴더를 Powershell에서 열어주자(Shift-RClick 후 Open PS window
    here)

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/d30dfea3b9c0295d2babd39eeb186bcb.png)

-   `./pcie_patcher.cmd ../Workspace/B15A4.CAP` 명령으로 패치.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/9c49863adca3628bd6ecf31811127c01.png)

-   패치끝. 밑의 오류는 신경 쓸 필요 없다.

![](https://raw.githubusercontent.com/thy2134/thy2134.github.io/master/static/images/46ec8ac62701aac69b985f07425e2660.png)

-   Workspace 폴더에 B15A4.CAP.patched 파일이 생성됐음을 볼 수 있다. 이것이
    완성본.

1.  기타

-   i5 이상의 CPU일 경우,
    <http://www.hwbattle.com/bbs/board.php?bo_table=cpumbram&wr_id=112671> 를
    참고해서 CPU 접점에 절연 및 은박신공을 해야 한다.

-   Non-K CPU만 박아야 한다. 안그러면 CPU 수명이 단축될 수 있다고.

-   B/H 칩셋에서는 i7의 HT가 제대로 지원되지 않는다고 한다.

-   ASUS 및 일부 애자락 보드의 경우 Windows에서 돌아가는 FW Update Tool이 아니라
    CH341A ROM Writer를 이용해서 수동으로 ROM을 올려야 한다. ROM Writer의
    사용법은
    [컴갤](http://gall.dcinside.com/board/view/?id=pridepc_new3&no=7795125)에
    [자세히](http://gall.dcinside.com/board/view/?id=pridepc_new3&no=7795139)
    정리되어 있다.

    -   만약 당신의 보드에 BIOS Chip이 납땜되어 있다면, SOP8 Clip과 점퍼 F-F
        케이블을 이용하여 ROM Writer에 연결해야 한다.

-   1에서 언급한 1046이라는 숫자는 보드 SKL에 따라 다를 수 있다. 내 보드가
    B150M-A가 아닐 경우에는 맨 위 출처를 참고하여 숫자를 알아내자.
