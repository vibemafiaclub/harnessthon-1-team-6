# 02 — 화면·컴포넌트 명세

- 화면 폭: **390** (기존 화면과 동일. "화면 가로 폭은 기존 화면과 동일하게(390) 맞춰주세요")
- 신규 최상위 프레임 이름은 **`New/` 프리픽스** ("신규 화면의 최상위 프레임 이름은 `New/` 로 시작해주세요")
- 운임 등급 선택은 **별도 화면이 아니라 화면 위에 올라오는 시트** ("별도 화면이 아니라 이 화면 위에 올라오는 형태로 만들어주세요")

## 화면 목록

| ID  | 화면 이름                    | 유형(기본/시트/모달/상태변형) | 근거 (PRD 문장 인용) |
| --- | ---------------------------- | ----------------------------- | -------------------- |
| S01 | New/Home                     | 기본                          | "5-1. 홈 `New/Home` — 서로 다른 이유로 들어온 사용자가 각자 자기 다음 행동을 바로 찾는다" |
| S02 | New/Home-Empty               | 상태변형 (S01 clone)          | "앱을 처음 깐 사용자에게는 예정된 여정도, 담아둔 목적지도 하나도 없습니다." |
| S03 | New/Results                  | 기본                          | "5-2. 항공권 검색 결과 `New/Results` — 수십 건 중에서 나에게 맞는 한 편을 골라 결정까지 간다" / "한 번 검색하면 항공편이 보통 40~60건" |
| S04 | New/Results-Loading          | 상태변형 (S03 clone)          | "항공 요금은 실시간이라 불러오는 데 몇 초가 걸리고" |
| S05 | New/Results-NoMatch          | 상태변형 (S03 clone)          | "조건을 까다롭게 좁히면 맞는 항공편이 한 건도 없는 경우가 나옵니다." |
| S06 | New/Results-FilterSheet      | 시트 (S03 위)                 | "조건 좁히기 — 직항 여부·시간대·항공사 등" (진입점을 누르면 뜨는 시트) |
| S07 | New/Results-FareSheet        | 시트 (S03 위)                 | "운임 등급 선택 — 같은 항공편이라도 환불·변경·수하물 조건이 다른 여러 운임 (별도 화면이 아니라 이 화면 위에 올라오는 형태로 만들어주세요)" |
| S08 | New/Results-CompareSheet     | 시트 (S03 위)                 | "비교함 — 항공편을 담고, 담긴 것들을 나란히 보는 진입점" / "조건이 비슷한 항공권 몇 개를 담아 나란히 비교하고 싶다" / "비교함에는 최대 3개까지 담을 수 있습니다." |
| S09 | New/Results-PriceChanged     | 상태변형 (S03 clone)          | "그 사이 값이 바뀌기도 합니다." / "담아둔 항공편이 결제 전에 마감되는 일이 하루에도 여러 번 있습니다." |

근거 보충 — S01에 반드시 들어갈 5개 영역은 PRD 5-1 필수 요소 그대로: "항공권 검색 진입 — 출발지·목적지·날짜·인원", "예정된 여정 — 출발까지 남은 기간, 그리고 그 목적지에서 살 수 있는 투어·체험·입장권 제안", "둘러보기 묶음 — 특가 항공권과 여행 상품을 여러 갈래로 소개", "담아둔 목적지 — 값이 떨어지면 알림을 받기 위해 담아두는 곳", "하단 주요 이동 수단". 둘러보기 갈래는 PRD가 "직접 정해주세요"라고 위임했으므로 4절 근거로 **특가(정가 대비 70% 할인) · 마감 임박(오늘 자정까지)** 2갈래 + 테마 1갈래로 정한다.

## 컴포넌트 인벤토리

범용 분류 체계 훑기 결과. 해당 없는 분류는 `해당 없음`으로 남긴다.

