# 04 — 저작된 화면 대장

> 실행: 2026-08-01, 병렬 하네스 (Wave A 컴포넌트 빌드 워커 6 + Wave B 화면 조합 워커 3, 오케스트레이터 병합)
> 워커별 원본 대장: `04-ledger-A1~A6.json`(컴포넌트), `04-ledger-B-home/results/sheets.json`(화면), 병합본 `04-ledger-components.json`

## 적용한 VOICE 요약

`01-voice.md`의 VOICE 적용: 해요체, 버튼은 동명사형 2~4어절("항공권 검색하기", "적용하기", "이 운임으로 선택하기", "조건 초기화하기"), 숫자는 콤마+단위·해석 문장("담은 항공편 2/3", "9시간 남음", "98,000원"), 명령형·느낌표 금지. 상태 문구도 동일 톤("실시간 요금을 불러오고 있어요", "맞는 항공편이 없어요", "담아둔 1편이 마감됐어요").

## Page: 3-toss-result

## 빌드된 컴포넌트 대장 (Wave A 결과 — 19/19 성공)

| ID | Penpot 이름 | shapeId | componentId | 상태 |
|---|---|---|---|---|
| C01 | 3-toss-result/C01-StatusBar | …6a58f92792c3 | …6a58f9377bd0 | ✅ |
| C02 | 3-toss-result/C02-TopBar | …6a591b0deebc | …6a591b2b699f | ✅ |
| C03 | 3-toss-result/C03-TabBar | …6a5900078618 | …6a5900471bc3 | ✅ |
| C04 | 3-toss-result/C04-SectionHeader | …6a5906828975 | …6a5906924c77 | ✅ |
| C05 | 3-toss-result/C05-FlightRow | …6a5912d67d6a | …6a591311b9db | ✅ |
| C06 | 3-toss-result/C06-DatePriceChip | …6a590c0a4af7 | …6a590c1fd145 | ✅ |
| C07 | 3-toss-result/C07-FilterChip | …6a59172a018a | …6a591739b2ac | ✅ |
| C08 | 3-toss-result/C08-Badge | …6a58d5f76ab1 | …6a58f111f260 | ✅ |
| C09 | 3-toss-result/C09-DealCard | …6a591f7533e9 | …6a591f92a4ae | ✅ |
| C10 | 3-toss-result/C10-SavedDestCard | …6a5922be09f8 | …6a5922dd1f4e | ✅ |
| C11 | 3-toss-result/C11-TourCard | …6a59300df700 | …6a59302c43a7 | ✅ |
| C13 | 3-toss-result/C13-PrimaryButton-retry2 | …6a59226ccf8a | …6a59227c39f5 | ✅ (1차 폐기→retry2) |
| C14 | 3-toss-result/C14-SheetFrame | …6a595cf253b8 | …6a595d330e0e | ✅ |
| C15 | 3-toss-result/C15-FareOptionCard | …6a58fb6b7897 | …6a5912af8a9b | ✅ |
| C16 | 3-toss-result/C16-FilterOptionRow | …6a5920402753 | …6a59206280ef | ✅ |
| C17 | 3-toss-result/C17-CompareCard | …6a593b68b55e | …6a593b854305 | ✅ |
| C18 | 3-toss-result/C18-Toast | …6a59247a94e1 | …6a59248dbc20 | ✅ |
| C19 | 3-toss-result/C19-EmptyState | …6a59494dce2e | …6a5949726c52 | ✅ |
| C20 | 3-toss-result/C20-SkeletonRow | …6a591d1d4150 | …6a591d2b3399 | ✅ |

(id 앞부분 공통 프리픽스 `b7cdc91d-61ba-80ad-8008-` 생략. 전체 id는 `04-ledger-components.json` 참조. C12는 02에서 결번 — TripCard는 화면 고유 요소로 강등)

## 화면 대장 (Wave B 결과 — 9/9 성공)

