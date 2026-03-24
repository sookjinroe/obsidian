---
aliases:
  - "plugin 복잡도 패턴: minimal"
  - standard
  - advanced
---
# Claude Code Plugin 복잡도 패턴 활용 가이드

---

## 한눈에 보기

|              | Minimal     | Standard              | Advanced                                          |
| ------------ | ----------- | --------------------- | ------------------------------------------------- |
| **한 줄 요약**   | 나 혼자, 목적 하나 | 팀 전체가, 기준을 맞춰서        | 외부 서비스 여러 개, 환경도 여러 개                             |
| **커맨드**      | 단일 파일       | 복수 파일 (flat)          | 하위 디렉토리 계층                                        |
| **에이전트**     | —           | 역할별 분리                | orchestration / specialized 분리                    |
| **스킬**       | —           | SKILL.md + references | SKILL.md + references + scripts                   |
| **훅**        | —           | PreToolUse + Stop     | SessionStart + PreToolUse ×2 + PostToolUse + Stop |
| **MCP 서버**   | —           | —                     | 커스텀 서버 복수 운영                                      |
| **공유 라이브러리** | —           | scripts/ (단순)         | lib / core · integrations · utils                 |
| **환경 설정**    | —           | —                     | config / environments 분리                          |

---

## 선택 흐름

```
커맨드 하나로 끝나는가?
    └── Yes → Minimal

팀원 모두가 같은 기준을 따라야 하는가?
    └── Yes → Standard

외부 서비스가 2개 이상이고, 대상/환경에 따라 설정이 달라지는가?
    └── Yes → Advanced
```

---

## 1. Minimal — 단일 목적을 빠르게 해결할 때

### 왜 이 패턴인가

반복적으로 쓰는 작업 하나를 자동화하고 싶은데, 에이전트나 훅까지는 필요 없는 상황이다. 커맨드 하나로 끝난다면 나머지는 전부 오버엔지니어링이다.

### 어떻게 구성하나

|컴포넌트|역할|없으면 어떻게 되나|
|---|---|---|
|`commands/`|사용자가 호출하는 슬래시 커맨드|플러그인이 아무것도 안 함|
|`plugin.json`|`name` 필드만 — 나머지는 선택|인식 자체가 안 됨|

훅, 에이전트, 스킬 디렉토리는 만들지 않는다. 파일이 없으면 Claude Code가 자동으로 무시한다.

---

### 예시 — 주간 보고서 템플릿 생성기

> **추론 예시** — 근거: "single-purpose utilities, no dependencies" 조건 직접 적용. 주간 보고서 작성은 반복 작업이지만, 검증·에이전트·외부 연동이 전혀 필요 없는 전형적인 단일 목적 케이스다.

**상황:** 팀마다 주간 보고서 포맷이 달라 매주 템플릿을 찾아 복붙하는 일이 생긴다. `/weekly-report` 커맨드 하나로 담당자 이름, 주차, 프로젝트별 섹션이 채워진 템플릿을 즉시 출력한다.

```
weekly-report/
├── .claude-plugin/
│   └── plugin.json          → {"name": "weekly-report"}
└── commands/
    └── weekly-report.md     → 주차·담당자 입력받아 보고서 템플릿 출력
```

**커맨드 파일 안에는** 템플릿 형식과 각 섹션 작성 지침만 담으면 된다. 스크립트도, 외부 호출도 없다.

---

### 파일 트리 레퍼런스

<details> <summary>전체 구조 보기</summary>

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          ← {"name": "plugin-name"} 만으로 충분
└── commands/
    └── command-name.md      ← 커맨드 구현 (YAML frontmatter + 지침)
