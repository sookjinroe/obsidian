---
name: Agent Designer — Design Session
description: This skill should be used when the user says "에이전트 설계 시작", "플러그인 설계하고 싶어", "에이전트 만들려고 해", "설계 이어서 해줘", "페르소나 정의해보자", "발화 뽑아줘", "요구사항 추출해줘", "Walk-through 해보자", "gap 검증해줘", "설계 확정해줘", This skill guides the full agent design workflow: it dynamically assesses the user's current understanding, delegates to design-drafter for autonomous draft generation once sufficient context is gathered, then guides review, plugin-dev handoff, coverage verification, gap validation, and finalization.
version: 0.4.0
argument-hint: "[프로젝트명 (선택, 예: redshift-analyzer)]"
allowed-tools: Read, Write, Bash, Agent
---

# Agent Designer — Design Session

에이전트 설계의 전체 과정을 이끄는 핵심 스킬. 대화를 통해 사용자의 현재 상태를 동적으로 파악하고, 충분한 맥락이 확보되면 design-drafter에 위임해 발화·요구사항 초안을 자율 생성한다. 구성요소·계위 결정은 plugin-dev:plugin-structure에 위임한다.

하나의 워킹 디렉토리 안에서 여러 에이전트 설계를 병행할 수 있다. 각 설계는 프로젝트명으로 구분되며 `.claude/agent-designer/{project-name}/`에 독립 저장된다.

## 시작 전: 프로젝트 선택

### 1. 기존 프로젝트 스캔

```bash
ls .claude/agent-designer/ 2>/dev/null
```

`.claude/agent-designer/` 아래 서브디렉토리가 있으면 각 `state.md`를 읽어 목록을 만든다.

### 2. 진입 분기

**argument로 프로젝트명이 주어진 경우:**
- 해당 프로젝트의 `.claude/agent-designer/{project-name}/state.md`를 읽는다
- 파일이 없으면 신규 설계 시작, 있으면 해당 단계로 직접 이동

**argument 없고 기존 프로젝트 없음:**
- 프로젝트명을 한 번 질문한다: "이 설계의 프로젝트명을 정해주세요 (예: redshift-analyzer, hr-chatbot)"
- 답변을 받으면 신규 설계 시작

**argument 없고 기존 프로젝트 있음:**
- 진행 중인 프로젝트 목록을 표시하고 선택을 요청한다:
  ```
  진행 중인 설계:
  1. redshift-analyzer (gap 검증 대기)
  2. hr-chatbot (초안 생성 중)
  새로 시작하려면 프로젝트명을 입력하세요.
  ```
- 선택 또는 신규 프로젝트명을 받으면 해당 흐름으로 진입

### 3. 프로젝트 경로 설정

프로젝트명 확정 후 이 스킬의 모든 파일 경로는 아래 변수를 기준으로 한다:

```
{project_path} = .claude/agent-designer/{project-name}
```

신규 설계인 경우:
```bash
mkdir -p {project_path}
```

상태 파일을 읽은 후 사용자에게 현재 위치를 한 줄로 안내한다.

## 충분성 판단 루프 (신규 설계)

대화를 통해 아래 세 가지가 모두 파악될 때까지 한 번에 하나씩 질문한다.

| 항목 | 확인 내용 |
|---|---|
| ① 사용 주체 | 누가 쓰는가 (역할, 부서, 업무 환경) |
| ② 핵심 목적과 불편함 | 무엇을 해결하는가, 현재 무엇이 불편한가 |
| ③ 핵심 제약 | 반드시 지켜야 할 것, 연결해야 할 시스템 |

**매 답변 후 충분성을 판단한다.** 세 가지가 모두 충족되면 즉시 design-drafter를 호출한다. 사용자의 첫 발화에서 이미 충족된 경우 바로 호출한다.

충분성 판단 시 주의:
- ①②③이 명확하면 충분. 세부 시스템 명칭·테이블 구조·내부 용어는 필수가 아님 — drafter가 ⚠️로 마킹한다
- 같은 항목을 두 번 묻지 않는다
- 사용자가 자발적으로 제공한 정보는 충분성에 반영한다

상세 진행 방식은 `references/workflow-steps.md`의 충분성 판단 루프 섹션을 참조한다.

## design-drafter 호출

충분성 충족 시:

1. `{project_path}/state.md`를 `current_step: drafting`으로 초기 생성 (아직 없는 경우)
2. "지금까지 파악한 내용으로 설계 초안을 생성하겠습니다"라고 안내
3. 수집된 정보를 구조화된 컨텍스트 요약으로 정리해 design-drafter에 전달 (반드시 `프로젝트 경로` 포함)
4. design-drafter가 `{project_path}/persona.md`, `{project_path}/artifact.md` (발화·요구사항만) 자율 생성

## 초안 확인 및 보완

design-drafter 완료 후:

1. 생성된 발화 수와 요구사항 수를 요약 제시
2. "큰 방향이 맞나요? 맞지 않는 부분이 있으면 말씀해주세요"로 전체 확인
3. ⚠️ 항목에 대해 추가 정보 요청 (선택)
4. 수정사항을 `{project_path}/artifact.md`에 반영

