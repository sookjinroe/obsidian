# agent-designer

도메인 버티컬 에이전트를 처음 설계하는 사람을 위한 Claude Code 플러그인.

"무엇을 만들어야 하는지 모르는 상태"에서 시작해 plugin-dev가 바로 구현에 들어갈 수 있는 설계 산출물까지 도달하는 전 과정을 지원한다. 구현 단계에서 발생하는 요구사항 변경도 지속적으로 추적한다.

## 설계 흐름

```
대화 (사용자 상태 파악)
  → 설계 초안 자율 생성
  → 초안 확인 및 보완
  → gap 검증
  → 구성 확정
```

사용자가 아는 만큼 말하면, 플러그인이 그 상태를 파악해 전체 초안을 자율 생성한다. 사용자는 초안을 검토하고 수정하는 역할을 맡는다.

## 구성요소

| 구성요소 | 유형 | 역할 |
|---|---|---|
| `design-session` | Skill | 사용자 상태 파악 → design-drafter 위임 → 초안 확인 → gap 검증 → 구성 확정 전 과정 진행 |
| `design-drafter` | Agent | 수집된 맥락으로 페르소나·발화·요구사항·구성요소 전체 초안 자율 생성 |
| `gap-analyzer` | Agent | 설계 후 자율 검증 — "연결 없음"·"트리거 미정의" gap 탐지 |
| `requirements-sync` | Skill | 구현 중 요구사항 변경 추적 및 입출력 불일치·도구 미연결·실패 경로 미처리 사후 검증 |
| `state-reconcile` | Skill | 상태 불일치 시 수동 복원 (`/agent-designer:sync`) |
| `SessionStart` | Hook | 세션 시작 시 이전 설계 진행 상태 자동 안내 |

## 다중 프로젝트 지원

하나의 워킹 디렉토리에서 여러 에이전트 설계를 병행할 수 있다. 각 설계는 프로젝트명으로 구분된다.

```
.claude/agent-designer/
├── redshift-analyzer/        # 프로젝트 1
│   ├── state.md
│   ├── persona.md
│   ├── artifact-v1.md ~ v5.md
│   ├── artifact-final.md
│   └── gap-report.md
├── hr-chatbot/               # 프로젝트 2 (진행 중)
│   ├── state.md
│   └── artifact-v1.md ~ v4.md
└── slack-notifier/           # 프로젝트 3 (시작 전)
    └── state.md
```

## 산출물 저장 위치

각 프로젝트 디렉토리 `.claude/agent-designer/{project-name}/` 아래에 저장된다.

```
state.md              # 현재 설계 상태
persona.md            # 페르소나 카드 (초안 생성 완료 시)
artifact-v1.md        # 발화 목록 (초안 생성 완료 시)
artifact-v2.md        # + 요구사항 (초안 생성 완료 시)
artifact-v3.md        # + 구성요소·계위 (초안 생성 완료 시)
artifact-v4.md        # + 발화 보완 및 확인 수정 (초안 확인 완료 시)
artifact-v5.md        # + gap 기록 (gap 검증 완료 시)
artifact-final.md     # 확정본 (구성 확정 완료 시)
gap-report.md         # gap-analyzer gap 검증 보고서
eval-set.md           # skill-creator eval 발화 세트 (구현 완료 후 생성)
```

## 함께 사용하는 플러그인

- **plugin-dev**: 초안 확인 시 구성요소 계위 판단 협업, 구성 확정 이후 구현 담당
- **skill-creator**: plugin-dev Skill 구현 완료 후 트리거 품질 검증

## 사용 방법

**새 설계 시작:**
```
"에이전트 설계 시작하고 싶어 redshift-analyzer"
또는
"에이전트 설계 시작하고 싶어"  ← 프로젝트명을 한 번 질문함
```

**기존 설계 이어서:**
```
"설계 이어서 해줘 redshift-analyzer"
또는
"설계 이어서 해줘"  ← 진행 중인 프로젝트가 1개면 자동 선택
```

**세션 시작 시 자동 안내:**
새 세션이 시작되면 진행 중인 모든 설계의 현황을 자동으로 표시한다.

**구현 중 요구사항 변경 시:**
```
"요구사항이 바뀌었어" 또는 "이 부분 수정해야 해"
```

**전체 상태 동기화:**
```
"상태 동기화해줘" 또는 /agent-designer:sync
"상태 동기화해줘 redshift-analyzer"  ← 특정 프로젝트만
```