```

`commands/` 외의 디렉토리는 만들지 않는다.

</details>

---

## 2. Standard — 팀 전체가 일관성 있게 써야 할 때

### 왜 이 패턴인가

혼자 쓰는 도구가 아니라, 팀 전체가 같은 기준으로 작업해야 할 때다. 커맨드 하나로는 커버가 안 되고, 상황에 따라 다른 에이전트가 붙어야 하며, **"기준을 어겼을 때 자동으로 잡아주는" 훅**이 필요해진다. MCP 서버 없이 agents + skills + hooks 조합으로 팀 수준의 자동화를 커버한다.

### 어떻게 구성하나

|컴포넌트|역할|언제 실행되나|
|---|---|---|
|`commands/`|작업 유형별 진입점|사용자가 `/커맨드`를 입력할 때|
|`agents/`|특정 역할에 특화된 서브에이전트|컨텍스트에 맞게 자동 선택되거나 수동 호출|
|`skills/`|자동 활성화되는 도메인 지식|관련 작업이 감지되면 자동 로드|
|`hooks/PreToolUse`|작업 실행 전 기준 준수 여부 검증|Write·Edit 등 지정 도구 실행 직전|
|`hooks/Stop`|작업 완료 전 최종 검사|전체 태스크가 끝나기 직전|

훅이 없으면 팀원이 기준을 몰라도 플러그인이 아무것도 막지 않는다. 스킬의 `references/` 하위 폴더는 `SKILL.md`가 너무 길어질 때 심화 내용을 분리 수납하는 공간이다.

---

### 예시 — 영업 제안서 품질 관리

> **추론 예시** — 근거: 문서의 "multiple entry points", "consistency enforcement", "production plugins for distribution" 조건 조합. 영업 제안서는 작성(커맨드) → 검토(에이전트) → 기준 확인(훅)의 다중 진입점이 필요하고, 팀 전체가 동일한 품질 기준을 따라야 하는 전형적인 Standard 케이스다.

**상황:** 영업팀 제안서마다 포맷, 필수 섹션, 가격 표기 방식이 달라 고객 신뢰도에 영향을 준다. 작성부터 검토, 최종 발송 전 검사까지 하나의 플러그인으로 표준화한다.

```
proposal-quality/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── draft.md             → 고객사 정보 입력받아 제안서 초안 생성
│   ├── review.md            → 작성된 제안서 품질 검토 요청
│   └── finalize.md          → 최종 발송 전 체크리스트 실행
├── agents/
│   └── proposal-reviewer.md → 필수 섹션 누락·가격 표기 오류·톤 일관성 검토 전문
├── skills/
│   └── proposal-standards/
│       ├── SKILL.md         → 자동 활성화: 제안서 작성·검토 시
│       └── references/
│           └── tone-guide.md → 고객사 유형별 커뮤니케이션 톤 가이드
├── hooks/
│   ├── hooks.json
│   └── scripts/
│       └── check-required-sections.sh
└── scripts/
    └── parse-proposal.py
```

**훅이 실제로 하는 일:**

|이벤트|동작|막는 것|
|---|---|---|
|PreToolUse (Write·Edit)|`proposal-standards` 스킬 기준으로 LLM이 제안서 기준 준수 여부 검증|기준 미달 초안이 저장되는 것|
|Stop|`check-required-sections.sh` 실행 → 필수 섹션(목적·범위·가격·조건) 누락 확인|미완성 상태로 작업이 완료되는 것|

`proposal-reviewer` 에이전트는 `/review` 커맨드 실행 시 `proposal-standards` 스킬을 자동으로 불러와 팀 공통 기준으로 검토한다. `check-required-sections.sh`는 Stop 훅으로 작업 완료 직전에 자동 실행된다.

---

### 파일 트리 레퍼런스

<details> <summary>전체 구조 보기</summary>

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          ← 버전, author, license, keywords 풀셋
├── commands/
│   ├── action-a.md
│   └── action-b.md
├── agents/
│   ├── role-a.md
│   └── role-b.md
├── skills/
│   └── skill-name/
│       ├── SKILL.md
│       └── references/
│           └── detail.md
├── hooks/
│   ├── hooks.json           ← PreToolUse + Stop
│   └── scripts/
│       └── validate.sh
└── scripts/
    └── utility.py
```

`plugin.json`에는 `version`, `author`, `license`, `keywords`까지 채우는 것이 권장된다. 배포 대상 플러그인이라면 `homepage`, `repository`도 추가한다.

</details>

---

## 3. Advanced — 외부 서비스가 여러 개, 환경도 여러 개일 때

### 왜 이 패턴인가

각각 독립적인 외부 서비스를 **동시에** 다뤄야 하고, 팀·지역·고객사 등 환경마다 설정이 달라야 하며, Slack·이메일 같은 알림 채널도 연결해야 할 때다.

이 규모에서 MCP 서버를 병렬로 운영하지 않으면 작업이 순차적으로 블로킹되고, 공유 라이브러리 없이 각 컴포넌트에 같은 코드를 반복하면 유지보수가 불가능해진다.

Standard와 가장 크게 다른 세 지점:

1. **MCP 서버 복수 운영** — 외부 서비스마다 별도 서버, `servers/` 디렉토리에 소스 보관
2. **`lib/` 공유 라이브러리** — core(로깅·인증) · integrations(Slack·CRM) · utils(retry·validation)로 분리해 중복 제거
3. **`config/environments/`** — 팀·지역·고객사별 설정을 코드와 분리

### 어떻게 구성하나

