# agent-designer 소개

도메인 에이전트를 처음 설계하는 사람을 위한 Claude Code 플러그인.

"무엇을 만들어야 하는지 모르는 상태"에서 시작해, plugin-dev가 바로 구현에 들어갈 수 있는 설계 산출물까지 도달하는 전 과정을 지원한다.

---

## 어떤 문제를 해결하는가

에이전트를 새로 설계할 때 부딪히는 두 가지 문제가 있다.

**첫째, 시작점이 없다.** 어떤 발화를 수집해야 하는지, 어떤 요구사항을 도출해야 하는지, 어디서부터 시작해야 하는지 막막하다.

**둘째, 역할이 뒤섞인다.** 발화를 수집하다 보면 자연스럽게 "이건 Skill이겠지", "이건 Hook으로 해야겠다"는 생각이 끼어든다. 구현 결정이 설계 단계에 섞이면 설계가 오염된다.

agent-designer는 이 두 가지를 분리해서 해결한다. 사용자가 아는 만큼 말하면 플러그인이 전체 초안을 자율 생성한다. 구성요소·계위 결정은 plugin-dev가 독립적으로 판단한다.

---

## 전체 흐름

```
① 대화 — 사용자 상태 파악
② 자율 생성 — 발화·요구사항 초안
③ 검토 — 사용자가 확인·수정
④ 핸드오프 — plugin-dev가 구성요소·계위 결정
⑤ 커버리지 보완 — 발동 발화 없는 구성요소 탐지
⑥ gap 검증 — 연결 끊김·트리거 미정의 자동 탐지
⑦ 확정 — plugin-dev:create-plugin으로 구현 시작
```

---

## 실제 대화로 보는 작동 방식

아래는 "재무팀 SQL 조회 에이전트" 설계를 처음부터 끝까지 진행한 실제 대화다.

---

### ① 맥락 수집 — 한 문장으로 시작

```
사용자: 에이전트 설계 시작하고 싶어
플러그인: 이 설계의 프로젝트명을 정해주세요. (예: redshift-analyzer, hr-chatbot)
사용자: finance-sql
플러그인: 누가 사용하나요? 어떤 역할/부서의 사람이, 어떤 환경에서 쓰게 되나요?
```

세 가지를 파악하면 초안 생성을 시작한다. 사용자 발화에 이미 모두 담겨있으면 추가 질문 없이 바로 넘어간다.

```
사용자: 재무팀 데이터 분석가들이 Redshift에서 자연어로 SQL 조회를 하고 싶어.
        매번 개발팀에 요청하는 게 병목이거든. DML은 절대 차단해야 하고,
        읽기 전용 접근만 허용해야 해.

플러그인: 세 가지가 모두 확인됐습니다. 설계 초안을 생성하겠습니다.
```

사용 주체(재무팀 데이터 분석가), 핵심 목적(자연어 SQL 조회), 핵심 제약(DML 차단, 읽기 전용)이 한 문장에 담겼기 때문에 추가 질문 없이 바로 초안 생성으로 넘어갔다.

---

### ② 초안 자율 생성 — 발화·요구사항만

design-drafter가 페르소나 카드와 발화·요구사항 초안을 자율 생성한다.

**생성된 발화 목록 (일부):**

| 발화 ID | 유형 | 발화 내용 |
|---|---|---|
| U01 | 일상 업무 | "이번 달 부서별 영업비용 합계 뽑아줘. 지난달이랑 비교도 같이." |
| U04 | 임기응변 | "방금 뽑은 거에서 마케팅 팀 것만 따로 빼줘. 거래처별로도 쪼개줘." |
| U07 | 크로스 시스템 | "ERP랑 Redshift 숫자가 다른데, 어떤 테이블 써야 정확해?" |
| U09 | 치명적 경로 | "이 데이터 맞지? 내일 CFO 보고용인데 틀리면 큰일나." |
| U10 | 치명적 경로 | "DELETE 쳐도 돼? 테스트로 한 번 해보려고." |

