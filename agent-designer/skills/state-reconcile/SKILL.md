---
name: Agent Designer — State Reconcile
description: This skill should be used when the user invokes `/agent-designer:sync`, or says "상태 동기화해줘", "산출물 상태 확인해줘", "현재 설계 상태가 맞는지 확인해줘", "state.md가 이상한 것 같아", "어디까지 진행됐는지 모르겠어". This skill reads all design artifact files, re-derives the consistent current state, reports any inconsistencies, and fixes the state.md file.
version: 0.3.0
argument-hint: "[프로젝트명 (선택, 생략 시 전체 프로젝트 표시)]"
allowed-tools: Read, Write, Bash
---

# Agent Designer — State Reconcile

여러 세션에 걸쳐 설계가 진행되면서 `state.md`와 실제 산출물 파일 사이에 불일치가 생겼을 때 일관성을 복원한다. 다중 프로젝트를 지원하며, 특정 프로젝트명이 argument로 주어지면 해당 프로젝트만, 없으면 전체 프로젝트를 처리한다.

## 실행 순서

### 1. 프로젝트 목록 확인

```bash
ls .claude/agent-designer/ 2>/dev/null
```

`.claude/agent-designer/` 아래 서브디렉토리 목록을 확인한다.

- **argument로 프로젝트명이 주어진 경우:** 해당 프로젝트만 처리
- **argument 없는 경우:** 모든 서브디렉토리를 순회하여 각 프로젝트의 state.md를 검사

서브디렉토리가 없으면 "진행 중인 설계가 없습니다."를 출력하고 종료.

### 2. 각 프로젝트 파일 읽기

각 프로젝트 경로(`{project_path} = .claude/agent-designer/{project-name}`)에 대해 아래 파일들을 읽는다. 없는 파일은 "미존재"로 기록한다.

```
{project_path}/state.md
{project_path}/persona.md
{project_path}/artifact-v1.md
{project_path}/artifact-v2.md
{project_path}/artifact-v3.md
{project_path}/artifact-v4.md
{project_path}/artifact-v5.md
{project_path}/artifact-final.md
{project_path}/gap-report.md
```

### 3. 실제 상태 재도출

파일 존재 여부로 실제 `current_step`을 판단한다.

| 파일 상태 | 실제 current_step | 의미 |
|---|---|---|
| artifact-v4.md 없음 (또는 v1~v4 일부 누락) | drafting | 초안 생성 전 또는 진행 중 |
| artifact-v4.md 있음, artifact-v5.md 없음 | F | 초안 확인 완료, gap 검증 대기 |
| artifact-v5.md 있음, artifact-final.md 없음 | G | gap 검증 완료, 구성 확정 대기 |
| artifact-final.md 있음 | finalized | 설계 완료 |

**유효한 current_step 값:** `drafting`, `F`, `G`, `finalized`

### 4. 불일치 감지 및 보고

`state.md`의 `current_step`과 실제 파일 상태를 비교한다.

불일치 유형:
- state.md의 current_step이 파일 상태보다 앞서 있음
- state.md의 current_step이 파일 상태보다 뒤처져 있음
- state.md 파일 자체가 없음
- state.md에 구 스키마 값(A~E) 있음 → `drafting`으로 변환

사용자에게 발견된 불일치를 명확히 보고한다:

```
[redshift-analyzer]
state.md: current_step = F
실제 파일: artifact-v4.md 존재, artifact-v5.md 없음
→ 파일 상태와 일치. 수정 불필요.

[hr-chatbot]
state.md: current_step = G
실제 파일: artifact-v5.md 없음
→ 불일치. state.md를 current_step: F로 수정합니다.
```

### 5. state.md 수정

실제 상태에 맞게 각 프로젝트의 `state.md`를 Write 도구로 갱신한다. 수정 전 사용자에게 변경 내용을 알린다.

구 스키마 값(A, B, C, D, E) 발견 시 → `drafting`으로 일괄 변환.

### 6. 전체 상태 요약 출력

수정 완료 후 전체 프로젝트 현황과 다음 할 일을 안내한다:

```
=== 설계 상태 ===
[redshift-analyzer]
현재 단계: 설계 완료
반복 횟수: 2회
구현 완료 구성요소: 0개

[hr-chatbot]
현재 단계: gap 검증 대기
반복 횟수: 1회
구현 완료 구성요소: 0개
=================
이어서 진행: "설계 이어서 해줘 [프로젝트명]"
새 설계 시작: "에이전트 설계 시작하고 싶어 [프로젝트명]"
```

단계 설명 및 다음 할 일:

| current_step | 단계 설명 | 다음 할 일 |
|---|---|---|
| drafting | 초안 생성 전 또는 진행 중 | "에이전트 설계 시작하고 싶어 [프로젝트명]" |
| F | 초안 확인 완료, gap 검증 대기 | "설계 이어서 해줘 [프로젝트명]"로 gap 검증 시작 |
| G | gap 검증 완료, 구성 확정 대기 | "설계 이어서 해줘 [프로젝트명]"로 구성 확정 |
| finalized | 설계 완료 | plugin-dev:create-plugin으로 구현 시작 가능 |
