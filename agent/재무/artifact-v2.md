# Walk-through → 요구사항 도출 (artifact-v2)

---

## Walk-through

---

[발화 U01]: "이번 달 BU별 매출 합계 뽑아줘"

단계 1. 질문 의도 파악
    → R01: 자연어 질문을 SQL SELECT 문으로 변환하는 기능

단계 2. 데이터/정보 위치 파악
    → R02: Redshift 스키마/테이블/컬럼 정보를 참조하여 올바른 테이블·컬럼명 특정

단계 3. 실행/처리
    → R03: Redshift에 읽기 전용으로 쿼리를 실행하고 결과 반환

단계 4. 제약 적용
    → R04: DML(INSERT/UPDATE/DELETE/DROP 등) 구문이 생성되지 않도록 차단

---

[발화 U02]: "지난 분기 코스트센터별 비용 내역 조회해줘"

단계 1. 질문 의도 파악
    → R01: 자연어 질문을 SQL SELECT 문으로 변환하는 기능

단계 2. 데이터/정보 위치 파악
    → R02: Redshift 스키마/테이블/컬럼 정보를 참조하여 올바른 테이블·컬럼명 특정
    → R05: "지난 분기"와 같은 상대적 날짜 표현을 절대 날짜 범위로 해석

단계 3. 실행/처리
    → R03: Redshift에 읽기 전용으로 쿼리를 실행하고 결과 반환

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U03]: "작년 동기 대비 올해 영업이익 비교해줘"

단계 1. 질문 의도 파악
    → R01: 자연어 질문을 SQL SELECT 문으로 변환하는 기능
    → R05: "작년 동기", "올해"와 같은 상대적 날짜 표현을 절대 날짜 범위로 해석

단계 2. 데이터/정보 위치 파악
    → R02: Redshift 스키마/테이블/컬럼 정보를 참조

단계 3. 실행/처리
    → R03: Redshift에 읽기 전용으로 쿼리를 실행하고 결과 반환
    → R06: YoY 비교처럼 두 기간을 나란히 비교하는 집계 쿼리 생성

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U04]: "어, 방금 그 결과에서 상위 10개만 다시 보여줘"

단계 1. 질문 의도 파악
    → R07: 직전 대화 맥락(이전 쿼리/결과)을 유지하여 후속 질문을 처리

단계 2. 데이터/정보 위치 파악
    → (이전 쿼리 재활용, 별도 스키마 조회 불필요)

단계 3. 실행/처리
    → R01: 기존 SQL에 LIMIT 10 조건을 추가 변환
    → R03: Redshift 재실행

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U05]: "매출 테이블이 어떻게 생겼는지 구조 좀 봐줘"

단계 1. 질문 의도 파악
    → R08: 특정 테이블의 스키마 구조(컬럼명, 타입 등)를 조회하는 기능

단계 2. 데이터/정보 위치 파악
    → R02: Redshift information_schema 또는 SVV_COLUMNS 등 메타데이터 조회

단계 3. 실행/처리
    → R03: Redshift에 읽기 전용으로 메타데이터 쿼리 실행

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U06]: "아 그냥 3월 숫자 다 뽑아줘 — 어떤 테이블인지는 나도 몰라" ⚠️

단계 1. 질문 의도 파악
    → R09: 테이블명 불명확 시 의도를 파악하여 후보 테이블을 제안하거나 명확화 질문

단계 2. 데이터/정보 위치 파악
    → R02: Redshift 스키마 전반을 탐색하여 관련 테이블 후보 식별

단계 3. 실행/처리
    → R01: 후보 테이블 확정 후 SQL 변환 및 실행
    → R03: Redshift 실행

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U07]: "예산 테이블이랑 실적 테이블 조인해서 달성률 계산해줘"

단계 1. 질문 의도 파악
    → R01: 다중 테이블 JOIN 및 계산식 포함 SQL 생성

단계 2. 데이터/정보 위치 파악
    → R02: 두 테이블의 스키마 및 조인 키 파악

단계 3. 실행/처리
    → R03: Redshift 실행

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U08]: "부서 마스터랑 비용 내역 합쳐서 팀장별로 묶어줘" ⚠️

단계 1. 질문 의도 파악
    → R01: 다중 테이블 JOIN 및 GROUP BY SQL 생성

단계 2. 데이터/정보 위치 파악
    → R02: 부서 마스터 테이블 존재 여부 및 조인 키 파악

단계 3. 실행/처리
    → R03: Redshift 실행

단계 4. 제약 적용
    → R04: DML 구문 차단

---

[발화 U09]: "이 데이터 지워줘 — 잘못 들어간 것 같아"

단계 1. 질문 의도 파악
    → DELETE 요청 — 치명적 경로

단계 4. 제약 적용
    → R10: DML 요청(DELETE) 감지 시 즉시 거부 메시지 반환, 쿼리 미실행

---

