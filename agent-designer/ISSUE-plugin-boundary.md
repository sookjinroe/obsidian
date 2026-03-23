```markdown
# 설계 구조 재정의

작성일: 2026-03-23
상태: 미착수

---

## 배경

기존 이슈 문서(ISSUE-plugin-boundary.md)의 해결 방향을 재검토한 결과,
gap-analyzer의 입력을 플러그인 디렉토리 스캔으로 바꾸는 접근이 잘못된 방향임을 확인.
근본 원인과 해결 방법을 다시 정의한다.

---

## 근본 원인

**design-drafter가 계위·구성요소를 생성한다.**

계위 판단은 plugin-dev의 역할이다. design-drafter가 이를 생성하면:
- 그것이 제안이더라도 기준점이 되어 plugin-dev의 독립적 판단을 방해한다
- artifact에 잘못된 정보가 섞여 gap 검증의 기준 자체가 오염된다
- v1~v5 파일 분리는 이 구조의 파생 문제다

---

## 확정된 구조

### 역할 경계

| 담당 | 작업 | 산출물 |
|---|---|---|
| design-session | 맥락 수집, design-drafter 호출, 검토, 커버리지 보완, gap 검증 | persona.md, artifact.md |
| design-drafter | 발화 + 요구사항 생성 | artifact.md (발화~요구사항 컬럼) |
| plugin-dev:plugin-structure | 구성요소·계위·소속플러그인 결정 | artifact.md (구성요소 컬럼 추가) |
| gap-analyzer | artifact.md 기반 ①③ 검증 | gap-report.md |

### 산출물: artifact.md 단일 파일

파일을 단계마다 나누지 않는다. 하나의 파일에 컬럼이 단계마다 추가된다.

```
[design-drafter 완료 후]
발화ID | 유형 | 발화내용 | 요구사항ID | 요구사항

[plugin-dev 완료 후]
발화ID | 유형 | 발화내용 | 요구사항ID | 요구사항 | 구성요소명 | 계위 | 소속플러그인

[gap 검증 완료 후]
발화ID | 유형 | 발화내용 | 요구사항ID | 요구사항 | 구성요소명 | 계위 | 소속플러그인 | gap설명 | 심각도
```

전체 파일: `persona.md`, `artifact.md`, `gap-report.md`

### 전체 흐름

```
[1] design-session — 맥락 수집
    사용 주체 / 핵심 목적 / 제약 파악
    충분하면 design-drafter 호출

[2] design-drafter — artifact 초안 생성
    산출물: persona.md
            artifact.md (발화ID | 유형 | 발화내용 | 요구사항ID | 요구사항)
    ※ 계위·구성요소 없음

[3] 사용자 검토
    발화/요구사항 확인 및 ⚠️ 항목 보완

[4] 핸드오프 → plugin-dev:plugin-structure
    입력: artifact.md
    작업: 요구사항 → 구성요소명·계위·소속플러그인 결정
    산출물: artifact.md 컬럼 추가

[5] design-session 재개 — 커버리지 보완
    재개 시 artifact.md를 읽어 구성요소명 컬럼 유무 확인
    컬럼 없음 → "plugin-dev:plugin-structure가 완료되지 않았습니다. 완료 후 이어서 해주세요" 안내 후 중단
    컬럼 있음 → state.md current_step을 component_added로 업데이트
    커버리지 보완: 구성요소 중 발동 발화 없는 것 확인, 필요시 발화 행 추가 → artifact.md 업데이트

[6] gap-analyzer
    입력: artifact.md
    검증: ①연결없음, ③트리거미정의
    산출물: gap-report.md

[7] 분기
    심각 gap → [3]으로 루프백 (어느 발화, 어느 요구사항 명시)
    심각 gap 없음 → [8]

[8] 확정
    artifact.md에 gap 컬럼 추가
    state.md current_step → finalized
    "plugin-dev:create-plugin으로 구현 시작 가능"
