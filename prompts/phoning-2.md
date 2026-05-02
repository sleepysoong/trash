아래는 프론트 개발자에게 바로 전달하는 클론 구현용 디스크립션처럼 쓴 버전이야. “감성 설명”보다 구조, 컴포넌트, 배치, CSS 처리, 인터랙션 중심으로 정리했어.


---

Y2K Phoning-style Fan App UI Clone Spec

1. 전체 방향

이 웹은 현대적인 SaaS UI가 아니라, 2000년대 초반 한국 팬사이트 + 싸이월드 미니홈피 + 플립폰 앱 + 스티커 다이어리 + Windows XP 웹 그래픽을 섞은 레트로 팬앱 UI다.

디자인의 핵심은 다음이다.

정갈한 그리드보다는 콜라주형 배치

사진, 하트, 키링, 말풍선, 로고가 겹겹이 쌓인 레이어 구조

하늘색, 라임그린, 연노랑, 핑크, 흰색 중심의 파스텔 + 형광 포인트

1px~2px 회색/검은색 라인

둥근 흰색 버튼

저해상도 아이콘

2000년대식 그라데이션과 드롭섀도우

약간 촌스럽고 어설픈데 귀여운 완성도


중요한 건 “예쁜 현대 앱”처럼 만들면 안 된다. 일부러 이미지가 과하게 많고, 아이콘이 조금 투박하고, 텍스트가 살짝 촌스러워야 한다.


---

2. 기준 해상도 / 레이아웃

Desktop 기준

전체 페이지는 가로로 긴 쇼케이스 형태.

[ Home Screen ] [ Call / Message / Calendar Screen ]

또는 3개 스크린을 동시에 보여줄 때는:

[ Home Screen ] [ Call Screen ] [ Message Screen ]

두 번째 예시처럼 홈 + 캘린더를 보여줄 때는:

[ Home Screen ] [ Calendar Screen ]

추천 프레임 크기

각 스크린은 모바일 앱 목업처럼 세로형으로 만든다.

Home Screen:      width 390~430px / height 820~900px
Right Screen:     width 390~430px / height 820~900px
Desktop wrapper:  display flex / align-items center / gap 18~28px

전체 배경은 연하늘색.

body {
  background: #b8d9f7;
}

스크린 자체는 overflow: hidden이 필요하다. 특히 홈 화면은 스티커와 사진이 화면 밖으로 살짝 나가도 예쁘지만, 기본적으로는 프레임 안에서 잘려야 한다.


---

3. 전체 컴포넌트 구조

React 기준이면 이런 식으로 나누면 좋다.

App
 ├─ LayoutShowcase
 │   ├─ HomeScreen
 │   │   ├─ HeroBadge
 │   │   ├─ StickerCollage
 │   │   ├─ PhoningLogo
 │   │   └─ RetroIconDock
 │   └─ RightPanel
 │       ├─ CallScreen
 │       ├─ MessageScreen
 │       └─ CalendarScreen

오른쪽 패널은 탭 전환 가능하게 만들어도 된다.

Home bottom icons 클릭
- Calls 클릭 → CallScreen
- Message 클릭 → MessageScreen
- Calendar 클릭 → CalendarScreen
- Settings 클릭 → 더미 설정 화면 또는 alert


---

4. 공통 스타일 토큰

컬러

:root {
  --sky-strong: #6da6ff;
  --sky: #9dccff;
  --sky-soft: #cde9ff;

  --lime: #9cff43;
  --lime-dark: #71d933;
  --lime-soft: #d8ff9c;

  --yellow-bg: #fff9b8;
  --yellow-soft: #fffddd;

  --pink: #ff8bd8;
  --hot-pink: #ff63ca;

  --heart-red: #ff574f;
  --badge-red: #e8202a;

  --text: #111;
  --muted: #8d8d8d;
  --line: #8f8f8f;
  --soft-line: #e4e4e4;

  --white: #fff;
}

공통 버튼

상단 뒤로가기, 알림 버튼, 오늘 버튼 등은 전부 레트로 플라스틱 느낌.

.round-btn {
  width: 38px;
  height: 38px;
  border-radius: 999px;
  background: #fff;
  border: 2px solid #8b8b8b;
  box-shadow: 0 2px 3px rgba(0,0,0,0.25);
  display: flex;
  align-items: center;
  justify-content: center;
}

테두리 / 카드