| 화면 ID | board 이름 | board id | 사용 인스턴스 | export 확인 |
|---|---|---|---|---|
| S01 | New/Home (x=0, h=1554) | …6a5a6af7c12a | C01, C02, C03, C04×4, C09×6, C10×3, C11×3, C13 + SearchEntryCard·TripCard(고유) | ✅ |
| S02 | New/Home-Empty (x=500) | …6a5b10d6be9c | S01 clone → C19×2 교체(여정·담은 목적지 빈 상태) | ✅ |
| S03 | New/Results (x=1000) | …6a5a70376acf | C01, C02, C05×6(직항4·경유2), C06×7, C07×4 + CompareBar(고유, "담은 항공편 2/3") | ✅ |
| S04 | New/Results-Loading (x=1500) | …6a5afc38da87 | S03 clone → C20×5 + 로딩 캡션 | ✅ |
| S05 | New/Results-NoMatch (x=2000) | …6a5b217c5d01 | S03 clone → C19(0건, CTA "조건 초기화하기") | ✅ |
| S06 | New/Results-FilterSheet (x=3000) | …6a5c16508f17 | backdrop=S03 clone(opacity 0.35) + C14(detach), C16×4, C13 | ✅ |
| S07 | New/Results-FareSheet (x=3500) | …6a5c535c751a | backdrop=S03 clone + C14(detach), C15×3(스탠다드 selected), C13 | ✅ (재-export 1회) |
| S08 | New/Results-CompareSheet (x=4000) | …6a5c7eec7018 | backdrop=S03 clone + C14(detach), C17×3(1개 closed), C18, C13 | ✅ (재-export 1회) |
| S09 | New/Results-PriceChanged (x=2500) | …6a5b40c7d58e | S03 clone → 가격 인상 행·soldout 행·C18 warning 토스트 | ✅ |

이미지: unsplash 실사진 11건 `uploadMediaUrl` 업로드 성공 (placeholder 대체 없음).

## 미저작/보류 화면

| 화면 ID | 사유 |
|---|---|
| 없음 | — |

## 명세 대비 변경·기록 사항

- **S08**: 02의 "비교 항목 행 라벨 열"은 3열(110×3+gap 20)이 시트 내폭 350을 꽉 채워 자리가 없어 캡션 1줄("가격·소요·경유를 나란히 봐요")로 대체.
- **C14 grabber**: radius 999가 export http error를 유발해 반지름 2로 교정.
- **C16 토글 pill**: radius 999→13 오버라이드 (동일 원인).
- **정리(전부 hidden 처리, 삭제는 플러그인 정지 함정으로 회피)**: 실패 잔여 마스터 4건(C01·C03·C07·C19 중복), probe 3건(a3-sigtest·A4-probe·A5-probe), C13 1차 폐기분 1건 — 총 8건 hidden.

## 실행 중 발견한 함정 (AGENTS.md 미기재 — 다음 실행자용)

1. `penpot.library.local.createComponent(board)`는 실패한다 — **배열 인자 `createComponent([board])`** 가 정답.
2. createComponent 실패 시 잔여 board가 **남는 케이스가 있다** (롤백되는 케이스와 혼재) — 재실행 전 페이지에서 동명 board를 확인할 것.
3. 라이브러리 등록명은 `{PAGE}/` 프리픽스가 **path로 분리** 저장되고, 페이지 shape 이름은 슬래시 양옆 공백(`"New / Home"`)으로 저장된다 — 이름 전체일치 검색 금지, **componentId로 조회**.
4. 작은 rect의 radius 999(full)는 export_shape http error를 유발한다 — 높이/2 이하 실값 사용.
5. auto-height 텍스트가 h=1로 굳으면 같은 높이 resize로는 안 풀린다 — **fontSize×1.25 명시 높이로 resize**.
6. 여러 호출에 걸쳐 조립한 flex board는 직후 높이가 stale하게 읽힌다 — export가 어긋나 보이면 재-export 1회.
7. clone 안에서 hidden 인스턴스를 unhide하면 flex 재배치가 안 된다 — 같은 부모에 `appendChild` 재호출로 재계산.
8. clone한 화면 board 안의 자식 `shape.remove()`는 (컴포넌트 마스터와 달리) 안전하게 동작한다.
