## 플러그인 메타데이터

**`.claude-plugin/plugin.json`**

- 이름: `skill-creator`
- 설명: Skill 생성, 개선, 성능 측정. 처음부터 만들기/기존 업데이트/eval 실행/벤치마크 모두 포함.
- 저자: Anthropic

---

## 핵심 Skill: `skills/skill-creator/SKILL.md`

### 역할

Skill을 만들고 반복 개선하는 전체 프로세스를 안내하는 Skill. Claude.ai, Claude Code, Cowork 모두 지원하며 각 환경별 동작 방식 차이 명시.

### description의 undertrigger 방지 원칙 (원문 확인됨)

```
Note: currently Claude has a tendency to "undertrigger" skills.
To combat this, make descriptions a little bit "pushy":

대신: "How to build a dashboard"
써야 함: "How to build a dashboard. Make sure to use this skill 
whenever the user mentions dashboards, data visualization, 
or wants to display any kind of company data, even if they 
don't explicitly ask for a 'dashboard.'"
```

### 전체 워크플로우 (6단계)

**Stage 1: Intent Capture**

```
기존 대화에서 캡처하려는 workflow 추출 시도:
- 사용된 도구, 단계 순서, 수정 내용, 입출력 형식

4가지 질문:
1. 무엇을 할 수 있게 하는 Skill인가?
2. 언제 트리거되어야 하는가?
3. 예상 출력 형식은?
4. 테스트 케이스가 필요한가?

"Skill with objectively verifiable outputs → 테스트 필요"
"Skill with subjective outputs (writing style) → 테스트 선택적"
```

**Stage 2: Interview & Research**

```
엣지케이스, 입출력 형식, 예시 파일, 성공 기준, 의존성 질문.
테스트 프롬프트는 이 단계 완료 전에 작성하지 않음.
가능하면 MCP로 관련 문서 사전 조사.
```

**Stage 3: Write SKILL.md**

```
description 작성 핵심 원칙:
1. "무엇을 하는가" + "언제 사용하는가" 모두 포함
2. description에만 트리거 조건 넣음 (body는 트리거 후에만 로드)
3. "pushy" 스타일로 undertrigger 방지

Skill 구조:
skill-name/
├── SKILL.md (frontmatter: name, description)
└── Bundled Resources
    ├── scripts/   반복 실행 코드
    ├── references/ 컨텍스트 문서
    └── assets/    출력에 사용하는 파일

Progressive Disclosure:
Level 1: name + description (항상)
Level 2: SKILL.md body (트리거 시)
Level 3: bundled resources (필요할 때)
```

**Stage 4: Test Cases & Parallel Execution**

```
2-3개 실제 테스트 프롬프트 생성 후 사용자 확인.
evals/evals.json에 저장 (assertions 없이):
{
  "evals": [{"id": 1, "prompt": "...", "files": []}]
}

같은 턴에 모든 subagent 동시 실행:
- with-skill run: Skill 경로 + 태스크
- baseline run: Skill 없이 같은 태스크 (신규 Skill)
           또는 old_skill run (기존 Skill 개선)

실행 중 assertions 초안 작성:
- 객관적으로 검증 가능한 것만
- 결과 뷰어에서 바로 읽힐 수 있도록 서술적 이름
- 주관적 Skill (글쓰기 스타일)은 assertions 강제하지 않음

타이밍 데이터 즉시 캡처 (작업 완료 알림에서):
{"total_tokens": 84852, "duration_ms": 23332}
→ timing.json에 즉시 저장 (이후 복구 불가)
```

**Stage 5: Grade, Aggregate, View**

```
1. grader subagent 실행 (agents/grader.md 읽어서):
   - 각 assertion을 outputs/transcript에 대해 PASS/FAIL 판정
   - evidence 필수 인용
   - eval 자체 개선 제안 (flaky한 assertion 지적)
   - grading.json 저장 (text/passed/evidence 필드 정확히)

2. 집계 스크립트 실행:
   python -m scripts.aggregate_benchmark <workspace>/iteration-N --skill-name <name>
   → benchmark.json, benchmark.md 생성

3. Analyzer subagent (agents/analyzer.md):
   - non-discriminating assertions 탐지
   - high-variance evals 탐지
   - time/token tradeoff 분석

4. eval-viewer 실행:
   python <skill-creator-path>/eval-viewer/generate_review.py \
     <workspace>/iteration-N \
     --skill-name "my-skill" \
     --benchmark <workspace>/iteration-N/benchmark.json
   → 브라우저에서 결과 확인

CRITICAL: 뷰어 먼저 실행 → 사용자 리뷰 → 그 다음 Skill 수정
리뷰어 생성 전에 Claude가 직접 수정하면 안 됨.
```