|컴포넌트|역할|언제 실행되나|
|---|---|---|
|`commands/category/`|기능군별로 묶인 커맨드 계층|사용자 호출 시|
|`agents/orchestration/`|여러 MCP 서버의 작업 순서 조율|복합 태스크 실행 시 자동 선택|
|`agents/specialized/`|도메인별 전문 에이전트|관련 작업 감지 시 자동 선택|
|`skills/*/scripts/`|스킬 내부에서 실행 가능한 스크립트|스킬 활성화 후 필요 시 호출|
|`hooks/SessionStart`|세션 시작 시 권한·환경 설정 검증|Claude Code 세션이 시작될 때마다|
|`hooks/PreToolUse (matcher A)`|특정 도구 실행 전 조건 A 검증|지정 도구 실행 직전|
|`hooks/PreToolUse (matcher B)`|특정 도구 실행 전 조건 B 검증|다른 지정 도구 실행 직전|
|`hooks/PostToolUse`|도구 실행 후 상태 업데이트|지정 도구 실행 직후|
|`hooks/Stop`|완료 전 최종 검사 + 이해관계자 알림|전체 태스크 종료 직전|
|`lib/integrations/`|여러 컴포넌트가 공유하는 외부 서비스 클라이언트|각 컴포넌트에서 import|
|`config/environments/`|환경별 설정값 (코드와 분리)|MCP 서버 시작 시 로드|

---

### 예시 — 콘텐츠 제작 파이프라인 자동화

> **추론 예시** — 근거: 문서가 Advanced 선택 기준으로 명시한 세 가지 — "separate MCP servers for parallel operations", "shared libraries reduce duplication", "environment configs enable customization" — 를 동시에 충족하는 도메인으로 콘텐츠 팀을 선택했다. Notion(초안 관리) + Google Calendar(발행 일정) + Gmail(배포 알림)은 각각 독립적인 외부 서비스이고, 브랜드·언어·채널별로 설정이 달라지며, 완료 시 담당자 알림이 운영 필수 조건이다.

**상황:** 콘텐츠 팀이 초안 작성(Notion), 발행 일정 관리(Google Calendar), 배포 알림(Gmail)을 각각 별도 도구로 관리하고 있어 콘텐츠 하나를 발행하려면 세 곳을 수동으로 오가야 한다. 브랜드 채널(공식 블로그·뉴스레터·SNS)마다 톤과 포맷이 다르고, 글로벌 팀은 한국어·영어 설정을 분리해서 써야 한다.

```
content-pipeline/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── create/
│   │   ├── draft.md         → 채널·언어 지정해 초안 생성 후 Notion 저장
│   │   └── translate.md     → 기존 초안 다국어 버전 생성
│   └── publish/
│       ├── schedule.md      → 발행 일정 Calendar에 등록
│       └── distribute.md    → 채널별 포맷으로 변환 후 배포 알림
├── agents/
│   ├── orchestration/
│   │   └── publish-orchestrator.md  → Notion 확인 → Calendar 등록 → Gmail 발송 순서 조율
│   └── specialized/
│       ├── copy-writer.md           → 채널별 톤·길이 최적화 전문
│       └── seo-reviewer.md          → 키워드·메타데이터 검토 전문
├── skills/
│   ├── brand-voice/
│   │   ├── SKILL.md         → 자동 활성화: 콘텐츠 작성·검토 시
│   │   ├── references/
│   │   │   └── tone-by-channel.md
│   │   └── scripts/
│   │       └── check-brand-compliance.sh
│   └── seo-standards/
│       ├── SKILL.md
│       └── references/
│           └── keyword-guide.md
├── hooks/
│   ├── hooks.json
│   └── scripts/
│       ├── validate-channel-config.sh  → SessionStart: 채널 설정 유효성 확인
│       ├── check-brand-tone.sh         → PreToolUse(Write): 브랜드 톤 준수 검사
│       ├── verify-seo-meta.sh          → PreToolUse(Edit): SEO 메타데이터 존재 확인
│       └── notify-stakeholders.sh      → Stop: 발행 완료 시 담당자 Gmail 알림
├── .mcp.json                → notion-mcp + google-calendar-mcp + gmail-mcp
├── servers/
│   ├── notion-mcp/          → 초안 저장·조회·상태 업데이트
│   ├── google-calendar-mcp/ → 발행 일정 등록·충돌 확인
│   └── gmail-mcp/           → 배포 알림·승인 요청 발송
├── lib/
│   ├── core/
│   │   ├── logger.js
│   │   └── auth.js
│   ├── integrations/
│   │   ├── notion.js        → 여러 커맨드·에이전트가 공유
│   │   ├── calendar.js
│   │   └── gmail.js
│   └── utils/
│       ├── retry.js
│       └── format-validator.js
└── config/
    ├── environments/
    │   ├── korea.json        → 한국어 채널, KST 시간대, 국내 담당자
    │   ├── global.json       → 영어 채널, UTC 시간대, 글로벌 담당자
    │   └── staging.json      → 테스트용 Notion 워크스페이스
    └── templates/
        ├── blog-post.md
        └── newsletter.md
```

