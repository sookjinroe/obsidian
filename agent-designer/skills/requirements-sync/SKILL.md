---
name: Agent Designer — Requirements Sync
description: This skill should be used when the user says "요구사항이 바뀌었어", "이 부분 수정해야 해", "구현하다 보니 달라졌어", "요구사항 추가됐어", "설계 변경이 필요해", "plugin-dev가 [구성요소] 구현 완료했어", "이 구성요소 다 만들었어", "[구성요소명] 완성됐어", "plugin-dev Phase 5 완료됐어", "Skill 구현 다 끝났어", "skill-creator 연결해줘". This skill tracks requirement changes, validates post-implementation gaps (criteria ②④⑤), and prepares skill-creator eval sets after plugin-dev Phase 5 completion.
version: 0.1.0
allowed-tools: Read, Write
---

# Agent Designer — Requirements Sync

설계 확정(finalized) 이후 플러그인 빌드 과정에서 발생하는 모든 요구사항 변경과 구현 완료를 추적하고, 설계 산출물을 살아있는 문서로 유지한다.

## 프로젝트 경로 결정

실행 시작 시 현재 작업 중인 프로젝트를 결정한다:

1. 사용자 메시지에 프로젝트명이 명시된 경우 → 해당 프로젝트 사용
2. `.claude/agent-designer/`를 스캔해 `current_step: finalized` 또는 구현 중인 프로젝트가 1개면 자동 선택
3. 여러 프로젝트가 있으면 목록을 제시하고 선택 요청

이후 모든 파일 경로는 `{project_path} = .claude/agent-designer/{project-name}` 기준으로 한다.

## 이 스킬이 다루는 세 가지 트리거

### 트리거 A — 요구사항 변경

사용자 또는 plugin-dev가 기존 요구사항의 변경, 추가, 제거를 알릴 때.

### 트리거 B — 구성요소 구현 완료

plugin-dev가 특정 구성요소의 구현을 완료했음을 알릴 때. 이 시점에 해당 구성요소에 대해 ②④⑤ 기준 사후 검증을 실행한다.

### 트리거 C — plugin-dev Phase 5 완료 → skill-creator 연결

plugin-dev의 Phase 5(모든 SKILL.md 작성 완료)가 끝났을 때. artifact.md의 발화 목록을 skill-creator의 eval 발화 세트 형식으로 변환해 제공한다.

---

## 트리거 A: 요구사항 변경 처리

### 진행 순서

1. **변경 내용 확인**
   - 무엇이 바뀌었는가? (요구사항 ID 또는 설명)
   - 왜 바뀌었는가? (기술적 제약, 비즈니스 변경 등)
   - 어느 구성요소에 영향을 주는가?

2. **영향 범위 역추적**
   - `{project_path}/artifact.md`를 읽는다
   - 변경된 요구사항 ID를 기준으로 연결된 발화, 구성요소를 역추적한다
   - 영향받는 항목 목록을 사용자에게 제시한다

3. **이미 구현된 구성요소 확인**
   - `{project_path}/state.md`의 `implementation_status`를 읽는다
   - 영향받는 구성요소 중 `implemented` 상태인 것이 있으면 별도 표시 → plugin-dev에 수정 요청 필요

4. **산출물 업데이트**
   - `{project_path}/artifact.md`에 변경 사항 반영
   - 변경 이력 섹션에 기록:
     ```markdown
     ## 변경 이력
     | 날짜 | 변경 항목 | 변경 전 | 변경 후 | 이유 |
     ```

5. **재검증 범위 판단 및 재진입 준비**
   - 변경이 Walk-through 결과를 무효화하는가?

   **핵심 처리 경로에 영향 → design-session 재진입 준비:**
   1. 변경 내용을 `artifact.md`에 반영한다
   2. `state.md`의 `current_step`을 `reviewed`(구성요소 추가/제거 없는 경우) 또는 `drafting`(구성요소 구조 변경)으로 설정한다
   3. 사용자에게 안내: "핵심 경로가 변경됐습니다. '설계 이어서 해줘'로 [plugin-dev 핸드오프/gap 검증]부터 재검증을 시작하세요."
   - design-session은 state.md를 읽어 설정된 단계(drafting 또는 reviewed)부터 자동으로 재개한다

   **구현 완료된 구성요소에만 영향 → ②④⑤ 즉시 재검증 실행 (트리거 B 흐름으로 처리)**

   **경미한 변경 → Could 항목으로 artifact.md에 기록**

---

## 트리거 B: 구현 완료 후 사후 검증 (②④⑤)

