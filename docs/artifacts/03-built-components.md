# 03 — 컴포넌트 빌드 명세

> ⚠️ **이 문서의 컴포넌트는 아직 Penpot에 없다.**
> 4단계가 화면 조합을 시작하기 전에, 아래 빌드 코드를 **빌드 순서대로** 실행해 먼저 만든다.
> 코드 안의 `{PAGE}`는 실행 시점의 작업 Page 이름으로 치환한다 (문서 전체 일괄 치환 가능 — `{PAGE}`는 이 용도 외에 쓰지 않았다).

## 실행 규약 (4단계 필독)

1. **1블록 = 1 `use_figma` 호출.** 몰아 실행하지 않는다. 블록마다 반환값 `{shapeId, componentId}`를 대장에 기록한다.
2. **모든 §Cxx 블록은 "공통 프리앰블" 코드를 블록 코드 맨 앞에 그대로 이어붙여 실행한다.** (프리앰블 = TOKENS 상수 + Page 가드 + 헬퍼 3개. 블록 안에서는 `TOKENS.*`와 헬퍼만 참조한다.)
3. Page 전환은 **별도 호출로 먼저** 한다. 블록 안에서는 전환하지 않는다 — 프리앰블 가드가 Page가 다르면 에러를 반환하고 멈춘다.
4. 블록 실행 후 `export_shape`로 눈으로 확인한다. flex 자식 순서가 뒤집혀 보이면 `board.insertChild(index, child)`로 교정한다 (지우지 않는다).
5. 컴포넌트 이름·자식 이름은 **생성 시점에 박혀 있다. 생성 후 이름 변경·자식 remove 금지** (플러그인이 멈춘다). 잘못 만들었으면 **새 이름**(예: 접미사 `-v2`)으로 새로 만들고 대장의 id를 갱신한다.
6. 빌드 위치는 작업 Page의 `x = -2000` 세로 열(스테이징 영역)이다. 화면 밖이므로 화면 조합과 충돌하지 않는다.
7. variant는 Penpot 컴포넌트 기능이 아니라 **인스턴스 오버라이드 조합**이다. 각 §의 "variant 처방"대로 인스턴스에서 덮는다.
8. **C14(SheetFrame)만 예외 규약**: 인스턴스에는 자식을 추가할 수 없으므로, 4단계는 `inst = comp.instance()` → `inst.detach()` 후 `content` 보드에 자식을 채운다 (§C14 참조).

## 빌드 순서

리프 → 합성(리프 인스턴스를 품음) 순. 괄호는 선행 의존.

```
C08 → C13 → C01 → C02 → C03 → C04 → C06 → C07 → C20
→ C05(C08) → C09(C08) → C10 → C11(C08)
→ C15 → C16 → C17(C08) → C19(C13) → C18 → C14(C13)
```

C12는 결번 (02에서 화면 고유 요소로 강등).

## 컴포넌트 목록

| ID | 이름(빌드 시) | 오버라이드 방법 (슬롯별 — 상세는 각 §) | 빌드 코드 |
|---|---|---|---|
| C01 | `{PAGE}/C01-StatusBar` | 시각→`time`.characters | §C01 |
| C02 | `{PAGE}/C02-TopBar` | 타이틀→`title`.characters / title형→`left-icon`.hidden=true / 우측 아이콘 숨김→`right-icon`.hidden=true | §C02 |
| C03 | `{PAGE}/C03-TabBar` | 레이블→`tab-1-label`~`tab-5-label`.characters / 활성 탭 전환→`tab-N-icon`.fills + `tab-N-label`.fills (활성=textStrong, 비활성=textSub) | §C03 |
| C04 | `{PAGE}/C04-SectionHeader` | 타이틀→`title`.characters / 액션 문구→`action`.characters / action=none→`action`.hidden=true | §C04 |
| C05 | `{PAGE}/C05-FlightRow` | 출발시각→`depTime`.characters / 도착시각→`arrTime`.characters / 소요→`duration`.characters / 경유줄→`layoverText`.characters (직항이면 .hidden=true) / 항공사→`airline`.characters / 가격→`price`.characters / 뱃지→`badge`(C08 인스턴스) 하위 `label`.characters+.fills / soldout→§C05 variant 처방 | §C05 |
| C06 | `{PAGE}/C06-DatePriceChip` | 날짜→`date`.characters / 가격→`price`.characters / selected→루트 strokes=primary / cheapest→`price`.fills=primary | §C06 |
| C07 | `{PAGE}/C07-FilterChip` | 라벨→`label`.characters / active→루트 fills=primary + `label`.fills=textStrong | §C07 |
| C08 | `{PAGE}/C08-Badge` | 문구→`label`.characters / tone→`label`.fills (discount·deadline=accent, info=textSub, closed=danger) | §C08 |
| C09 | `{PAGE}/C09-DealCard` | 이미지→`image`.fills(fillImage) / 뱃지→`badge` 하위 `label`.characters / 목적지→`title`.characters / 가격→`price`.characters | §C09 |
| C10 | `{PAGE}/C10-SavedDestCard` | 이미지→`image`.fills(fillImage) / 목적지명→`name`.characters / 최저가→`price`.characters / 변동→`delta`.characters (trend=same이면 `delta`.fills=textSub) | §C10 |
| C11 | `{PAGE}/C11-TourCard` | 이미지→`image`.fills(fillImage) / 카테고리→`category` 하위 `label`.characters / 상품명→`title`.characters / 가격→`price`.characters | §C11 |
| C13 | `{PAGE}/C13-PrimaryButton` | 라벨→`label`.characters / secondary→루트 fills=bgChip + `label`.fills=textPrimary / disabled→루트 fills=bgChip + `label`.fills=textSub | §C13 |
| C14 | `{PAGE}/C14-SheetFrame` | 타이틀→`title`.characters / 콘텐츠→**detach 후** `content` 보드에 자식 추가 / CTA→`cta`(C13 인스턴스) 하위 `label`.characters | §C14 |
| C15 | `{PAGE}/C15-FareOptionCard` | 등급명→`name`.characters / 가격→`price`.characters / 환불→`refund`.characters / 변경→`change`.characters / 수하물→`baggage`.characters / selected→루트 strokes=primary | §C15 |
| C16 | `{PAGE}/C16-FilterOptionRow` | 라벨→`label`.characters / 값→`value`.characters / control 전환→`toggle`·`check`·`value` 중 하나만 .hidden=false | §C16 |
| C17 | `{PAGE}/C17-CompareCard` | 항공사→`airline`.characters / 시각·소요→`times`.characters / 경유→`stops`.characters / 가격→`price`.characters / 뱃지→`badge` 하위 `label`.characters+.fills / closed→§C17 variant 처방 | §C17 |
| C18 | `{PAGE}/C18-Toast` | 메시지→`message`.characters / tone→`icon`.fills (info=primary, warning=accent) | §C18 |
| C19 | `{PAGE}/C19-EmptyState` | 타이틀→`title`.characters / 설명→`desc`.characters / CTA 문구→`cta` 하위 `label`.characters / CTA 없음→`cta`.hidden=true | §C19 |
| C20 | `{PAGE}/C20-SkeletonRow` | 슬롯 없음 (반복 개수는 4단계가 인스턴스 수로 결정 — S04는 5개) | §C20 |

