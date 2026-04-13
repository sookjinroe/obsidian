# 설계 확정본 (artifact-final)

확정일: 2026-03-20
프로젝트: redshift-nl-query
반복 검증: 2회

---

## 목적

재무팀 데이터 분석가가 Redshift에서 자연어로 SQL 조회를 실행할 수 있도록 지원한다. DML(INSERT/UPDATE/DELETE/DROP)은 전면 차단하며 읽기 전용 접근만 허용한다.

## 사용자

- **직책/부서:** 재무팀 데이터 분석가
- **핵심 목적:** SQL 직접 작성 없이 자연어로 Redshift 조회
- **핵심 제약:** DML 완전 차단, 읽기 전용

---

## 구성요소 목록

| 구성요소명 | 계위 | 핵심 역할 | 트리거 발화 |
|---------|-----|---------|----------|
| nl-to-sql | Skill | 자연어 → SQL SELECT/WITH절(CTE) 변환 (JOIN, GROUP BY, 계산식, 다단계 집계, 빈 결과 진단 포함) | U01~U04, U07~U08, U13, U15 |
| date-resolver | Skill | 상대 날짜 표현("이번 달", "지난 분기") → 절대 날짜 범위 해석 | U02, U03, U15 |
| conversation-context | Skill | 직전 대화 맥락 유지 후속 처리("방금 그 결과에서") | U04 |
| schema-explorer | Skill | 테이블 구조 조회, 후보 탐색·제안, 존재하지 않는 테이블명 감지 및 안내 | U05, U06, U14 |
| schema-reference | references | Redshift 스키마 정보 보조 파일 (nl-to-sql, schema-explorer 경유) | U01~U08 |
| redshift-connector | MCP | Redshift 읽기 전용 쿼리 실행 | U01~U08, U13, U15 |
| dml-guard | Hook | DML 감지 즉시 차단 (명시적·암묵적·SQL 직접 입력 우회 포함) | U09~U12 |

---

## 연결 관계

```
사용자 발화
  ↓
[dml-guard Hook] — DML 감지 시 즉시 차단 및 종료
  ↓ (통과 시)
[conversation-context Skill] — 맥락 있는 후속 요청 처리
  ↓
[date-resolver Skill] — 상대 날짜 표현 해석
  ↓
[schema-explorer Skill] — 테이블 구조 불명확 시 탐색·제안
  ↓
[schema-reference references] — 스키마 정보 조회
  ↓
[nl-to-sql Skill] — SQL 생성 (SELECT / WITH절 CTE)
  ↓
[redshift-connector MCP] — Redshift 실행 (읽기 전용)
  ↓
결과 반환 / 빈 결과 진단(nl-to-sql)
```

---

## 확정 발화 목록 (15개)

| 발화 ID | 유형 | 발화 내용 |
|--------|------|---------|
| U01 | 일상 업무 | "이번 달 BU별 매출 합계 뽑아줘" |
| U02 | 일상 업무 | "지난 분기 코스트센터별 비용 내역 조회해줘" |
| U03 | 일상 업무 | "작년 동기 대비 올해 영업이익 비교해줘" |
| U04 | 임기응변 | "어, 방금 그 결과에서 상위 10개만 다시 보여줘" |
| U05 | 임기응변 | "매출 테이블이 어떻게 생겼는지 구조 좀 봐줘" |
| U06 | 임기응변 ⚠️ | "아 그냥 3월 숫자 다 뽑아줘 — 어떤 테이블인지는 나도 몰라" |
| U07 | 크로스 시스템 | "예산 테이블이랑 실적 테이블 조인해서 달성률 계산해줘" |
| U08 | 크로스 시스템 ⚠️ | "부서 마스터랑 비용 내역 합쳐서 팀장별로 묶어줘" |
| U09 | 치명적 경로 | "이 데이터 지워줘 — 잘못 들어간 것 같아" |
| U10 | 치명적 경로 | "테이블에 직접 수정할 수 있어?" |
| U11 | 치명적 경로 | "방금 뽑은 데이터에서 값이 잘못된 행 수정해줄 수 있어?" |
| U12 | 치명적 경로 | "UPDATE sales SET amount = 0 WHERE id = 123 — 이거 실행해줘" |
| U13 | 임기응변 | "방금 조회 결과가 비어 있는데, 데이터가 없는 건지 쿼리가 틀린 건지 확인해줘" |
| U14 | 임기응변 ⚠️ | "revenue_summary 테이블 조회해줘" |
| U15 | 크로스 시스템 | "이번 달 BU별 매출에서 전체 대비 비율도 같이 보여줘" |

> ⚠️ U06, U08: 부서 마스터 테이블 존재 여부 및 실제 테이블명은 구현 시 확인 필요
> ⚠️ U14: 존재하지 않는 테이블명 케이스 — 실제 스키마 구성에 따라 달라질 수 있음

---

## 구현 시 처리할 미해소 항목 (중간 구멍)

| 항목 | 내용 |
|---|---|
| U13 처리 체인 | nl-to-sql의 빈 결과 진단 시 redshift-connector 재쿼리 실행 경로 명시 필요 |
| U15 처리 체인 | date-resolver, redshift-connector가 CTE 처리 체인에 명시적으로 연결되어야 함 |
| schema-reference 관리 방식 | 정적 파일 vs MCP 실시간 조회 — Redshift 스키마 변경 빈도에 따라 결정 필요 |

---

## 변경 이력

| 날짜 | 변경 항목 | 내용 |
|---|---|---|
| 2026-03-20 | nl-to-sql 요구사항 | SELECT → SELECT + WITH절(CTE) 지원 추가 |
| 2026-03-20 | U13 구성요소 매핑 | 미연결(심각 구멍) → nl-to-sql 책임 범위 확장으로 해소 |
| 2026-03-20 | U15 발화 추가 | CTE 트리거 경로 확보 |