| 분류         | 훑은 결과 |
| ------------ | --------- |
| Chrome       | 상태바 C01 · 상단바 C02 · 하단 탭바 C03("하단 주요 이동 수단") · 홈 인디케이터는 C03에 포함 |
| Navigation   | 탭/세그먼트 `해당 없음` · 필터 진입점 칩 C07 · 날짜 가격 칩 C06 · 페이지네이션 `해당 없음`(세로 무한 리스트) |
| Collection   | 리스트 행 C05 · 카드 C09/C10/C11 · 그리드 셀 `해당 없음` · 가로 스크롤 레일은 카드 인스턴스 나열로 구성 · 섹션 헤더 C04 |
| Data display | 뱃지 C08 · 아바타 `해당 없음` · 수치/지표는 각 행·카드의 가격 슬롯으로 흡수 · 게이지·차트 `해당 없음`(전후 날짜 가격 비교는 칩 C06으로 표현) · 메타 라인은 C05 내부 요소로 흡수 |
| Action       | 주 버튼 C13 · 아이콘 버튼은 C02의 variant로 흡수 · FAB `해당 없음` · 하단 고정 액션바는 S03 화면 고유 요소(CompareBar) |
| Input        | 텍스트 입력 `해당 없음`(검색 진입은 홈의 진입점 카드이며 입력 화면은 스코프 밖) · 선택 행 C16 · 토글은 C16의 variant로 흡수 · 스테퍼 `해당 없음` · 업로더 `해당 없음` |
| Overlay      | 시트 C14 · 모달 `해당 없음`(운임 선택 포함 전부 시트) · 스크림은 함정 회피를 위해 뒤 보드 opacity로 대체(컴포넌트 아님, 3단계 인계 메모 참조) · 토스트 C18 |
| Feedback     | 빈 상태 C19 · 스켈레톤 C20 · 에러·재시도는 C19의 variant로 흡수(0건+조건 초기화 CTA) · 진행 표시는 C20으로 흡수 |