## 토큰 매핑 확정 (02 가칭 → 01 TOKENS)

02의 토큰명은 의미 기반 가칭이다. 아래로 확정한다. **01에 없어 신규 정의가 필요하다던 5개 토큰은 전부 기존 토큰으로 흡수했다 — "출처 없음—제안값"으로 새 값을 만든 색·radius는 없다.**

| 02 가칭 | 01 확정 | 근거 |
|---|---|---|
| color/bg | `TOKENS.color.bg` #17171B | 화면·섹션 배경 최다 면색 |
| color/surface | `TOKENS.color.bgCard` #1F2026 | 카드·리스트 컨테이너 배경 |
| color/text-primary | `TOKENS.color.textPrimary` #E4E4E5 (단, **제목·활성·버튼라벨은 `textStrong` #FFFFFF** — 01 밝기 위계 4단 준수) | 01 텍스트 위계 |
| color/text-secondary | `TOKENS.color.textBody` #C3C3C6 | 일반 본문 최다 텍스트색 |
| color/text-tertiary | `TOKENS.color.textSub` #9E9EA3 | 보조·비활성 |
| color/brand | `TOKENS.color.primary` #4880EE | 토스 블루 |
| color/text-inverse | `TOKENS.color.textStrong` #FFFFFF (primary 면 위) / **반전 토스트에서는 `TOKENS.color.bg`** (§C18) | — |
| **color/discount** (신규 요청) | `TOKENS.color.primary` — 가격 강조·하락 텍스트. 단 **배지 안 discount 문구는 `accent`** (01 배지 문법: 11px 500 accent) | 01 primary=강조 텍스트 문법, accent=배지 문법 |
| **color/warning** (신규 요청) | `TOKENS.color.accent` #EDA54B. 단 **마감(closed·soldout) 상태 텍스트는 `danger`** #D64854 | 01 danger는 "실패·마감 전용" — 정확히 이 용도 |
| **color/skeleton** (신규 요청) | `TOKENS.color.bgChip` #2C2C34 | bgCard 위에서 한 단계 밝은 기존 면색 |
| type/title-md | `size.cardTitle` 18 / weight 700 | 카드 제목 |
| type/title-sm | `size.emphasis` 16 / 700 | 강조행·금액 |
| type/body | `size.body` 15 / 400 (강조 행은 500) | 최다 본문 |
| type/body-sm | `size.action` 13 / 400 | 링크형 CTA 크기 |
| type/caption | `size.caption` 12 / 400 | 보조 설명 |
| type/caption-bold | `size.badge` 11 / 500 | 배지 |
| radius/card | `TOKENS.radius.lg` 20 (단 **C17 소형 카드는 `md` 12** — 01 "md=중형 요소") | 카드 11회 |
| radius/chip | `TOKENS.radius.sm` 6 | 버튼·칩 6회 |
| radius/button | `TOKENS.radius.md` 12 — 52px 대형 CTA는 칩이 아닌 중형 요소로 판단 | 01 md=중형 요소 |
| **radius/badge** (신규 요청) | `TOKENS.radius.sm` 6 | 버튼·칩 계열 |
| **radius/sheet** (신규 요청) | `TOKENS.radius.lg` 20 — 상단 두 모서리에만 | 카드·컨테이너 |
| radius/full | `TOKENS.radius.full` 999 | 원형 |

### 02 수치의 교정 (01 적용 규칙과 충돌한 것만 — 나머지 02 수치는 그대로)

| 항목 | 02 값 | 확정 값 | 근거 |
|---|---|---|---|
| 풀폭 컴포넌트 폭 (C05·C10·C13·C18·C19·C20) | 358 | **346** | 01 구속 규칙: 콘텐츠 폭 = screen.w − gutter×2 = 390 − 44 = 346 (02의 358은 거터 16 가정) |
| 풀블리드 컴포넌트(C01~C04)의 좌우 패딩 | 16 | **22 (`TOKENS.gutter`)** | 01 "좌우 여백은 항상 gutter" |
| C19 텍스트 칸 폭 | 310 | **290** | 폭 346 − 좌우 패딩 24×2 = 298 안쪽 |
| C18 메시지 칸 폭 | 290 | **280** | 폭 346 − 패딩·아이콘 실계산 |
| StatusBar 시각 크기 | caption 12 | **emphasis 16 / 500** | 01 관찰 "9:41" 17px은 비토큰 → 최근접 토큰 16으로 흡수 |
| C04 타이틀 크기 | title-sm | **`size.sectionTitle` 20 / 700** | 01 관찰 노트 "섹션 헤더 20px 700 textStrong" |

**제안값(입력에 수치 근거 없음 — 명시):** 내부 슬롯 폭 일부(C06 텍스트 60, C10 `name`·`price` 140, C15 `name` 160, C16 `label` 160, C08 `label` 72/보드 84)는 컴포넌트 내부 폭 산식으로 도출한 제안값이다. 아이콘 자리는 전부 플레이스홀더 도형(글리프 텍스트·ellipse·rect)이며, 스테이징 좌표(x=-2000)는 운영 편의값이다.

**danger 사용 기록 (01 규칙 준수):** `danger` #D64854는 C08 tone=closed, C05 state=soldout, C17 state=closed의 **마감 표시에만** 사용한다. 그 외 사용 금지.

## 사용한 토큰 스냅샷 + 공통 프리앰블

아래 코드 전체를 **모든 §Cxx 블록 코드 맨 앞에 그대로 이어붙인다.**