[발화 U10]: "테이블에 직접 수정할 수 있어?"

단계 1. 질문 의도 파악
    → UPDATE/쓰기 권한 문의 — 치명적 경로

단계 4. 제약 적용
    → R10: DML 요청(UPDATE/쓰기) 관련 질문에 읽기 전용 정책을 안내하고 대안 제시

---

## 요구사항-구성요소 매핑 표

| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 | 구성요소명 | 계위 |
|--------|------|---------|-----------|--------|---------|-----|
| U01 | 일상 업무 | "이번 달 BU별 매출 합계 뽑아줘" | R01 | 자연어를 SQL SELECT/WITH절(CTE) 문으로 변환 | nl-to-sql | Skill |
| U01 | | | R02 | Redshift 스키마/테이블/컬럼 참조 | schema-reference | references |
| U01 | | | R03 | Redshift 읽기 전용 쿼리 실행 | redshift-connector | MCP |
| U01 | | | R04 | DML 구문 생성·실행 차단 | dml-guard | Hook |
| U02 | 일상 업무 | "지난 분기 코스트센터별 비용 내역 조회해줘" | R01 | 자연어를 SQL SELECT/WITH절(CTE) 문으로 변환 | nl-to-sql | Skill |
| U02 | | | R02 | Redshift 스키마/테이블/컬럼 참조 | schema-reference | references |
| U02 | | | R03 | Redshift 읽기 전용 쿼리 실행 | redshift-connector | MCP |
| U02 | | | R04 | DML 구문 차단 | dml-guard | Hook |
| U02 | | | R05 | 상대 날짜 표현을 절대 날짜 범위로 해석 | date-resolver | Skill |
| U03 | 일상 업무 | "작년 동기 대비 올해 영업이익 비교해줘" | R01 | 자연어를 SQL SELECT/WITH절(CTE) 문으로 변환 | nl-to-sql | Skill |
| U03 | | | R02 | Redshift 스키마/테이블/컬럼 참조 | schema-reference | references |
| U03 | | | R03 | Redshift 읽기 전용 쿼리 실행 | redshift-connector | MCP |
| U03 | | | R04 | DML 구문 차단 | dml-guard | Hook |
| U03 | | | R05 | 상대 날짜 표현 해석 | date-resolver | Skill |
| U03 | | | R06 | YoY 등 기간 비교 집계 쿼리 생성 | nl-to-sql | Skill |
| U04 | 임기응변 | "방금 그 결과에서 상위 10개만 다시 보여줘" | R07 | 직전 대화 맥락 유지 후속 처리 | conversation-context | Skill |
| U04 | | | R01 | 기존 SQL 수정(LIMIT 추가) | nl-to-sql | Skill |
| U04 | | | R03 | Redshift 읽기 전용 쿼리 실행 | redshift-connector | MCP |
| U04 | | | R04 | DML 구문 차단 | dml-guard | Hook |
| U05 | 임기응변 | "매출 테이블 구조 좀 봐줘" | R08 | 테이블 스키마 구조 조회 | schema-explorer | Skill |
| U05 | | | R02 | Redshift 메타데이터 참조 | schema-reference | references |
| U05 | | | R03 | Redshift 메타데이터 쿼리 실행 | redshift-connector | MCP |
| U06 | 임기응변 ⚠️ | "어떤 테이블인지는 나도 몰라" | R09 | 테이블 불명확 시 후보 제안 또는 명확화 | schema-explorer | Skill |
| U06 | | | R02 | Redshift 스키마 전반 탐색 | schema-reference | references |
| U06 | | | R03 | Redshift 실행 | redshift-connector | MCP |
| U07 | 크로스 시스템 | "예산·실적 조인해서 달성률 계산해줘" | R01 | 다중 테이블 JOIN SQL 생성 | nl-to-sql | Skill |
| U07 | | | R02 | 두 테이블 스키마 및 조인 키 파악 | schema-reference | references |
| U07 | | | R03 | Redshift 실행 | redshift-connector | MCP |
| U07 | | | R04 | DML 구문 차단 | dml-guard | Hook |
| U08 | 크로스 시스템 ⚠️ | "부서 마스터랑 비용 내역 합쳐서 팀장별로 묶어줘" | R01 | 다중 테이블 JOIN + GROUP BY SQL 생성 | nl-to-sql | Skill |
| U08 | | | R02 | 부서 마스터 테이블 스키마 파악 | schema-reference | references |
| U08 | | | R03 | Redshift 실행 | redshift-connector | MCP |
| U08 | | | R04 | DML 구문 차단 | dml-guard | Hook |
| U09 | 치명적 경로 | "이 데이터 지워줘" | R10 | DML 요청 감지 시 즉시 거부 및 안내 | dml-guard | Hook |
| U10 | 치명적 경로 | "테이블에 직접 수정할 수 있어?" | R10 | 쓰기 요청에 읽기 전용 정책 안내 | dml-guard | Hook |