초안 확인 완료 후 `{project_path}/state.md`의 `current_step`을 `reviewed`로 업데이트한다.

## plugin-dev 핸드오프

초안 확인 완료 후 사용자에게 안내한다:

```
발화·요구사항 초안이 확정됐습니다.
다음으로 plugin-dev:plugin-structure를 실행해 구성요소명·계위·소속 플러그인을 결정해주세요.

입력: {project_path}/artifact.md
작업: 각 요구사항에 구성요소명·계위·소속플러그인 컬럼 추가
완료 후: "설계 이어서 해줘 {project-name}"으로 돌아오세요.
```

이 시점에서 design-session은 대기한다. plugin-dev가 artifact.md에 컬럼을 추가한 후 사용자가 재진입한다.

## 재개 후 커버리지 보완

사용자가 재진입("설계 이어서 해줘")하면:

1. `{project_path}/artifact.md`를 읽어 구성요소명 컬럼 유무를 확인한다
2. **컬럼 없음** → "plugin-dev:plugin-structure가 완료되지 않았습니다. 완료 후 이어서 해주세요" 안내 후 중단
3. **컬럼 있음** → `{project_path}/state.md`의 `current_step`을 `component_added`로 업데이트하고 진행

커버리지 보완:
- artifact.md의 구성요소 목록을 순회하며 발동 발화가 없는 구성요소를 찾는다
- 발동 발화가 없는 구성요소가 있으면 해당 경로를 발동시킬 발화 행을 추가한다 (유형: "보완")
- artifact.md에 추가된 행을 저장한다

## gap 검증

`gap-analyzer` 에이전트를 호출한다. 호출 시 프로젝트 경로를 명시한다:

```
프로젝트 경로: {project_path}
입력: {project_path}/artifact.md
출력: {project_path}/gap-report.md
```

`{project_path}/gap-report.md`를 읽어 결과를 사용자에게 보고한다.

- **심각 gap이 있으면:** `{project_path}/state.md`의 `loop_history`에 기록, `iteration` +1, `current_step`을 `reviewed`로 되돌리고 초안 보완으로 복귀
- **심각 gap이 없으면:** artifact.md에 gap 컬럼을 추가 (gap-report.md 내용 머지), `current_step`을 `gap_check`로 업데이트, 확정으로 진행

gap 판정 기준(①연결 없음, ③트리거 미정의)과 심각도 분류는 `references/gap-criteria.md`를 참조한다.

## 확정

gap 컬럼이 추가된 artifact.md를 기반으로 사용자에게 아래를 안내한다:

- 확정본 요약 (목적, 사용자, 구성요소 목록, 계위, 연결 관계)
- 미해소 gap(중간/경미) 목록
- "plugin-dev:create-plugin에 이 산출물을 전달해 구현을 시작할 수 있습니다"

사용자 최종 확인 후 `{project_path}/state.md`의 `current_step`을 `finalized`로 업데이트한다.

## 산출물 저장 정책

모든 파일은 `{project_path}/` 아래에 저장한다.

| 완료 시점 | 저장/변경 파일 |
|---|---|
| design-drafter 완료 | `persona.md`, `artifact.md` (발화·요구사항) |
| 초안 확인 완료 | `artifact.md` (수정사항 반영), `state.md` current_step → reviewed |
| plugin-dev 완료 감지 | `state.md` current_step → component_added |
| 커버리지 보완 완료 | `artifact.md` (보완 발화 행 추가) |
| gap 검증 완료 | `artifact.md` (gap 컬럼 추가), `gap-report.md`, `state.md` current_step → gap_check |
| 확정 완료 | `state.md` current_step → finalized |

## 루프백 처리

심각 gap 발견으로 초안 보완이 필요할 때:

1. 보완 이유(어느 발화, 어느 gap)를 `{project_path}/state.md`의 `loop_history`에 기록
2. `iteration` +1, `current_step`을 `reviewed`로 업데이트
3. 사용자에게 "[발화 ID]에서 심각 gap이 발견되어 설계를 보완합니다. [이유]" 안내
4. `{project_path}/artifact.md`에서 해당 항목 수정 후 gap-analyzer 재실행

## 대화 진행 원칙

- 충분성 판단 루프에서 한 번에 한 질문만 한다
- 각 단계 시작 시 "[단계명]을 시작합니다. [목적 한 줄]"로 맥락을 환기한다
- 사용자가 중간에 다른 질문을 하더라도 현재 단계 완료 후 돌아온다

## 참조 파일

| 파일 | 내용 |
|---|---|
| `references/workflow-steps.md` | 충분성 판단 기준·예시, drafter 연동 절차, gap·확정 상세 절차 |
| `references/walkthrough-guide.md` | Walk-through 진행 방식 |
| `references/gap-criteria.md` | gap ①③ 기준 및 심각도 분류 |
| `references/artifact-schema.md` | 산출물 표 형식 및 단계별 컬럼 구조 |
| `references/state-schema.md` | state.md 파일 형식 정의 |
