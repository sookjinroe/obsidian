## 정리

```
① anthropics/skills/skill-creator
   → 순수한 Skill (SKILL.md 폴더)
   → Claude.ai, Claude Code, API 어디서나 쓸 수 있는 범용
   → 기능: 초안 작성 + eval 루프 + description 최적화

② anthropics/claude-plugins-official/skill-creator
   → 플러그인 (plugin.json 포함)
   → Claude Code 전용
   → 기능: ①과 동일 + eval 에이전트 4개 + 유틸 스크립트 추가
```

Skill Creator 플러그인은 Create, Eval, Improve, Benchmark 4가지 모드를 제공하며, Executor·Grader·Comparator·Analyzer 4개의 에이전트가 내부에서 동작합니다. [Claude](https://claude.com/plugins/skill-creator)

```
③ plugin-dev/skills/skill-development
   → plugin-dev 플러그인 안에 포함된 Skill 중 하나
   → 기능: Skill 작성 방법 안내 (progressive disclosure + 트리거)
   → eval 루프는 없음 — 작성 가이드에 특화
```

anthropics/skills
└── skill-creator/          ← Skill (범용, 가벼움)
    └── SKILL.md

anthropics/claude-plugins-official
├── skill-creator/          ← 플러그인 (Claude Code 전용, 강력)
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── skills/skill-creator/SKILL.md   (①과 내용 거의 동일)
│   ├── agents/             ← eval 에이전트 4개 추가
│   └── scripts/            ← 유틸 스크립트 추가
│
└── plugin-dev/             ← 별개 플러그인
    └── skills/
        ├── skill-development/   ← Skill 작성 가이드
        ├── hook-development/
        ├── mcp-integration/
        └── ...

Claude Code 환경에서 플러그인 빌드 시
└── skill-creator 플러그인 (claude-plugins-official)
    eval 에이전트 + 스크립트까지 있어서 가장 강력
    /plugin install skill-creator@claude-plugins-official
    + plugin-dev 플러그인 (claude-plugins-official)
    plugin-dev 안의 skill-development는
    skill-creator와 목적이 다름
    → 작성 가이드 / skill-creator는 작성+검증 루프


Anthropic 측에서도 `claude-plugins-official`, `claude-code/plugins/`, `anthropics/skills` 세 레포 간 역할 중복과 장기 전략에 대한 질문이 올라오는 상황입니다. [GitHub](https://github.com/anthropics/claude-plugins-official/issues/487) 현재는 claude-plugins-official이 가장 관리되고 있는 공식 마켓플레이스입니다.

---
# plugin-dev 플러그인 완전 분석

## 플러그인 메타데이터

**`.claude-plugin/plugin.json`**

- 이름: `plugin-dev`
- 설명: hooks, MCP, plugin structure, settings, commands, agents, skill development를 위한 7개 Skills + AI 보조 생성 툴킷
- 저자: Anthropic (Daisy Hollman)
- 라이선스: Apache 2.0

---

## 핵심 커맨드: `/plugin-dev:create-plugin`

**`commands/create-plugin.md`**

### 역할

사용자 입력을 받아 플러그인을 처음부터 끝까지 만드는 8단계 가이드 워크플로우. 에이전트가 아닌 **커맨드**이므로, 사용자가 `/plugin-dev:create-plugin`을 치면 이 파일의 내용이 Claude의 실행 지침이 된다.

### 허용 도구

`Read, Write, Grep, Glob, Bash, TodoWrite, AskUserQuestion, Skill, Task`

### 8단계 실제 동작

```
Phase 1: Discovery
  입력: $ARGUMENTS (선택적 설명)
  → 목적이 불분명하면 4가지 질문:
    "무슨 문제를 해결하는가?"
    "누가 언제 쓰는가?"
    "뭘 해야 하는가?"
    "참고할 유사 플러그인 있는가?"
  → 목적 명확화 후 사용자 확인
  산출물: 플러그인 목적 서술문

Phase 2: Component Planning
  → plugin-structure Skill을 Skill 도구로 로드
  → 필요한 구성요소 결정:
    Skills / Agents / Hooks / MCP / Settings
  → 표 형태로 사용자에게 제시:
    | Component | Count | Purpose |
  → 사용자 확인 후 진행
  산출물: 확정된 구성요소 목록

Phase 3: Detailed Design (CRITICAL: DO NOT SKIP)
  → 각 구성요소별 미결 사항 질문:
    Skills: 어떤 발화에서 트리거? 어떤 도구 필요?
    Agents: 능동적/반응적? 도구 목록?
    Hooks: 어떤 이벤트? Prompt vs Command?
    MCP: 서버 타입? 인증 방식?
  → 모든 질문 제시 후 사용자 답변 대기
  → "알아서 해줘"라고 하면 구체적 권고안 제시 후 재확인
  산출물: 각 구성요소 상세 명세

Phase 4: Structure Creation
  → 플러그인 이름 결정 (kebab-case)
  → 생성 위치 질문
  → Bash로 디렉토리 생성:
    mkdir -p plugin-name/.claude-plugin
    mkdir -p plugin-name/skills/<name>
    mkdir -p plugin-name/agents (필요시)
    mkdir -p plugin-name/hooks (필요시)
  → plugin.json, README.md 생성
  → .gitignore 생성 (필요시)
  산출물: 폴더 구조

Phase 5: Component Implementation
  → 구성요소 유형별로 해당 Skill 로드:
    Skills → skill-development Skill 로드
    Agents → agent-development Skill + agent-creator 에이전트 실행
    Hooks → hook-development Skill 로드
    MCP → mcp-integration Skill 로드
    Settings → plugin-settings Skill 로드
  → 각 구성요소 작성 후 skill-reviewer로 검토
  산출물: 작성된 SKILL.md, 에이전트 파일, hooks.json 등

Phase 6: Validation
  → plugin-validator 에이전트 실행
  → skill-reviewer 에이전트로 각 Skill 검토
  → validate-agent.sh 실행 (에이전트 파일 검증)
  → validate-hook-schema.sh 실행 (hooks.json 검증)
  → "N개 이슈 발견. 지금 수정할까요?" 질문
  산출물: 검증 리포트

Phase 7: Testing
  → cc --plugin-dir /path/to/plugin 로 테스트 방법 안내
  → 체크리스트 제공:
    [ ] Skills가 트리거 구문에 반응하는가?
    [ ] /help에 커맨드가 나타나는가?
    [ ] Hooks가 이벤트에 반응하는가?
    [ ] MCP 서버가 연결되는가?
  → "직접 테스트할까요, 제가 안내할까요?" 질문
  산출물: 테스트 가이드

Phase 8: Documentation
  → README 완전성 확인
  → 마켓플레이스 등록 방법 안내 (선택)
  → TodoWrite로 모든 항목 완료 처리
  → 생성된 구성요소 목록 요약
  산출물: 완성된 플러그인 + README
```

### 중요 설계 패턴

**Phase 3는 절대 스킵 불가.** 파일에 `CRITICAL: This is one of the most important phases. DO NOT SKIP.`이 명시되어 있음. 각 Key Decision Point에서 사용자 확인 없이 다음 단계로 진행하지 않도록 설계됨.

**commands/ vs skills/ 정책:** `commands/` 디렉토리는 레거시로 표시됨. 신규 플러그인에서는 slash command도 `skills/<name>/SKILL.md` 형태로 만들어야 함. 두 형태가 로딩 방식은 동일하고 파일 레이아웃만 다름.

**실제 사용 시나리오:**

```
사용자: /plugin-dev:create-plugin
Claude: 무슨 문제를 해결하는 플러그인인가요?
사용자: 우리 회사 코딩 표준 검사 플러그인
Claude: [Phase 1 요약] → [Phase 2 구성요소 표] → 확인?
사용자: 맞습니다
Claude: [Phase 3] Hooks는 어떤 이벤트에 연결할까요?
사용자: 코드 저장할 때마다
Claude: PreToolUse(Write) + Stop(테스트 검증) 추천합니다. 맞나요?
사용자: 네
Claude: [Phase 4~8 자동 실행]
```

---

## 에이전트 3개

### `agents/agent-creator.md`

**역할:** 사용자가 원하는 에이전트를 AI 보조 생성으로 만들어주는 전문 에이전트.

**트리거:** "create an agent", "build a new agent", "make me an agent that..."

**허용 도구:** `Write, Read`

**실제 동작:**

```
1. 사용자 요청 분석
   "코드 리뷰하는 에이전트 만들어줘"

2. 6개 설계 결정:
   - identifier 생성 (lowercase, hyphens, 3-50자)
   - description 작성 (Use this agent when... + <example> 블록 2-4개)
   - model 선택 (대부분 inherit)
   - color 결정 (도메인 기반)
   - tools 결정 (최소 권한 원칙)
   - system prompt 작성 (500-3000자, 역할/책임/프로세스/출력형식/엣지케이스)

3. agents/[identifier].md 파일 생성

4. 결과 요약:
   ## Agent Created: code-reviewer
   - Triggers: 코드 작성 후, 명시적 리뷰 요청 시
   - File: agents/code-reviewer.md
   - Test: "이 코드 리뷰해줘"

5. validate-agent.sh 실행 권고
```

**시스템 프롬프트의 핵심 원칙:**

- CLAUDE.md 컨텍스트 반영 (프로젝트별 코딩 표준 고려)
- 코드 리뷰 에이전트는 전체 코드베이스가 아닌 최근 변경 코드만 대상
- proactive triggering 예시 포함 (사용자가 명시 요청 안 해도 트리거)

---

### `agents/plugin-validator.md`

**역할:** 플러그인의 전체 구조와 각 구성요소를 포괄적으로 검증하는 에이전트.

**트리거:** "validate my plugin", "check plugin structure", 플러그인 생성/수정 후 자동 트리거.

**허용 도구:** `Read, Grep, Glob, Bash`

**검증 범위 (10개 항목):**

```
1. .claude-plugin/plugin.json 위치 및 JSON 문법
2. name 필드 (kebab-case, 공백 없음)
3. version (semver), description, author 형식
4. commands/*.md 파일 frontmatter 구조
5. agents/*.md 파일:
   - validate-agent.sh 실행
   - name 형식 (lowercase, hyphens, 3-50자)
   - description의 <example> 블록
   - model 유효성 (inherit/sonnet/opus/haiku)
   - color 유효성
   - system prompt 실질적 내용
6. skills/*/SKILL.md 파일:
   - name, description frontmatter
   - references/ 참조 파일 존재 여부
7. hooks/hooks.json:
   - validate-hook-schema.sh 실행
   - matcher 패턴
   - hook type (command/prompt)
8. .mcp.json 서버 설정
9. README.md, .gitignore 존재 여부
10. 보안 검사 (하드코딩 자격증명, HTTP vs HTTPS)
```

**출력 형식:**

```
## Plugin Validation Report
### Plugin: my-plugin
### Critical Issues (2)
- agents/reviewer.md - 'name' too short - min 3 chars
### Warnings (1)
- .mcp.json - HTTP instead of HTTPS
### Overall Assessment: FAIL
```

---

### `agents/skill-reviewer.md`

**역할:** Skill의 품질을 검토하고 개선점을 제안하는 에이전트.

**트리거:** Skill 생성 후, "review my skill", "improve skill description", description 수정 후.

**허용 도구:** `Read, Grep, Glob`

**검토 8단계:**

```
1. SKILL.md 로드 및 references/ 디렉토리 탐색
2. 구조 검증 (frontmatter, 필수 필드)
3. description 품질 평가:
   - trigger phrases 구체성
   - 3인칭 형식 ("This skill should be used when...")
   - 길이 (50-500자)
   - 다른 Skill과 혼동 가능성
4. 본문 품질:
   - 단어 수 (1000-3000자 권장)
   - imperative/infinitive 형식 사용
   - 섹션 구성
5. Progressive Disclosure 구현 여부:
   - SKILL.md 핵심 내용만
   - references/에 상세 내용
   - SKILL.md에서 references/ 파일 참조
6. references/, examples/, scripts/ 파일 품질
7. 심각도별 이슈 분류 (critical/major/minor)
8. 개선 권고안 (before/after 포함)
```

**출력 형식:**

```
## Skill Review: text-to-sql
### Description Analysis
Current: "SQL 관련 작업을 처리한다"
Issues: 너무 짧고 추상적, trigger phrases 없음
Suggested: "This skill should be used when the user asks to..."
### Overall Rating: Needs Major Revision
```

---

## Skills 7개

### `skills/plugin-structure/SKILL.md`

**트리거:** "create a plugin", "plugin.json manifest", "plugin directory layout", "add commands/agents/skills/hooks"

**핵심 내용:**

```
플러그인 디렉토리 구조:
my-plugin/
├── .claude-plugin/plugin.json  (필수)
├── commands/                   (슬래시 커맨드, 레거시)
├── agents/                     (서브에이전트)
├── skills/                     (Skill 디렉토리들)
│   └── skill-name/SKILL.md
├── hooks/hooks.json
└── .mcp.json

plugin.json 필수 필드: name (kebab-case)
권장 필드: version, description, author, homepage, keywords

${CLAUDE_PLUGIN_ROOT}: 이식성을 위해 모든 내부 경로에 사용

Auto-discovery: .md → commands, agents 자동 발견
                */SKILL.md → skills 자동 발견
```

**references/ 2개:**

- `manifest-reference.md` (~6000자): plugin.json 전체 필드 명세, 경로 해석 규칙
- `component-patterns.md` (~6000자): 구성요소 조직 패턴 (flat/categorized/hierarchical)

**examples/ 3개:**

- `minimal-plugin.md`: plugin.json + 커맨드 1개
- `standard-plugin.md`: 커맨드+에이전트+Skills+Hooks 전체 예시
- `advanced-plugin.md`: 엔터프라이즈급 (MCP 3개, 에이전트 5개, Skills 3개, 공유 라이브러리)

---

### `skills/command-development/SKILL.md`

**트리거:** "create a slash command", "command frontmatter", "define command arguments", "interactive command"

**핵심 내용:**

```
커맨드 = Claude에게 주는 지침 (사용자에게 보내는 메시지가 아님)

파일 위치:
- 프로젝트: .claude/commands/
- 사용자: ~/.claude/commands/
- 플러그인: plugin-name/commands/ (레거시)
- 신규 권장: plugin-name/skills/<name>/SKILL.md

Frontmatter 필드:
- description: /help에 표시
- allowed-tools: 도구 제한 (Bash(git:*) 패턴)
- model: sonnet/opus/haiku
- argument-hint: 인자 문서화
- disable-model-invocation: 수동 전용

동적 인자:
- $ARGUMENTS: 모든 인자를 단일 문자열로
- $1, $2, $3: 위치 인자
- @path/to/file: 파일 내용 인라인 삽입
- !`command`: Bash 실행 결과 삽입
```

**references/ 4개:**

- `frontmatter-reference.md`: 전체 frontmatter 필드 명세
- `plugin-features-reference.md`: ${CLAUDE_PLUGIN_ROOT} 패턴, 플러그인 커맨드 조직
- `interactive-commands.md`: AskUserQuestion 도구 상세 가이드 (2500자)
- `advanced-workflows.md`: 멀티스텝, 상태 관리, 체크포인트 패턴
- `documentation-patterns.md`: 커맨드 자체 문서화 방법
- `marketplace-considerations.md`: 배포 고려사항
- `testing-strategies.md`: 커맨드 테스트 방법론

**examples/ 2개:**

- `simple-commands.md`: 10개 기본 커맨드 예시
- `plugin-commands.md`: 10개 플러그인 특화 커맨드 예시

---

### `skills/agent-development/SKILL.md`

**트리거:** "create an agent", "agent frontmatter", "when to use description", "agent examples", "autonomous agent"

**핵심 내용:**

```
에이전트 파일 구조:
---
name: agent-identifier          # lowercase-hyphens, 3-50자
description: |                  # 가장 중요한 필드
  Use this agent when [조건].
  <example>
  Context: [상황]
  user: "[발화]"
  assistant: "[반응]"
  <commentary>[이유]</commentary>
  </example>
model: inherit                  # 대부분 inherit
color: blue                     # blue/cyan/green/yellow/magenta/red
tools: ["Read", "Write"]        # 생략 시 모든 도구
---
시스템 프롬프트 (2인칭)

description의 <example> 블록이 트리거 메커니즘:
- 2-4개 예시
- proactive + reactive triggering 모두 포함
- commentary에 왜 트리거되어야 하는지 설명
```

**AI 보조 생성 워크플로우 (원문 확인됨):** Claude Code 내부 구현에서 추출된 정확한 프롬프트:

```
Create an agent configuration based on this request: "[설명]"
Return ONLY JSON:
{
  "identifier": "name",
  "whenToUse": "Use this agent when... <example>...</example>",
  "systemPrompt": "You are..."
}
```

**references/ 3개:**

- `agent-creation-system-prompt.md`: Claude Code 내부 에이전트 생성 프롬프트 전문
- `system-prompt-design.md` (~4000자): 4가지 패턴 (Analysis/Generation/Validation/Orchestration)
- `triggering-examples.md` (~2500자): \<example> 블록 작성 가이드

**examples/ 2개:**

- `agent-creation-prompt.md`: AI 보조 생성 템플릿 (예시 3개 포함)
- `complete-agent-examples.md`: 완성된 에이전트 4개 (code-reviewer, test-generator, docs-generator, security-analyzer)

**scripts/ 1개:**

- `validate-agent.sh`: 에이전트 파일 검증 (frontmatter 형식, name 규칙, description 길이, system prompt 길이, /\<example> 블록 존재 여부)

---

### `skills/skill-development/SKILL.md`

**트리거:** "create a skill", "add a skill to plugin", "improve skill description", "organize skill content"

**핵심 내용:**

```
Skills는 도메인 전문 지식의 온보딩 가이드.
Anatomy:
├── SKILL.md (필수)
│   ├── frontmatter: name + description
│   └── Markdown 지침
└── resources/ (선택)
    ├── scripts/   - 반복 실행 코드
    ├── references/ - 필요 시 컨텍스트 로드 문서
    └── assets/    - 출력에 사용하는 파일 (템플릿 등)

6단계 생성 프로세스:
1. 구체적 사용 예시로 용도 이해
2. 재사용 가능 리소스 계획 (scripts/references/assets)
3. 플러그인 디렉토리 구조 생성 (init_skill.py 없이 직접)
4. SKILL.md 편집:
   - imperative/infinitive 형식
   - 3인칭 description
   - 1500-2000자 SKILL.md 본문
   - 상세 내용은 references/로
5. 검증 및 테스트
6. 반복 개선

description 작성 원칙:
- 3인칭: "This skill should be used when..."
- 구체적 발화 예시 포함
- "Make sure to use this skill whenever..." 적극적 표현

references/의 skill-creator-original.md:
  anthropics/skills의 skill-creator SKILL.md 원본을 그대로 포함.
  plugin-dev 플러그인 내부에서 skill-creator의 방법론을 참조 가능.
```

---

### `skills/hook-development/SKILL.md`

**트리거:** "create a hook", "add a PreToolUse hook", "${CLAUDE_PLUGIN_ROOT}", "block dangerous commands", hook 이벤트 언급

**핵심 내용:**

```
Hook 유형 2가지:
1. Prompt-based (권장): LLM이 판단
   {"type": "prompt", "prompt": "...", "timeout": 30}
2. Command: 결정론적 bash
   {"type": "command", "command": "bash ...", "timeout": 60}

hooks.json 두 가지 형식:
- 플러그인: {"hooks": {"PreToolUse": [...]}} (wrapper 필요)
- 사용자 settings.json: {"PreToolUse": [...]} (직접)

지원 이벤트 9개:
PreToolUse, PostToolUse, Stop, SubagentStop,
UserPromptSubmit, SessionStart, SessionEnd,
PreCompact, Notification

Matcher 패턴:
- "Write"        단일 도구
- "Read|Write"   복수 도구
- "*"            모든 도구
- "mcp__.*"      모든 MCP 도구
- "mcp__asana_.*" 특정 플러그인 MCP

PreToolUse 출력:
{"hookSpecificOutput": {"permissionDecision": "allow|deny|ask"}}

Stop 출력:
{"decision": "approve|block", "reason": "..."}

중요: Hooks는 세션 시작 시 로드됨.
hooks.json 수정 후 Claude Code 재시작 필요.
핫스왑 불가.
```

**references/ 3개:**

- `patterns.md`: 10가지 검증된 패턴 (Security/Test Enforcement/Context Loading 등)
- `migration.md`: command hooks → prompt hooks 마이그레이션 가이드
- `advanced.md`: 멀티스테이지 검증, 상태 공유, 외부 시스템 연동

**examples/ 3개:**

- `validate-write.sh`: 파일 쓰기 검증 (경로 순회, 시스템 경로, 민감 파일)
- `validate-bash.sh`: Bash 명령 검증 (rm -rf, 권한 상승)
- `load-context.sh`: SessionStart에서 프로젝트 타입 감지 + $CLAUDE_ENV_FILE 설정

**scripts/ 3개:**

- `validate-hook-schema.sh`: hooks.json 구조 검증
- `test-hook.sh`: 샘플 입력으로 hook 스크립트 단독 테스트
- `hook-linter.sh`: bash hook 스크립트 코드 품질 검사 (set -euo pipefail, 변수 인용, 하드코딩 경로 등)

---

### `skills/mcp-integration/SKILL.md`

**트리거:** "add MCP server", "configure .mcp.json", "Model Context Protocol", "connect external service"

**핵심 내용:**

```
MCP 설정 위치 2곳:
1. .mcp.json (권장): 별도 파일
2. plugin.json의 mcpServers 필드: 인라인

서버 타입 4가지:
stdio: 로컬 프로세스 (npx, node, python)
SSE: 호스팅 서비스 + OAuth (Asana, GitHub)
HTTP: REST API + 토큰 인증
WebSocket: 실시간 양방향

환경변수:
${CLAUDE_PLUGIN_ROOT}: 플러그인 경로
${API_TOKEN}: 사용자 환경변수

MCP 도구 네이밍:
mcp__plugin_<plugin-name>_<server>__<tool>
예: mcp__plugin_asana_asana__asana_create_task

allowed-tools에 사전 허용:
["mcp__plugin_asana_asana__asana_create_task"]

보안: HTTPS/WSS 필수, 하드코딩 자격증명 금지
```

**references/ 3개:**

- `server-types.md` (~3200자): 4가지 서버 타입 상세
- `authentication.md` (~2800자): OAuth/Token/Environment 인증 패턴
- `tool-usage.md` (~2600자): 커맨드/에이전트에서 MCP 도구 사용법

**examples/ 3개:**

- `stdio-server.json`: filesystem, database, custom tools 설정
- `sse-server.json`: Asana, GitHub, custom OAuth 설정
- `http-server.json`: REST API 토큰 인증 설정

---

### `skills/plugin-settings/SKILL.md`

**트리거:** "plugin settings", ".local.md files", "store plugin configuration", "YAML frontmatter", "per-project plugin settings"

**핵심 내용:**

```
설정 파일 위치: .claude/plugin-name.local.md
구조: YAML frontmatter + Markdown body

예시:
---
enabled: true
mode: standard
max_retries: 3
---
# 추가 지시사항
여기는 에이전트에게 피드백으로 돌아오는 텍스트

Bash에서 파싱:
FRONTMATTER=$(sed -n '/^---$/,/^---$/{ /^---$/d; p; }' "$FILE")
ENABLED=$(echo "$FRONTMATTER" | grep '^enabled:' | sed 's/enabled: *//')
BODY=$(awk '/^---$/{i++; next} i>=2' "$FILE")

핵심 패턴:
1. 빠른 종료: 파일 없으면 exit 0
2. enabled 플래그로 on/off
3. Atomic 업데이트: temp 파일 → mv
4. .gitignore에 추가 필수
5. 변경 후 Claude Code 재시작 필요 (Hooks는 핫스왑 불가)
```

**실제 사용 사례 2개 (원본 코드에서 확인됨):**

**multi-agent-swarm 플러그인:**

```yaml
---
agent_name: auth-implementation
task_number: 3.5
coordinator_session: team-leader
enabled: true
---
JWT 인증 구현 작업...
```

→ agent-stop-notification.sh Hook이 읽어서 tmux로 코디네이터 세션에 알림 전송

**ralph-loop 플러그인:**

```yaml
---
iteration: 1
max_iterations: 10
completion_promise: "All tests passing"
---
린팅 에러를 모두 수정해주세요...
```

→ Stop Hook이 읽어서 완료 조건 확인, iteration 증가, body를 프롬프트로 피드백

**references/ 2개:**

- `parsing-techniques.md`: bash 파싱 기법 전체 (sed, awk, 엣지케이스)
- `real-world-examples.md`: multi-agent-swarm, ralph-loop 실제 구현 코드 분석

**examples/ 3개:**

- `read-settings-hook.sh`: 설정 파일 읽어 검증 동작하는 완성된 hook
- `create-settings-command.md`: AskUserQuestion으로 설정 파일 생성하는 커맨드
- `example-settings.md`: 다양한 설정 파일 템플릿

**scripts/ 2개:**

- `validate-settings.sh`: 설정 파일 구조 검증
- `parse-frontmatter.sh`: frontmatter에서 특정 필드 추출 유틸리티

---

# skill-creator 플러그인 완전 분석

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