.retro-card {
  border: 2px solid #6f6f6f;
  border-radius: 16px;
  background: #fff;
}

폰트

폰트는 통일하면 안 된다. 화면별로 다른 느낌이 필요하다.

기본 UI 한글:
- Pretendard, Noto Sans KR, Apple SD Gothic Neo

레트로/픽셀 텍스트:
- DungGeunMo
- NeoDunggeunmo
- Galmuri
- bitmap-style font

영문 장식 문구:
- Georgia
- Times New Roman
- serif italic

로고나 아이콘 라벨은 가능하면 픽셀 폰트 또는 두꺼운 아웃라인 폰트를 쓴다.


---

5. HomeScreen 상세 구현

5-1. 홈 스크린 배경

홈 화면 배경은 위에서 아래로 내려가는 하늘색 그라데이션.

.home-screen {
  position: relative;
  width: 410px;
  height: 860px;
  overflow: hidden;
  background:
    repeating-linear-gradient(
      to bottom,
      rgba(255,255,255,0.14) 0,
      rgba(255,255,255,0.14) 2px,
      transparent 2px,
      transparent 8px
    ),
    linear-gradient(to bottom, #6fa7ff 0%, #b8dcff 45%, #ffffff 100%);
}

가로줄이 너무 선명하면 안 되고, 아주 은은하게 깔려야 한다.


---

5-2. 상단 파란 말풍선 배지

상단 왼쪽~중앙에 진한 파란색 타원형 말풍선 배치를 만든다.

위치: top 20px, left 20px
크기: width 220px, height 72px
회전: -6deg 정도
색: 진한 블루
텍스트: 흰색 serif italic
문구: "NewJeans / Don't Be Blue" 같은 2줄 구성

CSS 예시:

.hero-badge {
  position: absolute;
  top: 24px;
  left: 20px;
  width: 230px;
  height: 72px;
  background: #2834b8;
  border-radius: 60% 55% 58% 50%;
  transform: rotate(-7deg);
  color: white;
  font-family: Georgia, serif;
  font-style: italic;
  font-size: 21px;
  line-height: 1.05;
  text-align: center;
  padding-top: 12px;
  z-index: 20;
  box-shadow: 2px 3px 0 rgba(0,0,0,0.25);
}

배지 아래에 작은 삼각형 꼬리를 추가해서 말풍선처럼 보이게 한다.


---

5-3. 메인 핑크 하트

홈 화면 중앙 상단에 큰 핑크 하트가 있어야 한다.

위치: top 80px, center
크기: 약 300px
색상: 핫핑크 또는 연핑크
역할: 메인 인물 사진 뒤 배경

CSS만으로 하트를 만들 수도 있지만, SVG 하트가 더 낫다.
하트 위에 큰 인물 사진이 겹쳐져야 한다.

.pink-heart {
  position: absolute;
  top: 85px;
  left: 58px;
  width: 300px;
  height: 270px;
  background: #ff90dc;
  clip-path: path("M150 260 C-40 130 55 -20 150 75 C245 -20 340 130 150 260 Z");
  z-index: 2;
}

실제 구현에서는 clip-path보다 SVG asset 추천.


---

5-4. 사진 스티커 콜라주

홈 화면에서 가장 중요한 부분이다.

모든 사진은 position: absolute로 배치한다. 자동 레이아웃 쓰면 감성이 죽는다.

공통 사진 스타일:

.photo-sticker {
  position: absolute;
  object-fit: cover;
  filter: drop-shadow(2px 3px 2px rgba(0,0,0,0.25));
  z-index: 10;
}

사진마다 다르게 적용:

큰 얼굴 사진:
- top 95px
- left 80px
- width 230px
- z-index 8

작은 전신 스티커 1:
- top 160px
- left 18px
- width 72px
- z-index 12

흑백 전신 스티커:
- top 390px
- left 20px
- width 85px
- z-index 12

오른쪽 전신 스티커:
- top 340px
- right 20px
- width 90px
- z-index 12

셀카/클로즈업 2명 사진:
- top 260px
- left 150px
- width 180px
- border-radius 약간
- z-index 18

중앙 작은 전신:
- top 235px
- left 145px
- width 85px
- z-index 20

여기서 중요한 점은 사진이 너무 깔끔하게 정렬되면 안 된다. 일부는 살짝 회전시킨다.

.sticker-a { transform: rotate(-3deg); }
.sticker-b { transform: rotate(4deg); }
.sticker-c { transform: rotate(-8deg); }


---

5-5. 3D 하트 스티커

상단 오른쪽에 빨간 3D 하트 풍선이 2~3개 있어야 한다.

색: glossy red
크기: 48~72px
위치: top 35px~150px, right 30px~90px
느낌: 젤리/풍선/플라스틱

CSS radial-gradient로 만들거나 PNG/SVG asset 사용.

.glossy-heart {
  position: absolute;
  width: 62px;
  height: 62px;
  background: radial-gradient(circle at 30% 25%, #fff7 0 12%, transparent 18%),
              linear-gradient(135deg, #ff8a7f, #ff332d);
  filter: drop-shadow(2px 4px 2px rgba(0,0,0,0.25));
  z-index: 25;
}

하트 모양은 SVG 추천.


---

5-6. 초록 하트 / 공원 사진 하트

중앙 하단에 큰 초록색 하트가 있다.

위치: top 430px
크기: width 300px 정도
내용: 잔디/공원/자연 사진을 하트 형태로 마스킹
위에 인물 사진이 겹침

구현:

.green-heart-photo {
  position: absolute;
  top: 420px;
  left: 48px;
  width: 315px;
  height: 260px;
  clip-path: path("M157 250 C-40 125 50 -25 157 70 C265 -25 355 125 157 250 Z");
  object-fit: cover;
  z-index: 4;
}


---

5-7. PHONING 로고

하단 중앙에 큰 레트로 로고 텍스트.

텍스트: PHONING
위치: top 610px 전후
크기: 매우 큼
스타일:
- 민트/하늘색 채움
- 흰색 두꺼운 stroke
- 진한 파란/회색 그림자
- 픽셀/아케이드/varsity 느낌
- 글자 간격 약간 불규칙

CSS 예시:

.phoning-logo {
  position: absolute;
  top: 610px;
  left: 42px;
  z-index: 30;
  font-family: "Galmuri11", "Arial Black", sans-serif;
  font-size: 58px;
  letter-spacing: -4px;
  color: #9ce8ff;
  -webkit-text-stroke: 3px white;
  text-shadow:
    3px 3px 0 #244b6e,
    5px 5px 0 rgba(0,0,0,0.25);
}

글자 하나에 토끼귀, 작은 손그림 아이콘, 스티커 등을 얹으면 더 좋다.


---

5-8. 하단 아이콘 독

하단에는 파란색 둥근 플랫폼이 2단으로 깔려 있다.

첫 번째 플랫폼:
- bottom 80px
- width 390px
- height 90px
- blue gradient
- border-radius top-left/top-right 크게

두 번째 플랫폼:
- bottom 0
- width 390px
- height 75px
- 조금 더 진한 파랑

CSS:

.bottom-platform-1 {
  position: absolute;
  left: 0;
  bottom: 58px;
  width: 100%;
  height: 105px;
  border-radius: 32px 32px 0 0;
  background: linear-gradient(to bottom, #58a0ff, #ffffff);
  z-index: 1;
}

.bottom-platform-2 {
  position: absolute;
  left: 22px;
  bottom: 0;
  width: 360px;
  height: 72px;
  border-radius: 24px 24px 0 0;
  background: linear-gradient(to bottom, #397dff, #8ab8ff);
  z-index: 2;
}

아이콘은 흰색 네모 박스.

.retro-icon {
  width: 58px;
  height: 58px;
  background: white;
  border: 2px solid #777;
  box-shadow: 2px 3px 0 rgba(0,0,0,0.2);
  image-rendering: pixelated;
}

아이콘 배치:

1행:
Calls     Message     Photo     Calendar

2행:
Settings  Profile/Link + Click 말풍선

각 아이콘은 약간 투박해야 한다. SVG로 너무 깔끔하게 그리지 말고, 선을 두껍게 하거나 픽셀 폰트를 사용한다.


---

6. CallScreen 상세 구현

6-1. 화면 배경

.call-screen {
  width: 410px;
  height: 860px;
  background: linear-gradient(to bottom, #6fa7ff 0%, #cde9ff 38%, #fff 70%);
  position: relative;
  overflow: hidden;
}

6-2. 상단 헤더

top: 24px
left: back button
center: title "통화"
right: bell button

.screen-header {
  height: 72px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.screen-title {
  font-size: 24px;
  font-weight: 800;
  color: #111;
}

뒤로가기 버튼은 왼쪽 24px, 알림 버튼은 오른쪽 24px.


---

6-3. 세그먼트 탭

위치: header 아래, 중앙
크기: width 210px, height 34px
왼쪽 active: 라임그린
오른쪽 inactive: 흰색
외곽선: 회색

.call-tabs {
  display: flex;
  margin: 18px auto 42px;
  width: 210px;
  height: 34px;
  border: 2px solid #777;
  border-radius: 999px;
  overflow: hidden;
  background: white;
}

.call-tab {
  flex: 1;
  font-size: 13px;
  font-weight: 700;
  display: flex;
  align-items: center;
  justify-content: center;
}

.call-tab.active {
  background: linear-gradient(to bottom, #c8ff76, #7ee739);
}


---

6-4. 통화 목록

각 row:

height: 94px
left: profile 58px circle
center: name, duration
right: time
divider: 연회색

.call-row {
  width: 310px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: 64px 1fr 82px;
  align-items: center;
  padding: 16px 0;
  border-bottom: 1px solid #e5e5e5;
}

프로필:

.avatar {
  width: 58px;
  height: 58px;
  border-radius: 999px;
  border: 3px solid white;
  outline: 2px solid #888;
  object-fit: cover;
}

이름:

.call-name {
  color: #e13535;
  font-size: 18px;
  font-weight: 800;
}

.call-duration {
  margin-top: 4px;
  color: #777;
  font-size: 15px;
}

.call-time {
  color: #888;
  font-size: 13px;
  text-align: right;
}


---

7. MessageScreen 상세 구현

7-1. 배경

메시지 화면은 전체적으로 연노랑.

.message-screen {
  width: 410px;
  height: 860px;
  background: linear-gradient(to bottom, #fff88f 0%, #fffbd4 22%, #fff 100%);
  position: relative;
  overflow: hidden;
}

7-2. 헤더

top-left: round back button
center: "메시지"

제목은 상단 중앙, 두껍게.

.message-title {
  font-size: 23px;
  font-weight: 900;
}

7-3. 섹션 제목

"대화"는 left 34px, top 105px 정도
"친구"는 대화 리스트 아래, left 34px

.section-title {
  font-size: 20px;
  font-weight: 900;
  margin-left: 34px;
}


---

7-4. 대화 리스트 Row

row height: 82~92px
left avatar: 58px circle with lime border
center: name bold, preview below
right: timestamp + red badge

.chat-row {
  display: grid;
  grid-template-columns: 68px 1fr 58px;
  column-gap: 10px;
  width: 350px;
  margin: 0 auto;
  padding: 12px 0;
  border-bottom: 1px solid #ededed;
}

아바타:

.chat-avatar {
  width: 58px;
  height: 58px;
  border-radius: 999px;
  border: 3px solid #92e644;
  outline: 2px solid #9a9a9a;
  object-fit: cover;
}

텍스트:

.chat-name {
  font-size: 17px;
  font-weight: 900;
}

.chat-preview {
  margin-top: 5px;
  font-size: 13px;
  color: #444;
  line-height: 1.35;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

오른쪽:

.chat-time {
  font-size: 12px;
  color: #aaa;
  text-align: right;
}

.unread-badge {
  margin-left: auto;
  margin-top: 8px;
  min-width: 22px;
  height: 22px;
  padding: 0 6px;
  border-radius: 999px;
  background: #e8202a;
  color: white;
  font-size: 13px;
  font-weight: 900;
  display: flex;
  align-items: center;
  justify-content: center;
}


---

7-5. 친구 리스트

친구 리스트는 대화 리스트보다 더 심플하다.

left: 작은 빨간 점
avatar: 원형, 라임 테두리
name: 굵은 한글
status: 작고 귀여운 메시지

.friend-row {
  position: relative;
  display: grid;
  grid-template-columns: 58px 1fr;
  column-gap: 12px;
  width: 330px;
  margin: 0 auto;
  padding: 11px 0;
  border-bottom: 1px solid #f0f0f0;
}

.online-dot {
  position: absolute;
  left: -8px;
  top: 18px;
  width: 7px;
  height: 7px;
  border-radius: 50%;
  background: #e8202a;
}


---

8. CalendarScreen 상세 구현

8-1. 배경

상단이 형광 라임, 아래로 흰색.

.calendar-screen {
  width: 410px;
  height: 860px;
  background: linear-gradient(to bottom, #42ff4f 0%, #d7ffd5 18%, #fff 45%);
  position: relative;
  overflow-y: auto;
}

overflow-y는 실제 앱처럼 스크롤 가능하게 해도 된다.


---

8-2. 상단 헤더

왼쪽: back button
가운데: "2022년 9월 13일" + down chevron
오른쪽: 오늘 버튼

.calendar-header {
  height: 68px;
  display: grid;
  grid-template-columns: 48px 1fr 70px;
  align-items: center;
  padding: 0 18px;
}

.calendar-date {
  font-size: 22px;
  font-weight: 900;
  text-align: center;
}

.today-btn {
  height: 34px;
  padding: 0 18px;
  border-radius: 999px;
  border: 2px solid #777;
  background: linear-gradient(to bottom, #fff, #dff2ff);
  font-weight: 800;
}


---

8-3. 멤버 필터 원형 리스트

날짜 아래에 가로 원형 필터.

height: 100px
display flex
gap 14px
각 item:
- circle 58px
- 아래 작은 라벨
첫 번째: 체크 아이콘
몇 개는 빈 흰 원
몇 개는 프로필 사진

.member-filter-bar {
  display: flex;
  gap: 12px;
  padding: 8px 18px 20px;
  align-items: flex-start;
}

.member-filter {
  width: 58px;
  text-align: center;
}

.member-circle {
  width: 58px;
  height: 58px;
  border-radius: 999px;
  background: white;
  border: 2px solid #777;
  overflow: hidden;
}

.member-circle.active {
  background: linear-gradient(to bottom, #e7ffd0, #b5f26d);
}

.member-label {
  margin-top: 6px;
  font-size: 9px;
  line-height: 1.1;
}


---

8-4. 날짜 그룹

각 날짜 그룹은 이런 구조.

[2022년 9월 5일]                         [월요일]
  [ schedule card ]
  [ schedule card ]

[2022년 9월 6일]                         [화요일]
  [ schedule card ]

.date-group {
  width: 360px;
  margin: 0 auto 18px;
}

.date-heading {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 10px;
  font-size: 18px;
  font-weight: 900;
}


---

8-5. 일정 카드

카드는 둥글고 테두리가 있다.

.schedule-card {
  min-height: 72px;
  border: 2px solid #666;
  border-radius: 14px;
  background: #fff;
  margin-bottom: 8px;
  padding: 13px 14px;
  display: grid;
  grid-template-columns: 26px 1fr 96px;
  column-gap: 8px;
  align-items: center;
}

.schedule-card.highlight {
  background: linear-gradient(to bottom, #d7ecff, #aed8ff);
}

왼쪽에는 체크 원 또는 작은 프로필.

.schedule-check {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid #e8e8e8;
}

중앙:

.schedule-owner {
  font-size: 11px;
  color: #777;
  margin-bottom: 5px;
}

.schedule-title {
  font-size: 15px;
  font-weight: 900;
}

오른쪽:

.schedule-meta {
  font-size: 11px;
  color: #777;
  text-align: right;
}


---

9. 구현 시 중요한 디테일

9-1. 절대 정렬하지 말 것

홈 화면 콜라주는 flex/grid로 맞추면 안 된다.
position: absolute로 각 요소를 수작업 배치해야 한다.

좋은 방식:

.sticker-01 { top: 120px; left: 60px; width: 220px; z-index: 8; }
.sticker-02 { top: 165px; left: 16px; width: 70px; z-index: 12; transform: rotate(-5deg); }
.sticker-03 { top: 255px; right: 46px; width: 170px; z-index: 18; transform: rotate(2deg); }

나쁜 방식:

display: grid;
grid-template-columns: repeat(3, 1fr);


---

9-2. 이미지 해상도 처리

아이콘과 일부 스티커는 일부러 저해상도처럼 보여야 한다.

.pixelated {
  image-rendering: pixelated;
  image-rendering: crisp-edges;
}

단, 인물 사진 전체를 너무 픽셀화하면 안 된다.
아이콘, 로고, 작은 장식에만 적용.


---

9-3. 그림자

요즘식 부드러운 그림자보다 2000년대식 단순 그림자.

box-shadow: 2px 3px 0 rgba(0,0,0,0.25);
filter: drop-shadow(2px 3px 1px rgba(0,0,0,0.25));

blur가 큰 shadow는 피한다.


---

9-4. 테두리

카드와 버튼은 거의 항상 테두리가 있어야 한다.

border: 2px solid #777;

작은 UI는 1px, 주요 버튼은 2px.


---

9-5. 텍스트

텍스트가 너무 깔끔하면 안 된다.
특히 로고, 아이콘 라벨, 장식 문구는 약간 비뚤어지고 촌스러워야 한다.

아이콘 라벨에는 이런 느낌이 좋다.

Calls
Settings
Calendar
Click
newjeans.kr

영문 장식 문구는 serif italic로.

font-family: Georgia, serif;
font-style: italic;


---

10. 인터랙션 명세

최소 인터랙션은 다음 정도면 충분하다.

홈 아이콘 클릭

Calls 클릭 → 오른쪽 패널 CallScreen 표시
Message 클릭 → 오른쪽 패널 MessageScreen 표시
Calendar 클릭 → 오른쪽 패널 CalendarScreen 표시
Settings 클릭 → 작은 레트로 팝업 표시
Profile 클릭 → 외부 링크 또는 더미 프로필 화면

탭

CallScreen의 전체 / 부재중 전화 탭은 active 상태만 바뀌어도 된다.

Calendar

멤버 필터 원 클릭 시 active border 또는 체크 표시 변경.
일정 카드 필터링까지 구현하면 좋지만 필수는 아니다.

Message

채팅 row 클릭 시 detail 화면까지 만들 필요는 없다.
hover 시 살짝 연하늘색 또는 연노랑으로 바뀌면 충분하다.

.chat-row:hover {
  background: rgba(166, 255, 77, 0.14);
}


---

11. Asset 가이드

필요한 asset 타입:

1. 메인 인물 컷아웃 이미지 5~8개
2. 셀카/클로즈업 이미지 2~3개
3. 프로필용 원형 이미지 5~8개
4. glossy red heart SVG/PNG
5. keychain charm PNG/SVG
6. park/grass photo for green heart mask
7. pixel-style icons:
   - phone
   - envelope
   - photo
   - calendar
   - gear
   - profile/link
8. bunny or small cute doodle icons

인물 사진이 없으면 임시로 placeholder sticker를 사용하되, 반드시 컷아웃처럼 흰색 테두리와 그림자를 넣는다.


---

12. 개발자에게 가장 중요한 한 줄

이 UI는 정돈된 앱 화면이 아니라 “2000년대 팬이 포토샵으로 열심히 꾸민 모바일 팬앱”을 웹으로 재현하는 것이다.
그래서 홈 화면은 absolute-positioned collage, 오른쪽 앱 화면은 retro mobile UI, 모든 요소는 pastel gradient + thick border + sticker shadow + pixel detail로 처리해야 한다.


---

13. 바로 전달용 짧은 작업 지시

Build a responsive front-end clone of a Y2K Korean fan mobile app UI. The page should display a tall collage-style HomeScreen on the left and a switchable app panel on the right for Calls, Messages, and Calendar.

The HomeScreen must be built as an absolute-positioned sticker collage, not a clean grid. Use a sky-blue gradient background with subtle horizontal stripes. Add a dark blue oval speech bubble badge at the top, a large pink heart behind the main portrait area, multiple cut-out photo stickers layered irregularly, glossy red 3D heart stickers, a keychain charm, a green heart-shaped photo mask in the lower middle, and a huge “PHONING” logo in chunky outlined pixel/varsity typography. At the bottom, create a two-layer rounded blue dock with retro square app icons for Calls, Messages, Photos, Calendar, Settings, and profile link.

The right panel should support three screens:
1. CallScreen: blue-to-white gradient, Korean title “통화”, round back/bell buttons, lime segmented control, and call rows with circular avatars, red usernames, durations, and timestamps.
2. MessageScreen: pale yellow background, Korean title “메시지”, conversation rows with lime-bordered avatars, timestamps, red unread badges, Korean previews, and a friends section with red online dots.
3. CalendarScreen: lime-green gradient header, date title “2022년 9월 13일”, today button, circular member filters, and date-grouped rounded schedule cards with Korean titles, small icons, and gray time labels.

Use pastel sky blue, lime green, pale yellow, hot pink, white, gray borders, red notification badges, low-res pixel icons, 1–2px outlines, old-web drop shadows, sticker-style image borders, and glossy gradients. Avoid modern minimalism. The final result should look cute, chaotic, nostalgic, slightly low-resolution, and intentionally early-2000s.
