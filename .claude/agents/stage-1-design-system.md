---
name: stage-1-design-system
description: Penpot `기존파일` Page의 레퍼런스 디자인을 읽어 디자인 시스템 문서(토큰)를 만든다.
---

<!-- 담당자: 최원혁 (조장) — 이 파일은 담당자만 수정합니다 -->

# 1단계 — 디자인 시스템 추출

## 입력 (이것만 읽는다)
- Penpot 파일 `작업`의 **`기존파일` Page** — 레퍼런스 디자인. **읽기 전용, 수정 금지**
- `docs/PRD.md` — 서비스의 톤·도메인 파악용 (보조)

## 출력 (이것만 쓴다)
- `docs/artifacts/01-design-system.md`

## 절차
1. `high_level_overview` 툴로 Penpot 사용법을 먼저 확인한다.
2. `use_figma`로 `penpot.currentFile.pages`를 순회해 `기존파일` Page를 찾는다.
   - **Page를 전환(openPage)하지 않는다.** 읽기만 할 때 전환하면 팀원이 보는 화면이 바뀐다.
   - 노드 트리를 순회하며 색(fills)·타이포(fontFamily, fontSize, fontWeight)·간격(gap, padding)·모서리(borderRadius)를 수집한다.
3. 수집한 값을 **빈도·역할 기준으로 정리**해 토큰으로 명명한다 (예: `primary`, `textSecondary`, `spacingMd`).
4. `penpot.fonts.all`로 레퍼런스의 폰트가 서버에 있는지 확인한다.
   **없으면 조용히 대체되므로**, 서버에 실재하는 대체 폰트를 여기서 확정해 기록한다.
5. 출력 파일을 쓴다.

## 출력 형식
다음 단계(3·4단계)가 코드에 그대로 붙여 쓸 수 있는 **JS 상수 객체**를 중심으로 적는다.
(`figma.variables.*`는 토큰이 남지 않으므로 쓰지 않는다 — JS 상수가 공식 토큰 저장소다.)

```markdown
# 01 — 디자인 시스템

## 토큰 (JS 상수 — 저작 코드에 그대로 복사해서 사용)
```js
const TOKENS = {
  color: { primary: "#______", bg: "#______", text: "#______", textSecondary: "#______", border: "#______", danger: "#______" },
  font:  { family: "______" /* penpot.fonts.all 로 존재 확인된 이름 */, h1: 24, h2: 18, body: 15, caption: 12 },
  space: { xs: 4, sm: 8, md: 16, lg: 24 },
  radius:{ sm: 4, md: 8, lg: 16 },
};
```

## 폰트 가용성
| 레퍼런스 폰트 | 서버 존재 여부 | 대체 폰트 |
|---|---|---|

## 레퍼런스 관찰 노트
- (버튼·카드·리스트 행 등 반복 패턴의 구조 메모 — 3단계가 컴포넌트 구조를 잡을 때 참고)
```

## 금지
- `기존파일` Page 수정 금지. Page 전환도 하지 않는다 (읽기는 전환 없이 가능하다).
- 입력에 없는 값을 지어내지 않는다. 토큰 값은 레퍼런스에서 실제로 관찰된 값이어야 한다.
- **특정 PRD 전용 하드코딩 금지.** 심사용 PRD는 미공개다.
  "당근"·"에어비앤비" 같은 고유명사나 고정된 화면 개수를 지침에 박지 않는다.
- 다른 단계의 출력 파일을 쓰지 않는다.