**훅이 실제로 하는 일:**

|이벤트|스크립트|막는 것|
|---|---|---|
|SessionStart|`validate-channel-config.sh`|잘못된 채널 설정으로 작업이 시작되는 것|
|PreToolUse (Write)|`check-brand-tone.sh`|브랜드 톤에 맞지 않는 초안이 Notion에 저장되는 것|
|PreToolUse (Edit)|`verify-seo-meta.sh`|SEO 메타데이터 없이 콘텐츠가 수정 완료되는 것|
|Stop|`notify-stakeholders.sh`|발행 완료 후 담당자가 수동으로 알림을 보내야 하는 것|

`publish-orchestrator`는 `/distribute` 커맨드 실행 시 Notion에서 최종 확인된 초안을 가져오고, Calendar에 발행 일정이 등록됐는지 확인한 뒤, Gmail로 채널 담당자에게 배포 알림을 순서대로 보낸다. `lib/integrations/`의 세 클라이언트를 여러 커맨드와 에이전트가 공유하므로 Notion 연결 방식이 바뀌어도 `notion.js` 하나만 수정하면 된다. `config/environments/korea.json`과 `global.json`은 같은 플러그인을 국내팀과 글로벌팀이 각각 다른 Notion 워크스페이스·시간대·담당자로 운영할 수 있게 한다.

---

### 파일 트리 레퍼런스

<details> <summary>전체 구조 보기</summary>

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          ← commands, agents를 배열로 다중 경로 등록
├── commands/
│   ├── category-a/
│   │   ├── action-1.md
│   │   └── action-2.md
│   └── category-b/
│       └── action-3.md
├── agents/
│   ├── orchestration/
│   │   └── main-orchestrator.md
│   └── specialized/
│       ├── specialist-a.md
│       └── specialist-b.md
├── skills/
│   └── skill-name/
│       ├── SKILL.md
│       ├── references/
│       └── scripts/
│           └── validate.sh
├── hooks/
│   ├── hooks.json           ← SessionStart + PreToolUse ×2 + PostToolUse + Stop
│   └── scripts/
│       ├── security/
│       ├── quality/
│       └── workflow/
├── .mcp.json
├── servers/
│   ├── service-a-mcp/
│   ├── service-b-mcp/
│   └── service-c-mcp/
├── lib/
│   ├── core/logger.js, auth.js
│   ├── integrations/service-a.js, service-b.js
│   └── utils/retry.js, validation.js
└── config/
    ├── environments/
    │   ├── production.json
    │   ├── staging.json
    │   └── development.json
    └── templates/
```

`plugin.json`에서 `commands`와 `agents`를 배열로 지정해 다중 경로를 등록한다. 커스텀 경로는 기본 디렉토리를 대체하지 않고 **추가**된다.

</details>

---

## 부록 — 판단 기준 상세

### "외부 서비스"란 무엇인가

MCP 서버를 별도로 운영해야 하는 모든 서비스가 해당된다. 개발 도구에 한정되지 않는다.

|도메인|해당되는 외부 서비스 예시|
|---|---|
|콘텐츠·문서|Notion, Google Docs, Confluence, CMS|
|비즈니스·영업|CRM, 회계 시스템, 이메일, 캘린더|
|HR·운영|인사 시스템, 슬랙, 화상회의 도구|
|개발·인프라|GitHub, Jira, Kubernetes, Terraform|

두 개 이상의 외부 서비스를 **동시에** 다룬다면 Advanced를 검토한다.

### "환경이 여러 개"란 무엇인가

같은 플러그인을 쓰지만 **설정값이 달라야 하는** 상황이다.

- 개발팀과 운영팀이 각각 다른 Notion 워크스페이스를 쓴다
- 한국 팀과 글로벌 팀이 다른 언어·시간대·담당자 설정을 쓴다
- 테스트 환경과 실제 환경이 다른 API 키를 쓴다

이 경우 `config/environments/`에 환경별 JSON을 두고 MCP 서버 시작 시 해당 파일을 로드하도록 구성한다.