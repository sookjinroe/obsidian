# 이슈: plugin-dev 역할 경계 재정의 및 핸드오프 구조 개선

작성일: 2026-03-20
상태: 미착수 (신규 브랜치에서 작업 예정)

---

## 배경

테스트 중 발견된 세 가지 코멘트의 공통 원인 분석:

- **코멘트 1**: 모든 작업이 사용자 허가 없이 자동 진행됨
- **코멘트 2**: design-drafter가 구성요소·계위를 결정함 → plugin-dev 전문 영역 침범
- **코멘트 3**: gap-analyzer 보고가 개발자 중심 언어 사용

코멘트 2가 설계 경계 문제의 근본 원인이며, 코멘트 1·3은 파생 증상이다.

---

## 확인된 구조적 문제

### 문제 1: design-drafter의 범위 초과

`agents/design-drafter.md`의 현재 실행 단계:

| 단계 | 산출물 | 적합 여부 |
|---|---|---|
| Step 1: 페르소나 생성 | persona.md | ✅ agent-designer 영역 |
| Step 2: 발화 목록 생성 | artifact-v1.md | ✅ agent-designer 영역 |
| Step 3: 요구사항 도출 | artifact-v2.md | ✅ agent-designer 영역 |
| Step 4: 구성요소·계위 결정 | artifact-v3.md | ❌ plugin-dev 영역 |
| Step 5: 커버리지 확인·발화 보완 | artifact-v4.md | ❌ plugin-dev 영역 |

### 문제 2: artifact-v2 내 구성요소 정보 혼입

`agents/design-drafter.md` Step 3의 산출물 표 형식(132번 줄)에 `구성요소명`, `계위` 컬럼이 포함되어 있음.

`skills/design-session/references/artifact-schema.md` 정의상 v2에는 구성요소 컬럼이 없어야 하나 실제 drafter 구현과 불일치.

- 현재 v2 표 형식: `| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 | 구성요소명 | 계위 |`
- 올바른 v2 표 형식: `| 발화 ID | 유형 | 발화 내용 | 요구사항 ID | 요구사항 내용 |`

### 문제 3: SKILL.md 파이프라인 구조 — 사용자 게이트 없음

`skills/design-session/SKILL.md`의 현재 흐름:

```
①②③ 충족        → [자동] drafter 호출
drafter 완료     → 확인 받음 → [자동] gap 검증 진행 (98번 줄)
gap 검증 완료    → [자동] artifact-v5 저장 (113번 줄)
심각 gap 없음    → [자동] 구성 확정 진행 (117번 줄)
"설계 이어서 해줘" → [자동] 현재 단계 재개
```

주요 전환마다 사용자 허가 없이 자동 진행됨.

---

## 재정의된 역할 경계

| 담당 | 작업 | 산출물 |
|---|---|---|
| agent-designer | 발화 수집, 요구사항 도출 | artifact-v1.md, artifact-v2.md |
| plugin-dev | 구성요소·계위 결정, 플러그인 구조 생성, 구현 | 플러그인 디렉토리 (agents/, skills/ 등) |
| agent-designer | gap 검증 (①③) — plugin-dev Phase 4 완료 후 | gap-report.md |
| agent-designer | 구현 후 검증 (②④⑤) | artifact-final.md 업데이트 |

---

## 핸드오프 구조 (확정)

```
[agent-designer — design-session]
  충분성 판단 → "초안 생성을 시작할까요?" → 사용자 허가 후 drafter 실행
  산출물: persona.md, artifact-v1.md, artifact-v2.md

  → 사용자 안내:
    "plugin-dev:create-plugin을 시작하세요.
     전달 파일: {project_path}/artifact-v2.md
     plugin-dev Phase 4 완료 후:
       1. 생성된 플러그인 디렉토리 경로를 확인하세요
       2. design-session을 다시 호출하며 경로를 알려주세요
          예: '설계 이어서 해줘 [project-name]. 플러그인 경로: ./my-plugin'"

[사용자가 plugin-dev:create-plugin 호출]
  Phase 2~3: artifact-v2.md 기반으로 구성요소·계위 결정
  Phase 4: 플러그인 디렉토리 생성 (agents/, skills/, hooks/ 등)

[사용자가 design-session 재호출 + 플러그인 경로 전달]
  gap-analyzer:
    - artifact-v2.md에서 요구사항 목록 읽음
    - {plugin_dir}/agents/, skills/, hooks/ 스캔하여 구성요소 목록 추출
    - 요구사항 내용 ↔ 구성요소 description 비교 → ①③ 검증
    - 경로가 유효하지 않거나 디렉토리 없음 → 사용자에게 에스컬레이션
  심각 gap 없음 → "plugin-dev Phase 5~8을 계속 진행하세요"
  심각 gap 있음 → 해당 발화와 요구사항 설명 후 plugin-dev 수정 요청 안내

[각 구성요소 구현 완료 시]
  requirements-sync 호출 → ②④⑤ 검증
```