```

---

## 변경 파일 목록

### 1. `agents/design-drafter.md`
- Step 4 (구성요소·계위 결정) 제거
- Step 5 (커버리지 확인) 제거
- Step 3 산출물 표: `구성요소명`, `계위` 컬럼 제거
- Output 요약: "구성요소 N개" → "요구사항 N개"로 변경
- 산출물 파일명: artifact-v2.md → artifact.md

### 2. `skills/design-session/references/artifact-schema.md`
- 버전별 파일 구조 → 단일 파일 컬럼 구조로 전면 재작성
- v1~v5, final 개념 제거
- 컬럼 추가 시점: drafter 완료 / plugin-dev 완료 / gap 검증 완료로 정의

### 3. `skills/design-session/references/state-schema.md`
- `artifacts` 필드: v1~v5, final 경로 목록 → `artifact: artifact.md` 단일 경로로 변경
- `current_step` 전이 재정의:
  - 기존: `drafting → F → G → finalized`
  - 변경: `drafting → reviewed → component_added → gap_check → finalized`
- `plugin_dir` 필드 제거 (gap-analyzer가 디렉토리 스캔하지 않으므로)

### 4. `skills/design-session/SKILL.md`
- 섹션 구조 변경:
  - 제거: `gap 검증 (작업 F)`, `구성 확정 (작업 G)`, `루프백 처리`
  - 추가: `plugin-dev 핸드오프`, `재개 후 커버리지 보완`, `gap 검증`, `확정`
- 사용자 게이트 3곳 추가:
  - drafter 완료 후 → "검토해주세요"
  - plugin-dev 핸드오프 → "plugin-dev:plugin-structure를 실행하세요"
  - 재개 시 → artifact.md 구성요소명 컬럼 유무 감지 → 미완료 시 재안내, 완료 시 state component_added 전환 후 커버리지 보완 진행
- 산출물 저장 정책: v* 파일명 → artifact.md로 통일
- v5 생성 주체 명시: design-session이 gap-report + artifact 머지 후 gap 컬럼 추가

### 5. `agents/gap-analyzer.md`
- 입력: artifact-v4.md + plugin_dir 스캔 → artifact.md 단일 파일로 변경
- 구성요소 목록 추출: 파일 스캔 제거, artifact.md의 구성요소명 컬럼에서 직접 읽기
- 에스컬레이션 로직 제거 (plugin_dir 불필요)
- 산출물: gap-report.md (변경 없음)

### 6. `skills/design-session/references/workflow-steps.md`
- 전체 흐름을 새 구조(위 흐름 [1]~[8])로 재작성
- plugin-dev 핸드오프 절차 섹션 추가
- drafter 산출물: v1+v2 → artifact.md로 변경

### 7. `skills/design-session/references/walkthrough-guide.md`
- 설계 전 Walk-through 유지
- 설계 후 Walk-through: artifact.md 기반으로 수정

### 8. `skills/design-session/references/gap-criteria.md`
- 검증 시점: "plugin-dev Phase 4 완료 후" → "plugin-dev:plugin-structure 완료 후"로 수정
- 내용 유지

### 9. `skills/state-reconcile/SKILL.md`
- 실제 상태 재도출 기준: artifact 파일 존재 여부 → artifact.md 내 컬럼 채워진 정도로 변경
- 상태명 변경 반영

### 10. `hooks/hooks.json`
- SessionStart 프롬프트: 상태명 변경 반영

### 11. `skills/requirements-sync/SKILL.md`
- 구성요소 목록 소스: artifact-v3/v4 → artifact.md로 변경
- artifact-final.md 참조 → artifact.md로 변경

### 12. `README.md`
- 산출물 구조: v1~v5, final → artifact.md 단일 파일로 재작성
- 역할 경계 명시
- 핸드오프 절차 섹션 추가

---

## 작업 순서

1. `skills/design-session/references/artifact-schema.md`
2. `skills/design-session/references/state-schema.md`
3. `agents/design-drafter.md`
4. `skills/design-session/SKILL.md`
5. `agents/gap-analyzer.md`
6. `skills/design-session/references/workflow-steps.md`
7. `skills/design-session/references/walkthrough-guide.md`
8. `skills/design-session/references/gap-criteria.md`
9. `skills/state-reconcile/SKILL.md`
10. `hooks/hooks.json`
11. `skills/requirements-sync/SKILL.md`
12. `README.md`
```