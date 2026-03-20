---
name: Agent Designer — Design Session
description: This skill should be used when the user says "에이전트 설계 시작", "플러그인 설계하고 싶어", "에이전트 만들려고 해", "설계 이어서 해줘", "페르소나 정의해보자", "발화 뽑아줘", "요구사항 추출해줘", "Walk-through 해보자", "구성요소 결정해줘", "구멍 검증해줘", "설계 확정해줘", or invokes `/agent-designer:design`. This skill guides the full A→G agent design workflow from persona definition to finalized design artifact ready for plugin-dev handoff.
version: 0.1.0
argument-hint: "[설계할 에이전트 이름 또는 설명 (선택)]"
allowed-tools: Read, Write
---

# Agent Designer — Design Session

에이전트 설계의 전체 과정(작업 A→G)을 이끄는 핵심 스킬. 페르소나 정의에서 시작해 plugin-dev가 즉시 구현에 착수할 수 있는 확정 산출물까지 도달하도록 안내한다.

## 시작 전: 상태 확인

세션 시작 시 또는 이 스킬이 활성화될 때, 먼저 상태 파일을 확인한다.

```
Read: .claude/agent-designer/state.md
```

- 파일이 없으면 → **작업 A**부터 시작
- 파일이 있으면 → `current_step` 값을 읽어 해당 단계로 직접 이동

상태 파일을 읽은 후 사용자에게 현재 위치를 한 줄로 안내한다. 예: "이전 세션에서 '[프로젝트명]' 설계가 작업 C까지 진행됐습니다. 이어서 진행할까요?"

## 작업 흐름 개요

```
A. 페르소나 정의
   ↓ (persona.md 저장)
B. 발화 가설 도출
   ↓ (artifact-v1.md 저장)
C. 설계 전 Walk-through (발화 → 요구사항)
   ↓ (artifact-v2.md 저장)
D. plugin-dev 계위 판단 연동 (요구사항 → 구성요소)
   ↓ (artifact-v3.md 저장)
E. 발화 목록 보완 (구성요소 커버리지 확인)
   ↓ (artifact-v4.md 저장)
F. 설계 후 Walk-through (구멍 검증 ①③)
   ↓ 심각 구멍 없음 → (artifact-v5.md 저장)
   ↓ 심각 구멍 있음 → 작업 D로 복귀
G. 구성 확정
   ↓ (artifact-final.md 저장)
   → plugin-dev 인계
```

각 단계의 상세 절차는 `references/workflow-steps.md`를 참조한다.

## 산출물 저장 정책

**자동 저장 시점 (작업 완료 경계):**

| 작업 완료 | 저장 파일 |
|---|---|
| 작업 A | `.claude/agent-designer/persona.md` |
| 작업 B | `.claude/agent-designer/artifact-v1.md` |
| 작업 C | `.claude/agent-designer/artifact-v2.md` |
| 작업 D | `.claude/agent-designer/artifact-v3.md` |
| 작업 E | `.claude/agent-designer/artifact-v4.md` |
| 작업 F | `.claude/agent-designer/artifact-v5.md` |
| 작업 G | `.claude/agent-designer/artifact-final.md` |

각 작업 완료 직후 반드시 해당 파일을 저장하고 `state.md`의 `current_step`을 다음 단계로 업데이트한다.

**수동 저장 (작업 내부):** 사용자가 "지금까지 저장해줘" 또는 "현재 산출물 보여줘"라고 요청할 때만 저장한다. 작업 중간의 잦은 자동 저장은 불완전한 상태를 남기므로 하지 않는다.

## 상태 파일 업데이트

작업이 완료될 때마다 `state.md`를 Write 도구로 갱신한다. 형식은 `references/state-schema.md`를 참조한다.

## 작업 D: plugin-dev 협업

작업 D에서는 plugin-dev 플러그인의 `plugin-structure` 스킬을 활용해 구성요소의 계위(Command / Skill / references / MCP / Hook / Agent)를 판단한다. plugin-dev와 design-session이 동시에 활성화된 상태에서 plugin-dev의 판단 결과를 이 스킬의 산출물에 통합한다.

계위 판단 후 사용자에게 각 판단의 근거를 설명하고 조정 의견을 받는다.

## 작업 F: 구멍 검증

작업 F 시점에 `gap-analyzer` 에이전트를 호출한다.

```
입력: .claude/agent-designer/artifact-v4.md
출력: .claude/agent-designer/gap-report.md
```

gap-analyzer가 완료되면 `gap-report.md`를 읽어 결과를 사용자에게 보고한다.

- 심각 구멍이 있으면: `state.md`의 `loop_history`에 복귀 이력을 기록하고 작업 D로 돌아간다
- 심각 구멍이 없으면: `artifact-v5.md`를 저장하고 작업 G로 진행한다

구멍 판정 기준(①연결 없음, ③트리거 미정의)과 심각도 분류는 `references/gap-criteria.md`를 참조한다.

## 작업 G: plugin-dev 인계

`artifact-final.md`를 생성하고 사용자에게 아래를 안내한다.

- 확정본에 포함된 정보 요약 (목적, 사용자, 구성요소 목록, 계위, 연결 관계)
- "plugin-dev:create-plugin에 이 산출물을 전달해 구현을 시작할 수 있습니다"

## 산출물 형식

산출물은 Markdown 표 형식을 사용한다. 발화 하나가 여러 요구사항에, 요구사항 하나가 여러 구성요소에 대응될 때 발화 ID를 반복하는 행 구조로 1:N 관계를 표현한다. 상세 형식은 `references/artifact-schema.md`를 참조한다.

## Walk-through 방법론

설계 전 Walk-through(작업 C)와 설계 후 Walk-through(작업 F, gap-analyzer)의 구체적인 진행 방식은 `references/walkthrough-guide.md`를 참조한다.

## 루프백 처리

심각 구멍 발견으로 작업 D로 복귀할 때:

1. 복귀 이유(어느 발화, 어느 구멍)를 `state.md`의 `loop_history`에 기록
2. `iteration` 카운터를 1 증가
3. 사용자에게 "U07에서 심각 구멍이 발견되어 작업 D로 돌아갑니다. [이유]" 안내
4. artifact-v3.md부터 해당 항목을 **덮어쓰며** 재진행

**버전 파일 덮어쓰기 정책:**
루프백 시 artifact-v3.md, v4.md, v5.md는 새 파일을 만들지 않고 기존 파일을 덮어쓴다. 이전 이터레이션의 내용은 `state.md`의 `loop_history`에 구멍 원인과 복귀 지점이 기록되므로 이력 추적이 가능하다. artifact 파일은 항상 "현재 진행 중인 설계의 최신 상태"를 나타낸다.

## 대화 진행 원칙

- 한 번에 한 단계씩 진행한다. 사용자 확인 없이 다음 단계로 넘어가지 않는다
- 사용자가 중간에 다른 질문을 하더라도 현재 단계 완료 후 돌아온다
- 각 단계 시작 시 "지금 작업 [X]를 시작합니다. [목적 한 줄]"로 맥락을 환기한다

## 참조 파일

| 파일 | 내용 |
|---|---|
| `references/workflow-steps.md` | 작업 A~G 각 단계 상세 절차 |
| `references/walkthrough-guide.md` | Walk-through 진행 방식 |
| `references/gap-criteria.md` | 구멍 ①③ 기준 및 심각도 분류 |
| `references/artifact-schema.md` | 산출물 표 형식 및 1:N 관계 표현 규칙 |
| `references/state-schema.md` | state.md 파일 형식 정의 |