발화는 일상 업무부터 치명적 경로까지 유형별로 고르게 생성된다. 추측에 기반한 항목은 ⚠️로 표시된다.

**이 단계에서 구성요소·계위는 포함하지 않는다.** 발화와 요구사항만 생성한다. 계위 결정은 설계 단계에서 판단할 수 없는 구현 결정이기 때문이다.

---

### ③ 초안 검토 — 사용자가 방향을 잡는다

```
사용자: 방향 맞아. DML 차단 발화는 "DELETE 쳐도 돼?" 같은 식으로 치명적
        경로에 넣어줘. 아티팩트에 불확실 항목은 안적어도 돼?

플러그인: 두 가지를 수정합니다. U10을 직접적인 DML 시도 발화로 교체합니다.
          [artifact.md 수정 완료]

          초안이 확정됐습니다. 다음 단계로 넘어가세요.
          plugin-dev:plugin-structure를 실행해 구성요소명·계위·소속 플러그인을
          결정해주세요.

          입력: .claude/agent-designer/finance-sql/artifact.md
          완료 후: "설계 이어서 해줘 finance-sql"
```

초안 확인이 끝나면 design-session은 gap 검증으로 바로 넘어가지 않는다. **plugin-dev에 먼저 위임한다.** 구성요소와 계위는 plugin-dev가 독립적으로 판단해야 하기 때문이다.

---

### ④ 핸드오프 — plugin-dev가 계위를 결정한다

plugin-dev:plugin-structure가 artifact.md를 읽고 각 요구사항에 구성요소·계위·소속 플러그인을 결정한다.

```
/plugin-dev:plugin-structure 이전 대화를 읽고 필요한 작업을 선별하세요
```

```
plugin-dev: 20개 요구사항을 기능 단위로 묶어 구성요소를 결정합니다.

┌─────────────────┬───────┬────────────────────────────────────────┐
│   구성요소명    │ 계위  │             커버 요구사항              │
├─────────────────┼───────┼────────────────────────────────────────┤
│ nl-to-sql       │ Skill │ R01~R11 (자연어→SQL, 컨텍스트 유지)    │
│ data-validator  │ Skill │ R12, R17, R18 (중복 집계·sanity check) │
│ schema-advisor  │ Skill │ R13, R14 (테이블 안내·불일치 진단)     │
│ result-exporter │ Skill │ R15, R16 (CSV/표 내보내기)             │
│ dml-guard       │ Hook  │ R19, R20 (DML 차단·읽기 전용 보장)     │
└─────────────────┴───────┴────────────────────────────────────────┘

artifact.md에 컬럼 추가 완료. "설계 이어서 해줘 finance-sql"로 돌아가세요.
```

---

### ⑤ 재개 감지 — 완료 여부를 자동으로 판단한다

plugin-dev 완료 전에 "설계 이어서 해줘"를 입력하면:

```
사용자: 설계 이어서 해줘 finance-sql
플러그인: artifact.md에 구성요소명 컬럼이 없습니다.
          plugin-dev:plugin-structure가 아직 완료되지 않았습니다.
          완료 후 다시 이어서 해주세요.
```

plugin-dev 완료 후 다시 입력하면:

```
사용자: 설계 이어서 해줘 finance-sql
플러그인: 구성요소명 컬럼 확인됐습니다. 커버리지 보완을 시작합니다.

┌─────────────────┬──────────────────────────────┐
│    구성요소     │          발동 발화           │
├─────────────────┼──────────────────────────────┤
│ nl-to-sql       │ U01, U02, U03, U04, U05, U06 │
│ data-validator  │ U06, U09                     │
│ schema-advisor  │ U07                          │
│ result-exporter │ U08                          │
│ dml-guard       │ U10                          │
└─────────────────┴──────────────────────────────┘

모든 구성요소에 발동 발화가 있습니다. gap 검증을 시작합니다.
```