**Stage 6: Improve & Iterate**

```
feedback.json 읽기 (빈 피드백 = "좋음"):
{
  "reviews": [
    {"run_id": "eval-0-with_skill", "feedback": "차트에 축 레이블 없음"}
  ]
}

개선 원칙:
1. 일반화: 3개 예시에만 맞는 좁은 수정 금지
2. 린 프롬프트: 비생산적 단계 제거
3. WHY 설명: MUST/NEVER 대신 이유 설명
4. 반복 패턴 감지: 3개 테스트가 모두 같은 스크립트 작성 
   → scripts/에 번들링 권고

개선 후 iteration-N+1/ 디렉토리에 재실행.
--previous-workspace로 이전 결과와 비교.
통과 기준: 사용자 만족 OR 모든 피드백 빈값 OR 개선 없음.
```

---

## 에이전트 3개

### `agents/grader.md`

**역할:** 실행 트랜스크립트와 outputs/를 읽어 각 assertion의 PASS/FAIL을 판정.

**두 가지 임무:**

1. 결과 채점
2. eval 자체 비판 (trivially satisfied assertions 탐지)

**채점 기준 (엄격):**

```
PASS 조건: 증거가 명확하고 진짜 실질적 완료를 반영
FAIL 조건:
- 증거 없음
- 증거가 표면적 (파일명은 맞지만 내용 잘못됨)
- 우연히 assertion 충족

예시:
"output includes the name 'John Smith'" → 
  파일 있어도 이름이 다른 맥락이면 FAIL
```

**출력 형식 (정확한 필드명 필수):**

```json
{
  "expectations": [
    {
      "text": "...",     ← text 필드 (name이 아님!)
      "passed": true,    ← passed (met이 아님!)
      "evidence": "..."  ← evidence (details이 아님!)
    }
  ],
  "eval_feedback": {
    "suggestions": [...],
    "overall": "No suggestions, evals look solid"
  }
}
```

→ viewer.html이 정확히 이 필드명을 파싱. 다른 이름 쓰면 viewer에서 빈값.

---

### `agents/comparator.md`

**역할:** 어떤 Skill이 어떤 output을 만들었는지 모르는 상태로 A/B를 비교하는 Blind Comparator.

**평가 루브릭:**

```
Content (correctness/completeness/accuracy) 1-5
Structure (organization/formatting/usability) 1-5
→ overall_score (1-10)
```

**주요 원칙:**

- 어떤 Skill이 어느 output인지 추론 시도 금지
- 순수 output 품질로만 판단
- TIE는 드물어야 함 → 결단력 있게 선택
- assertion 통과율은 보조 증거 (주요 결정 요인 아님)

---

### `agents/analyzer.md`

**두 가지 역할:**

**1. Post-hoc Analyzer (Blind Comparison 후):**

```
입력: winner/loser Skill + 각 transcript + comparator 결과
→ 왜 winner가 이겼는지 분석
→ loser Skill 개선 제안 (priority: high/medium/low)
→ instruction following 점수 (1-10)
```

**2. Benchmark Analyzer:**

```
benchmark.json 읽기
→ aggregate stats가 숨기는 패턴 발견:
  - 항상 PASS/FAIL하는 assertions (non-discriminating)
  - 높은 분산 (flaky eval 후보)
  - time/token tradeoff
  - Skill 없이도 항상 실패하는 evals
→ 문자열 배열로 저장 (benchmark.json의 notes 필드)
```

---

## eval-viewer 시스템

### `eval-viewer/generate_review.py`

**역할:** 워크스페이스를 스캔하여 인터랙티브 HTML 리뷰어를 생성하고 HTTP 서버로 서빙.

**동작:**

