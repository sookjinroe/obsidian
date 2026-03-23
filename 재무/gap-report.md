## Gap Analysis Report

분석 시각: 2026-03-20
대상: artifact-v4.md
발화 수: 15개 | 구성요소 수: 7개

> **재검증 사유 (iteration 2):** U13이 nl-to-sql(빈 결과 원인 진단)에 매핑됨. U14가 schema-explorer(존재하지 않는 테이블명 감지)에 매핑됨. U15 추가("이번 달 BU별 매출에서 전체 대비 비율도 같이 보여줘" → nl-to-sql CTE 경로 발동). nl-to-sql이 WITH절(CTE) 명시 지원. 변경 후 전체 발화(U01~U15)를 기준 ①③으로 재순회함.

---

### 심각 구멍 (0개)

| 발화 ID | 요구사항 ID | 기준 | 구성요소 | 설명 |
|--------|-----------|-----|---------|-----|

### 중간 구멍 (2개)

| 발화 ID | 요구사항 ID 또는 구성요소 | 기준 | 구성요소 | 설명 |
|--------|----------------------|-----|---------|-----|
| U13 | — | ①연결 없음 | redshift-connector | U13("방금 조회 결과가 비어 있는데, 데이터가 없는 건지 쿼리가 틀린 건지 확인해줘")의 처리 체인에 nl-to-sql만 매핑되어 있으나, 빈 결과 원인 진단을 위한 재조회 또는 검증 쿼리 실행에 필요한 redshift-connector가 체인에 누락됨 |
| U15 | — | ①연결 없음 | date-resolver, redshift-connector | U15("이번 달 BU별 매출에서 전체 대비 비율도 같이 보여줘")의 처리 체인에 nl-to-sql(CTE)만 명시되어 있으나, "이번 달" 날짜 해석을 담당하는 date-resolver와 생성된 CTE 쿼리를 실행하는 redshift-connector가 체인에 누락됨 |

### 경미 구멍 (0개)

| 발화 ID | 요구사항 ID 또는 구성요소 | 기준 | 설명 |
|--------|----------------------|-----|-----|
| — | — | — | 해당 없음 |

---

### ③ 트리거 미정의 점검 결과

기준 ③은 Skill 및 MCP 계위 구성요소에만 적용한다 (Hook, references 제외).

| 구성요소 | 계위 | 트리거 발화 | 판정 |
|---------|-----|-----------|-----|
| nl-to-sql | Skill | U01, U02, U03, U04, U07, U08, U13, U15 | 통과 |
| date-resolver | Skill | U02, U03 | 통과 |
| conversation-context | Skill | U04 | 통과 |
| schema-explorer | Skill | U05, U06, U14 | 통과 |
| redshift-connector | MCP | U01~U08 | 통과 |
| dml-guard | Hook | (③ 기준 적용 제외) | — |
| schema-reference | references | (③ 기준 적용 제외) | — |

---

### iteration 1 → iteration 2 변경 효과

| 구멍 | iteration 1 판정 | iteration 2 판정 | 비고 |
|-----|----------------|----------------|-----|
| U13 심각 — 빈 결과 진단 구성요소 없음 | 심각 | 해소 (nl-to-sql에 진단 책임 명시로 구성요소 매핑 확보) | 단, 체인 내 redshift-connector 누락은 신규 중간 구멍으로 등록 |
| nl-to-sql CTE 경로 트리거 미정의 | 중간 | 해소 (U15 추가로 CTE 발동 경로 확보) | 단, U15 체인에 date-resolver·redshift-connector 누락은 신규 중간 구멍으로 등록 |

---

### 요약

- 심각 구멍: 0개 → 없음
- 중간 구멍: 2개 → U13·U15의 처리 체인에 구성요소 매핑 보완 필요 (artifact-v4.md 수정 후 재검증)
- 경미 구멍: 0개
- 전체 발화 15개 중 13개 처리 가능, 2개 구멍 있음

#### 보완 지침

**U13 체인 보완:** 보완 발화 목록 U13의 발동 목적란에 redshift-connector 추가.
- 수정 전: `nl-to-sql — 빈 결과 원인 진단 및 사용자 안내 (책임 범위 확장)`
- 수정 후: `nl-to-sql → redshift-connector — 빈 결과 원인 진단용 재조회 실행 및 사용자 안내`

**U15 체인 보완:** 보완 발화 목록 U15의 발동 목적란에 date-resolver, redshift-connector 추가.
- 수정 전: `nl-to-sql — WITH절(CTE) 경로 발동 (다단계 집계)`
- 수정 후: `date-resolver → nl-to-sql(WITH절 CTE) → redshift-connector — 이번 달 날짜 해석 후 다단계 집계 쿼리 실행`
