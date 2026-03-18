# 에이전트 구성요소 레퍼런스

> 에이전트를 구성하는 요소들의 정의, 판단 기준, 구분 원칙. 설계 과정에서 "무엇을 어디에 넣을 것인가"를 결정할 때 참조. Claude Code 플러그인 구조(plugin.json, SKILL.md, commands/) 기반.

---

## 1. 구성요소 개요

|구성요소|한 줄 정의|핵심 질문|
|---|---|---|
|에이전트|목표를 자율적으로 달성하는 주체|스스로 계획하고 판단하는가?|
|플러그인|배포 가능한 능력 묶음|독립적으로 설치·배포할 수 있는 단위인가?|
|Command|Skill을 오케스트레이션하는 진입점|사용자 요청을 어떤 Skill로 연결하는가?|
|Skill|재사용 가능한 전문 기능 단위|독립 트리거 가능하거나 여러 Command에서 재사용되는가?|
|references/|Skill의 보조 참조 파일|Skill 본문에 항상 있을 필요 없는 상세 지식인가?|
|MCP 서버|외부 시스템 접근 도구|실행만 하면 되는가?|
|Hooks|이벤트 기반 자동 실행|항상, 판단 없이 실행되어야 하는가?|

---

## 2. 에이전트 (Agent)

### 정의

목표를 받아서 스스로 계획을 세우고, 도구를 실행하고, 결과를 보고 다음 행동을 수정하는 자율적 주체.

### 에이전트로 만드는 기준

```
① 목표 지향성
   단순 명령 실행이 아니라 "목표"를 받는다
   "매출 데이터 보여줘" (명령) vs
   "이번 달 실적 이상한 거 찾아서 원인까지 파악해줘" (목표)

② 멀티스텝 자율성
   여러 단계를 스스로 결정한다
   조회 → 이상치 감지 → 드릴다운 → 리포트
   각 단계를 사용자가 지시하지 않아도 된다

③ 자가 수정
   중간 결과를 보고 다음 행동을 바꾼다
   이상치가 없으면 드릴다운 생략
   데이터가 부족하면 추가 질의
```

### 에이전트 단위 기준

```
하나의 에이전트 = 하나의 페르소나 + 하나의 책임 영역

좋은 예
├── 경영정보 에이전트  (경영 데이터 분석 전담)
├── 계약 검토 에이전트 (법무 문서 검토 전담)
└── 코딩 에이전트      (코드 작성/리팩토링 전담)

나쁜 예
└── 뭐든지 에이전트   (경계 없음 → CF 설계 불가)
```

---

## 3. 플러그인 (Plugin)

### 정의

Commands, Skills, Hooks, MCP 서버를 하나의 배포 단위로 묶은 컨테이너. 에이전트에 설치해서 능력을 추가하는 단위.

### 플러그인으로 분리하는 기준 — 3가지 질문

```
Q1. 도메인 의존성
    이 능력이 특정 도메인 지식을 알아야만 실행 가능한가?

    Yes → 해당 도메인 플러그인에 포함
    No  → 범용 플러그인으로 분리 후보

Q2. 입출력 범용성
    입력이 특정 도메인 데이터에 고정되어 있는가?

    Yes → 해당 도메인 플러그인에 포함
    No  → 범용 플러그인으로 분리 후보

Q3. 단독 완결성
    분리했을 때 그 플러그인이 단독으로 의미 있는가?

    Yes → 분리
    No  → 합치기
```

### 플러그인 구조 (실제 파일 구조)

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json       # 메타데이터 (이름, 설명, 작성자)
├── commands/             # 사용자 진입점 — Skill을 로드하는 얇은 래퍼
│   ├── analyze.md
│   └── report.md
├── skills/               # 재사용 가능한 전문 기능
│   └── skill-name/
│       ├── SKILL.md
│       └── references/   # 필요 시 로드되는 상세 참조 파일
│           └── schema.md
├── hooks/                # 이벤트 자동 실행
│   └── hooks.json
└── .mcp.json             # 플러그인 레벨 MCP 서버 연결
```

---

## 4. Command

### 정의

사용자의 /슬래시 호출을 받아 어떤 Skill을 어떤 순서로 로드할지 지시하는 얇은 래퍼. Skill 로직을 직접 포함하지 않고, Skill을 오케스트레이션한다.

### Command의 역할

```
① 사용자 입력 수신
   사용자가 /dcf [company] 형태로 호출

② Skill 로드 순서 지시
   "Load the comps-analysis skill first,
    then load the dcf-model skill"

③ 여러 Skill 체이닝
   DCF 커맨드 → comps-analysis Skill 먼저 실행
             → 그 결과를 dcf-model Skill에 전달

④ 인자 처리
   argument-hint로 사용자에게 필요한 입력 안내
