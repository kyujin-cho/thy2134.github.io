---
layout: post
title: "S10 계열 갤럭시 CSC 변경"
date: 2019-07-05 17:30:00 +0900
categories: IT
---

S10 계열 기기가 없어서 테스트는 못해봤으나, 특정 프로그램 + UART의 조합으로 EIF Command를 입력할 수 있다는 모 카페에서의 게시글이 올라와서 해당 Method를 하나의 게시글로 정리해 보았다.

- 준비물
    1. USB Type-C UART Cable
    2. [SGKTEif 프로그램](https://androidfilehost.com/?fid=1322778262904004227) *
- 변경방법
    1. UART 케이블과 Windows 컴퓨터를 연결
    2. SGKTEif 실행
    ![CSC_1.png](https://raw.githubusercontent.com/kyujin-cho/thy2134.github.io/master/static/images/CSC_1.png)
    3. Port에 단말기가 올라오는지 체크 후 OPEN
    4. `AT` 입력 후 SEND => Ok가 리턴되어야 함
    5. `AT+preconfig=2,<CSC>` 입력 후 SEND => Ok가 리턴되어야 함 **
    6. 재부팅 

===
- 주석
    - *: 해당 ZIP 파일에는 암호가 걸려있다...
    - **: <CSC>에는 Provider별 CSC를 입력. 한국의 경우, SKT: SKC, KT: KTC, LG U+: LUC, 자급제: KOO
