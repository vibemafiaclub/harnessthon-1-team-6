---
name: start
description: PRD를 입력받아 단계별 sub agent를 순서대로 호출해 Penpot 디자인을 완성하는 하네스 진입점. "/start", "시작해줘", "디자인 만들어줘", "PRD 실행", "하네스 돌려줘" 등에 트리거된다.
---

# start — 하네스 진입점

> 🔒 **공용 파일입니다. 수정하려면 조장 승인이 필요합니다.**
>
> ✅ 2026-08-01 `/scaffold-harness`(조장 실행)로 단계 표·실행순서가 채워졌습니다.
> 각 단계의 고도화는 `.claude/agents/stage-*.md`에서 **담당자만** 진행하세요.
>
> 🔄 2026-08-01 3→4 계약 변경: 3단계는 **markdown 빌드 명세만** 작성하고(Penpot 저작 없음),
> 컴포넌트 Penpot 저작은 4단계가 화면 조합 전에 수행합니다. (조장 승인·한준희 sync 후 커밋)

## 입력

- `docs/PRD.md` — 만들어야 할 것의 명세
- 작업 Page 이름 — **매 실행마다 확인한다.** 없으면 묻고, 답을 받기 전엔 시작하지 않는다

## 실행 원칙

1. **각 단계는 반드시 sub agent에게 위임한다.** 오케스트레이터가 직접 저작하지 않는다.
2. 각 sub agent에게 **입력(읽을 파일)·출력(쓸 파일)·작업 Page 이름**을 명시적으로 넘긴다.
3. **의존관계가 없는 단계는 병렬로** 호출한다.
4. 중간 산출물은 전부 `docs/artifacts/`에 남긴다. 남지 않으면 다음 단계가 읽을 게 없다.
5. 한 단계가 출력 파일을 남기지 못했으면 **다음 단계로 넘어가지 않는다.** 멈추고 보고한다.

## 단계 정의

| # | 단계 | sub agent | 입력 | 출력 | 담당자 | 병렬 가능 |
|---|---|---|---|---|---|---|
| 1 | 디자인 시스템 추출 | `stage-1-design-system` | Penpot `기존파일` Page (읽기 전용), `docs/PRD.md` | `docs/artifacts/01-design-system.md` | 최원혁 | ✅ 2와 병렬 |
| 2 | 화면·컴포넌트 추출 | `stage-2-extract-components` | `docs/PRD.md` | `docs/artifacts/02-components.md` | 하이서 | ✅ 1과 병렬 |
| 3 | 컴포넌트 빌드 명세 | `stage-3-build-components` | 01, 02 | `docs/artifacts/03-built-components.md` (markdown 명세만 — Penpot 저작 없음) | 이태경 | — (1·2 이후) |
| 4 | 컴포넌트 빌드 + 화면 조합 | `stage-4-compose-screens` | 02, 03, **작업 Page 이름** | Penpot 컴포넌트·화면 board + `docs/artifacts/04-screens.md` | 한준희 | — (3 이후) |
| 5 | 검증 | `stage-verify-penpot` | Penpot 작업 Page(되읽기), PRD, 01~04, **작업 Page 이름** | `docs/artifacts/99-verify.md` | 현수환 | — (맨 끝 고정) |

## 실행 순서

0. **작업 Page 이름을 확정한다.** 없으면 사용자에게 묻고, 답을 받기 전에는 시작하지 않는다.
1. `stage-1-design-system` 과 `stage-2-extract-components` 를 **병렬 호출**
   → `01-design-system.md`, `02-components.md` 생성 확인
2. `stage-3-build-components` 호출 (Penpot 접근 없음 — Page 이름 불필요)
   → `03-built-components.md` 생성 확인
3. `stage-4-compose-screens` 호출 (작업 Page 이름 전달)
   → 03 명세대로 컴포넌트를 먼저 빌드한 뒤 화면을 조합한다
   → `04-screens.md` 생성 확인
4. `stage-verify-penpot` 호출 (작업 Page 이름 전달) → `99-verify.md`
   - FAIL 항목이 있으면 `99-verify.md`의 "실패 항목별 책임 단계"에 따라 해당 단계를 재실행하고 검증을 다시 돈다
   - 재실행 후에도 실패하면 무엇이 왜 비었는지 사용자에게 보고한다

## 마지막 단계 — 검증 (고정, 삭제 금지)

모든 단계가 끝나면 **항상** `stage-verify-penpot` 을 호출한다.

- 지정 Page에 board/frame이 1개 이상 있는가
- PRD가 요구한 화면이 전부 있는가
- 각 단계 산출물이 `docs/artifacts/`에 남아 있는가

결과는 `docs/artifacts/99-verify.md`. **실패 항목이 있으면 완료를 선언하지 않는다.**
해당 단계를 다시 호출하고, 재실행 후에도 실패하면 무엇이 왜 비었는지 사용자에게 보고한다.

## 완료 조건

- Penpot 파일의 **지정된 Page**에 화면이 실제로 만들어져 있다
- 각 단계의 중간 산출물이 `docs/artifacts/`에 남아 있다
- `docs/artifacts/99-verify.md` 가 전 항목 통과다