```
입력: workspace 경로
→ outputs/ 있는 모든 하위 디렉토리 재귀 탐색
→ eval_metadata.json → prompt 추출
→ outputs/ 파일 임베딩 (text/image/pdf/xlsx/binary)
→ grading.json 로드
→ 이전 iteration 비교 (--previous-workspace)
→ benchmark.json 로드 (--benchmark)
→ HTML 생성 (EMBEDDED_DATA에 모두 인코딩)
→ HTTP 서버 시작 (기본 포트 3117)
→ 브라우저 자동 오픈

--static 플래그: 서버 없이 standalone HTML 파일 생성
(Cowork 등 브라우저 없는 환경용)
```

**피드백 저장:**

```
POST /api/feedback → workspace/feedback.json
자동 저장 (800ms 디바운스)
"Submit All Reviews" → status: "complete"로 저장
서버 불가 시 → feedback.json 파일 다운로드
```

### `eval-viewer/viewer.html`

**인터페이스:**

```
두 탭:
1. Outputs 탭:
   - Prompt 섹션
   - Output 파일 인라인 렌더링 (text/image/PDF/xlsx/binary)
   - Previous Output (접힘, 이전 iteration 비교용)
   - Formal Grades (접힘, assertion PASS/FAIL)
   - Feedback 텍스트박스
   - Previous Feedback (이전 iteration 피드백)

2. Benchmark 탭:
   - 전체 요약 테이블 (Pass Rate/Time/Tokens, delta)
   - 에이전트별 breakdown
   - assertion별 체크마크/X 매트릭스
   - Notes

네비게이션: ←/→ 버튼 또는 키보드 화살표키
Visit 추적: 모든 케이스 방문 시 "Submit" 버튼 강조
```

---

## 스크립트 시스템

### `scripts/run_loop.py`

**역할:** eval + improve를 자동으로 반복하는 description 최적화 루프.

**핵심 알고리즘:**

```
eval_set → train (60%) + test (40%) 분리 (stratified by should_trigger)

for iteration in 1..max_iterations:
  1. train + test 전체를 병렬로 동시 평가 (한 번의 run_eval 호출)
  2. 결과를 train/test로 분리
  3. live_report_path에 중간 결과 기록 (auto-refresh HTML)
  4. train 실패 없으면 조기 종료
  5. blinded history (test 점수 숨김) 기반으로 description 개선
  6. 새 description으로 반복

best 선택: test score 기준 (overfitting 방지)
출력: best_description, history, 점수들
```

### `scripts/run_eval.py`

**역할:** 특정 description으로 모든 eval 쿼리를 병렬 실행하여 트리거 여부 측정.

**핵심 메커니즘:**

```python
# 임시 커맨드 파일 생성 (available_skills에 나타나도록)
.claude/commands/<skill-name>-<uuid>.md

# claude -p로 쿼리 실행 (--include-partial-messages 옵션)
# Stream 이벤트 실시간 파싱:
content_block_start → tool_use → name이 "Skill" or "Read"
content_block_delta → partial_json 누적 → skill 이름 포함?
message_stop → 결과 확정

# 테스트 후 임시 파일 삭제
```

**트리거 판정:**

```
runs_per_query번 실행 → trigger_rate 계산
should_trigger=True: trigger_rate >= threshold → PASS
should_trigger=False: trigger_rate < threshold → PASS
```

### `scripts/improve_description.py`

**역할:** 실패 케이스 기반으로 Claude (extended thinking)를 호출하여 새 description 생성.

**프롬프트 구조:**

```
1. 현재 description
2. 실패한 should-trigger 쿼리 목록
3. false trigger 쿼리 목록
4. 이전 시도 history (test 점수 숨김)
5. Skill 본문 내용 (컨텍스트)

개선 원칙:
- 특정 쿼리 나열 방지 (overfitting)
- 100-200자 제한
- 사용자 의도 중심 서술
- 창의적 시도 격려 (다음 iteration에서 검증)
```

**1024자 초과 시 자동 축약** (추가 API 호출).

### `scripts/aggregate_benchmark.py`

**역할:** grading.json 파일들을 읽어 benchmark.json + benchmark.md 생성.

**지원 디렉토리 레이아웃:**

```
레이아웃 A (workspace):
benchmark_dir/eval-N/with_skill/run-1/grading.json

레이아웃 B (legacy):
benchmark_dir/runs/eval-N/with_skill/run-1/grading.json
```

**통계 계산:**

```python
mean = sum(values) / n
stddev = sqrt(sum((x-mean)^2 for x in values) / (n-1))
delta = primary.mean - baseline.mean  # "+0.50" 형식
```

**출력 benchmark.json의 중요 필드:**