```js
// ── 공통 프리앰블 (01의 TOKENS 원본 그대로 + 가드 + 헬퍼) ──
const TOKENS = {
  color: {
    bg: "#17171B",
    bgCard: "#1F2026",
    bgChip: "#2C2C34",
    textStrong: "#FFFFFF",
    textPrimary: "#E4E4E5",
    textBody: "#C3C3C6",
    textSub: "#9E9EA3",
    primary: "#4880EE",
    accent: "#EDA54B",
    line: "#62626C",
    danger: "#D64854", // 마감·실패 전용 — C05 soldout / C08 closed / C17 closed에만
  },
  font: {
    family: "Noto Sans KR",
    familyLatin: "Noto Sans KR",
    weight: { regular: 400, medium: 500, bold: 700 },
    size: { pageTitle: 24, sectionTitle: 20, cardTitle: 18, emphasis: 16, body: 15, action: 13, caption: 12, badge: 11, nav: 10 },
    lineHeight: { default: 1.2 },
  },
  space: { xxs: 2, xs: 4, sm: 6, md: 10, lg: 13, xl: 15, xxl: 20 },
  gutter: 22,
  radius: { sm: 6, md: 12, lg: 20, full: 999 },
  screen: { w: 390, h: 844 },
};
const CONTENT_W = TOKENS.screen.w - TOKENS.gutter * 2; // 346

// 작업 Page 가드 — 전환은 하지 않는다 (전환은 4단계가 별도 호출로 선행)
if (penpot.currentPage.name !== "{PAGE}") {
  return { error: "작업 Page가 아님: " + penpot.currentPage.name };
}

// 텍스트: 생성 시점 명명. 가변 칸은 fix 폭 + auto-height + 정렬 (hug 금지 / growType fixed 금지)
function mkText(name, chars, size, weight, colorHex, opts) {
  opts = opts || {};
  const t = penpot.createText(chars);
  t.name = name;
  t.fontFamily = TOKENS.font.family;
  t.fontSize = String(size);
  t.fontWeight = String(weight);
  t.lineHeight = String(TOKENS.font.lineHeight.default);
  t.fills = [{ fillColor: colorHex, fillOpacity: 1 }]; // penpot 형식 — figma 형식 금지
  if (opts.width) {
    t.growType = "auto-height";
    t.resize(opts.width, t.height);
    t.align = opts.align || "left";
  } else {
    t.growType = "auto-width"; // 값 교체가 없는 고정 라벨·글리프만 허용
  }
  return t;
}

// 보드: 생성 시점 명명 + 명시 fill (fillHex 없으면 투명)
function mkBoard(name, w, h, fillHex) {
  const b = penpot.createBoard();
  b.name = name;
  b.resize(w, h);
  b.fills = fillHex ? [{ fillColor: fillHex, fillOpacity: 1 }] : [];
  return b;
}

// 선행 컴포넌트 조회 — {PAGE}/ 프리픽스 전체 일치로만 찾는다 (이름은 파일 전역이라 프리픽스 필수)
function findComp(name) {
  const c = penpot.library.local.components.find(function (k) { return k.name === name; });
  if (!c) throw new Error("선행 컴포넌트 없음: " + name + " — 빌드 순서 위반");
  return c;
}
// ── 프리앰블 끝. 이 아래에 §Cxx 블록 코드를 붙인다 ──
```

---

## §C08 — Badge

- 구조: 가로 중앙 정렬 단일 칩. 84×20 fix, bgChip 면 + radius sm.
- 슬롯: `label`(텍스트, fix 72 중앙 — tone별로 문구가 바뀌는 가변 칸).
- variant 처방(tone, 인스턴스 오버라이드): discount·deadline=`label`.fills accent(기본값) / info=textSub / closed=**danger**(마감 전용).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C08-Badge", 84, 20, TOKENS.color.bgChip);
board.x = -2000; board.y = 0;
board.borderRadius = TOKENS.radius.sm;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.justifyContent = "center";
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const label = mkText("label", "70% 할인", TOKENS.font.size.badge, TOKENS.font.weight.medium,
  TOKENS.color.accent, { width: 72, align: "center" });
board.appendChild(label);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C13 — PrimaryButton

- 구조: 가로 중앙·수직 중앙 단일 라벨. 346×52 fix, primary 면 + radius md(12 — 대형 CTA는 중형 요소로 확정).
- 슬롯: `label`(텍스트, fix 300 중앙).
- variant 처방: secondary=루트 fills bgChip + `label`.fills textPrimary / disabled=루트 fills bgChip + `label`.fills textSub. 시트 안(내폭 350)에서는 인스턴스를 `resize(350, 52)`.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C13-PrimaryButton", CONTENT_W, 52, TOKENS.color.primary);
board.x = -2000; board.y = 300;
board.borderRadius = TOKENS.radius.md;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.justifyContent = "center";
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const label = mkText("label", "확인", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textStrong, { width: 300, align: "center" });
board.appendChild(label);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C01 — StatusBar

- 구조: 가로 space-between. 390×44 fix, 투명(화면 배경 노출). 좌우 패딩 gutter 22.
- 슬롯: `time`(텍스트, fix 60 좌측). 우측 아이콘군은 플레이스홀더 rect 3개(오버라이드 대상 아님).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C01-StatusBar", TOKENS.screen.w, 44, null);
board.x = -2000; board.y = 600;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.justifyContent = "space-between";
flex.horizontalPadding = TOKENS.gutter;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const time = mkText("time", "9:41", TOKENS.font.size.emphasis, TOKENS.font.weight.medium,
  TOKENS.color.textStrong, { width: 60, align: "left" });
board.appendChild(time);

const icons = mkBoard("status-icons", 62, 12, null);
const iflex = icons.addFlexLayout();
iflex.dir = "row";
iflex.alignItems = "center";
iflex.columnGap = TOKENS.space.xs;
icons.horizontalSizing = "fix";
["signal", "wifi", "battery"].forEach(function (n) {
  const r = penpot.createRectangle();
  r.name = "icon-" + n;
  r.resize(16, 10);
  r.fills = [{ fillColor: TOKENS.color.textStrong, fillOpacity: 1 }];
  icons.appendChild(r);
});
board.appendChild(icons);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C02 — TopBar