사용자가 따로 알려주지 않아도 artifact.md의 컬럼 유무를 읽어 plugin-dev 완료 여부를 판단한다.

---

### ⑥ gap 검증 — 두 기준으로 자동 탐지

gap-analyzer가 전체 발화를 순회하며 두 기준을 검증한다.

- **① 연결 없음**: 요구사항에 매핑된 구성요소가 artifact에 없는 경우
- **③ 트리거 미정의**: Skill/Command가 있지만 어떤 발화도 발동시키지 않는 경우

```
gap 검증 완료

심각 gap: 0개 · 중간 gap: 0개 · 경미 gap: 0개

전체 발화 10개 모두 처리 가능합니다.
```

심각 gap이 발견되면 어느 발화, 어느 요구사항에서 끊어졌는지 명시하고 초안 보완으로 돌아간다.

---

### ⑦ 확정 — 구현 준비 완료

```
사용자: 확정해줘

플러그인:
목적: 재무팀 데이터 분석가의 Redshift 자연어 SQL 조회 자동화.
      개발팀 의존 병목 해소, DML 완전 차단.

┌─────────────────┬───────┬───────────────────────────────────────────────┐
│    구성요소     │ 계위  │                   핵심 역할                   │
├─────────────────┼───────┼───────────────────────────────────────────────┤
│ nl-to-sql       │ Skill │ 자연어→SQL, 기간 파싱, YoY/QoQ, 컨텍스트 유지 │
│ data-validator  │ Skill │ 중복 집계 진단, sanity check, 보고용 검토     │
│ schema-advisor  │ Skill │ 테이블 선택, ERP↔Redshift 불일치 진단         │
│ result-exporter │ Skill │ CSV/표 내보내기, Excel 병합용 형태 반환       │
│ dml-guard       │ Hook  │ DML/DDL 완전 차단                             │
└─────────────────┴───────┴───────────────────────────────────────────────┘

plugin-dev:create-plugin에 artifact.md를 전달해 구현을 시작할 수 있습니다.
```

---

## 산출물: artifact.md 한 파일이 자란다

설계 과정 전체가 하나의 파일에 누적된다. 단계마다 파일을 새로 만들지 않는다.

**design-drafter 완료 후**
```
| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 내용 |
```

**plugin-dev 완료 후** (구성요소 컬럼 추가)
```
| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 내용 | 구성요소명 | 계위 | 소속플러그인 |
```

**gap 검증 완료 후** (gap 컬럼 추가)
```
| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 내용 | 구성요소명 | 계위 | 소속플러그인 | gap설명 | 심각도 |
```

---

## 상태 관리

설계 진행 상태는 `.claude/agent-designer/{프로젝트명}/state.md`에 저장된다. 세션이 끊겨도 이어서 진행할 수 있다.

```
drafting          → 초안 생성 중
reviewed          → plugin-dev 핸드오프 대기
component_added   → gap 검증 대기
gap_check         → 확정 대기
finalized         → 구현 시작 가능
```

상태가 어긋났을 때는 state-reconcile이 artifact.md의 컬럼 상태를 직접 읽어 복원한다.

```
사용자: 상태 동기화해줘 finance-sql

플러그인: [finance-sql]
          state.md:  current_step = drafting
          실제 파일:  artifact.md — 구성요소명 컬럼 ✓, gap 컬럼 ✓
          → 실제 상태: gap_check
          → state.md를 current_step: gap_check로 수정합니다.
```

---

## 함께 사용하는 플러그인

```
agent-designer          설계
  └─ plugin-dev         구현 (plugin-structure → create-plugin)
       └─ skill-creator 트리거 품질 검증
```

agent-designer가 확정한 artifact.md를 plugin-dev:create-plugin에 전달하면 구현이 시작된다. Skill 구현 완료 후 requirements-sync가 skill-creator용 eval 발화 세트를 준비한다.

---

## 구현 단계에서도 설계 문서는 살아있다

확정 이후에도 agent-designer는 두 가지 역할을 계속한다. 구현 중 발생하는 변화를 추적하고, Skill 구현이 완료되면 트리거 검증을 준비한다.

