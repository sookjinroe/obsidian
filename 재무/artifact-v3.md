# 구성요소·계위 결정 (artifact-v3)

---

## 구성요소 목록

| 구성요소명 | 계위 | 선택 근거 | 연결 요구사항 | ⚠️ |
|---------|-----|---------|------------|-----|
| nl-to-sql | Skill | 사용자의 자연어 발화를 받아 Claude가 맥락에 따라 자동으로 SQL을 생성하는 능력. 명시적 실행 명령 없이 발화 자체가 트리거. | R01, R06 | |
| date-resolver | Skill | "이번 달", "지난 분기", "작년 동기" 등 상대적 날짜 표현을 SQL WHERE 조건의 절대 날짜 범위로 해석. nl-to-sql 실행 시 항상 함께 동작. | R05 | ⚠️ nl-to-sql과 별도 Skill로 분리할지 nl-to-sql 내부 로직으로 흡수할지 불확실 |
| conversation-context | Skill | 직전 쿼리·결과 맥락을 유지하여 "방금 그 결과에서..." 같은 후속 발화를 처리. 대화 세션 내 상태 참조. | R07 | |
| schema-explorer | Skill | 테이블 구조 조회, 테이블명 불명확 시 후보 테이블 탐색 및 제안. schema-reference를 활용하되 독립적 판단 포함. | R08, R09 | |
| schema-reference | references | Redshift 스키마 정보(테이블명, 컬럼명, 데이터 타입, 조인 키 등)를 담은 보조 파일. nl-to-sql 및 schema-explorer와 항상 함께 사용. | R02 | ⚠️ 스키마 정보를 정적 파일로 관리할지, MCP를 통해 실시간 조회할지 미확인 — 스키마 변경 빈도에 따라 결정 필요 |
| redshift-connector | MCP | Redshift에 실제 쿼리를 실행하고 결과를 반환하는 외부 시스템 연결. 읽기 전용(SELECT, SHOW, DESCRIBE 계열만 허용). DB 접근이므로 MCP 계위 적합. | R03 | |
| dml-guard | Hook | LLM 판단 없이 항상 실행되어야 하는 규칙. 생성된 SQL 또는 사용자 발화에 DML 키워드(INSERT/UPDATE/DELETE/DROP/TRUNCATE/ALTER 등) 포함 시 즉시 차단하고 거부 메시지 반환. 실행 파이프라인의 가장 앞단 또는 SQL 생성 직후에 위치. | R04, R10 | |

---

## 계위 결정 메모

### dml-guard → Hook
DML 차단은 Claude의 맥락 판단에 맡기면 안 되는 정책 규칙이다. "삭제해줘"를 Claude가 맥락상 허용 가능하다고 오판할 여지를 없애야 하므로 Hook(항상 실행)으로 결정.

### schema-reference → references
nl-to-sql, schema-explorer 모두 스키마 정보 없이는 작동 불가이므로 항상 함께 쓰이는 보조 파일로 분류. 단, 스키마가 자주 바뀐다면 MCP를 통한 실시간 메타데이터 조회로 전환 고려 필요(⚠️).

### date-resolver → Skill
날짜 해석은 nl-to-sql 생성 과정에서 자연스럽게 통합될 수 있으나, 재무 도메인에서 "지난 분기", "작년 동기" 등의 해석 오류가 데이터 신뢰도에 직접 영향을 주므로 명시적으로 분리하여 관리. ⚠️ 구현 시 nl-to-sql에 흡수 통합 여부 재검토 권장.

### redshift-connector → MCP
실제 DB 쿼리 실행은 외부 시스템 호출이므로 MCP. 읽기 전용 제약은 MCP 설정 레벨에서 강제(dml-guard와 이중 방어).
