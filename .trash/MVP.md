좋습니다. 지금까지 나온 것들을 전부 펼쳐놓고 MVP 기준으로 선별합니다.

---

## 지금까지 나온 것 전체

```
페르소나
└── 박지수 (경영기획팀 대리, 크로스 시스템 장벽)

시나리오
├── 월간 실적 보고서 작성
└── 예산 집행 현황 점검

플러그인
├── 경영정보
├── PPT
├── 그룹웨어
├── 스케줄러
└── Excel

경영정보 플러그인 내
├── Skills: text-to-sql, schema-lookup, anomaly-detect,
│          period-compare, drill-down, report-template
├── 서브에이전트: sql-validator, insight-writer, report-assembler
├── Hooks: DML차단, 쿼리확인, 출처첨부, 수치포맷
└── MCP: db-mcp, erp-mcp, viz-mcp
```

---

## MVP 선별 기준

두 가지로 판단합니다.

```
① 이게 없으면 핵심 가치가 증명 안 되는가?
② 이게 없어도 다른 것으로 대체 가능한가?
```

---

## 선별 결과

**페르소나/시나리오**

```
MVP                        이후
─────────────────────     ─────────────────────
박지수 (단일 페르소나)     팀장 페르소나 추가
월간 실적 보고 (단일)      예산 집행 점검 추가
단일 DB 연결               크로스 시스템 (ERP+DB)
```

예산 집행 시나리오는 ERP MCP가 필요합니다. MVP에서 DB 하나만 연결해서 핵심 가치를 먼저 증명하는 게 맞습니다.

**플러그인**

```
MVP                        이후
─────────────────────     ─────────────────────
경영정보 플러그인           PPT 플러그인
db-mcp                     erp-mcp
                           그룹웨어 플러그인
                           스케줄러 플러그인
                           Excel 플러그인
```

**경영정보 플러그인 내부**

```
MVP Skills                 이후 Skills
─────────────────────     ─────────────────────
text-to-sql               report-template
schema-lookup             period-compare (자동화)
anomaly-detect
drill-down

MVP 서브에이전트            이후 서브에이전트
─────────────────────     ─────────────────────
sql-validator             insight-writer
                          report-assembler

MVP Hooks                  이후 Hooks
─────────────────────     ─────────────────────
DML 차단 [pre]            수치 포맷 [post]
쿼리 확인 [pre]
출처 첨부 [post]

MVP MCP                    이후 MCP
─────────────────────     ─────────────────────
db-mcp                    erp-mcp
                          viz-mcp
```

`period-compare`를 MVP에서 뺀 이유는 "지난달", "전분기" 같은 표현을 사용자가 날짜로 직접 입력하게 하면 MVP에서는 대체 가능합니다. `insight-writer`, `report-assembler`를 뺀 이유는 데이터 조회와 원인 파악이 핵심 가치이고, 서술과 취합은 사용자가 직접 해도 PoC 검증에 지장이 없습니다.

---

## 로드맵

```
MVP (PoC 검증)
├── 단일 DB 연결
├── 자연어 → SQL → 결과 조회
├── 이상치 감지 + 드릴다운
├── SQL 검증 서브에이전트
└── 핵심 Hooks 3개

V1 (첫 번째 외부 고객)
├── ERP MCP 추가 (크로스 시스템)
├── PPT 플러그인 (보고서 완성)
├── 그룹웨어 플러그인 (공유)
├── period-compare (기간 자동 해석)
└── insight-writer, report-assembler

V2 (레퍼런스 확보 후)
├── 스케줄러 (정기 자동 보고)
├── Excel 플러그인
└── 팀장 페르소나 시나리오 추가
```

이 구조로 CF 설계서 작성하면 될까요?