---

### ⑧ 구현 완료 시 사후 검증 — ②④⑤ 기준

plugin-dev가 구성요소 하나를 완료할 때마다 requirements-sync가 설계 시점에 확인할 수 없었던 세 가지를 검증한다.

- **② 입출력 불일치**: 이 구성요소의 출력이 다음 구성요소의 입력과 맞는가
- **④ 도구 미연결**: allowed-tools에 필요한 MCP 서버가 모두 있는가
- **⑤ 실패 경로 미처리**: 이 구성요소가 실패할 때 에이전트의 행동이 정의되어 있는가

```
사용자: nl-to-sql Skill 구현 완료됐어.
        Redshift는 MCP로 연결했고, 출력은 { sql: string, params: [] } 형태야.

requirements-sync: nl-to-sql 사후 검증을 시작합니다.

② 입출력 불일치
  nl-to-sql 출력 { sql, params } → Redshift MCP 입력 형식과 일치. 통과.

④ 도구 미연결
  allowed-tools에 Redshift MCP 포함 확인. 통과.

⑤ 실패 경로 미처리
  Redshift 연결 실패 시 (타임아웃, 인증 오류) 에이전트 행동이
  SKILL.md에 정의되어 있지 않습니다.

  → 중간 gap: 실패 시 사용자에게 모호한 응답 반환 가능.
    artifact.md에 기록합니다. plugin-dev에 실패 처리 추가를 권고합니다.

  implementation_status: nl-to-sql → pending (gap 해소 후 implemented 처리)
```

설계 시점에는 "실패하면 어떻게 되는가"를 알 수 없다. 구현 결과물이 생긴 후에야 판단 가능한 gap이다.

---

### ⑨ 구현 중 요구사항이 바뀌었을 때

구현하다 보면 설계가 바뀌는 경우가 생긴다. requirements-sync가 변경의 영향 범위를 역추적하고 설계 문서를 갱신한다.

```
사용자: 구현하다 보니 schema-advisor가 ERP 데이터에 직접 접근하는 건
        안 될 것 같아. ERP API가 사내망에서만 열려 있어서.
        테이블 안내는 할 수 있지만 실시간 데이터 비교는 못 해.

requirements-sync: 영향 범위를 역추적합니다.

변경된 요구사항: R13 (공식 집계 기준 테이블 안내) — 유지 가능
                 R14 (숫자 불일치 원인 진단 쿼리) — 실시간 ERP 접근 불가로 제한

영향받는 발화: U07 "ERP랑 Redshift 숫자가 다른데, 어떤 테이블 써야 정확해?"
영향받는 구성요소: schema-advisor (Skill) — 현재 pending

artifact.md를 업데이트합니다.
R14 요구사항 내용: "ERP 실시간 비교 대신, 불일치 원인 진단 쿼리를
                   Redshift 내에서 제안하는 것으로 범위 제한"

변경 이력에 기록합니다.
| 2026-03-23 | R14 | ERP 직접 접근 → Redshift 내 진단 쿼리로 범위 제한 | 사내망 API 제약 |

핵심 처리 경로 변경은 아닙니다. 설계 재검증 없이 계속 진행 가능합니다.
plugin-dev에 R14 수정 사항을 전달하세요.
```

변경이 핵심 경로를 건드리는 경우 — 예를 들어 구성요소를 추가하거나 제거해야 하는 경우 — requirements-sync가 state.md를 `reviewed`로 되돌리고 design-session 재진입을 안내한다. 설계는 구현 내내 살아있는 문서로 유지된다.

---

### ⑩ 모든 Skill 구현 완료 — skill-creator로 트리거 검증

plugin-dev Phase 5(모든 Skill 구현 완료)가 끝나면 requirements-sync가 artifact.md의 발화 목록을 eval 형식으로 변환한다.

