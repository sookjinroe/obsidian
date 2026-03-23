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
  artifact: ".claude/agent-designer/{project-name}/artifact.md"
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
| `current_step` | enum | drafting, reviewed, component_added, gap_check, finalized 중 하나 |
| `mode` | enum | drafter (design-drafter가 초안 생성) |
| `iteration` | integer | 루프백 횟수. 첫 번째 진행은 1 |
| `last_updated` | date | 마지막 저장 날짜 (YYYY-MM-DD) |
| `artifacts` | object | 산출물 파일 경로. persona와 artifact 두 개 |
| `implementation_status` | object | 구성요소별 구현 상태 (pending / implemented) |
| `post_impl_gaps` | object | 구현 후 검증에서 발견된 gap 수 |
| `loop_history` | array | 루프백 이력 목록 |

---

## current_step 전이 규칙

| 이전 값 | 이후 값 | 조건 | 담당 |
|---|---|---|---|
| (없음) | drafting | 신규 설계 시작 | design-session |
| drafting | reviewed | 사용자 초안 검토 완료 | design-session |
| reviewed | component_added | 재개 시 artifact.md 구성요소명 컬럼 감지 | design-session |
| component_added | gap_check | gap-analyzer 실행 완료 | design-session |
| gap_check | finalized | 심각 gap 없음 확인 | design-session |
| gap_check | reviewed | 심각 gap 발견 (iteration +1, artifact.md 보완) | design-session |

---

## loop_history 항목 형식

```yaml
loop_history:
  - iteration: 2
    trigger_utterance: "U07"
    trigger_gap: "①연결 없음 — 법인 집계 구성요소 미존재"
    returned_to: "reviewed"
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