```json
{
  "runs": [{"configuration": "with_skill" or "without_skill", ...}],
  "run_summary": {
    "with_skill": {"pass_rate": {"mean": 0.85, "stddev": 0.05}},
    "without_skill": {...},
    "delta": {"pass_rate": "+0.50"}
  }
}
```

→ `configuration` 필드가 정확히 "with_skill"/"without_skill"이어야 viewer가 색상 분류.

### `scripts/generate_report.py`

**역할:** run_loop.py 출력을 HTML 트리거 최적화 리포트로 변환.

**출력:**

```
Iter | Train | Test | Description | [쿼리 컬럼들...]
1    | 6/10  | 4/6  | "Use when..." | ✓ ✓ ✗ ✓...
2    | 8/10  | 5/6  | "This skill..." | ✓ ✓ ✓ ✓...
```

- Should-trigger 열: 하단에 초록 밑줄
- Should-not-trigger 열: 빨간 밑줄
- Test 열: 파란색 배경
- Best iteration: 연초록 행 배경
- Auto-refresh (루프 진행 중)

### `scripts/package_skill.py`

**역할:** Skill 폴더를 `.skill` 파일(zip 형식)으로 패키징.

**동작:**

```
1. quick_validate.py로 검증 먼저
   - SKILL.md frontmatter 유효성
   - name: kebab-case, 64자 이하
   - description: 1024자 이하, 각괄호 없음
   - 허용 필드만: name/description/license/allowed-tools/metadata/compatibility

2. 검증 통과 시 ZIP 생성:
   - EXCLUDE_DIRS: __pycache__, node_modules
   - EXCLUDE_GLOBS: *.pyc
   - ROOT_EXCLUDE_DIRS: evals (루트에서만)

3. skill-name.skill 파일 출력
```

### `scripts/quick_validate.py`

**역할:** Skill 구조 빠른 검증.

**허용 frontmatter 필드:**

```
{name, description, license, allowed-tools, metadata, compatibility}
이 외의 필드는 오류 발생
```

### `assets/eval_review.html`

**역할:** Description 최적화 전에 eval 쿼리를 사용자가 검토/편집하는 인터랙티브 HTML.

**기능:**

```
테이블: Query | Should Trigger (토글) | Delete
+ Add Query 버튼
Export Eval Set → eval_set.json 다운로드
```

---

## 환경별 동작 차이 (원문 명시)

|기능|Claude.ai|Claude Code|Cowork|
|---|---|---|---|
|Subagents|없음|있음|있음|
|Baseline run|건너뜀|실행|실행|
|Browser viewer|--static으로 파일 출력|서버 실행|--static으로 파일 출력|
|Quantitative benchmark|건너뜀|실행|실행|
|Description optimization|건너뜀 (claude -p 필요)|실행|실행|
|Packaging|실행|실행|실행|

Cowork 특이사항:

- 뷰어 생성 전에 Claude가 직접 수정하는 경향 → SKILL.md에 대문자로 경고 명시
- "GENERATE THE EVAL VIEWER _BEFORE_ evaluating inputs yourself"

---

## 두 플러그인 핵심 설계 패턴 정리

**plugin-dev의 패턴 (Tom Ashworth 분석과 일치):**

```
1. Multi-phase with user checkpoints
   각 Phase 완료 후 사용자 확인 대기
   "CRITICAL: DO NOT SKIP" 명시

2. Dynamic Skill loading
   커맨드가 필요한 Skill을 on-demand 로드
   Phase 2: plugin-structure Skill 로드
   Phase 5: 구성요소 유형별 해당 Skill 로드

3. Specialized agents
   agent-creator → 에이전트 파일 생성
   plugin-validator → 전체 플러그인 검증
   skill-reviewer → Skill 품질 검토
```

**skill-creator의 패턴:**

```
1. 사람 in the loop
   Claude가 직접 판단하기 전에 항상 사람이 먼저 결과 확인

2. Train/test split
   overfitting 방지 (test score로 best 선택)

3. Parallel subagents
   with-skill + baseline을 같은 턴에 동시 실행

4. Timing data 즉시 캡처
   notification에서 total_tokens/duration_ms 바로 저장

5. 엄격한 필드명 규약
   grading.json: text/passed/evidence (다른 이름 금지)
   benchmark.json: configuration="with_skill" (정확한 값)
```