- 구조: 가로 [`left-icon` 24 | `title` fix 260 좌측 | `right-icon` 24] · gap 8(02) · 좌우 패딩 22 · bg 면. 390×56 fix.
- 슬롯: `title`(가변 텍스트 fix 260 좌측 auto-height — back형 요약 "출발지→목적지·날짜·인원"이 길면 2줄로 늘어난다).
- variant 처방: title형=`left-icon`.hidden true / back형=기본값(뒤로가기 노출) / 우측 불필요 화면=`right-icon`.hidden true.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C02-TopBar", TOKENS.screen.w, 56, TOKENS.color.bg);
board.x = -2000; board.y = 900;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.columnGap = 8; // 02 명시값
flex.horizontalPadding = TOKENS.gutter;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const leftIcon = mkBoard("left-icon", 24, 24, null);
const lflex = leftIcon.addFlexLayout();
lflex.dir = "row"; lflex.alignItems = "center"; lflex.justifyContent = "center";
leftIcon.appendChild(mkText("left-icon-glyph", "←", TOKENS.font.size.cardTitle,
  TOKENS.font.weight.regular, TOKENS.color.textStrong, null));
board.appendChild(leftIcon);

const title = mkText("title", "화면 제목", TOKENS.font.size.cardTitle, TOKENS.font.weight.bold,
  TOKENS.color.textStrong, { width: 260, align: "left" });
board.appendChild(title);

const rightIcon = mkBoard("right-icon", 24, 24, null);
const rflex = rightIcon.addFlexLayout();
rflex.dir = "row"; rflex.alignItems = "center"; rflex.justifyContent = "center";
rightIcon.appendChild(mkText("right-icon-glyph", "✕", TOKENS.font.size.emphasis,
  TOKENS.font.weight.regular, TOKENS.color.textStrong, null));
board.appendChild(rightIcon);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C03 — TabBar

- 구조: 세로 [탭 행(5개 균등, 각 78×48) | 홈 인디케이터 rect 134×5 radius full] · 상하 패딩 8(02) · bg 면. 390×**84 fix**(화면 하단 고정 — spacer에 맡기지 않는다).
- 슬롯: `tab-1-label`~`tab-5-label`(텍스트 fix 70 중앙). 아이콘 자리는 `tab-N-icon` ellipse 22 플레이스홀더.
- variant 처방: 활성 탭=해당 `tab-N-icon`.fills textStrong + `tab-N-label`.fills textStrong (빌드 기본값: tab-1 활성, 나머지 textSub). 레이블 기본값은 플레이스홀더 — **4단계가 PRD의 "하단 주요 이동 수단"으로 반드시 오버라이드**.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C03-TabBar", TOKENS.screen.w, 84, TOKENS.color.bg);
board.x = -2000; board.y = 1200;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.alignItems = "center";
flex.justifyContent = "space-between";
flex.verticalPadding = 8; // 02 명시값
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const tabs = mkBoard("tabs", TOKENS.screen.w, 48, null);
const tflex = tabs.addFlexLayout();
tflex.dir = "row";
tflex.alignItems = "center";
tabs.horizontalSizing = "fix";
for (let i = 1; i <= 5; i++) {
  const active = i === 1;
  const c = active ? TOKENS.color.textStrong : TOKENS.color.textSub;
  const item = mkBoard("tab-" + i, 78, 48, null);
  const iflex = item.addFlexLayout();
  iflex.dir = "column";
  iflex.alignItems = "center";
  iflex.justifyContent = "center";
  iflex.rowGap = TOKENS.space.xs;
  item.horizontalSizing = "fix";
  const icon = penpot.createEllipse();
  icon.name = "tab-" + i + "-icon";
  icon.resize(22, 22);
  icon.fills = [{ fillColor: c, fillOpacity: 1 }];
  item.appendChild(icon);
  item.appendChild(mkText("tab-" + i + "-label", "탭" + i, TOKENS.font.size.nav,
    TOKENS.font.weight.regular, c, { width: 70, align: "center" }));
  tabs.appendChild(item);
}
board.appendChild(tabs);

const indicator = penpot.createRectangle();
indicator.name = "home-indicator";
indicator.resize(134, 5);
indicator.borderRadius = TOKENS.radius.full;
indicator.fills = [{ fillColor: TOKENS.color.textStrong, fillOpacity: 1 }];
board.appendChild(indicator);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C04 — SectionHeader

- 구조: 가로 space-between [`title` | `action`] · 패딩 상 20 하 12(02) 좌우 22(gutter 교정). 390 fix × 56 fix.
- 슬롯: `title`(fix 280 좌측, sectionTitle 20/700 — 01 관찰 노트대로 교정), `action`(fix 60 우측, action 13/400 primary — 링크형 CTA 문법).
- variant 처방: action=none이면 `action`.hidden true.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C04-SectionHeader", TOKENS.screen.w, 56, null);
board.x = -2000; board.y = 1500;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "end";
flex.justifyContent = "space-between";
flex.topPadding = TOKENS.space.xxl;
flex.bottomPadding = TOKENS.radius.md; // 12 (02 명시값과 동수)
flex.horizontalPadding = TOKENS.gutter;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const title = mkText("title", "섹션 제목", TOKENS.font.size.sectionTitle, TOKENS.font.weight.bold,
  TOKENS.color.textStrong, { width: 280, align: "left" });
board.appendChild(title);
const action = mkText("action", "더보기", TOKENS.font.size.action, TOKENS.font.weight.regular,
  TOKENS.color.primary, { width: 60, align: "right" });
board.appendChild(action);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C06 — DatePriceChip

- 구조: 세로 중앙 [`date` | `price`] · gap 2(space.xxs) · bgCard 면 + radius sm. **68×52 fix** (가변 가격 — hug 금지, 내부 텍스트 fix 60 중앙).
- 슬롯: `date`, `price`.
- variant 처방: selected=루트 strokes `[{strokeColor: primary, strokeOpacity: 1, strokeWidth: 1}]` / cheapest=`price`.fills primary.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C06-DatePriceChip", 68, 52, TOKENS.color.bgCard);
board.x = -2000; board.y = 1800;
board.borderRadius = TOKENS.radius.sm;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.alignItems = "center";
flex.justifyContent = "center";
flex.rowGap = TOKENS.space.xxs;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

board.appendChild(mkText("date", "5.12(화)", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textSub, { width: 60, align: "center" }));
board.appendChild(mkText("price", "12.4만", TOKENS.font.size.caption, TOKENS.font.weight.medium,
  TOKENS.color.textPrimary, { width: 60, align: "center" }));

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C07 — FilterChip

