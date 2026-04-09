
![[agent phase matrix-1775710125213.png]]

<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>에이전트 패러다임 변화 — 6기 × 5축</title>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background: #f9f8f5;
    color: #2c2c2a;
    padding: 32px 24px;
    min-height: 100vh;
  }
  @media (prefers-color-scheme: dark) {
    body { background: #1a1a18; color: #d4d2c8; }
  }
  h1 {
    font-size: 15px;
    font-weight: 500;
    color: #2c2c2a;
    margin-bottom: 4px;
  }
  @media (prefers-color-scheme: dark) { h1 { color: #d4d2c8; } }
  .subtitle {
    font-size: 12px;
    color: #888780;
    margin-bottom: 20px;
  }
  .tl { padding: 6px 0; }
  .tl-grid {
    display: grid;
    grid-template-columns: 56px repeat(6, 1fr);
    gap: 4px;
  }
  .thc {
    text-align: center;
    font-size: 10px;
    font-weight: 500;
    color: #888780;
    padding: 3px 0 8px;
    border-bottom: 0.5px solid rgba(0,0,0,0.12);
  }
  @media (prefers-color-scheme: dark) {
    .thc { border-color: rgba(255,255,255,0.1); color: #5f5e5a; }
  }
  .thc.now { color: #2c2c2a; font-weight: 600; }
  @media (prefers-color-scheme: dark) { .thc.now { color: #d4d2c8; } }
  .ax {
    display: flex;
    align-items: center;
    font-size: 10px;
    font-weight: 500;
    color: #888780;
    line-height: 1.3;
    padding-right: 4px;
  }
  @media (prefers-color-scheme: dark) { .ax { color: #5f5e5a; } }
  .tc {
    border-radius: 6px;
    padding: 7px 8px;
    cursor: pointer;
    transition: opacity .15s;
    border: 0.5px solid;
    display: flex;
    flex-direction: column;
    min-height: 80px;
  }
  .tc:hover { opacity: .75; }
  .em {
    opacity: .15;
    cursor: default;
    background: #f1efe8 !important;
    border-color: rgba(0,0,0,0.1) !important;
  }
  @media (prefers-color-scheme: dark) { .em { background: #2c2c2a !important; } }
  .em:hover { opacity: .15; }
  .tk { font-size: 10px; font-weight: 500; line-height: 1.35; margin-bottom: 2px; }
  .td { font-size: 9px; line-height: 1.4; flex: 1; opacity: .72; }
  .tt {
    font-size: 9px;
    margin-top: 4px;
    padding-top: 4px;
    border-top: 0.5px solid currentColor;
    opacity: .45;
    line-height: 1.3;
  }

  /* Axis colors — light */
  .c1 { background: #EEEDFE; color: #3C3489; border-color: #AFA9EC; }
  .c2 { background: #E1F5EE; color: #085041; border-color: #5DCAA5; }
  .c3 { background: #FAECE7; color: #712B13; border-color: #F0997B; }
  .c4 { background: #E6F1FB; color: #0C447C; border-color: #85B7EB; }
  .c5 { background: #FAEEDA; color: #633806; border-color: #EF9F27; }

  /* Axis colors — dark */
  @media (prefers-color-scheme: dark) {
    .c1 { background: #26215C; color: #CECBF6; border-color: #534AB7; }
    .c2 { background: #04342C; color: #9FE1CB; border-color: #0F6E56; }
    .c3 { background: #4A1B0C; color: #F5C4B3; border-color: #993C1D; }
    .c4 { background: #042C53; color: #B5D4F4; border-color: #185FA5; }
    .c5 { background: #412402; color: #FAC775; border-color: #854F0B; }
  }

  .leg-row {
    display: flex;
    flex-wrap: wrap;
    gap: 6px 14px;
    margin-top: 10px;
    padding-top: 8px;
    border-top: 0.5px solid rgba(0,0,0,0.1);
  }
  @media (prefers-color-scheme: dark) { .leg-row { border-color: rgba(255,255,255,0.08); } }
  .leg { display: flex; align-items: center; gap: 4px; font-size: 10px; color: #888780; }
  .ld { width: 7px; height: 7px; border-radius: 50%; flex-shrink: 0; }
  .hint { font-size: 9px; color: #b4b2a9; margin-top: 5px; }
</style>
</head>
<body>
<h1>에이전트 패러다임 변화</h1>
<p class="subtitle">설정 방식·모델 능력·메모리·연결·자율성 — 5개 축으로 본 2022→2026 발전</p>

<div class="tl">
<div class="tl-grid">

<div></div>
<div class="thc">2022</div>
<div class="thc">2023</div>
<div class="thc">2024 초</div>
<div class="thc">2024 말</div>
<div class="thc">2025</div>
<div class="thc now">2026 ·</div>

<!-- 모델 능력 -->
<div class="ax">모델<br>능력</div>
<div class="tc c1">
  <div class="tk">CoT · ReAct</div>
  <div class="td">단계 추론 + 생각→행동→관찰 루프를 프롬프트로 유도</div>
  <div class="tt">Wei et al., Google 2022</div>
</div>
<div class="tc c1">
  <div class="tk">Function Calling</div>
  <div class="td">도구 호출이 프롬프트 설득 → 모델 능력으로 내재화. Toolformer가 먼저 증명, GPT-4가 제도화</div>
  <div class="tt">Toolformer (Meta), GPT-4 (OpenAI)</div>
</div>
<div class="tc c1">
  <div class="tk">Long context · 멀티모달</div>
  <div class="td">128K+ 컨텍스트 실용화. 이미지·코드 동시 처리. 멀티스텝 추론이 안정권 진입</div>
  <div class="tt">GPT-4o, Claude 3 Opus</div>
</div>
<div class="tc c1">
  <div class="tk">Computer Use · o1</div>
  <div class="td">환경을 직접 보고 행동. o1은 추론을 행동과 분리해 자기 검증 가능</div>
  <div class="tt">Claude (Anthropic), o1 (OpenAI)</div>
</div>
<div class="tc c1">
  <div class="tk">추론 모델 실용화</div>
  <div class="td">확장 사고로 복잡한 계획 수립. 추론 토큰을 따로 사용해 행동 전 검증</div>
  <div class="tt">o3 (OpenAI), Claude 3.7 Sonnet</div>
</div>
<div class="tc c1">
  <div class="tk">추론+행동 통합</div>
  <div class="td">복잡한 태스크에서 인간 수준 벤치마크 도달. 에이전트 실행 중 실시간 재계획 가능</div>
  <div class="tt">GPT-5, Claude 4.6, Gemini 3.1</div>
</div>

<!-- 메모리 -->
<div class="ax">메모리</div>
<div class="tc c2">
  <div class="tk">컨텍스트 윈도우</div>
  <div class="td">4K~8K 토큰. 대화 기록을 통째로 넣는 방식. 길어지면 앞 내용을 잃음</div>
  <div class="tt">GPT-3.5 (4K)</div>
</div>
<div class="tc c2">
  <div class="tk">RAG + Vector DB</div>
  <div class="td">파라미터 밖 지식을 실시간 검색. 재학습 없이 지식 교체 가능. 에이전트의 첫 장기 메모리</div>
  <div class="tt">Pinecone, Chroma, FAISS</div>
</div>
<div class="tc c2">
  <div class="tk">State 관리</div>
  <div class="td">실행 흐름 중 에이전트 상태를 노드 간에 전달·누적. 루프 재진입 시 이전 결과 유지</div>
  <div class="tt">LangGraph, Mem0</div>
</div>
<div class="tc c2">
  <div class="tk">장기 외부 메모리</div>
  <div class="td">DB에 요약·사실 저장 후 필요할 때 검색. 사용자 개인 맥락을 에이전트가 참조</div>
  <div class="tt">Mem0, Zep, LangChain Memory</div>
</div>
<div class="tc c2">
  <div class="tk">지속 메모리</div>
  <div class="td">대화가 끝나도 기억 유지. 에이전트가 사용자를 점진적으로 학습</div>
  <div class="tt">Claude Memory (Anthropic)</div>
</div>
<div class="tc c2">
  <div class="tk">개인화 장기 메모리</div>
  <div class="td">사용자 선호·작업 패턴 지속 누적. 에이전트가 컨텍스트 없이도 개인화된 판단</div>
  <div class="tt">Claude Memory, MemGPT</div>
</div>

<!-- 연결 -->
<div class="ax">연결</div>
<div class="tc em"></div>
<div class="tc c3">
  <div class="tk">코드 기반 통합</div>
  <div class="td">API·DB·검색엔진을 Python 클라이언트 코드로 연결. LangChain이 패턴 표준화</div>
  <div class="tt">LangChain integrations</div>
</div>
<div class="tc c3">
  <div class="tk">에이전트 간 연결</div>
  <div class="td">에이전트끼리 메시지를 주고받으며 역할 분담. 연결 대상이 외부 API에서 다른 에이전트로 확장</div>
  <div class="tt">CrewAI, AutoGen (Microsoft)</div>
</div>
<div class="tc c3">
  <div class="tk">MCP</div>
  <div class="td">외부 연결을 JSON 선언 한 줄로. 클라이언트 코드 불필요. 에디터·IDE에서 동일하게 작동</div>
  <div class="tt">MCP (Anthropic, 2024.11)</div>
</div>
<div class="tc c3">
  <div class="tk">A2A · ACP</div>
  <div class="td">MCP가 에이전트↔도구라면, A2A·ACP는 에이전트↔에이전트 간 통신 표준화</div>
  <div class="tt">A2A (Google), ACP (Linux Foundation)</div>
</div>
<div class="tc c3">
  <div class="tk">에이전트 네트워크</div>
  <div class="td">MCP+A2A로 서로 다른 벤더의 에이전트가 협업. 에이전트 마켓플레이스 형성 시작</div>
  <div class="tt">Google Vertex AI, AWS Bedrock</div>
</div>

<!-- 설정 방식 -->
<div class="ax">설정<br>방식</div>
<div class="tc c4">
  <div class="tk">프롬프트</div>
  <div class="td">텍스트로 모든 행동 정의. 설정이 모델 입력과 섞임. 재사용·버전 관리 어려움</div>
  <div class="tt">ChatGPT system prompt</div>
</div>
<div class="tc c4">
  <div class="tk">체인 (Chain)</div>
  <div class="td">조건·순서를 코드 객체로 표현. 컴포넌트를 조립해 에이전트 구성. 재사용 가능한 패턴</div>
  <div class="tt">LangChain</div>
</div>
<div class="tc c4">
  <div class="tk">그래프 (Graph)</div>
  <div class="td">노드+엣지로 루프·분기·상태 흐름 표현. 순환 구조를 설정 안에서 직접 선언</div>
  <div class="tt">LangGraph</div>
</div>
<div class="tc c4">
  <div class="tk">프로토콜 선언</div>
  <div class="td">외부 연결을 JSON 한 줄로. 코드 없이 외부 경계를 config에 표현 가능해짐</div>
  <div class="tt">MCP servers config</div>
</div>
<div class="tc c4">
  <div class="tk">환경 선언</div>
  <div class="td">CLAUDE.md + hooks + MCP가 맞물려 파일시스템 구조 자체가 에이전트 정체성을 정의</div>
  <div class="tt">Claude Code</div>
</div>
<div class="tc c4">
  <div class="tk">로우코드 빌더</div>
  <div class="td">비개발자도 에이전트 구성 가능. 설정 방식의 민주화. 도메인 전문가가 직접 에이전트 정의</div>
  <div class="tt">n8n, Vertex AI Agent Builder</div>
</div>

<!-- 자율성 -->
<div class="ax">자율성</div>
<div class="tc c5">
  <div class="tk">단일 응답</div>
  <div class="td">한 번 실행 후 종료. 사용자가 모든 중간 판단을 직접. 자율성이라는 개념 자체가 없음</div>
  <div class="tt">ChatGPT (GPT-3.5)</div>
</div>
<div class="tc c5">
  <div class="tk">AutoGPT · BabyAGI</div>
  <div class="td">목표 주면 스스로 계획·실행 첫 시도. 실용성보다 가능성 증명. 루프 탈출 못 하는 경우 많음</div>
  <div class="tt">AutoGPT, BabyAGI (오픈소스)</div>
</div>
<div class="tc c5">
  <div class="tk">루프 + 자기교정</div>
  <div class="td">실패 시 원인 파악 후 재시도. 조건 기반으로 루프 종료 제어. 자율성이 안정권으로</div>
  <div class="tt">LangGraph, Self-RAG</div>
</div>
<div class="tc c5">
  <div class="tk">멀티에이전트 협업</div>
  <div class="td">역할 나눈 에이전트 팀이 병렬 작업. 오케스트레이터가 서브에이전트를 조율</div>
  <div class="tt">CrewAI, AutoGen</div>
</div>
<div class="tc c5">
  <div class="tk">장시간 자율 실행</div>
  <div class="td">수십 분~수 시간 독립 작업. 파일 읽기·코드 실행·테스트·배포를 사람 없이 완주</div>
  <div class="tt">Claude Code, Devin (Cognition)</div>
</div>
<div class="tc c5">
  <div class="tk">엔드투엔드 워크플로우</div>
  <div class="td">이상 감지→티켓 생성→고객 알림을 에이전트가 처음부터 끝까지. "디지털 어셈블리 라인"</div>
  <div class="tt">ChatGPT Agent, Amazon Connect</div>
</div>

</div>

<div class="leg-row">
  <div class="leg"><div class="ld" style="background:#7F77DD"></div>모델 능력</div>
  <div class="leg"><div class="ld" style="background:#1D9E75"></div>메모리</div>
  <div class="leg"><div class="ld" style="background:#D85A30"></div>연결</div>
  <div class="leg"><div class="ld" style="background:#378ADD"></div>설정 방식</div>
  <div class="leg"><div class="ld" style="background:#BA7517"></div>자율성</div>
</div>
<div class="hint" style="margin-top:10px;">Config as App 프레임워크 — 설정 방식 축의 Phase 1·2·3은 프롬프트→체인→그래프→프로토콜→환경 선언 흐름에 대응</div>
</div>

</body>
</html>