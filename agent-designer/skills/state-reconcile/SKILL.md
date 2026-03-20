---
name: Agent Designer — State Reconcile
description: This skill should be used when the user invokes `/agent-designer:sync`, or says "상태 동기화해줘", "산출물 상태 확인해줘", "현재 설계 상태가 맞는지 확인해줘", "state.md가 이상한 것 같아", "어디까지 진행됐는지 모르겠어". This skill reads all design artifact files, re-derives the consistent current state, reports any inconsistencies, and fixes the state.md file.
version: 0.1.0
argument-hint: ""
allowed-tools: Read, Write
---

# Agent Designer — State Reconcile

여러 세션에 걸쳐 설계가 진행되면서 `state.md`와 실제 산출물 파일 사이에 불일치가 생겼을 때 일관성을 복원한다.

## 실행 순서

### 1. 모든 파일 읽기

아래 파일들을 순서대로 읽는다. 없는 파일은 "미존재"로 기록한다.

```
.claude/agent-designer/state.md
.claude/agent-designer/persona.md
.claude/agent-designer/artifact-v1.md
.claude/agent-designer/artifact-v2.md
.claude/agent-designer/artifact-v3.md
.claude/agent-designer/artifact-v4.md
.claude/agent-designer/artifact-v5.md
.claude/agent-designer/artifact-final.md
.claude/agent-designer/gap-report.md
```

### 2. 실제 상태 재도출

파일 존재 여부를 기준으로 실제 도달한 단계를 판단한다.

| 파일 존재 여부 | 실제 완료 단계 |
|---|---|
| persona.md만 있음 | 작업 A 완료, 작업 B 대기 |
| artifact-v1.md까지 있음 | 작업 B 완료, 작업 C 대기 |
| artifact-v2.md까지 있음 | 작업 C 완료, 작업 D 대기 |
| artifact-v3.md까지 있음 | 작업 D 완료, 작업 E 대기 |
| artifact-v4.md까지 있음 | 작업 E 완료, 작업 F 대기 |
| artifact-v5.md까지 있음 | 작업 F 완료, 작업 G 대기 |
| artifact-final.md 있음 | 작업 G 완료 (finalized) |

### 3. 불일치 감지 및 보고

`state.md`의 `current_step`과 실제 파일 상태를 비교한다.

불일치 유형:
- state.md의 단계가 실제보다 앞서 있음 (저장은 됐으나 state 미갱신)
- state.md의 단계가 실제보다 뒤처져 있음 (state는 갱신됐으나 파일 미저장)
- state.md 파일 자체가 없음

사용자에게 발견된 불일치를 명확히 보고한다:
```
state.md: current_step = F
실제 파일: artifact-v4.md까지 존재, artifact-v5.md 없음
→ 실제 상태: 작업 E 완료, 작업 F 미시작
→ state.md를 current_step: F에서 E로 수정합니다
```

### 4. state.md 수정

실제 상태에 맞게 `state.md`를 Write 도구로 갱신한다. 수정 전 사용자에게 변경 내용을 알린다.

### 5. 현재 상태 요약 출력

수정 완료 후 현재 상태를 한눈에 보여준다:

```
=== 설계 상태 ===
프로젝트: [이름]
현재 단계: 작업 E 완료 → 작업 F 대기
반복 횟수: 1회
구현 완료 구성요소: 0개
미해소 구멍(사후 검증): 심각 0 / 중간 0 / 경미 0
마지막 업데이트: 2026-03-20
=================

다음 단계: 작업 F (설계 후 Walk-through)를 시작하려면 "설계 이어서 해줘"라고 말하세요.
```