- 구조: 가로 [`label` | `arrow` 글리프] · gap 4 · 패딩 좌우 12 상하 8(02) · bgChip 면 + radius full. 높이 36 fix · **폭 auto(hug)** — 02가 "라벨 고정, 값 교체 없음"으로 hug를 허용한 유일한 칩.
- 슬롯: `label`(auto-width — 교체 시 칩 폭이 따라 늘어난다).
- variant 처방: active=루트 fills primary + `label`.fills textStrong + `arrow`.fills textStrong.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C07-FilterChip", 90, 36, TOKENS.color.bgChip);
board.x = -2000; board.y = 2100;
board.borderRadius = TOKENS.radius.full;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.columnGap = TOKENS.space.xs;
flex.horizontalPadding = TOKENS.radius.md; // 12 (02 명시값과 동수)
flex.verticalPadding = 8; // 02 명시값
board.horizontalSizing = "auto"; // hug 허용 케이스
board.verticalSizing = "fix";

board.appendChild(mkText("label", "직항", TOKENS.font.size.action, TOKENS.font.weight.regular,
  TOKENS.color.textBody, null));
board.appendChild(mkText("arrow", "▾", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textSub, null));

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C20 — SkeletonRow

- 구조: 세로 [회색 블록 3줄] · gap 10(space.md) · 패딩 상하 15 좌우 22(01 카드 패딩) · bgCard 면 + radius lg. 346×96 fix (C05 유사 높이).
- 슬롯: 없음. 블록 색 = bgChip(=color/skeleton 확정값).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C20-SkeletonRow", CONTENT_W, 96, TOKENS.color.bgCard);
board.x = -2000; board.y = 2400;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.rowGap = TOKENS.space.md;
flex.verticalPadding = TOKENS.space.xl;
flex.horizontalPadding = TOKENS.gutter;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

[["bar-time", 180, 14], ["bar-stops", 120, 12], ["bar-price", 90, 14]].forEach(function (s) {
  const r = penpot.createRectangle();
  r.name = s[0];
  r.resize(s[1], s[2]);
  r.borderRadius = TOKENS.radius.sm;
  r.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
  board.appendChild(r);
});

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C05 — FlightRow (선행: C08)

- 구조: 세로 3줄 [시각줄 | 경유줄 | 하단줄] · gap 8(02) · 패딩 상하 15 좌우 22 · bgCard 면 + radius lg. **346 fix × 높이 auto**. 내부 폭 302.
- 슬롯: `depTime`(fix 56 좌) `arrTime`(fix 56 좌) `duration`(fix 60 우) `layoverText`(fix 302 좌 — "어디서 얼마나 기다리는지") `airline`(fix 120 좌) `price`(fix 110 우 — 가변 가격, hug 금지) `badge`(C08 인스턴스).
- variant 처방: stops=direct → `layoverText`.hidden true / state=selected → 루트 strokes primary 1px / state=soldout → `depTime`·`arrTime`·`airline`·`price`.fills textSub + `badge` 하위 `label`.characters "마감" + `label`.fills **danger**(마감 전용 — 사용 기록됨).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C05-FlightRow", CONTENT_W, 120, TOKENS.color.bgCard);
board.x = -2000; board.y = 2700;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.rowGap = 8; // 02 명시값
flex.verticalPadding = TOKENS.space.xl;
flex.horizontalPadding = TOKENS.gutter;
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const INNER = CONTENT_W - TOKENS.gutter * 2; // 302

// 1줄: 시각·소요·뱃지
const rowTimes = mkBoard("row-times", INNER, 24, null);
const f1 = rowTimes.addFlexLayout();
f1.dir = "row"; f1.alignItems = "center"; f1.columnGap = 8;
rowTimes.horizontalSizing = "fix"; rowTimes.verticalSizing = "auto";
rowTimes.appendChild(mkText("depTime", "07:30", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 56, align: "left" }));
rowTimes.appendChild(mkText("time-arrow", "→", TOKENS.font.size.action, TOKENS.font.weight.regular,
  TOKENS.color.textSub, null));
rowTimes.appendChild(mkText("arrTime", "10:55", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 56, align: "left" }));
rowTimes.appendChild(mkText("duration", "3시간 25분", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textSub, { width: 60, align: "right" }));
const badge = findComp("{PAGE}/C08-Badge").instance();
badge.name = "badge";
rowTimes.appendChild(badge);
board.appendChild(rowTimes);

// 2줄: 경유 (direct variant에서 hidden)
board.appendChild(mkText("layoverText", "타이베이 2시간 10분 대기", TOKENS.font.size.caption,
  TOKENS.font.weight.regular, TOKENS.color.textSub, { width: INNER, align: "left" }));

// 3줄: 항공사·가격·담기
const rowBottom = mkBoard("row-bottom", INNER, 32, null);
const f3 = rowBottom.addFlexLayout();
f3.dir = "row"; f3.alignItems = "center"; f3.justifyContent = "space-between";
rowBottom.horizontalSizing = "fix"; rowBottom.verticalSizing = "auto";
rowBottom.appendChild(mkText("airline", "항공사명", TOKENS.font.size.action, TOKENS.font.weight.regular,
  TOKENS.color.textBody, { width: 120, align: "left" }));
const rowPrice = mkBoard("row-price", 146, 32, null);
const fp = rowPrice.addFlexLayout();
fp.dir = "row"; fp.alignItems = "center"; fp.columnGap = 8;
rowPrice.horizontalSizing = "fix";
rowPrice.appendChild(mkText("price", "124,000원", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 110, align: "right" }));
const saveBtn = penpot.createEllipse();
saveBtn.name = "save-btn";
saveBtn.resize(28, 28);
saveBtn.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
rowPrice.appendChild(saveBtn);
rowBottom.appendChild(rowPrice);
board.appendChild(rowBottom);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C09 — DealCard (선행: C08)

- 구조: 세로 [이미지 블리드 | 본문(뱃지·목적지·가격)] · 본문 gap 6 패딩 좌우/하 12 · bgCard 면 + radius lg(이미지는 상단 두 모서리만 20). **150 fix**(가로 레일) × auto.
- 슬롯: `image`(rect 150×100 — 실사진은 fillImage 패턴) `badge`(C08 인스턴스) `title`(fix 126 좌) `price`(fix 126 좌, **primary** = color/discount 확정값).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C09-DealCard", 150, 200, TOKENS.color.bgCard);
board.x = -2000; board.y = 3000;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "column";
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const image = penpot.createRectangle();
image.name = "image";
image.resize(150, 100);
image.borderRadiusTopLeft = TOKENS.radius.lg;
image.borderRadiusTopRight = TOKENS.radius.lg;
image.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
// 실사진 오버라이드(4단계): const img = await penpot.uploadMediaUrl(name, url);
//                          inst의 image.fills = [{ fillOpacity: 1, fillImage: img }];
board.appendChild(image);

