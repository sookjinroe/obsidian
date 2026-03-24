
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
