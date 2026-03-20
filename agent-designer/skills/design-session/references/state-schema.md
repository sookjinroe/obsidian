# state.md 형식 정의

## 위치

`.claude/agent-designer/{project-name}/state.md`

각 프로젝트는 독립된 서브디렉토리에 저장된다. 여러 설계를 병행할 때:

```
.claude/agent-designer/
├── redshift-analyzer/state.md
├── hr-chatbot/state.md
└── slack-notifier/state.md
```

---

## 형식

```yaml
---
project: "[에이전트 이름]"
current_step: "drafting"
mode: "drafter"
iteration: 1
last_updated: "2026-03-20"
artifacts:
  persona: ".claude/agent-designer/{project-name}/persona.md"
  v1: ".claude/agent-designer/{project-name}/artifact-v1.md"
  v2: ".claude/agent-designer/{project-name}/artifact-v2.md"
  v3: ".claude/agent-designer/{project-name}/artifact-v3.md"
  v4: ".claude/agent-designer/{project-name}/artifact-v4.md"
  v5: ".claude/agent-designer/{project-name}/artifact-v5.md"
  final: ".claude/agent-designer/{project-name}/artifact-final.md"
implementation_status: {}
post_impl_gaps:
  critical: 0
  moderate: 0
  minor: 0
loop_history: []
---
```

---

## 필드 정의

| 필드 | 타입 | 설명 |
|---|---|---|
| `project` | string | 설계 중인 에이전트 이름 |
| `current_step` | enum | drafting, F, G, finalized 중 하나 |
| `mode` | enum | drafter (design-drafter가 초안 생성) |
| `iteration` | integer | 루프백 횟수. 첫 번째 진행은 1 |
| `last_updated` | date | 마지막 저장 날짜 (YYYY-MM-DD) |
| `artifacts` | object | 각 버전 파일 경로. 아직 생성되지 않은 버전은 포함하지 않음 |
| `implementation_status` | object | 구성요소별 구현 상태 (pending / implemented) |
| `post_impl_gaps` | object | 구현 후 검증에서 발견된 gap 수 |
| `loop_history` | array | 루프백 이력 목록 |

---

## loop_history 항목 형식

```yaml
loop_history:
  - iteration: 2
    trigger_utterance: "U07"
    trigger_gap: "①연결 없음 — 법인 집계 구성요소 미존재"
    returned_to: "D"
    date: "2026-03-21"
```

---

## implementation_status 예시

```yaml
implementation_status:
  sql-translator: implemented
  schema-ref: implemented
  db-connector: pending
  dml-guard: pending
```

---

## current_step 전이 규칙

| 이전 값 | 이후 값 | 조건 |
|---|---|---|
| (없음) | drafting | 신규 설계 시작 (충분성 판단 루프 ~ drafter 완료) |
| drafting | F | 초안 확인 완료 |
| F | G | 심각 gap 없음 확인 |
| F | drafting | 심각 gap 발견 (iteration +1, artifact-v4.md 보완) |
| G | finalized | 사용자 최종 확인 완료 |