const body = mkBoard("deal-body", 150, 100, null);
const bflex = body.addFlexLayout();
bflex.dir = "column";
bflex.rowGap = TOKENS.space.sm;
bflex.horizontalPadding = TOKENS.radius.md; // 12 (02 명시값과 동수)
bflex.topPadding = TOKENS.space.md;
bflex.bottomPadding = TOKENS.radius.md;
body.horizontalSizing = "fix";
body.verticalSizing = "auto";

const badge = findComp("{PAGE}/C08-Badge").instance();
badge.name = "badge";
body.appendChild(badge);
body.appendChild(mkText("title", "목적지", TOKENS.font.size.body, TOKENS.font.weight.medium,
  TOKENS.color.textPrimary, { width: 126, align: "left" }));
body.appendChild(mkText("price", "89,000원~", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.primary, { width: 126, align: "left" }));
board.appendChild(body);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C10 — SavedDestCard

- 구조: 가로 space-between [`image` 48 | 세로 스택(`name`·`price`) | `delta`] · gap 12(02) · 패딩 12 · bgCard 면 + radius lg. 346×72 fix.
- 슬롯: `image`(rect 48×48 radius md) `name`(fix 140 좌) `price`(fix 140 좌) `delta`(fix 96 우 — 가격 변동, primary = color/discount 확정값).
- variant 처방: trend=same → `delta`.fills textSub.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C10-SavedDestCard", CONTENT_W, 72, TOKENS.color.bgCard);
board.x = -2000; board.y = 3300;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.justifyContent = "space-between";
flex.columnGap = 12; // 02 명시값
flex.horizontalPadding = TOKENS.radius.md; // 12
flex.verticalPadding = TOKENS.radius.md;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const image = penpot.createRectangle();
image.name = "image";
image.resize(48, 48);
image.borderRadius = TOKENS.radius.md;
image.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
board.appendChild(image);

const info = mkBoard("dest-info", 140, 48, null);
const iflex = info.addFlexLayout();
iflex.dir = "column";
iflex.rowGap = TOKENS.space.xxs;
iflex.justifyContent = "center";
info.horizontalSizing = "fix";
info.appendChild(mkText("name", "목적지명", TOKENS.font.size.body, TOKENS.font.weight.medium,
  TOKENS.color.textPrimary, { width: 140, align: "left" }));
info.appendChild(mkText("price", "최저 124,000원", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textSub, { width: 140, align: "left" }));
board.appendChild(info);

board.appendChild(mkText("delta", "▼ 12,000원", TOKENS.font.size.action, TOKENS.font.weight.medium,
  TOKENS.color.primary, { width: 96, align: "right" }));

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C11 — TourCard (선행: C08)

- 구조: 세로 [이미지 132×88 | `category` 뱃지 | `title` 2줄 고정 | `price`] · gap 6 · bgCard 면 + radius lg. **132 fix** × auto. 텍스트부 좌우 패딩 10.
- 슬롯: `image` `category`(C08 인스턴스) `title`(fix 112, resize(112,36)으로 2줄 높이 고정) `price`(fix 112 좌).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C11-TourCard", 132, 200, TOKENS.color.bgCard);
board.x = -2000; board.y = 3600;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "column";
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const image = penpot.createRectangle();
image.name = "image";
image.resize(132, 88);
image.borderRadiusTopLeft = TOKENS.radius.lg;
image.borderRadiusTopRight = TOKENS.radius.lg;
image.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
board.appendChild(image);

const body = mkBoard("tour-body", 132, 100, null);
const bflex = body.addFlexLayout();
bflex.dir = "column";
bflex.rowGap = TOKENS.space.sm;
bflex.horizontalPadding = TOKENS.space.md;
bflex.topPadding = TOKENS.space.md;
bflex.bottomPadding = TOKENS.radius.md;
body.horizontalSizing = "fix";
body.verticalSizing = "auto";

const category = findComp("{PAGE}/C08-Badge").instance();
category.name = "category";
body.appendChild(category);
const title = mkText("title", "투어·체험 상품명 두 줄까지", TOKENS.font.size.action,
  TOKENS.font.weight.regular, TOKENS.color.textBody, { width: 112, align: "left" });
title.resize(112, 36); // 2줄 고정 높이 (02 명시)
body.appendChild(title);
body.appendChild(mkText("price", "45,000원", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 112, align: "left" }));
board.appendChild(body);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C15 — FareOptionCard

- 구조: 세로 [상단 가로줄(`name`·`price`) | 조건 3줄(라벨 fix 56 + 값)] · gap 8(02) · 패딩 15 · bgCard 면 + radius lg + **line 테두리**. 350 fix(시트 내폭) × auto. 내부 폭 320.
- 슬롯: `name`(fix 160 좌) `price`(fix 100 우) `refund` `change` `baggage`(각 fix 240 좌 — 조건 값 텍스트. 라벨 `refund-label` 등은 고정문구 "환불"·"변경"·"수하물", 오버라이드 대상 아님).
- variant 처방: selected=루트 strokes `[{strokeColor: primary, strokeOpacity: 1, strokeWidth: 2}]`.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C15-FareOptionCard", 350, 160, TOKENS.color.bgCard);
board.x = -2000; board.y = 3900;
board.borderRadius = TOKENS.radius.lg;
board.strokes = [{ strokeColor: TOKENS.color.line, strokeOpacity: 1, strokeWidth: 1 }];
const flex = board.addFlexLayout();
flex.dir = "column";
flex.rowGap = 8; // 02 명시값
flex.verticalPadding = TOKENS.space.xl;
flex.horizontalPadding = TOKENS.space.xl;
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const INNER = 350 - TOKENS.space.xl * 2; // 320

const head = mkBoard("fare-head", INNER, 24, null);
const hflex = head.addFlexLayout();
hflex.dir = "row"; hflex.alignItems = "center"; hflex.justifyContent = "space-between";
head.horizontalSizing = "fix"; head.verticalSizing = "auto";
head.appendChild(mkText("name", "스탠다드", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textStrong, { width: 160, align: "left" }));
head.appendChild(mkText("price", "124,000원", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 100, align: "right" }));
board.appendChild(head);