| ID  | 컴포넌트 이름   | 분류         | 구조 요약 (자식·방향·gap·padding) | variant 축 | 슬롯 (이름:타입) | 사이징 | 토큰 | 승격 근거 |
| --- | --------------- | ------------ | --------------------------------- | ---------- | ---------------- | ------ | ---- | --------- |
| C01 | StatusBar       | Chrome       | 가로 · [시각, spacer, 아이콘군] · padding 16/0 | 단일 | time:텍스트 | 폭 390 fix · 높이 44 fix | color/text-primary, type/caption | 전 화면(2화면 이상) |
| C02 | TopBar          | Chrome       | 가로 · [좌측 아이콘, 타이틀, 우측 아이콘] · gap 8 · padding 16 | type: title\|back | title:텍스트, rightIcon:아이콘 | 폭 390 fix · 높이 56 fix · 타이틀 칸 **fix 폭 260 + 좌측 정렬**(가변 텍스트) | color/bg, color/text-primary, type/title-md | 2화면 (S01 title형, S03 back형: 뒤로가기 + "출발지→목적지·날짜·인원" 요약 타이틀) |
| C03 | TabBar          | Chrome       | 가로 · 탭 아이템 5개 균등 · 하단 홈 인디케이터 포함 · padding 상 8 | 단일 (활성 탭은 인스턴스에서 색 오버라이드) | labels:텍스트×5, icons:아이콘×5 | 폭 390 fix · **높이 84 fix**(화면 하단 고정 — 남는 공간에 맡기지 않음) · 탭 아이템 각 fill | color/bg, color/text-primary, color/text-tertiary, type/caption | 근거: "하단 주요 이동 수단". S01·S02 2화면 |
| C04 | SectionHeader   | Collection   | 가로 · [타이틀, spacer, 액션 텍스트] · padding 20/16/12 | action: none\|more | title:텍스트, action:텍스트 | 폭 390 fix · 타이틀 **fix 폭 280 + 좌측 정렬** | color/text-primary, color/brand, type/title-sm | S01에서 3회 이상(예정 여정·둘러보기 갈래들·담아둔 목적지) + S03(전후 날짜 비교) → 2화면 |
| C05 | FlightRow       | Collection   | 세로 · [상단 가로줄: 시각/소요/뱃지, 경유줄, 하단 가로줄: 항공사·가격·담기버튼] · gap 8 · padding 16 · radius/card | stops: direct\|layover · state: default\|selected\|soldout | depTime:텍스트, arrTime:텍스트, duration:텍스트, layoverText:텍스트("어디서 얼마나 기다리는지" 예: "타이베이 2시간 10분 대기"), airline:텍스트, price:텍스트, badge:C08 인스턴스 | 폭 358 fix · 높이 auto · **가격 칸 fix 폭 110 + 우측 정렬**(가변 텍스트) · 시각 칸 fix 폭 64 | color/surface, color/text-primary, color/text-secondary, type/title-sm, type/body, type/caption, radius/card, space/16 | 근거: "항공편 목록 — 출발·도착 시각, 소요 시간, 경유 여부, 항공사, 가격" + "경유 구간 표현". S03에서 **3회반복** 이상(40~60건 중 대표 5~6행 저작). layover variant는 경유줄 자식이 추가되므로 variant 축으로 관리 |
| C06 | DatePriceChip   | Navigation   | 세로 · [날짜, 가격] · gap 2 · padding 8 · radius/chip | state: default\|selected\|cheapest | date:텍스트, price:텍스트 | **폭 68 fix**(가변 가격 텍스트 — hug 금지) · 높이 52 fix · 텍스트 중앙 정렬 | color/surface, color/brand, color/discount, type/caption, radius/chip | 근거: "출발일 앞뒤 가격 비교 — 며칠 전후로 옮기면 얼마인지". S03 가로 레일에서 **3회반복**(±3일 = 7개) |
| C07 | FilterChip      | Navigation   | 가로 · [텍스트, (화살표 아이콘)] · gap 4 · padding 12/8 · radius/full | state: default\|active | label:텍스트 | 높이 36 fix · 폭 auto(칩은 탭 대상이라 hug 허용 — 값 교체 없음, 라벨 고정) | color/surface, color/brand, color/text-secondary, type/body-sm, radius/full | 근거: "조건 좁히기 — 직항 여부·시간대·항공사 등". S03 칩 그룹에서 **3회반복**(직항·시간대·항공사·정렬) |
| C08 | Badge           | Data display | 가로 · [텍스트] · padding 6/3 · radius/badge | tone: discount\|deadline\|info\|closed | label:텍스트 | 높이 20 fix · 폭 auto | color/discount, color/warning, color/text-tertiary, type/caption-bold, radius/badge | 근거: "정가 대비 70% 할인", "오늘 자정까지", "결제 전에 마감". S01(딜 카드)·S03(항공편 행)·S08(마감) → **2화면 이상** |
| C09 | DealCard        | Collection   | 세로 · [이미지, 뱃지 C08, 목적지 텍스트, 가격] · gap 6 · padding 0(이미지 bleed) + 텍스트부 12 · radius/card | 단일 | image:이미지, badge:C08 인스턴스, title:텍스트, price:텍스트 | **폭 150 fix**(가로 레일 카드) · 이미지 높이 100 fix · **가격 칸 fix 폭 126 + 좌측 정렬** | color/surface, color/text-primary, color/discount, type/body, type/title-sm, radius/card | 근거: "둘러보기 묶음 — 특가 항공권과 여행 상품을 여러 갈래로 소개". S01 각 갈래 레일에서 **3회반복** |
| C10 | SavedDestCard   | Collection   | 가로 · [목적지 이미지(원형/사각 소), 세로: 목적지명+최저가, spacer, 가격 변동 표시] · gap 12 · padding 12 · radius/card | trend: down\|same | image:이미지, name:텍스트, price:텍스트, delta:텍스트 | 폭 358 fix · 높이 72 fix · **가격·변동 칸 fix 폭 96 + 우측 정렬** | color/surface, color/discount, color/text-secondary, type/body, radius/card | 근거: "담아둔 목적지 — 값이 떨어지면 알림을 받기 위해 담아두는 곳" + 유저 스토리 2 "값이 떨어지면 알림을 받고 싶다". S01에서 **3회반복**(담은 목적지 2~3건) |
| C11 | TourCard        | Collection   | 세로 · [이미지, 카테고리 뱃지 C08, 상품명, 가격] · gap 6 · radius/card | 단일 | image:이미지, category:C08 인스턴스, title:텍스트, price:텍스트 | **폭 132 fix**(가로 레일) · 이미지 높이 88 fix · 상품명 **fix 폭 132, 2줄 고정 높이 36** | color/surface, type/body-sm, type/title-sm, radius/card | 근거: "그 목적지에서 살 수 있는 투어·체험·입장권 제안". S01 예정 여정 아래 레일에서 **3회반복** |
| C13 | PrimaryButton   | Action       | 가로 중앙 · [라벨] · radius/button | style: primary\|secondary · state: default\|disabled | label:텍스트 | **폭 358 fix · 높이 52 fix**(시트 하단 고정 CTA — 높이 명시) · 라벨 중앙 정렬 | color/brand, color/text-inverse, color/surface, type/title-sm, radius/button | S06(필터 적용)·S07(이 운임으로 선택)·S08(이 항공편으로 결정)·S05(조건 초기화) → **2화면 이상** |
| C14 | SheetFrame      | Overlay      | 세로 · [그랩바, 시트 헤더(타이틀+닫기), 콘텐츠 슬롯, 하단 CTA 영역] · gap 16 · padding 20 · 상단 radius/sheet | 단일 | title:텍스트, content:반복개수 가변(자식 교체), cta:C13 인스턴스 | 폭 390 fix · 높이 auto(콘텐츠 따라) · **하단 CTA 영역 높이 84 fix**(고정 — spacer에 맡기지 않음) · 타이틀 **fix 폭 300 + 좌측 정렬** | color/bg, color/text-primary, type/title-md, radius/sheet, space/20 | 근거: "이 화면 위에 올라오는 형태". S06·S07·S08 **3개 시트 공통** → 2화면 이상 |
| C15 | FareOptionCard  | Collection   | 세로 · [상단 가로줄: 등급명+가격, 조건 3줄(환불·변경·수하물)] · gap 8 · padding 16 · radius/card · 테두리 | state: default\|selected | name:텍스트, price:텍스트, refund:텍스트, change:텍스트, baggage:텍스트 | 폭 350 fix · 높이 auto · **가격 칸 fix 폭 100 + 우측 정렬** · 조건 라벨 칸 fix 폭 56 | color/surface, color/brand(selected 테두리), color/text-secondary, type/title-sm, type/body-sm, radius/card | 근거: "같은 항공편이라도 환불·변경·수하물 조건이 다른 여러 운임". S07 시트 안에서 **3회반복**(운임 3등급) |
| C16 | FilterOptionRow | Input        | 가로 · [라벨, spacer, 컨트롤(토글/체크/선택값)] · padding 16/12 | control: toggle\|check\|value | label:텍스트, value:텍스트 | 폭 350 fix · 높이 52 fix · **값 칸 fix 폭 120 + 우측 정렬** | color/text-primary, color/brand, type/body, space/16 | 근거: "직항 여부·시간대·항공사 등". S06 시트 안에서 **3회반복** |
| C17 | CompareCard     | Collection   | 세로 · [항공사, 시각·소요, 경유, 가격, 상태 뱃지 C08] · gap 8 · padding 12 · radius/card | state: default\|closed | airline:텍스트, times:텍스트, stops:텍스트, price:텍스트, badge:C08 인스턴스 | **폭 110 fix**(3열 나란히 = 최대 3개) · 높이 168 fix(열끼리 정렬 유지) · 가격 **fix 폭 86 + 좌측 정렬** | color/surface, color/warning(closed), type/body-sm, type/title-sm, radius/card | 근거: "담긴 것들을 나란히 보는" + "최대 3개까지 담을 수 있습니다" + "결제 전에 마감". S08에서 **3회반복**(3열) |
| C18 | Toast           | Overlay      | 가로 · [아이콘, 메시지] · gap 8 · padding 16/12 · radius/card | tone: info\|warning | message:텍스트 | **폭 358 fix** · 높이 48 fix · 메시지 **fix 폭 290 + 좌측 정렬** | color/text-primary(배경 반전), color/text-inverse, type/body-sm, radius/card | S09(가격 변동 안내)·S08(마감 안내) → **2화면 이상** |
| C19 | EmptyState      | Feedback     | 세로 중앙 · [일러스트/아이콘, 타이틀, 설명, CTA C13] · gap 12 · padding 40/24 | 단일 (문구·CTA는 슬롯) | icon:이미지, title:텍스트, desc:텍스트, cta:C13 인스턴스 | 폭 358 fix · 타이틀·설명 **fix 폭 310 + 중앙 정렬** | color/text-primary, color/text-secondary, type/title-sm, type/body | S02(여정·담은 목적지 없음)·S05(결과 0건: "맞는 항공편이 한 건도 없는") → **2화면** |
| C20 | SkeletonRow     | Feedback     | 세로 · [회색 블록 3줄: 시각줄·경유줄·가격줄] · gap 10 · padding 16 · radius/card | 단일 | 반복개수:개수(행 수) | 폭 358 fix · 높이 96 fix(C05와 유사 높이) | color/skeleton, radius/card | 근거: "불러오는 데 몇 초가 걸리고". S04에서 **3회반복**(5행) |

