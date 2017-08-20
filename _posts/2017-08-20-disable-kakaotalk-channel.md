---
layout: post
title:  "카카오톡 채널 기능 차단하기"
date:   2017-08-20 15:30:00 +0900
categories: IT
---

클리앙의 [카카오톡 채널 탭 삭제하기](https://www.clien.net/service/board/lecture/11099227) 를 보고 작성.  
대상: 안드로이드 폰.  
아이폰은 테스트 해보지 않음. 아마 안 될 거 같다.  

1. [AdGuard](https://adguard.com/en/welcome.html) 설치
    - 플레이 스토어 버전이 아니라 해당 페이지에서 다운받은 버전이어야 한다.
    - 애드가드는 유료 소프트웨어이며, 루블화를 통해 결제를 할 경우 1년 2천원대, 평생 6천원대의 가격으로 사용할 수 있다고 한다. 자세한 건 꺼무위키에...

![Step 2](https://thy2134.github.io/static/images/Screenshot_20170820-153524.png)
2. 하단의 'GET ADGUARD PREMIUM'을 눌러 라이센스 등록.
![Step 3](https://thy2134.github.io/static/images/Screenshot_20170820-153651.png)
3. 등록 후, 메인 화면의 전원 버튼을 눌러 차단 활성화. 
    - 애드가드는 VPN을 이용하여 트래픽을 선별적으로 차단하는 방식이므로 VPN 권한 활성화가 필요하다.

![Step 4](https://thy2134.github.io/static/images/Screenshot_20170820-153658.png)
4. 설정 - 유저 필터 로 이동.
5. 상단의 3점 메뉴를 누르고 불러오기 선택.
![Step 6](https://thy2134.github.io/static/images/Screenshot_20170820-153720.png)
6. `https://gist.githubusercontent.com/thy2134/229d09e75f2171e410b1a400e7195277/raw/3bd9278a538cbe71b96e28cfd8a18236b02ccbf0/kakao_channel_filter.txt`
해당 링크를 붙여넣기 
![Step 7](https://thy2134.github.io/static/images/Screenshot_20170820-153725.png)
7. 필터가 정상적으로 불러와졌는지 확인
8. 카카오톡 앱 재설치
    - 당연히 데이터가 날라가기 때문에 백업 후 재설치 필수

![Step 9](https://thy2134.github.io/static/images/Screenshot_20170820-153734.png)
9. ?????
10. PROFIT!!!  