[["refund", "환불", "출발 전 무료 환불"], ["change", "변경", "1회 무료 변경"],
 ["baggage", "수하물", "위탁 15kg 포함"]].forEach(function (row) {
  const line = mkBoard("cond-" + row[0], INNER, 18, null);
  const lflex = line.addFlexLayout();
  lflex.dir = "row"; lflex.alignItems = "center"; lflex.columnGap = 8;
  line.horizontalSizing = "fix"; line.verticalSizing = "auto";
  line.appendChild(mkText(row[0] + "-label", row[1], TOKENS.font.size.caption,
    TOKENS.font.weight.regular, TOKENS.color.textSub, { width: 56, align: "left" }));
  line.appendChild(mkText(row[0], row[2], TOKENS.font.size.action, TOKENS.font.weight.regular,
    TOKENS.color.textBody, { width: 240, align: "left" }));
  board.appendChild(line);
});

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C16 — FilterOptionRow

- 구조: 가로 space-between [`label` | 컨트롤 자리(`toggle`·`check`·`value` 3종 공존, 2종 hidden)] · 패딩 좌우 16 상하 12(02) · 투명(시트 bg 노출). 350×52 fix.
- 슬롯: `label`(fix 160 좌) `value`(fix 120 우, primary).
- variant 처방(control): 기본=value만 노출. toggle형→`value`.hidden true + `toggle`.hidden false / check형→`value`.hidden true + `check`.hidden false. 토글 off는 `toggle`.fills bgChip + `toggle-knob` x 이동 대신 **인스턴스에서 `toggle`.fills만 bgChip으로** (knob 위치는 고정 — 표현 단순화).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C16-FilterOptionRow", 350, 52, null);
board.x = -2000; board.y = 4200;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.justifyContent = "space-between";
flex.horizontalPadding = 16; // 02 명시값
flex.verticalPadding = TOKENS.radius.md; // 12
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

board.appendChild(mkText("label", "직항만 보기", TOKENS.font.size.body, TOKENS.font.weight.regular,
  TOKENS.color.textPrimary, { width: 160, align: "left" }));

const controls = mkBoard("controls", 120, 28, null);
const cflex = controls.addFlexLayout();
cflex.dir = "row"; cflex.alignItems = "center"; cflex.justifyContent = "end";
controls.horizontalSizing = "fix";

const toggle = mkBoard("toggle", 44, 26, TOKENS.color.primary);
toggle.borderRadius = TOKENS.radius.full;
const tflex = toggle.addFlexLayout();
tflex.dir = "row"; tflex.alignItems = "center"; tflex.justifyContent = "end";
tflex.horizontalPadding = 3;
const knob = penpot.createEllipse();
knob.name = "toggle-knob";
knob.resize(20, 20);
knob.fills = [{ fillColor: TOKENS.color.textStrong, fillOpacity: 1 }];
toggle.appendChild(knob);
toggle.hidden = true;
controls.appendChild(toggle);

const check = penpot.createEllipse();
check.name = "check";
check.resize(22, 22);
check.fills = [{ fillColor: TOKENS.color.primary, fillOpacity: 1 }];
check.hidden = true;
controls.appendChild(check);

controls.appendChild(mkText("value", "선택값", TOKENS.font.size.body, TOKENS.font.weight.regular,
  TOKENS.color.primary, { width: 120, align: "right" }));
board.appendChild(controls);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C17 — CompareCard (선행: C08)

- 구조: 세로 [`airline` | `times` | `stops` | `price` | `badge`] · gap 8(02) · 패딩 12 · bgCard 면 + radius **md 12**(소형 카드 — 매핑 표 참조). **110×168 fix**(3열 정렬 유지: 시트 내폭 350 = 110×3 + gap 10×2).
- 슬롯: `airline` `times` `stops` `price`(각 fix 86 좌) `badge`(C08 인스턴스).
- variant 처방: state=closed → `airline`·`times`·`stops`·`price`.fills textSub + `badge` 하위 `label`.characters "마감" + `label`.fills **danger**(마감 전용 — 사용 기록됨).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C17-CompareCard", 110, 168, TOKENS.color.bgCard);
board.x = -2000; board.y = 4500;
board.borderRadius = TOKENS.radius.md;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.rowGap = 8; // 02 명시값
flex.horizontalPadding = TOKENS.radius.md; // 12
flex.verticalPadding = TOKENS.radius.md;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

board.appendChild(mkText("airline", "항공사", TOKENS.font.size.action, TOKENS.font.weight.medium,
  TOKENS.color.textPrimary, { width: 86, align: "left" }));
board.appendChild(mkText("times", "07:30-10:55", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textBody, { width: 86, align: "left" }));
board.appendChild(mkText("stops", "직항", TOKENS.font.size.caption, TOKENS.font.weight.regular,
  TOKENS.color.textSub, { width: 86, align: "left" }));
board.appendChild(mkText("price", "124,000원", TOKENS.font.size.emphasis, TOKENS.font.weight.bold,
  TOKENS.color.textPrimary, { width: 86, align: "left" }));
const badge = findComp("{PAGE}/C08-Badge").instance();
badge.name = "badge";
board.appendChild(badge);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C19 — EmptyState (선행: C13)

- 구조: 세로 중앙 [`icon` ellipse 64 | `title` | `desc` | `cta`] · gap 12(02) · 패딩 상하 40 좌우 24(02) · 투명. 346 fix × auto.
- 슬롯: `title`(fix 290 중앙) `desc`(fix 290 중앙) `cta`(C13 인스턴스, resize(200,52)).
- variant 처방: CTA 불필요 화면(S02 일부) → `cta`.hidden true.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C19-EmptyState", CONTENT_W, 300, null);
board.x = -2000; board.y = 4800;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.alignItems = "center";
flex.rowGap = 12; // 02 명시값
flex.verticalPadding = 40; // 02 명시값
flex.horizontalPadding = 24; // 02 명시값
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const icon = penpot.createEllipse();
icon.name = "icon";
icon.resize(64, 64);
icon.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
board.appendChild(icon);

board.appendChild(mkText("title", "아직 아무것도 없어요", TOKENS.font.size.emphasis,
  TOKENS.font.weight.bold, TOKENS.color.textStrong, { width: 290, align: "center" }));
board.appendChild(mkText("desc", "설명 문구가 들어갑니다", TOKENS.font.size.body,
  TOKENS.font.weight.regular, TOKENS.color.textSub, { width: 290, align: "center" }));

const cta = findComp("{PAGE}/C13-PrimaryButton").instance();
cta.name = "cta";
cta.resize(200, 52);
board.appendChild(cta);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C18 — Toast