- ID C12는 결번 (초안의 TripCard는 S01에 1회만 등장 → 승격 규칙 "승격 안 함: 한 화면 1회"에 걸려 화면 고유 요소로 강등).
- 매핑 표 검증: 모든 컴포넌트가 1개 이상 화면에서 참조됨 — 삭제 대상 없음.

## 화면 × 컴포넌트 매핑

| 화면 ID | 사용하는 컴포넌트 ID | 인스턴스 수 | 화면 고유 요소 (컴포넌트가 아닌 것) |
| ------- | -------------------- | ----------- | ----------------------------------- |
| S01     | C01, C02(title), C03, C04×4, C08, C09, C10, C11 | C04 4 · C09 6(갈래 2레일×3) · C10 3 · C11 3 · C08 카드 내 포함 | **SearchEntryCard**(출발지·목적지·날짜·인원 진입 카드 — 홈 1회), **TripCard**(예정 여정: 목적지·"출발 D-N" 카운트다운 — 홈 1회) |
| S02     | C01, C02(title), C03, C04, C08, C09, C19 | C19 2(여정 없음·담은 목적지 없음) · C09 6 | SearchEntryCard (둘러보기는 그대로 유지 — 첫 사용자도 "싼 표를 구경하러" 올 수 있음, 유저 스토리 1) |
| S03     | C01, C02(back), C05, C06, C07, C08 | C05 6 · C06 7 · C07 4 | **CompareBar**(하단 고정: 담긴 n/3 표시 + "비교하기" 진입점 — 높이 56 fix, Results 계열에만 존재) |
| S04     | C01, C02(back), C06, C07, C20 | C20 5 | CompareBar 없음(로딩 중) · "요금 불러오는 중" 캡션 |
| S05     | C01, C02(back), C06, C07, C19, C13 | C19 1 | 활성 필터 칩 강조(C07 active) |
| S06     | C14, C16, C13 | C16 4(직항 토글·출발 시간대·항공사·가격순 정렬) | 뒤 보드(S03) opacity 낮춤 |
| S07     | C14, C15, C13, C08 | C15 3 | 시트 상단 선택 항공편 요약 1줄(해당 시트 1회) · 뒤 보드 opacity 낮춤 |
| S08     | C14, C17, C13, C08, C18 | C17 3 | 비교 항목 행 라벨 열(가격·소요·경유·수하물) · 뒤 보드 opacity 낮춤 |
| S09     | C01, C02(back), C05, C06, C07, C18, C08 | C05 6(그중 1행 state=soldout) · C18 1 | CompareBar(1/3 담김 상태) |