```

### Command vs Skill 구분

```
Command (얇은 래퍼)              Skill (실제 도메인 로직)
────────────────────────        ──────────────────────────
수십 줄 이하                     수백~수천 줄
"이 Skill을 써라" 지시            실제 판단 기준과 절차
Skill 오케스트레이션              독립적 실행 가능
사용자가 /slash로 직접 호출       Command 또는 에이전트가 로드
```

### Command 파일 예시

```markdown
---
description: Build a DCF valuation model
argument-hint: "[company name or ticker]"
---

Load the `comps-analysis` skill first to build trading comps.
Then load the `dcf-model` skill to construct the valuation.

If a company name is provided, use it. Otherwise ask the user.
```

---

## 5. Skill

### 정의

재사용 가능한 전문 기능 단위. LLM이 특정 작업을 어떻게 생각하고 행동해야 하는지를 가르치는 지침. 신입사원 온보딩 가이드처럼 작성한다.

### Skill로 만드는 기준 — 하나라도 해당하면 Skill

```
① 독립 트리거 가능성
   사용자가 이것만 단독으로 요청할 수 있는가?
   "DCF 모델 만들어줘"   → dcf-model Skill ✅
   "스키마 조회해줘"      → 단독 요청 없음 → references/schema.md ✗

② 재사용성
   여러 Command에서 공통으로 쓰이는가?
   comps-analysis → DCF 커맨드에서도, LBO 커맨드에서도 사용 ✅

③ 컨텍스트 효율
   항상 로드하기엔 너무 크고 복잡한가?
   엄격한 규칙, 복잡한 단계, 긴 예시 포함 → 필요할 때만 로드 ✅
```

### 셋 다 해당 없으면

```
Command 본문에 인라인으로 포함
또는 상위 Skill의 references/ 파일로
```

### Progressive Disclosure — 3단계 로딩

Skill은 컨텍스트를 아끼기 위해 3단계로 나눠 로드된다.

```
Level 1: frontmatter만 (항상 컨텍스트에, ~100 tokens)
         name + description
         에이전트가 "언제 이 Skill을 쓸지"만 앎

Level 2: SKILL.md 본문 (Skill 트리거 시 로드)
         핵심 지침, Rules, 주요 Examples
         500줄 이하 권장
         상세 내용은 references/로

Level 3: references/ 파일들 (Claude가 필요하다고 판단할 때만 로드)
         스키마, 공식 레퍼런스, 상세 사례 등
         SKILL.md에 "See references/schema.md for table details" 방식으로 참조
         이론상 무제한 컨텍스트 가능
```

### references/ 파일 사용 패턴

```
text-to-sql/
├── SKILL.md                  ← 핵심 SQL 변환 지침
└── references/
    ├── schema.md             ← "SQL 생성 시 이 파일 참조"
    └── date-parsing.md       ← "기간 표현 해석 시 이 파일 참조"

3-statement-model/
├── SKILL.md                  ← 핵심 모델링 지침
└── references/
    ├── formulas.md           ← 수식 참조 시
    ├── formatting.md         ← 포맷 기준 참조 시
    └── sec-filings.md        ← SEC 데이터 필요 시에만
```

**schema-lookup을 별도 Skill로 만들지 않는 이유:** 스키마 탐색은 text-to-sql 없이 단독으로 요청되지 않는다. 여러 Command에서 독립적으로 재사용되지 않는다. → text-to-sql/references/schema.md가 맞는 위치

### Skill 파일 구조 (SKILL.md)

```yaml
---
name: text-to-sql
description: >
  자연어 질문을 SQL 쿼리로 변환.
  사용자가 데이터 조회, 매출/비용/실적 관련 질문을 할 때 사용.
  "보여줘", "뽑아줘", "얼마야" 표현 시 발동.
allowed-tools: mcp__db-mcp__query
---

# Text-to-SQL

## Rules
- 생성하는 쿼리는 SELECT만. DML 절대 생성 금지
- 수치는 반드시 출처 테이블·컬럼과 함께 제시
- 실행 전 사용자에게 쿼리 확인

## 작업 절차
1. 스키마 확인 (references/schema.md 참조)
2. 질문 의도 해석
3. SQL 생성
4. 사용자 확인 후 실행

## Examples
[좋은 예]
입력: "3월 사업부별 매출 보여줘"
출력: SELECT dept, SUM(revenue) FROM sales WHERE month=3 GROUP BY dept

[나쁜 예]
입력: 동일
출력: SELECT * FROM sales  ← 집계 없음, 컬럼 불명확
```

---

## 6. MCP 서버

### 정의

외부 시스템에 실제로 접근하는 실행 도구. LLM의 판단 없이 입력을 받아 실행하고 결과를 반환.

### MCP 위치 전략

```
.mcp.json은 플러그인 루트에 하나만 존재
└── 플러그인 내 모든 Skill이 기본적으로 접근 가능