### 진행 순서

1. **완료된 구성요소 확인**
   - 어떤 구성요소가 완료됐는가?
   - plugin-dev로부터 구성요소의 명세(입출력 형식, allowed-tools, 실패 처리)를 받는다

2. **② 입출력 불일치 검증**
   - 이 구성요소의 출력 형식이 다음 구성요소의 입력 형식과 맞는가?
   - artifact.md에서 이 구성요소 이후에 연결된 구성요소를 찾아 비교한다

3. **④ 도구 미연결 검증**
   - 이 Skill의 allowed-tools에 실제로 필요한 MCP 서버가 모두 있는가?
   - artifact.md의 계위 정보와 plugin-dev의 구현 명세를 비교한다

4. **⑤ 실패 경로 미처리 검증**
   - 이 구성요소가 실패할 때(API 오류, 데이터 없음, 타임아웃 등) 에이전트의 행동이 정의되어 있는가?
   - 정의되지 않은 실패 경로는 gap으로 기록한다

5. **결과 기록**
   - gap 발견 시: `{project_path}/artifact.md`에 기록 + `{project_path}/state.md`의 `post_impl_gaps` 업데이트
   - gap 없음: `{project_path}/state.md`의 `implementation_status`에서 해당 구성요소를 `implemented`로 업데이트

6. **심각 gap 처리**
   - ②④⑤에서 심각 gap이 발견되면: plugin-dev에 수정 요청 안내
   - 심각도 중간: 해당 구성요소 수정 권고
   - 심각도 경미: Could 항목으로 기록

### 심각도 기준 (②④⑤)

| 기준 | 심각 | 중간 | 경미 |
|---|---|---|---|
| ② 입출력 불일치 | 다음 단계가 이 출력을 처리할 수 없어 전체 흐름 중단 | 처리되나 변환 필요, 오류 가능성 있음 | 형식 차이가 있으나 자동 조정 가능 |
| ④ 도구 미연결 | 핵심 기능에 필요한 MCP가 없어 작동 불가 | 보조 기능에 필요한 도구 누락 | 선택적 도구 누락 |
| ⑤ 실패 경로 미처리 | 실패 시 에이전트가 멈추거나 잘못된 응답 반환 | 실패 시 사용자에게 모호한 응답 | 경미한 실패 케이스 미처리 |

---

## 트리거 C: plugin-dev Phase 5 완료 → skill-creator eval 세트 준비

plugin-dev가 Phase 5(Skill 구현 완료)를 마쳤을 때 발화 목록을 skill-creator가 바로 사용할 수 있는 eval 형식으로 변환한다.

### 진행 순서

1. **artifact.md에서 발화-구성요소 연결 추출**
   - 각 Skill/Command 구성요소에 연결된 발화 목록을 추출한다

2. **eval 발화 세트 분류**

   | 발화 유형 | should_trigger | 이유 |
   |---|---|---|
   | 일상 업무 발화 | true | 핵심 경로, 반드시 발동되어야 함 |
   | 임기응변·모호한 발화 | true | 명시적 요청 없어도 맥락이면 발동 |
   | 치명적 경로 발화 | true | Hook 포함 전체 경로 발동 필요 |
   | 범위 밖 발화 (artifact에 없는 것) | false | 이 Skill이 발동되면 안 되는 경우 |

3. **eval 파일 생성**
   `{project_path}/eval-set.md`에 각 Skill별 eval 발화 목록을 작성한다:

   ```markdown
   ## Skill: sql-translator — Eval 발화 세트

   ### should_trigger: true
   - "3월 사업부별 매출 보여줘" (일상 업무)
   - "이번 달 팀별 인건비 대비 매출" (일상 업무)
   - "지난번 그거 다시 뽑아줘" (임기응변)

   ### should_trigger: false
   - "이 쿼리 삭제해줘" (DML — 범위 밖)
   - "채용 공고 올려줘" (다른 도메인)
   ```

4. **skill-creator 안내**
   "eval-set.md가 생성됐습니다. skill-creator에게 이 파일을 전달해 각 Skill의 트리거 품질을 검증하세요. undertrigger 발생 시 description에 구체적 발화 예시와 '명시적 요청 없어도 맥락이면 사용한다'는 표현을 추가하면 발동 정확도가 높아집니다."

---

## 상태 파일 업데이트

변경 처리 완료 후 `state.md`를 업데이트한다:

```yaml
implementation_status:
  sql-translator: implemented   # 완료 처리
  db-connector: pending

post_impl_gaps:
  critical: 0
  moderate: 1
  minor: 2
```