## 상태 변형 계획

원본을 다시 짓지 않고 **clone 후 덮어쓸 것만** 적는다.

| 원본 화면 ID | 변형 (빈/로딩/에러/모달) | clone 후 바꿀 것 |
| ------------ | ------------------------ | ---------------- |
| S01          | S02 빈 상태              | TripCard+투어 레일 → C19 인스턴스로 교체, C10×3 → C19 인스턴스로 교체. 나머지(검색 진입·둘러보기·탭바) 유지 |
| S03          | S04 로딩                 | C05×6 → C20×5로 교체, CompareBar 제거, "요금 불러오는 중" 캡션 추가 |
| S03          | S05 결과 0건             | C05×6 → C19 1개("맞는 항공편이 없어요" + CTA "조건 초기화" C13)로 교체, C07 중 2개를 active로 오버라이드 |
| S03          | S06 필터 시트            | 보드 opacity 낮춤 + C14(내용: C16×4, CTA "적용하기") 위에 얹기 |
| S03          | S07 운임 시트            | 보드 opacity 낮춤 + C14(내용: 선택 항공편 요약 + C15×3, CTA "이 운임으로 선택") 위에 얹기 |
| S03          | S08 비교함 시트          | 보드 opacity 낮춤 + C14(내용: C17×3 — 1개는 state=closed + C08 tone=closed, CTA "이 항공편으로 결정") 위에 얹기 |
| S03          | S09 가격변동·마감        | C05 1행 텍스트를 인상된 가격으로 오버라이드 + 1행 state=soldout, 상단에 C18(tone=warning, "요금이 변동됐어요") 추가 |