- 구조: 가로 [`icon` ellipse 20 | `message`] · gap 8(02) · 패딩 좌우 15 · **반전 배경**(textPrimary 면 + bg색 글자 — 02 "배경 반전" 그대로) + radius lg. 346×48 fix.
- 슬롯: `message`(fix 280 좌).
- variant 처방(tone): info=`icon`.fills primary(기본값) / warning=`icon`.fills accent.

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C18-Toast", CONTENT_W, 48, TOKENS.color.textPrimary);
board.x = -2000; board.y = 5100;
board.borderRadius = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "row";
flex.alignItems = "center";
flex.columnGap = 8; // 02 명시값
flex.horizontalPadding = TOKENS.space.xl;
board.horizontalSizing = "fix";
board.verticalSizing = "fix";

const icon = penpot.createEllipse();
icon.name = "icon";
icon.resize(20, 20);
icon.fills = [{ fillColor: TOKENS.color.primary, fillOpacity: 1 }];
board.appendChild(icon);

board.appendChild(mkText("message", "안내 메시지", TOKENS.font.size.action, TOKENS.font.weight.medium,
  TOKENS.color.bg, { width: 280, align: "left" }));

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

## §C14 — SheetFrame (선행: C13)

- 구조: 세로 중앙 정렬 [`grabber` 40×4 | `sheet-header`(타이틀+닫기) | `content` 슬롯 보드 | `cta-area` 84 fix] · gap 16(02) · 패딩 20(02=space.xxl) · bg 면 + **상단만 radius lg**. 390 fix × auto. 내폭 350.
- 슬롯: `title`(fix 300 좌) `content`(보드 — 자식 교체 슬롯) `cta`(C13 인스턴스 350×52).
- **4단계 사용 규약(중요)**: 인스턴스에는 자식을 추가할 수 없다. 시트는 `const inst = comp.instance(); inst.detach();` 후 detach된 사본의 `content` 보드에 C16/C15/C17 인스턴스를 `appendChild`하고, `content` 안 플레이스홀더 rect(`content-placeholder`)는 **remove하지 말고 `hidden = true`** 처리한다(자식 remove는 플러그인 정지 함정).
- 스크림: 사용하지 않는다. 뒤 화면 보드 opacity를 낮춘다 (02 인계 메모 그대로 — 4단계 책임).

```js
// use_figma로 실행 (프리앰블 선행). 반환: { shapeId, componentId }
const board = mkBoard("{PAGE}/C14-SheetFrame", TOKENS.screen.w, 400, TOKENS.color.bg);
board.x = -2000; board.y = 5400;
board.borderRadiusTopLeft = TOKENS.radius.lg;
board.borderRadiusTopRight = TOKENS.radius.lg;
const flex = board.addFlexLayout();
flex.dir = "column";
flex.alignItems = "center";
flex.rowGap = 16; // 02 명시값
flex.verticalPadding = TOKENS.space.xxl;
flex.horizontalPadding = TOKENS.space.xxl;
board.horizontalSizing = "fix";
board.verticalSizing = "auto";

const grabber = penpot.createRectangle();
grabber.name = "grabber";
grabber.resize(40, 4);
grabber.borderRadius = TOKENS.radius.full;
grabber.fills = [{ fillColor: TOKENS.color.line, fillOpacity: 1 }];
board.appendChild(grabber);

const header = mkBoard("sheet-header", 350, 28, null);
const hflex = header.addFlexLayout();
hflex.dir = "row"; hflex.alignItems = "center"; hflex.justifyContent = "space-between";
header.horizontalSizing = "fix"; header.verticalSizing = "auto";
header.appendChild(mkText("title", "시트 제목", TOKENS.font.size.cardTitle, TOKENS.font.weight.bold,
  TOKENS.color.textStrong, { width: 300, align: "left" }));
header.appendChild(mkText("close", "✕", TOKENS.font.size.emphasis, TOKENS.font.weight.regular,
  TOKENS.color.textSub, null));
board.appendChild(header);

const content = mkBoard("content", 350, 120, null);
const cflex = content.addFlexLayout();
cflex.dir = "column";
cflex.rowGap = TOKENS.radius.md; // 12
content.horizontalSizing = "fix";
content.verticalSizing = "auto";
const ph = penpot.createRectangle();
ph.name = "content-placeholder"; // 4단계: remove 금지 — hidden=true로 끈다
ph.resize(350, 120);
ph.borderRadius = TOKENS.radius.md;
ph.fills = [{ fillColor: TOKENS.color.bgChip, fillOpacity: 1 }];
content.appendChild(ph);
board.appendChild(content);

const ctaArea = mkBoard("cta-area", 350, 84, null);
const aflex = ctaArea.addFlexLayout();
aflex.dir = "column"; aflex.alignItems = "center";
aflex.topPadding = TOKENS.radius.md;
ctaArea.horizontalSizing = "fix";
ctaArea.verticalSizing = "fix"; // 하단 CTA 영역 84 고정 — spacer에 맡기지 않음 (02 명시)
const cta = findComp("{PAGE}/C13-PrimaryButton").instance();
cta.name = "cta";
cta.resize(350, 52);
ctaArea.appendChild(cta);
board.appendChild(ctaArea);

const comp = penpot.library.local.createComponent(board);
return { shapeId: board.id, componentId: comp.id };
```

---

## 4단계 인계 메모

- **오버라이드 경로 표기 규약**: `슬롯 → 자식이름.프로퍼티`. 중첩 인스턴스(C08·C13 내장)는 `인스턴스이름 하위 자식이름.프로퍼티` — 예: C05의 뱃지 문구는 `badge` 인스턴스 하위 `label`.characters.
- 텍스트 오버라이드는 인스턴스에서 **된다** (penpot 형식 fills로 지었으므로 fills 오버라이드도 된다).
- 화면 조립 후 마무리로 `growType === "auto-height"` 텍스트 전부 `resize` 재계산 (hHug 잘림 함정 — AGENTS.md).
- `export_shape`가 빈 영역이면 재-export 한 번 후 판단.
- C03 탭 레이블, C08 문구 등 기본값은 전부 플레이스홀더다 — **PRD 문장으로 오버라이드해서 쓴다** (특정 PRD 하드코딩 없음).
- 화면 고유 요소(SearchEntryCard·TripCard·CompareBar)는 컴포넌트가 아니다 — 4단계가 02의 화면 명세 + 이 문서의 TOKENS로 직접 저작한다.