접근 제한이 필요하면 각 Skill의 allowed-tools로 스코프 제한
├── allowed-tools: mcp__db-mcp__query
│   → 이 Skill만 db-mcp 사용 가능
└── allowed-tools 없음
    → 플러그인의 모든 MCP 접근 가능 (기본값)
```

### 에이전트 레벨 vs 플러그인 레벨

```
에이전트 레벨 .mcp.json
└── 여러 플러그인이 공통으로 쓰는 범용 MCP
    예: 그룹웨어, 로깅

플러그인 레벨 .mcp.json (권장)
└── 해당 플러그인 전용 MCP
    예: 영업 DB, ERP (경영정보 플러그인만 접근)
    보안·격리 목적
    민감한 데이터일수록 플러그인 레벨로 좁힌다
```

### Skill vs MCP 구분 예시

```
Skill (판단)                    MCP (실행)
────────────────────────       ──────────────────────────
자연어 → SQL 변환               SELECT문 DB에 실행
이상치인지 판단                  ERP API 호출
어떤 테이블 쓸지 결정            파일 저장
결과 해석·요약                   메신저 메시지 전송
```

---

## 7. Hooks

### 정의

특정 이벤트 발생 시 LLM 판단 없이 자동으로 실행되는 코드. "항상 이래야 한다"는 규칙을 코드 레벨에서 강제. CF(Context Framework)는 확률적이므로 100% 보장이 필요한 것은 Hooks로.

### Hooks로 만드는 기준

```
LLM 판단에 맡기면 안 되는 것인가?
    → CF는 확률적. 100% 보장 필요한 것은 Hook

항상 실행되어야 하는 것인가?
    → 예외 없이 모든 경우에 적용

부작용 방어가 목적인가?
    → 잘못된 실행을 막는 안전장치
```

### 이벤트 유형

```
PreToolUse  ← 도구 실행 전
    → 허용/거부/수정 결정
    → 예: DML 차단, 위험 경로 접근 차단

PostToolUse ← 도구 실행 후
    → 결과 가공·로깅
    → 예: 출처 첨부, 결과 포맷 변환

UserPromptSubmit ← 사용자 입력 시
    → 입력 전처리
    → 예: 개인정보 마스킹

Stop ← 에이전트 종료 시
    → 정리 작업
    → 예: 세션 로그 저장
```

---

## 8. 판단 트리 — 작업 계위 결정

새로운 작업이 생겼을 때 이 순서로 결정.

```
새 작업 등장
      ↓
외부 시스템 접근이 필요한가?
      ├── Yes → MCP 서버
      └── No  ↓

항상·자동으로 실행되어야 하는가? (판단 불필요)
      ├── Yes → Hooks (PreToolUse / PostToolUse)
      └── No  ↓

목표를 받아 멀티스텝으로 자율 실행하는가?
      ├── Yes → 에이전트
      └── No  ↓

LLM 판단이 필요한가?
      ├── No  → 기본 도구 사용으로 해소
      └── Yes ↓

Skill 기준 중 하나라도 해당하는가?
(① 독립 트리거 ② 여러 Command에서 재사용 ③ 항상 로드하기엔 너무 큼)
      ├── Yes → Skill
      └── No  ↓

항상 특정 Skill과 함께 쓰이는 보조 지식인가?
      ├── Yes → 상위 Skill의 references/ 파일
      └── No  → Command 본문에 인라인 포함

사용자가 /슬래시로 직접 호출하는 진입점인가?
      └── Yes → Command (Skill을 오케스트레이션)

플러그인 분리 여부
      ↓
도메인 의존성 있는가? + 입출력 범용성 있는가? + 단독 완결성 있는가?
      ├── 도메인 종속 → 해당 도메인 플러그인
      └── 범용        → 별도 플러그인으로 분리
```

---

## 9. Skill 체이닝 vs 서브에이전트

실제 복잡한 워크플로우에서 "여러 작업 위임"은 대부분 Skill 체이닝으로 구현된다.

### Skill 체이닝 (일반적 패턴)

```
Command가 여러 Skill을 순서대로 로드
DCF 커맨드
  Step 1: comps-analysis Skill 로드 → 피어 데이터 수집
  Step 2: dcf-model Skill 로드 → comps 결과 활용해서 DCF 구성

특징
├── 같은 컨텍스트 안에서 실행
├── 앞 Skill의 결과를 다음 Skill이 이어받음
└── 대부분의 순차적 워크플로우에 적합
```

### 서브에이전트 (특수한 경우)

```
메인 에이전트가 독립 실행 환경에 작업 위임

실제로 필요한 경우
├── 격리된 실행 환경이 필요한 경우 (보안, 샌드박스)
├── 진짜 병렬 처리가 필요한 경우
└── 메인과 완전히 다른 도구 세트가 필요한 경우

설계 원칙
→ Skill 체이닝으로 해결 가능하면 서브에이전트 불필요
→ 서브에이전트는 과도하게 설계하지 않는다
```