## 3단계 인계 메모

| 항목                           | 내용 |
| ------------------------------ | ---- |
| 저작 순서 (의존성 낮은 것부터) | ① 리프 컴포넌트: C08 → C13 → C01·C03·C02·C04 → C06·C07 → C20 ② 합성 컴포넌트(리프를 품음): C05 → C09·C10·C11 → C15·C16·C17 → C19 → C18 → C14 ③ 기본 화면: S01 → S03 ④ clone 변형: S02·S04·S05·S09 ⑤ 시트: S06·S07·S08 |
| 이름 확정 목록                 | 화면: `New/Home`, `New/Home-Empty`, `New/Results`, `New/Results-Loading`, `New/Results-NoMatch`, `New/Results-FilterSheet`, `New/Results-FareSheet`, `New/Results-CompareSheet`, `New/Results-PriceChanged`. 컴포넌트: `StatusBar`, `TopBar`, `TabBar`, `SectionHeader`, `FlightRow`, `DatePriceChip`, `FilterChip`, `Badge`, `DealCard`, `SavedDestCard`, `TourCard`, `PrimaryButton`, `SheetFrame`, `FareOptionCard`, `FilterOptionRow`, `CompareCard`, `Toast`, `EmptyState`, `SkeletonRow`. 화면 고유 요소: `SearchEntryCard`, `TripCard`, `CompareBar`. **저작 후 이름 변경 불가 — 이 표기 그대로 생성한다** |
| 1단계에 없어 새로 필요한 토큰  | 이 문서의 토큰명은 의미 기반 가칭이다. 3단계는 저작 전 1단계 산출물의 실제 토큰명과 1:1 매핑하고, 다음이 1단계에 없으면 신규 정의를 요청한다: `color/discount`(70% 할인 강조), `color/warning`(마감·가격변동), `color/skeleton`(로딩 블록), `radius/sheet`(시트 상단 라운드), `radius/badge` |
| 스크림 처리                    | 반투명 스크림 fillOpacity가 렌더링에서 사라지는 함정이 있으므로, 시트 화면(S06~S08)은 스크림 대신 **뒤 화면 보드의 opacity를 낮추는 방식**으로 저작한다 |