---

## 플러그인 경로 관리

### state.md에 plugin_dir 필드 추가

```yaml
plugin_dir: "./my-plugin"  # plugin-dev Phase 4 완료 후 사용자가 전달
```

### 경로 미설정·유효하지 않을 때 에스컬레이션

```
"플러그인 디렉토리 경로를 확인할 수 없습니다.
 plugin-dev Phase 4에서 생성된 플러그인 폴더 경로를 알려주세요.
 예: '플러그인 경로는 ./redshift-analyzer입니다'"
```

### 경로 안내 위치

1. design-session SKILL.md — 핸드오프 섹션
2. README.md — 사용법 섹션

---

## 변경 파일 목록

### 1. `agents/design-drafter.md`

- Step 4 (artifact-v3) 제거
- Step 5 (artifact-v4) 제거
- Step 3 표 형식에서 `구성요소명`, `계위` 컬럼 제거
- Output 요약: "구성요소 N개" → "요구사항 N개"로 변경

### 2. `skills/design-session/SKILL.md`

사용자 게이트 추가 (3곳):

| 전환 시점 | 현재 | 변경 후 |
|---|---|---|
| ①②③ 충족 후 | 즉시 drafter 호출 | 컨텍스트 요약 후 "초안 생성을 시작할까요?" |
| drafter 완료 후 | 확인 시 즉시 gap 진행 | plugin-dev 핸드오프 안내 후 종료 |
| 재진입 시 | 현재 단계 즉시 재개 | 상태 요약 후 "어떻게 진행할까요?" |

섹션 구조 변경:
- 제거: `gap 검증 (작업 F)`, `구성 확정 (작업 G)`, `루프백 처리`
- 추가: `plugin-dev 핸드오프`, `plugin-dev 복귀 후 처리 (gap 검증 + 결과 보고)`
- 상태 전이: `drafting → handed_off → gap_check → finalized`

### 3. `agents/gap-analyzer.md`

- 입력 변경: artifact-v4.md → artifact-v2.md + `{plugin_dir}` 스캔
- 구성요소 목록 추출: agents/, skills/, hooks/ 디렉토리의 실제 파일에서 추출
- 에스컬레이션 로직 추가 (plugin_dir 미설정·유효하지 않을 때)

### 4. `skills/design-session/references/state-schema.md`

- `artifacts`에서 v3, v4, v5, final 제거
- `plugin_dir` 필드 추가
- `current_step` 전이: `drafting → F → G → finalized` → `drafting → handed_off → gap_check → finalized`
- `loop_history`, `post_impl_gaps` 유지

### 5. `skills/design-session/references/artifact-schema.md`

- v2 컬럼에서 구성요소명·계위·소속 플러그인 제거
- v3~final을 "plugin-dev 산출물"로 명시

### 6. `skills/design-session/references/workflow-steps.md`

- drafter 호출 결과: v1+v2만 생성으로 수정
- gap 검증, 구성 확정 섹션: plugin-dev 복귀 후 흐름으로 재작성
- plugin-dev 핸드오프 절차 섹션 추가

### 7. `skills/design-session/references/walkthrough-guide.md`

- `설계 후 Walk-through (작업 F)` 섹션 제거
- `설계 전 Walk-through (작업 C)` 유지

### 8. `skills/design-session/references/gap-criteria.md`

- 검증 시점을 "plugin-dev Phase 4 완료 후"로 명시 수정
- 내용 자체는 유지

### 9. `skills/state-reconcile/SKILL.md`

- 상태명 변경: F/G → handed_off/gap_check
- plugin_dir 정보 표시 추가

### 10. `hooks/hooks.json`

- SessionStart 프롬프트: 상태명 변경 반영

### 11. `skills/requirements-sync/SKILL.md`

- artifact-final.md 생성 시점: gap_check 완료 후로 재정의
- 구성요소 목록 소스: artifact-v3/v4 → plugin 디렉토리에서 추출로 변경

### 12. `README.md`

- 역할 경계 명시
- 핸드오프 절차 섹션 추가 (경로 전달 방법 포함)

---

## 기존 프로젝트 호환성

변경 후 state.md의 `current_step`이 F 또는 G인 기존 프로젝트가 있을 수 있음.
state-reconcile이 이를 감지해 사용자에게 안내하도록 처리 필요.

---

## 작업 순서

1. `agents/design-drafter.md`
2. `skills/design-session/references/artifact-schema.md`
3. `skills/design-session/references/state-schema.md`
4. `skills/design-session/SKILL.md`
5. `agents/gap-analyzer.md`
6. `skills/design-session/references/workflow-steps.md`
7. `skills/design-session/references/walkthrough-guide.md`
8. `skills/design-session/references/gap-criteria.md`
9. `skills/state-reconcile/SKILL.md`
10. `hooks/hooks.json`
11. `skills/requirements-sync/SKILL.md`
12. `README.md`
