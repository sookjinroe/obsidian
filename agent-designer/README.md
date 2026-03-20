# agent-designer

도메인 버티컬 에이전트를 처음 설계하는 사람을 위한 Claude Code 플러그인.

"무엇을 만들어야 하는지 모르는 상태"에서 시작해 plugin-dev가 바로 구현에 들어갈 수 있는 설계 산출물까지 도달하는 전 과정을 지원한다. 구현 단계에서 발생하는 요구사항 변경도 지속적으로 추적한다.

## 구성요소

| 구성요소 | 유형 | 역할 |
|---|---|---|
| `design-session` | Skill | 페르소나 정의 → 발화 도출 → 요구사항 추출 → 구성요소 결정 → 구멍 검증 → 구성 확정 (A→G) |
| `requirements-sync` | Skill | 구현 중 요구사항 변경 추적 및 ②④⑤ 사후 검증 |
| `state-reconcile` | Skill | 상태 불일치 시 수동 복원 (`/agent-designer:sync`) |
| `gap-analyzer` | Agent | 설계 후 Walk-through — ①③ 기준 자율 검증 |
| `SessionStart` | Hook | 세션 시작 시 이전 설계 상태 자동 복원 |

## 산출물 저장 위치

```
.claude/agent-designer/
  state.md              # 현재 설계 상태
  persona.md            # 페르소나 카드 (작업 A 완료 시)
  artifact-v1.md        # 발화 목록 (작업 B)
  artifact-v2.md        # + 요구사항 (작업 C)
  artifact-v3.md        # + 구성요소·계위 (작업 D)
  artifact-v4.md        # + 발화 보완 (작업 E)
  artifact-v5.md        # + 구멍 (작업 F)
  artifact-final.md     # 확정본 (작업 G)
  gap-report.md         # gap-analyzer 보고서
```

## 함께 사용하는 플러그인

- **plugin-dev**: 작업 D에서 구성요소 계위 판단, 작업 G 이후 구현 담당
- **skill-creator**: plugin-dev Phase 5 완료 후 Skill 트리거 품질 검증

## 사용 방법

설계 시작:
```
"에이전트 설계 시작하고 싶어" 또는 /agent-designer:design
```

설계 재개:
```
새 세션 시작 시 자동으로 이전 상태 안내
```

상태 동기화:
```
/agent-designer:sync
```