```
사용자: plugin-dev Phase 5 완료됐어. 모든 Skill 다 만들었어.

requirements-sync: eval-set.md를 생성합니다.
                   artifact.md의 발화 10개를 Skill별로 분류합니다.
```

**생성되는 eval-set.md (일부):**

```
## Skill: nl-to-sql — Eval 발화 세트

### should_trigger: true
- "이번 달 부서별 영업비용 합계 뽑아줘. 지난달이랑 비교도 같이." (일상 업무)
- "2분기 전체 매출이랑 원가 데이터 필요해. BU별로 나눠서." (일상 업무)
- "방금 뽑은 거에서 마케팅 팀 것만 따로 빼줘." (임기응변)
- "작년 동기 대비 이번 달 인건비 증감률 계산해줄 수 있어?" (일상 업무)

### should_trigger: false
- "DELETE 쳐도 돼? 테스트로 한 번 해보려고." (DML — dml-guard 담당)
- "이 데이터 중복 집계된 거 아니야?" (data-validator 담당)
- "어떤 테이블 써야 정확해?" (schema-advisor 담당)


## Skill: data-validator — Eval 발화 세트

### should_trigger: true
- "지금 나온 숫자가 내가 알던 거랑 달라. 중복 집계된 거 아니야?" (임기응변)
- "이 데이터 맞지? 내일 CFO 보고용인데 틀리면 큰일나." (치명적 경로)

### should_trigger: false
- "3월 비용 중에 건당 500만 원 넘는 항목만 골라줘." (nl-to-sql 담당)
- "어떤 테이블 써야 정확해?" (schema-advisor 담당)
```

```
requirements-sync: eval-set.md 생성 완료.
                   skill-creator에 이 파일을 전달해 각 Skill의 트리거 품질을 검증하세요.
```

skill-creator가 각 Skill의 description을 실제 발화로 테스트한다. undertrigger(발동되어야 하는데 안 되는 경우)가 발견되면 description 보완을 권고한다.

```
skill-creator: nl-to-sql — undertrigger 발견

"방금 뽑은 거에서 마케팅 팀 것만 따로 빼줘" → 발동되지 않음.
모호한 후속 발화는 맥락 없이는 nl-to-sql을 발동시키지 못합니다.

권고: description에 아래 내용 추가
  "직전 조회 결과에 조건을 추가하거나 필터를 변경하는 후속 요청에도 사용한다.
   명시적 SQL 언급 없이 업무 용어로만 표현된 발화에도 발동한다."
```

이 피드백이 Skill description에 반영되면 설계에서 수집한 임기응변 발화가 실제로 에이전트를 발동시키는 능력으로 이어진다.

---

## 전체 사이클 요약

```
① 대화         사용자 상태 파악
② 자율 생성    발화·요구사항 초안 (design-drafter)
③ 검토         방향 확인 및 수정
④ 핸드오프     구성요소·계위 결정 (plugin-dev:plugin-structure)
⑤ 커버리지     발동 발화 없는 구성요소 탐지
⑥ gap 검증     연결 끊김·트리거 미정의 탐지 (gap-analyzer)
⑦ 확정         구현 시작 가능 상태

── 구현 단계 ──────────────────────────────────────────
⑧ 사후 검증    구성요소별 ②④⑤ 검증 (requirements-sync)
⑨ 변경 추적    요구사항 변경 → 영향 범위 역추적 (requirements-sync)
⑩ eval 준비    발화 → eval 세트 변환 → skill-creator 전달
```

artifact.md는 이 과정 전체를 기록한다. 설계가 확정된 후에도 구현 결과와 변경 이력이 누적되며, 언제든 "이 발화는 왜 이 구성요소로 연결됐는가"를 역추적할 수 있다.

---

## 설치 및 시작

```bash
# 플러그인 로드
claude --plugin-dir ./agent-designer

# 새 설계 시작
에이전트 설계 시작하고 싶어

# 기존 설계 이어서
설계 이어서 해줘 finance-sql

# 상태 동기화
상태 동기화해줘
```
