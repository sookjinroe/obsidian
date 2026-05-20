<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>글로서리 스터디 — 개념 지도</title>
<style>
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f5f4ef;
  --bg-tertiary: #ebeae3;
  --bg-info: #e6f1fb;
  --text-primary: #1a1a1a;
  --text-secondary: #5f5e5a;
  --text-tertiary: #888780;
  --text-info: #185fa5;
  --border-tertiary: rgba(0,0,0,0.15);
  --border-info: #b5d4f4;
  --radius-md: 8px;
  --radius-lg: 12px;
  --font-sans: -apple-system, BlinkMacSystemFont, 'Apple SD Gothic Neo', 'Pretendard', system-ui, sans-serif;
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg-primary: #1a1a1a;
    --bg-secondary: #262625;
    --bg-tertiary: #333330;
    --bg-info: #0c447c;
    --text-primary: #f5f5f5;
    --text-secondary: #b4b2a9;
    --text-tertiary: #888780;
    --text-info: #85b7eb;
    --border-tertiary: rgba(255,255,255,0.15);
    --border-info: #185fa5;
  }
}
* { box-sizing: border-box; }
html, body { margin: 0; padding: 0; }
body {
  font-family: var(--font-sans);
  color: var(--text-primary);
  background: var(--bg-primary);
  line-height: 1.6;
  font-size: 14px;
}

/* App shell */
.app { display: flex; min-height: 100vh; }

/* Sidebar */
.sidebar {
  width: 240px;
  flex-shrink: 0;
  background: var(--bg-secondary);
  padding: 28px 20px;
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
  border-right: 0.5px solid var(--border-tertiary);
}
.sidebar h1 {
  font-size: 16px;
  font-weight: 500;
  margin: 0 0 4px;
  color: var(--text-primary);
}
.sidebar .lead-mini {
  font-size: 12px;
  color: var(--text-tertiary);
  margin: 0 0 24px;
  line-height: 1.5;
}
.sidebar nav { display: flex; flex-direction: column; gap: 4px; }
.nav-item {
  display: block;
  padding: 10px 12px;
  font-size: 13px;
  color: var(--text-secondary);
  text-decoration: none;
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: background 0.15s, color 0.15s;
}
.nav-item:hover { background: var(--bg-tertiary); color: var(--text-primary); }
.nav-item.active {
  background: var(--bg-info);
  color: var(--text-info);
  font-weight: 500;
}

/* Main */
main {
  flex: 1;
  padding: 32px 40px;
  max-width: 1600px;
}
.chapter { display: none; }
.chapter.active { display: block; }

h2 { font-size: 22px; font-weight: 500; margin: 0 0 12px; }
h3.section-h { font-size: 16px; font-weight: 500; margin: 24px 0 12px; }
.lead { color: var(--text-secondary); margin: 0 0 24px; font-size: 14px; }

/* Chapter 1 split layout */
.ch1-intro {
  margin: 0 0 24px;
  padding: 14px 18px;
  background: var(--bg-secondary);
  border-radius: var(--radius-md);
  font-size: 14px;
  line-height: 1.6;
}
.ch1-intro span { color: var(--text-info); }

.ch1-split {
  display: grid;
  grid-template-columns: minmax(0, 1.3fr) minmax(0, 1fr);
  gap: 32px;
  align-items: start;
}
.ch1-left {
  position: sticky;
  top: 32px;
}
.ch1-right { min-width: 0; }

/* Map SVG */
.cmap-svg { width: 100%; height: auto; display: block; }
.cmap-svg .node { cursor: pointer; }
.cmap-svg .node rect { transition: stroke-width .15s; }
.cmap-svg .node:hover rect { stroke-width: 1.5; }
.cmap-svg .node.active rect { stroke-width: 2; }
.cmap-svg text.t { font-family: var(--font-sans); font-size: 14px; fill: var(--text-primary); }
.cmap-svg text.ts { font-family: var(--font-sans); font-size: 12px; fill: var(--text-secondary); }
.cmap-svg text.th { font-family: var(--font-sans); font-size: 14px; font-weight: 500; fill: var(--text-primary); }
.cmap-svg .arr { stroke: var(--text-secondary); stroke-width: 1; fill: none; }
.cmap-svg g.c-blue rect { fill: #e6f1fb; stroke: #185fa5; }
.cmap-svg g.c-blue text.th, .cmap-svg g.c-blue text.t { fill: #0c447c; }
.cmap-svg g.c-blue text.ts { fill: #185fa5; }
.cmap-svg g.c-coral rect { fill: #faece7; stroke: #993c1d; }
.cmap-svg g.c-coral text.th, .cmap-svg g.c-coral text.t { fill: #712b13; }
.cmap-svg g.c-coral text.ts { fill: #993c1d; }
.cmap-svg g.c-gray rect { fill: #f1efe8; stroke: #5f5e5a; }
.cmap-svg g.c-gray text.th, .cmap-svg g.c-gray text.t { fill: #2c2c2a; }
.cmap-svg g.c-gray text.ts { fill: #444441; }
.cmap-svg g.c-purple rect { fill: #eeedfe; stroke: #534ab7; }
.cmap-svg g.c-purple text.th, .cmap-svg g.c-purple text.t { fill: #3c3489; }
.cmap-svg g.c-purple text.ts { fill: #534ab7; }
.cmap-svg g.c-amber rect { fill: #faeeda; stroke: #BA7517; }
.cmap-svg g.c-amber text.th, .cmap-svg g.c-amber text.t { fill: #633806; }
.cmap-svg g.c-amber text.ts { fill: #854F0B; }
@media (prefers-color-scheme: dark) {
  .cmap-svg g.c-blue rect { fill: #0c447c; stroke: #85b7eb; }
  .cmap-svg g.c-blue text.th, .cmap-svg g.c-blue text.t { fill: #b5d4f4; }
  .cmap-svg g.c-blue text.ts { fill: #85b7eb; }
  .cmap-svg g.c-coral rect { fill: #712b13; stroke: #f0997b; }
  .cmap-svg g.c-coral text.th, .cmap-svg g.c-coral text.t { fill: #f5c4b3; }
  .cmap-svg g.c-coral text.ts { fill: #f0997b; }
  .cmap-svg g.c-gray rect { fill: #444441; stroke: #b4b2a9; }
  .cmap-svg g.c-gray text.th, .cmap-svg g.c-gray text.t { fill: #d3d1c7; }
  .cmap-svg g.c-gray text.ts { fill: #b4b2a9; }
  .cmap-svg g.c-purple rect { fill: #3c3489; stroke: #afa9ec; }
  .cmap-svg g.c-purple text.th, .cmap-svg g.c-purple text.t { fill: #cecbf6; }
  .cmap-svg g.c-purple text.ts { fill: #afa9ec; }
  .cmap-svg g.c-amber rect { fill: #633806; stroke: #EF9F27; }
  .cmap-svg g.c-amber text.th, .cmap-svg g.c-amber text.t { fill: #FAC775; }
  .cmap-svg g.c-amber text.ts { fill: #EF9F27; }
}

/* Description panel */
.cmap-panel {
  padding: 18px 20px;
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  margin-bottom: 16px;
}
.cmap-title { font-size: 18px; font-weight: 500; margin: 0 0 6px; }
.cmap-def { margin: 0 0 14px; line-height: 1.6; color: var(--text-primary); }
.cmap-section { margin-bottom: 12px; }
.cmap-label {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-secondary);
  margin: 0 0 6px;
  letter-spacing: .4px;
  text-transform: uppercase;
}
.cmap-section ul { margin: 0; padding-left: 18px; line-height: 1.7; color: var(--text-primary); }
.cmap-section p { margin: 0; line-height: 1.6; color: var(--text-primary); }
.cmap-link { color: var(--text-info); cursor: pointer; border-bottom: 1px dotted; }

/* Supplement */
.cmap-supplement {
  padding: 18px 20px;
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
}
.cmap-supplement-label {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-secondary);
  margin: 0 0 10px;
  letter-spacing: .4px;
  text-transform: uppercase;
}
.cmap-supplement-divider { margin-top: 22px; padding-top: 18px; border-top: 0.5px solid var(--border-tertiary); }
.cmap-card {
  padding: 14px;
  background: var(--bg-primary);
  border: 0.5px solid var(--border-tertiary);
  border-radius: var(--radius-md);
}
.cmap-card-title { font-size: 13px; font-weight: 500; margin-bottom: 6px; }
.cmap-card-body { font-size: 12px; color: var(--text-secondary); line-height: 1.5; }
.cmap-card-list { margin: 0; padding-left: 16px; font-size: 12px; line-height: 1.7; color: var(--text-secondary); }
.cmap-card-here { background: var(--bg-info); border-color: var(--border-info); }
.cmap-card-here .cmap-card-title, .cmap-card-here .cmap-card-body { color: var(--text-info); }
.cmap-grid3 { display: grid; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 12px; }
.cmap-grid2 { display: grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 12px; }

/* Chapter 2 — signal left-right split */
.ch2-split {
  display: grid;
  grid-template-columns: 220px minmax(0, 1fr);
  gap: 24px;
  align-items: start;
  margin: 24px 0;
}
.ch2-left {
  position: sticky;
  top: 32px;
}
.signal-list {
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  padding: 12px;
  display: flex;
  flex-direction: column;
  gap: 4px;
}
.signal-list-item {
  padding: 12px 14px;
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: background 0.15s;
}
.signal-list-item:hover { background: var(--bg-tertiary); }
.signal-list-item.active { background: var(--bg-info); }
.signal-list-item.active .signal-list-name { color: var(--text-info); }
.signal-outside {
  border: 1px dashed var(--border-tertiary);
  background: transparent !important;
  margin-bottom: 4px;
}
.signal-list-item.active.signal-outside { background: var(--bg-info) !important; border-color: var(--border-info); }
.signal-list-name {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-primary);
  margin-bottom: 2px;
}
.signal-list-sub {
  font-size: 11px;
  color: var(--text-tertiary);
}
.signal-boundary-line {
  font-size: 11px;
  color: var(--text-tertiary);
  text-align: center;
  padding: 8px 0;
  border-top: 0.5px dashed var(--border-tertiary);
  border-bottom: 0.5px dashed var(--border-tertiary);
  margin: 4px 0;
}
.signal-panel {
  padding: 20px 22px;
  background: var(--bg-secondary);
  border-radius: var(--radius-lg);
  min-height: 300px;
}
.sp-title { font-size: 20px; font-weight: 500; margin: 0 0 4px; }
.sp-def { font-size: 14px; color: var(--text-secondary); margin: 0 0 20px; line-height: 1.6; }
.sp-section { margin-bottom: 16px; }
.sp-label {
  font-size: 11px;
  font-weight: 500;
  color: var(--text-tertiary);
  letter-spacing: 0.4px;
  text-transform: uppercase;
  margin-bottom: 8px;
}
.sp-body { font-size: 13px; color: var(--text-primary); line-height: 1.65; }
.sp-loc-row {
  display: flex;
  gap: 10px;
  align-items: flex-start;
  margin-bottom: 8px;
}
.sp-loc-tag {
  flex-shrink: 0;
  font-size: 11px;
  padding: 3px 10px;
  border-radius: 10px;
  background: var(--bg-tertiary);
  color: var(--text-secondary);
  font-weight: 500;
  margin-top: 1px;
}
.sp-loc-desc { font-size: 13px; color: var(--text-secondary); line-height: 1.55; }
.sp-divider { border: none; border-top: 0.5px solid var(--border-tertiary); margin: 16px 0; }

@media (max-width: 900px) {
  .ch2-split { grid-template-columns: 1fr; }
  .ch2-left { position: static; }
}
.note-box {
  margin: 16px 0;
  padding: 12px 16px;
  background: var(--bg-info);
  border-radius: var(--radius-md);
  color: var(--text-info);
  font-size: 13px;
  line-height: 1.6;
  max-width: 900px;
}

/* Width constraints for readability */
.lead { max-width: 900px; }
.ch1-intro { max-width: 1100px; }

/* Chapter 3 viz constraints */
.chapter[data-chapter="ch4"] > svg.cmap-svg {
  max-width: 820px;
  display: block;
  margin: 16px 0;
}
.chapter[data-chapter="ch4"] .cmap-grid2 {
  max-width: 1000px;
}


@media (max-width: 900px) {
  .app { flex-direction: column; }
  .sidebar { 
    width: 100%; 
    height: auto; 
    position: relative;
    padding: 16px 20px;
  }
  .sidebar nav { flex-direction: row; flex-wrap: wrap; }
  .nav-item { flex: 1; min-width: 0; text-align: center; }
  main { padding: 24px 20px; max-width: 100%; }
  .ch1-split { grid-template-columns: 1fr; }
  .ch1-left { position: static; }
  .cmap-grid3, .cmap-grid2 { grid-template-columns: 1fr; }
}
/* Chapter 4 — augmentation tabs + maps */
.aug-tabs {
  display: flex;
  gap: 8px;
  margin: 24px 0 20px;
}
.aug-tab {
  padding: 10px 20px;
  border-radius: var(--radius-md);
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  border: 0.5px solid var(--border-tertiary);
  background: var(--bg-secondary);
  color: var(--text-secondary);
  transition: background 0.15s, color 0.15s;
}
.aug-tab:hover { background: var(--bg-tertiary); color: var(--text-primary); }
.aug-tab.active { background: var(--bg-info); color: var(--text-info); border-color: var(--border-info); }
.aug-map { display: none; }
.aug-map.active { display: block; }
.aug-map-svg { width: 100%; height: auto; display: block; max-width: 920px; margin: 0 0 16px; }
/* ch4 split */
.ch4-split { display: grid; grid-template-columns: 1fr 320px; gap: 0; margin-top: 16px; }
.ch4-left { position: sticky; top: 0; height: fit-content; padding-right: 20px; }
.ch4-right { padding-left: 20px; border-left: 0.5px solid var(--border-tertiary); min-height: 300px; }
.aug-src { cursor: pointer; transition: opacity 0.12s; }
.aug-src:hover { opacity: 0.82; }
.aug-src-ring { opacity: 0; transition: opacity 0.12s; pointer-events: none; }
.aug-src.active .aug-src-ring { opacity: 1; }
@media (max-width: 900px) {
  .ch4-split { grid-template-columns: 1fr; }
  .ch4-right { border-left: none; border-top: 0.5px solid var(--border-tertiary); padding: 16px 0 0; margin-top: 16px; }
}
</style>
</head>
<body>

<div class="app">

<aside class="sidebar">
  <h1>글로서리 스터디</h1>
  <p class="lead-mini">엔지니어링 결정의 토대가 될 개념 정리.</p>
  <nav>
    <a class="nav-item active" data-chapter="ch1">1. 의미 레이어의 구성요소</a>
    <a class="nav-item" data-chapter="ch2">2. 카탈로그의 구성과 관계</a>
    <a class="nav-item" data-chapter="ch3">3. 의미 레이어를 채우는 소스</a>
    <a class="nav-item" data-chapter="ch4">4. DB 정보 증강</a>
  </nav>
</aside>

<main>

<section data-chapter="ch1" class="chapter active">
  <h2>1. 의미 레이어와 카탈로그</h2>
  <p class="lead">의미 레이어는 데이터가 가져야 할 의미의 총체다. 데이터 자체에는 없으며, 카탈로그·코드·BI·협업 도구 등에 분산되어 있다. 일부는 채워져 있고 일부는 비어있다.</p>

  <svg class="cmap-svg" viewBox="0 0 760 430" role="img" style="max-width:920px;margin:8px 0 32px">
    <title>의미 레이어 풍경도</title>
    <desc>의미 레이어는 카탈로그·코드·BI·협업 도구에 분산되어 있고, 비어있는 부분을 채우면 AI-ready data가 됨</desc>
    <defs>
      <marker id="arrow-c1" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
        <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
      </marker>
    </defs>

    <g class="c-gray">
      <rect x="20" y="20" width="720" height="260" rx="14" stroke-width="0.5"/>
      <text class="th" x="40" y="48" dominant-baseline="central">의미 레이어</text>
      <text class="ts" x="40" y="66" dominant-baseline="central">데이터가 가져야 할 의미의 총체 · 일부는 채워져 있고 일부는 비어있음</text>
    </g>

    <g class="c-blue">
      <rect x="44" y="92" width="200" height="160" rx="8" stroke-width="0.5"/>
      <text class="th" x="144" y="124" text-anchor="middle" dominant-baseline="central">카탈로그</text>
      <text class="ts" x="144" y="148" text-anchor="middle" dominant-baseline="central">공식적으로 관리되는</text>
      <text class="ts" x="144" y="166" text-anchor="middle" dominant-baseline="central">의미</text>
      <text class="ts" x="144" y="208" text-anchor="middle" dominant-baseline="central">Atlan, DataHub 등</text>
    </g>

    <g class="c-coral">
      <rect x="264" y="92" width="156" height="160" rx="8" stroke-width="0.5" stroke-dasharray="4 4"/>
      <text class="th" x="342" y="124" text-anchor="middle" dominant-baseline="central">코드</text>
      <text class="ts" x="342" y="148" text-anchor="middle" dominant-baseline="central">비형식 의미</text>
      <text class="ts" x="342" y="166" text-anchor="middle" dominant-baseline="central">분산 상태</text>
      <text class="ts" x="342" y="208" text-anchor="middle" dominant-baseline="central">ORM, Enum, i18n</text>
    </g>

    <g class="c-purple">
      <rect x="440" y="92" width="140" height="160" rx="8" stroke-width="0.5" stroke-dasharray="4 4"/>
      <text class="th" x="510" y="124" text-anchor="middle" dominant-baseline="central">BI 도구</text>
      <text class="ts" x="510" y="148" text-anchor="middle" dominant-baseline="central">비형식 의미</text>
      <text class="ts" x="510" y="166" text-anchor="middle" dominant-baseline="central">분산 상태</text>
      <text class="ts" x="510" y="208" text-anchor="middle" dominant-baseline="central">대시보드·쿼리</text>
    </g>

    <g class="c-amber">
      <rect x="600" y="92" width="116" height="160" rx="8" stroke-width="0.5" stroke-dasharray="4 4"/>
      <text class="th" x="658" y="124" text-anchor="middle" dominant-baseline="central">협업</text>
      <text class="ts" x="658" y="148" text-anchor="middle" dominant-baseline="central">비형식 의미</text>
      <text class="ts" x="658" y="166" text-anchor="middle" dominant-baseline="central">분산 상태</text>
      <text class="ts" x="658" y="208" text-anchor="middle" dominant-baseline="central">Slack, Wiki</text>
    </g>

    <line x1="380" y1="290" x2="380" y2="342" stroke="#5f5e5a" stroke-width="1.2" fill="none" marker-end="url(#arrow-c1)"/>
    <text class="ts" x="390" y="320" dominant-baseline="central">비어있는 의미를 채우는 작업</text>

    <g class="c-gray">
      <rect x="180" y="348" width="400" height="64" rx="8" stroke-width="0.5"/>
      <text class="th" x="380" y="372" text-anchor="middle" dominant-baseline="central">AI-ready data</text>
      <text class="ts" x="380" y="392" text-anchor="middle" dominant-baseline="central">에이전트가 컨텍스트를 갖고 데이터를 활용할 수 있는 상태</text>
    </g>
  </svg>

  <h3 class="section-h">의미 레이어</h3>
  <p>데이터에 부여될 수 있는 의미의 총체. 컬럼이 어떤 비즈니스 개념을 표현하는지, 어떤 값들이 가능한지, 어디서 왔고 누가 책임지는지 등을 포함한다. 데이터 안에 있지 않고 별도로 존재하며, 일부는 명시적으로 기록되어 있고 일부는 비어있다.</p>

  <h3 class="section-h">카탈로그</h3>
  <p>의미 레이어를 공식적으로 관리하는 시스템. Atlan, DataHub, Collibra 등의 도구가 해당된다. Glossary, Domain, Asset, Lineage, Classification 같은 구성요소로 의미를 정형화해 저장한다. 의미 레이어 전체와 동일하지 않으며 부분집합이다.</p>

  <h3 class="section-h">분산된 의미</h3>
  <p>카탈로그 외에 코드(ORM, Enum, i18n, 변수명, 주석), BI 도구(대시보드·쿼리 사용 패턴), 협업 도구(Slack, Jira, Confluence 등)에 존재한다. 형식화되지 않은 상태로 흩어져 있어 통합 접근에는 별도 작업이 필요하다.</p>

  <h3 class="section-h">AI-ready data</h3>
  <p>의미 레이어의 비어있는 부분을 채워서 에이전트가 데이터를 활용할 수 있는 상태로 만든 결과. DB 컬럼의 형태만으로는 에이전트가 의미를 파악할 수 없으므로, 의미가 정형화되어 있어야 한다.</p>
</section>

<section data-chapter="ch2" class="chapter">
  <h2>2. 카탈로그의 구성과 관계</h2>
  <p class="lead">박스를 클릭하면 우측 패널에 정의·관련 개념·보조 시각화가 나타남.</p>
  
  <div class="ch1-intro">
    글로서리는 회사 내부 비즈니스 의미의 single source of truth. 부서·시간에 따라 같은 단어가 다르게 쓰이는 <span>semantic drift</span>를 막기 위해 존재.
  </div>
  
  <div class="ch1-split">
    <div class="ch1-left">
      <svg class="cmap-svg" viewBox="0 0 680 380" role="img">
        <title>글로서리 개념 지도</title>
        <desc>의미 레이어와 조직 레이어가 Asset에서 만나는 관계도</desc>
        <defs>
          <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </marker>
        </defs>
        
        <text class="ts" x="125" y="22" text-anchor="middle" dominant-baseline="central">의미 레이어</text>
        <text class="ts" x="555" y="22" text-anchor="middle" dominant-baseline="central">조직 레이어</text>
        
        <g class="node c-blue" data-id="glossary" onclick="showConcept('glossary')">
          <rect x="50" y="50" width="150" height="50" rx="8" stroke-width="0.5"/>
          <text class="th" x="125" y="78" text-anchor="middle" dominant-baseline="central">Glossary</text>
        </g>
        <line x1="125" y1="100" x2="125" y2="135" class="arr" marker-end="url(#arrow)"/>
        
        <g class="node c-blue" data-id="category" onclick="showConcept('category')">
          <rect x="50" y="135" width="150" height="50" rx="8" stroke-width="0.5"/>
          <text class="th" x="125" y="163" text-anchor="middle" dominant-baseline="central">Category</text>
        </g>
        <line x1="125" y1="185" x2="125" y2="220" class="arr" marker-end="url(#arrow)"/>
        
        <g class="node c-blue" data-id="term" onclick="showConcept('term')">
          <rect x="50" y="220" width="150" height="50" rx="8" stroke-width="0.5"/>
          <text class="th" x="125" y="248" text-anchor="middle" dominant-baseline="central">Term</text>
        </g>
        
        <g class="node c-coral" data-id="domain" onclick="showConcept('domain')">
          <rect x="480" y="50" width="150" height="50" rx="8" stroke-width="0.5"/>
          <text class="th" x="555" y="78" text-anchor="middle" dominant-baseline="central">Domain</text>
        </g>
        <line x1="555" y1="100" x2="555" y2="135" class="arr" marker-end="url(#arrow)"/>
        
        <g class="node c-coral" data-id="subdomain" onclick="showConcept('subdomain')">
          <rect x="480" y="135" width="150" height="50" rx="8" stroke-width="0.5"/>
          <text class="th" x="555" y="163" text-anchor="middle" dominant-baseline="central">Subdomain</text>
        </g>
        
        <g class="node c-gray" data-id="asset" onclick="showConcept('asset')">
          <rect x="265" y="295" width="150" height="60" rx="8" stroke-width="0.5"/>
          <text class="th" x="340" y="316" text-anchor="middle" dominant-baseline="central">Asset</text>
          <text class="ts" x="340" y="335" text-anchor="middle" dominant-baseline="central">컬럼·테이블·대시보드</text>
        </g>
        
        <path d="M200 245 Q 235 280 268 297" stroke="#185FA5" stroke-width="1.2" fill="none" marker-end="url(#arrow)"/>
        <g class="node c-blue" data-id="link" onclick="showConcept('link')">
          <rect x="207" y="263" width="44" height="20" rx="10" stroke-width="0.5"/>
          <text class="ts" x="229" y="276" text-anchor="middle" dominant-baseline="central">link</text>
        </g>
        <text class="ts" x="263" y="290">N:N</text>
        
        <path d="M480 175 Q 445 240 415 297" stroke="#993C1D" stroke-width="1" stroke-dasharray="4 4" fill="none" marker-end="url(#arrow)"/>
        <text class="ts" x="430" y="252">belongs</text>
        <text class="ts" x="448" y="280">1:1</text>
      </svg>
    </div>
    
    <div class="ch1-right">
      <div class="cmap-panel" id="cmap-desc"></div>
      <div class="cmap-supplement" id="cmap-supplement"></div>
    </div>
  </div>
</section>

<section data-chapter="ch3" class="chapter">
  <h2>3. 의미 레이어를 채우는 소스</h2>
  <p class="lead">의미 레이어를 채우기 위한 신호는 다양한 소스에 분산되어 있다. 소스를 선택하면 어떤 신호를 얻을 수 있고, 그걸로 DB에 대해 어떤 정보를 증강할 수 있는지 나타남.</p>

  <div class="ch2-split">
    <div class="ch2-left">
      <div class="signal-list">
        <div class="signal-list-item" data-signal="db" onclick="showSignal('db')">
          <div class="signal-list-name">DB</div>
          <div class="signal-list-sub">항상 존재</div>
        </div>
        <div class="signal-list-item" data-signal="catalog" onclick="showSignal('catalog')">
          <div class="signal-list-name">Catalog</div>
          <div class="signal-list-sub">선택적</div>
        </div>

        <div class="signal-boundary-line">── 통합 필요 ──</div>

        <div class="signal-list-item signal-outside" data-signal="code" onclick="showSignal('code')">
          <div class="signal-list-name">Code</div>
          <div class="signal-list-sub">Git 저장소</div>
        </div>
        <div class="signal-list-item signal-outside" data-signal="bi" onclick="showSignal('bi')">
          <div class="signal-list-name">BI Tools</div>
          <div class="signal-list-sub">Looker · Tableau 등</div>
        </div>
        <div class="signal-list-item signal-outside" data-signal="collaboration" onclick="showSignal('collaboration')">
          <div class="signal-list-name">Collaboration</div>
          <div class="signal-list-sub">Slack · Jira · Confluence</div>
        </div>
      </div>
    </div>

    <div class="ch2-right">
      <div class="signal-panel" id="signal-panel"></div>
    </div>
  </div>

</section>

<section data-chapter="ch4" class="chapter">
  <h2>4. DB 정보 증강</h2>
  <p class="lead">증강 대상마다 탐색하는 소스와 그로부터 알 수 있는 것이 다르다. 탐색 결과(raw)와 그 해석(의미)이 합쳐져 하나의 결과가 만들어진다.</p>

  <div class="aug-tabs">
    <button class="aug-tab active" data-map="desc">컬럼 Description</button>
    <button class="aug-tab" data-map="term">Glossary Term</button>
    <button class="aug-tab" data-map="cls">Classification</button>
  </div>

  <div class="ch4-split">
    <div class="ch4-left">

      <!-- Map 1: Description -->
      <div class="aug-map active" id="aug-map-desc">
        <svg class="cmap-svg" style="max-width:100%;margin:16px 0" viewBox="0 0 880 400" role="img">
          <title>컬럼 Description 증강 — TAX_EXMP_FLG</title>
          <defs>
            <marker id="ad" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
              <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </marker>
          </defs>
          <g class="aug-src" data-map="desc" data-src="db">
            <g class="c-gray"><rect x="15" y="15" width="225" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="127" y="32" text-anchor="middle" dominant-baseline="central">DB</text>
            <line x1="25" y1="44" x2="230" y2="44" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="27" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="27" y="72" dominant-baseline="central">TAX_EXMP_FLG</text>
            <text class="ts" x="27" y="87" dominant-baseline="central">CHAR(1) · LOAN_APPL_HIST</text>
            <line x1="25" y1="100" x2="230" y2="100" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="27" y="113" dominant-baseline="central">의미</text>
            <text class="t" x="27" y="128" dominant-baseline="central">세금 관련 단일 코드값</text>
            <text class="t" x="27" y="145" dominant-baseline="central">대출 신청 이력 테이블 속성</text>
            <rect class="aug-src-ring" x="12" y="12" width="231" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="desc" data-src="code">
            <g class="c-coral"><rect x="255" y="15" width="280" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="395" y="32" text-anchor="middle" dominant-baseline="central">Code</text>
            <line x1="265" y1="44" x2="525" y2="44" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="267" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="267" y="71" dominant-baseline="central">TaxExemption enum</text>
            <text class="ts" x="267" y="85" dominant-baseline="central">Y=면세 · N=과세</text>
            <text class="ts" x="267" y="99" dominant-baseline="central">P=부분면세 · X=해당없음</text>
            <line x1="265" y1="111" x2="525" y2="111" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="267" y="124" dominant-baseline="central">의미</text>
            <text class="t" x="267" y="139" dominant-baseline="central">4가지 세금 처리 상태 구분</text>
            <text class="t" x="267" y="156" dominant-baseline="central">각 값의 비즈니스 의미 확정</text>
            <rect class="aug-src-ring" x="252" y="12" width="286" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="desc" data-src="catalog">
            <g class="c-blue"><rect x="550" y="15" width="295" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="697" y="32" text-anchor="middle" dominant-baseline="central">Catalog</text>
            <line x1="560" y1="44" x2="835" y2="44" stroke="#185fa5" stroke-width="0.5"/>
            <text class="ts" x="562" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="562" y="72" dominant-baseline="central">LOAN 도메인</text>
            <text class="ts" x="562" y="87" dominant-baseline="central">lineage: 세금계산모듈 참조</text>
            <line x1="560" y1="100" x2="835" y2="100" stroke="#185fa5" stroke-width="0.5"/>
            <text class="ts" x="562" y="113" dominant-baseline="central">의미</text>
            <text class="t" x="562" y="128" dominant-baseline="central">대출 신청 처리의</text>
            <text class="t" x="562" y="145" dominant-baseline="central">세금 계산에 직접 활용</text>
            <rect class="aug-src-ring" x="547" y="12" width="301" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <line x1="127" y1="185" x2="420" y2="285" stroke="#888780" stroke-width="1" fill="none" marker-end="url(#ad)"/>
          <line x1="395" y1="185" x2="420" y2="285" stroke="#993c1d" stroke-width="1.5" fill="none" marker-end="url(#ad)"/>
          <line x1="697" y1="185" x2="420" y2="285" stroke="#185fa5" stroke-width="1" fill="none" marker-end="url(#ad)"/>
          <g class="aug-src" data-map="desc" data-src="result">
            <g class="c-gray"><rect x="115" y="290" width="610" height="95" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="420" y="315" text-anchor="middle" dominant-baseline="central">대출 신청 건의 세금 면제 상태 코드.</text>
            <text class="t" x="420" y="338" text-anchor="middle" dominant-baseline="central">Y=면세 · N=과세 · P=부분면세 · X=해당없음</text>
            <g class="c-blue">
              <rect x="340" y="357" width="160" height="22" rx="11" stroke-width="0.5"/>
              <text class="ts" x="420" y="368" text-anchor="middle" dominant-baseline="central">Confidence · High</text>
            </g>
            <rect class="aug-src-ring" x="112" y="287" width="616" height="103" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
        </svg>
      </div>

      <!-- Map 2: Glossary Term -->
      <div class="aug-map" id="aug-map-term">
        <svg class="cmap-svg" style="max-width:100%;margin:16px 0" viewBox="0 0 860 400" role="img">
          <title>Glossary Term 증강 — 세금면제</title>
          <defs>
            <marker id="at" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
              <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </marker>
          </defs>
          <g class="aug-src" data-map="term" data-src="code">
            <g class="c-coral"><rect x="15" y="15" width="255" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="142" y="32" text-anchor="middle" dominant-baseline="central">Code</text>
            <line x1="25" y1="44" x2="260" y2="44" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="27" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="27" y="72" dominant-baseline="central">TaxExemption 클래스</text>
            <text class="ts" x="27" y="87" dominant-baseline="central">3개 파일에서 반복 참조</text>
            <line x1="25" y1="100" x2="260" y2="100" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="27" y="113" dominant-baseline="central">의미</text>
            <text class="t" x="27" y="128" dominant-baseline="central">코드 전반에 걸쳐</text>
            <text class="t" x="27" y="145" dominant-baseline="central">반복되는 비즈니스 개념</text>
            <rect class="aug-src-ring" x="12" y="12" width="261" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="term" data-src="db">
            <g class="c-gray"><rect x="285" y="15" width="280" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="425" y="32" text-anchor="middle" dominant-baseline="central">DB</text>
            <line x1="295" y1="44" x2="555" y2="44" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="297" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="297" y="72" dominant-baseline="central">TAX_EXMP_FLG · _RSN_CD</text>
            <text class="ts" x="297" y="87" dominant-baseline="central">· TAX_EXMP_HIST (동일 prefix)</text>
            <line x1="295" y1="100" x2="555" y2="100" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="297" y="113" dominant-baseline="central">의미</text>
            <text class="t" x="297" y="128" dominant-baseline="central">여러 컬럼이</text>
            <text class="t" x="297" y="145" dominant-baseline="central">같은 개념 공유</text>
            <rect class="aug-src-ring" x="282" y="12" width="286" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="term" data-src="bi">
            <g class="c-purple"><rect x="580" y="15" width="260" height="170" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="710" y="32" text-anchor="middle" dominant-baseline="central">BI</text>
            <line x1="590" y1="44" x2="830" y2="44" stroke="#534ab7" stroke-width="0.5"/>
            <text class="ts" x="592" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="592" y="72" dominant-baseline="central">"세금면제율" 지표명</text>
            <line x1="590" y1="87" x2="830" y2="87" stroke="#534ab7" stroke-width="0.5"/>
            <text class="ts" x="592" y="100" dominant-baseline="central">의미</text>
            <text class="t" x="592" y="115" dominant-baseline="central">비즈니스 사용자가</text>
            <text class="t" x="592" y="132" dominant-baseline="central">실제로 쓰는 언어</text>
            <rect class="aug-src-ring" x="577" y="12" width="266" height="176" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <line x1="142" y1="185" x2="415" y2="285" stroke="#993c1d" stroke-width="1" fill="none" marker-end="url(#at)"/>
          <line x1="425" y1="185" x2="420" y2="285" stroke="#888780" stroke-width="1" fill="none" marker-end="url(#at)"/>
          <line x1="710" y1="185" x2="425" y2="285" stroke="#534ab7" stroke-width="1" fill="none" marker-end="url(#at)"/>
          <g class="aug-src" data-map="term" data-src="result">
            <g class="c-gray"><rect x="100" y="290" width="650" height="95" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="425" y="312" text-anchor="middle" dominant-baseline="central">Term "세금면제"</text>
            <text class="t" x="425" y="334" text-anchor="middle" dominant-baseline="central">정의: 특정 거래·항목에 세금이 부과되지 않는 상태</text>
            <text class="ts" x="425" y="355" text-anchor="middle" dominant-baseline="central">연결 자산: TAX_EXMP_FLG · TAX_EXMP_RSN_CD · TAX_EXMP_HIST</text>
            <rect class="aug-src-ring" x="97" y="287" width="656" height="101" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
        </svg>
      </div>

      <!-- Map 3: Classification -->
      <div class="aug-map" id="aug-map-cls">
        <svg class="cmap-svg" style="max-width:100%;margin:16px 0" viewBox="0 0 760 520" role="img">
          <title>Classification 증강 — CUST_EMAIL</title>
          <defs>
            <marker id="ac" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
              <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </marker>
          </defs>
          <g class="aug-src" data-map="cls" data-src="db">
            <g class="c-gray"><rect x="15" y="15" width="340" height="150" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="185" y="32" text-anchor="middle" dominant-baseline="central">DB</text>
            <line x1="25" y1="44" x2="345" y2="44" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="27" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="27" y="72" dominant-baseline="central">CUST_EMAIL · VARCHAR(100)</text>
            <line x1="25" y1="85" x2="345" y2="85" stroke="#888780" stroke-width="0.5"/>
            <text class="ts" x="27" y="98" dominant-baseline="central">의미</text>
            <text class="t" x="27" y="113" dominant-baseline="central">이메일 주소 컬럼</text>
            <rect class="aug-src-ring" x="12" y="12" width="346" height="156" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="cls" data-src="code">
            <g class="c-coral"><rect x="370" y="15" width="375" height="150" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="557" y="32" text-anchor="middle" dominant-baseline="central">Code</text>
            <line x1="380" y1="44" x2="735" y2="44" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="382" y="57" dominant-baseline="central">raw</text>
            <text class="ts" x="382" y="72" dominant-baseline="central">customerEmail 변수</text>
            <text class="ts" x="382" y="87" dominant-baseline="central">@PersonalInfo 어노테이션</text>
            <line x1="380" y1="100" x2="735" y2="100" stroke="#993c1d" stroke-width="0.5"/>
            <text class="ts" x="382" y="113" dominant-baseline="central">의미</text>
            <text class="t" x="382" y="128" dominant-baseline="central">개인정보로 코드에서 명시</text>
            <rect class="aug-src-ring" x="367" y="12" width="381" height="156" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <line x1="185" y1="165" x2="360" y2="200" stroke="#888780" stroke-width="1" fill="none" marker-end="url(#ac)"/>
          <line x1="557" y1="165" x2="400" y2="200" stroke="#993c1d" stroke-width="1" fill="none" marker-end="url(#ac)"/>
          <g class="c-gray">
            <rect x="230" y="205" width="300" height="50" rx="8" stroke-width="0.5"/>
            <text class="th" x="380" y="228" text-anchor="middle" dominant-baseline="central">PII 탐지 및 확정</text>
            <text class="ts" x="380" y="245" text-anchor="middle" dominant-baseline="central">CUST_EMAIL = PII 확정</text>
          </g>
          <line x1="380" y1="255" x2="380" y2="285" stroke="#888780" stroke-width="1" fill="none" marker-end="url(#ac)"/>
          <g class="aug-src" data-map="cls" data-src="catalog">
            <g class="c-blue"><rect x="190" y="290" width="380" height="120" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="380" y="308" text-anchor="middle" dominant-baseline="central">Catalog</text>
            <line x1="200" y1="319" x2="560" y2="319" stroke="#185fa5" stroke-width="0.5"/>
            <text class="ts" x="202" y="332" dominant-baseline="central">raw</text>
            <text class="ts" x="202" y="348" dominant-baseline="central">lineage: ETL → EMAIL_MASKED 파생</text>
            <line x1="200" y1="362" x2="560" y2="362" stroke="#185fa5" stroke-width="0.5"/>
            <text class="ts" x="202" y="375" dominant-baseline="central">의미</text>
            <text class="t" x="202" y="393" dominant-baseline="central">CUST_EMAIL에서 파생된 컬럼 존재</text>
            <rect class="aug-src-ring" x="187" y="287" width="386" height="126" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <line x1="380" y1="410" x2="380" y2="428" stroke="#888780" stroke-width="1" fill="none"/>
          <line x1="380" y1="428" x2="175" y2="428" stroke="#888780" stroke-width="1" fill="none"/>
          <line x1="380" y1="428" x2="585" y2="428" stroke="#888780" stroke-width="1" stroke-dasharray="4 3" fill="none"/>
          <line x1="175" y1="428" x2="175" y2="445" stroke="#888780" stroke-width="1" fill="none" marker-end="url(#ac)"/>
          <line x1="585" y1="428" x2="585" y2="445" stroke="#888780" stroke-width="1" stroke-dasharray="4 3" fill="none" marker-end="url(#ac)"/>
          <g class="aug-src" data-map="cls" data-src="out_confirmed">
            <g class="c-blue"><rect x="30" y="448" width="290" height="55" rx="8" stroke-width="0.5"/></g>
            <text class="th" x="175" y="467" text-anchor="middle" dominant-baseline="central">CUST_EMAIL</text>
            <text class="ts" x="175" y="486" text-anchor="middle" dominant-baseline="central">PII 확정 · 즉시 부여 (Link 통해)</text>
            <rect class="aug-src-ring" x="27" y="445" width="296" height="61" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
          <g class="aug-src" data-map="cls" data-src="out_candidate">
            <g class="c-amber"><rect x="440" y="448" width="290" height="55" rx="8" stroke-width="0.5" stroke-dasharray="4 3"/></g>
            <text class="th" x="585" y="467" text-anchor="middle" dominant-baseline="central">EMAIL_MASKED</text>
            <text class="ts" x="585" y="486" text-anchor="middle" dominant-baseline="central">PII 후보 · 검토 필요 (Lineage 통해)</text>
            <rect class="aug-src-ring" x="437" y="445" width="296" height="61" rx="10" fill="none" stroke="var(--border-info)" stroke-width="2"/>
          </g>
        </svg>
      </div>

    </div><!-- /ch4-left -->

    <div class="ch4-right">
      <div id="ch4-panel">
        <p class="sp-def" style="color:var(--text-tertiary);margin-top:24px">소스 박스를 클릭하면 상세 내용이 표시됩니다.</p>
      </div>
    </div>
  </div>

</section>


</main>
</div>

<script>
const cmapData = {
  glossary: {
    title: "Glossary",
    def: "비즈니스 용어들의 공식 모음. 의미의 single source of truth 역할.",
    properties: [
      "Owner (비즈니스 담당자 — 정의의 최종 승인 권한) 와 Steward (실무 운영자 — 정의를 실제로 작성·유지·갱신) 가 함께 관리",
      "도메인별로 특화 가능 — Finance 팀은 'ARR', 'EBITDA' 같은 재무 어휘, HR 팀은 'Headcount', 'FTE' 같은 인사 어휘에 집중한 Glossary를 따로 운영할 수 있음",
      "한 회사 안에 여러 개 공존 가능 (federation) — Finance Glossary의 'Customer'와 HR Glossary의 'Client'는 별개의 Term이지만 synonym 관계로 연결할 수 있음. Term을 공유하는 게 아니라 각자 정의하고 연결하는 구조."
    ],
    related: [
      { id: "category", verb: "포함" },
      { id: "term", verb: "간접 포함 — Category를 통해" },
      { id: "domain", verb: "병렬 존재 — 같은 트리 아님" }
    ],
    example: "Finance Glossary (ARR·EBITDA 등), HR Glossary (Headcount·FTE 등)"
  },
  category: {
    title: "Category",
    def: "한 Glossary 안에서 Term들을 묶는 분류 그룹. Term들을 탐색·관리하기 위한 구조.",
    properties: [
      "존재 이유 — Glossary 안에 Term이 수백 개면 flat하게 나열됐을 때 찾기 어려움. Category로 묶으면 'Finance > Loan > Collateral' 식으로 탐색 가능",
      "같은 Glossary 안에서만 존재 — Category는 독립 개념이 아니라 Glossary의 하위 구조. 다른 Glossary에 같은 이름의 Category가 있어도 별개",
      "계층적 (sub-category 가능) — 대분류 → 소분류로 깊게 나눌 수 있음. 단, 깊을수록 유지 비용 증가 → 보통 2-3 depth 권장"
    ],
    related: [
      { id: "glossary", verb: "속함" },
      { id: "term", verb: "포함" }
    ],
    example: "Finance Glossary 안의 'Customer 식별' (고객 관련 Term 묶음), 'Loan 운영' (대출 관련 Term 묶음)"
  },
  term: {
    title: "Term",
    def: "비즈니스 개념을 나타내는 글로서리의 기본 단위.",
    properties: [
      "하나의 Category에 속함 — Category → Glossary로 이어지므로, Term의 귀속 Glossary는 항상 하나",
      "다층 객체 — 정체성+의미 / 맥락+연결 / 거버넌스 세 그룹의 facet",
      "거버넌스 속성: Status (Draft/Verified/Deprecated), Classification (PII 등), Owner",
      "다른 Term과 관계: synonym, antonym, preferred, valid_values — 다른 Glossary의 Term과도 연결 가능"
    ],
    related: [
      { id: "category", verb: "속함" },
      { id: "asset", verb: "link로 연결" }
    ],
    example: "'Customer ID', 'Loan Status', 'MAU'"
  },
  domain: {
    title: "Domain",
    def: "비즈니스 책임·관심사를 묶는 조직 단위. '이 데이터가 누구 것인가'를 정의.",
    properties: [
      "팀·그룹이 소유권(ownership)을 가짐 — 'Finance Domain'의 데이터 정의·품질에 대해 Finance 팀이 책임. Data Mesh 아키텍처의 핵심 원칙",
      "Glossary와 평행한 별개 축 — Glossary는 '이 단어가 무슨 뜻인가 (의미)', Domain은 '이 데이터가 누구 것인가 (소유)'. 같은 Asset에 둘 다 적용됨",
      "계층적 (subdomain 가능) — Finance → Loan, Customer, Risk처럼 하위 단위로 나눌 수 있음",
      "Asset은 한 Domain에만 속함 (1:1) — 책임 소재를 명확히 하기 위한 원칙. 두 부서가 공동 소유하면 책임 경계가 흐려지기 때문. 보통 테이블 단위로 매핑하고 컬럼은 상속",
      "belongs 설정 방식 — 주로 데이터 스튜어드가 수동으로 테이블에 Domain을 지정. 일부 카탈로그는 테이블 명명 규칙 기반으로 자동 제안 가능 (예: 'fin_' 접두사 → Finance Domain)",
      "변경 시 동작 — Domain 재지정 시 해당 테이블 하위 컬럼 전체가 자동으로 새 Domain을 상속. Link의 Tag Propagation과 달리 검토 없이 즉시 반영."
    ],
    related: [
      { id: "subdomain", verb: "포함" },
      { id: "asset", verb: "Asset이 belongs to" }
    ],
    example: "Finance Domain (재무 데이터 소유), Marketing Domain (캠페인·고객 데이터 소유)"
  },
  subdomain: {
    title: "Subdomain",
    def: "Domain 하위의 세분화된 책임 단위.",
    properties: [
      "존재 이유 — Domain이 너무 크면 소유권이 모호해짐. Subdomain으로 쪼개면 팀 내 더 작은 단위(스쿼드·챕터)가 실질적으로 책임지게 됨",
      "Asset 귀속 — Asset은 Domain 대신 Subdomain에 직접 매핑되기도 함 (카탈로그마다 다름). Subdomain이 없는 경우 Domain에 직접 매핑",
      "깊이 — Domain → Subdomain 2 depth가 일반적. 더 깊은 계층은 관리 비용 대비 효과 낮음",
      "belongs 설정 방식 — Domain과 동일하게 스튜어드가 수동 지정. Subdomain 재구성 시 (예: 팀 분리·합병) Asset 일괄 재지정 필요 — 조직 변경과 데이터 소유권 변경이 연동되는 지점",
      "변경 시 동작 — Subdomain 변경 시 하위 컬럼 전체 자동 재상속. Domain 변경과 동일한 즉시 반영 방식."
    ],
    related: [
      { id: "domain", verb: "속함" },
      { id: "asset", verb: "Asset이 belongs to" }
    ],
    example: "Finance Domain 안의 'Loan' (대출 관련 테이블 담당), 'Customer' (고객 데이터 담당), 'Risk' (리스크 지표 담당)"
  },
  asset: {
    title: "Asset",
    def: "카탈로그가 추적하는 모든 1급 객체. 테이블·컬럼·대시보드·쿼리·모델 등 광범위.",
    properties: [
      "DB 자산 — Database, Schema, Table, View, Column (가장 기본. 자체 hierarchy: DB → Schema → Table → Column)",
      "분석 자산 — Dashboard, Chart, Report, Metric (BI 도구에서 생성되는 결과물)",
      "파이프라인 자산 — Query, dbt Model, Airflow DAG, Job (데이터를 만들고 변환하는 코드·작업)",
      "기타 — API, File, ML Feature (카탈로그마다 지원 범위 다름)",
      "메타데이터 3종 — Description (짧음, 모든 자산) / README (긴 맥락, 일부) / Classification (PII 등, propagation으로 받음)",
      "다른 Asset과 관계: lineage, parent-child, reference",
      "Domain 매핑은 보통 테이블 단위 + 컬럼은 상속 (Domain panel 참고)"
    ],
    related: [
      { id: "term", verb: "link로 연결 (의미 부여)" },
      { id: "domain", verb: "belongs to (1:1 소속)" },
      { id: "link", verb: "Term과의 연결 메커니즘" }
    ],
    example: "customer_id 컬럼, LOAN_APPL_HIST 테이블, Sales Dashboard"
  },
  link: {
    title: "Link (Term ↔ Asset)",
    def: "Term과 Asset을 잇는 그래프 엣지. 단순 표시가 아니라 메타데이터를 가진 객체.",
    properties: [
      "양방향, N:N cardinality — 하나의 Term이 여러 Asset에, 하나의 Asset이 여러 Term에 연결될 수 있음",
      "메타데이터 — timestamp (언제 연결됐는지), confidence (얼마나 확신하는지), 생성자 (사람·룰·AI 중 어떻게 만들어졌는지)",
      "4가지 생성 방식 — ① 수동: 사람이 직접 Term-Asset 선택 / ② 룰 기반: 컬럼명 패턴 매칭 (예: '_id' 접미사 → ID term) / ③ AI 기반: LLM이 컬럼 컨텍스트 분석해서 term 추천 / ④ Bulk import: CSV·API로 일괄 등록",
      "Confidence 등급 — High: 패턴·정의가 명확해서 확신 / Medium: 일부 추론 포함 / Low: 컨텍스트 부족, 약어 해석 불확실. 낮을수록 사람 검토 우선순위 높음",
      "핵심 효과 — Tag Propagation: Term의 분류(예: PII)가 link된 Asset으로 자동 전파. lineage 따라 downstream Asset에도 전파 후보 생성"
    ],
    related: [
      { id: "term", verb: "한쪽 끝" },
      { id: "asset", verb: "다른 쪽 끝" }
    ],
    example: "'Customer ID' term ↔ users.id 컬럼 (AI 생성, Confidence High)"
  }
};

const supplements = {
  glossary: () => `
    <div class="cmap-supplement-label">관련 개념 비교</div>
    <div class="cmap-grid3">
      <div class="cmap-card">
        <div class="cmap-card-title">데이터 딕셔너리</div>
        <div class="cmap-card-body">기술적 메타데이터 — 컬럼 타입, NULL 여부, 키 제약. 자동 추출 가능.</div>
      </div>
      <div class="cmap-card cmap-card-here">
        <div class="cmap-card-title">글로서리 ← 여기</div>
        <div class="cmap-card-body">비즈니스 의미 — 사람이 합의한 정의. 자동 추출 어려움.</div>
      </div>
      <div class="cmap-card">
        <div class="cmap-card-title">데이터 카탈로그</div>
        <div class="cmap-card-body">위 둘 + lineage + 거버넌스 + 검색. 통합 시스템.</div>
      </div>
    </div>
  `,
  term: () => `
    <div class="cmap-supplement-label">Term의 다층 구조 — 3 그룹의 facet</div>
    <div class="cmap-grid3">
      <div class="cmap-card">
        <div class="cmap-card-title">정체성 + 의미</div>
        <ul class="cmap-card-list">
          <li>Name (이름)</li>
          <li>Aliases (별칭)</li>
          <li>ID (영구 식별자)</li>
          <li>Definition (짧은 정의)</li>
          <li>README (긴 맥락)</li>
          <li>Source (출처·권위)</li>
        </ul>
      </div>
      <div class="cmap-card">
        <div class="cmap-card-title">맥락 + 연결</div>
        <ul class="cmap-card-list">
          <li>Category 소속</li>
          <li>Related Terms<br>&nbsp;&nbsp;(synonym, antonym,<br>&nbsp;&nbsp;preferred, valid_values)</li>
          <li>Linked Assets</li>
          <li>Linked Data Products</li>
        </ul>
      </div>
      <div class="cmap-card">
        <div class="cmap-card-title">거버넌스</div>
        <ul class="cmap-card-list">
          <li>Owner (의미 권위자)</li>
          <li>Steward (운영 관리자)</li>
          <li>Status<br>&nbsp;&nbsp;(Draft / Verified /<br>&nbsp;&nbsp;Deprecated)</li>
          <li>Classification (PII 등)</li>
          <li>Tags</li>
        </ul>
      </div>
    </div>
    <div class="cmap-supplement-divider">
      <div class="cmap-supplement-label">Term들 사이의 관계 — Glossary 트리를 가로지르는 cross-edge</div>
      <svg class="cmap-svg" viewBox="0 0 680 200" role="img">
        <defs>
          <marker id="arrow-tt" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </marker>
        </defs>
        <g class="c-blue">
          <rect x="80" y="20" width="120" height="36" rx="6" stroke-width="0.5"/>
          <text class="th" x="140" y="40" text-anchor="middle" dominant-baseline="central">Customer</text>
        </g>
        <line x1="200" y1="38" x2="320" y2="38" stroke="#185fa5" stroke-width="1" marker-end="url(#arrow-tt)" marker-start="url(#arrow-tt)"/>
        <text class="ts" x="260" y="30" text-anchor="middle">synonym</text>
        <g class="c-blue">
          <rect x="320" y="20" width="120" height="36" rx="6" stroke-width="0.5"/>
          <text class="th" x="380" y="40" text-anchor="middle" dominant-baseline="central">Client</text>
        </g>
        <g class="c-gray">
          <rect x="80" y="80" width="120" height="36" rx="6" stroke-width="0.5"/>
          <text class="th" x="140" y="100" text-anchor="middle" dominant-baseline="central">Cust (deprecated)</text>
        </g>
        <line x1="200" y1="98" x2="320" y2="98" stroke="#888780" stroke-width="1" stroke-dasharray="4 4" marker-end="url(#arrow-tt)"/>
        <text class="ts" x="260" y="90" text-anchor="middle">preferred over</text>
        <g class="c-blue">
          <rect x="320" y="80" width="120" height="36" rx="6" stroke-width="0.5"/>
          <text class="th" x="380" y="100" text-anchor="middle" dominant-baseline="central">Customer</text>
        </g>
        <g class="c-blue">
          <rect x="80" y="140" width="120" height="36" rx="6" stroke-width="0.5"/>
          <text class="th" x="140" y="160" text-anchor="middle" dominant-baseline="central">Loan Status</text>
        </g>
        <line x1="200" y1="158" x2="320" y2="158" stroke="#3c3489" stroke-width="1" marker-end="url(#arrow-tt)"/>
        <text class="ts" x="260" y="150" text-anchor="middle">valid_values</text>
        <g class="c-purple">
          <rect x="320" y="140" width="280" height="36" rx="6" stroke-width="0.5"/>
          <text class="t" x="460" y="160" text-anchor="middle" dominant-baseline="central">Pending, Approved, Rejected</text>
        </g>
      </svg>
      <div style="font-size: 12px; color: var(--text-secondary); margin-top: 8px;">facet은 한 Term의 내부 구조, 위 관계들은 여러 Term을 가로지르는 그래프 엣지.</div>
    </div>
  `,
  domain: () => `
    <div class="cmap-supplement-label">테이블 단위 도메인 매핑 + 컬럼 상속</div>
    <svg class="cmap-svg" viewBox="0 0 680 140" role="img">
      <defs>
        <marker id="arrow-domain" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
          <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </marker>
      </defs>
      <g class="c-coral">
        <rect x="40" y="50" width="140" height="40" rx="6" stroke-width="0.5"/>
        <text class="th" x="110" y="70" text-anchor="middle" dominant-baseline="central">Domain: Loan</text>
      </g>
      <line x1="180" y1="70" x2="218" y2="70" stroke="#993C1D" stroke-width="1" stroke-dasharray="4 4" marker-end="url(#arrow-domain)"/>
      <text class="ts" x="199" y="62" text-anchor="middle">belongs</text>
      <g class="c-purple">
        <rect x="220" y="50" width="200" height="40" rx="6" stroke-width="0.5"/>
        <text class="th" x="320" y="70" text-anchor="middle" dominant-baseline="central">LOAN_APPL_HIST 테이블</text>
      </g>
      <line x1="320" y1="90" x2="320" y2="103" class="arr" marker-end="url(#arrow-domain)"/>
      <text class="ts" x="332" y="100" dominant-baseline="central">상속</text>
      <g class="c-purple">
        <rect x="220" y="105" width="200" height="25" rx="4" stroke-width="0.5"/>
        <text class="t" x="320" y="118" text-anchor="middle" dominant-baseline="central">customer_id, status_code, ...</text>
      </g>
      <text class="ts" x="445" y="80">테이블이 도메인에 명시 매핑되면</text>
      <text class="ts" x="445" y="100">컬럼들은 그 도메인을 상속</text>
    </svg>
    <div style="font-size: 12px; color: var(--text-secondary); margin-top: 8px;">컬럼이 직접 도메인을 갖지 않고 부모 테이블에서 받는다 — 도메인 변경 시 일관성 유지의 핵심.</div>
  `,
  subdomain: () => `
    <div class="cmap-supplement-label">Domain 트리의 한 노드</div>
    <svg class="cmap-svg" viewBox="0 0 680 140" role="img">
      <defs>
        <marker id="arrow-sub" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
          <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </marker>
      </defs>
      <g class="c-coral">
        <rect x="240" y="20" width="200" height="40" rx="6" stroke-width="0.5"/>
        <text class="th" x="340" y="40" text-anchor="middle" dominant-baseline="central">Domain: Finance</text>
      </g>
      <line x1="290" y1="60" x2="170" y2="90" class="arr" marker-end="url(#arrow-sub)"/>
      <line x1="340" y1="60" x2="340" y2="90" class="arr" marker-end="url(#arrow-sub)"/>
      <line x1="390" y1="60" x2="510" y2="90" class="arr" marker-end="url(#arrow-sub)"/>
      <g class="c-coral">
        <rect x="80" y="92" width="180" height="36" rx="6" stroke-width="0.5"/>
        <text class="th" x="170" y="111" text-anchor="middle" dominant-baseline="central">Subdomain: Loan</text>
      </g>
      <g class="c-coral">
        <rect x="280" y="92" width="120" height="36" rx="6" stroke-width="0.5"/>
        <text class="th" x="340" y="111" text-anchor="middle" dominant-baseline="central">Customer</text>
      </g>
      <g class="c-coral">
        <rect x="420" y="92" width="180" height="36" rx="6" stroke-width="0.5"/>
        <text class="th" x="510" y="111" text-anchor="middle" dominant-baseline="central">Subdomain: Risk</text>
      </g>
    </svg>
    <div style="font-size: 12px; color: var(--text-secondary); margin-top: 8px;">Domain은 트리 구조 — Subdomain이 sub-category처럼 작동하지만 Glossary와는 별개의 트리.</div>
  `,
  asset: () => `
    <div class="cmap-supplement-label">Asset의 메타데이터 3종</div>
    <div class="cmap-grid3">
      <div class="cmap-card">
        <div class="cmap-card-title">Description</div>
        <div class="cmap-card-body">짧은 정의. 모든 자산 필요. 자동 생성 가능.</div>
      </div>
      <div class="cmap-card">
        <div class="cmap-card-title">README</div>
        <div class="cmap-card-body">긴 운영 맥락. tribal knowledge. 일부 자산만.</div>
      </div>
      <div class="cmap-card">
        <div class="cmap-card-title">Classification</div>
        <div class="cmap-card-body">PII·기밀 등. propagation으로 자동 부여.</div>
      </div>
    </div>
    <div class="cmap-supplement-divider">
      <div class="cmap-supplement-label">Asset 간 관계 — 자산들은 자기들끼리도 그래프</div>
      <svg class="cmap-svg" viewBox="0 0 680 200" role="img">
        <defs>
          <marker id="arrow-ax" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
            <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </marker>
        </defs>
        <g class="c-purple">
          <rect x="20" y="40" width="140" height="40" rx="6" stroke-width="0.5"/>
          <text class="th" x="90" y="60" text-anchor="middle" dominant-baseline="central">Schema PUBLIC</text>
        </g>
        <line x1="160" y1="60" x2="218" y2="60" class="arr" marker-end="url(#arrow-ax)"/>
        <text class="ts" x="189" y="52" text-anchor="middle">contains</text>
        <g class="c-purple">
          <rect x="220" y="40" width="140" height="40" rx="6" stroke-width="0.5"/>
          <text class="th" x="290" y="60" text-anchor="middle" dominant-baseline="central">users 테이블</text>
        </g>
        <line x1="360" y1="60" x2="418" y2="60" stroke="#3B6D11" stroke-width="1" marker-end="url(#arrow-ax)"/>
        <text class="ts" x="389" y="52" text-anchor="middle" fill="#3B6D11">lineage</text>
        <g class="c-purple">
          <rect x="420" y="40" width="200" height="40" rx="6" stroke-width="0.5"/>
          <text class="th" x="520" y="60" text-anchor="middle" dominant-baseline="central">users_clean 테이블</text>
        </g>
        <line x1="290" y1="80" x2="290" y2="118" class="arr" marker-end="url(#arrow-ax)"/>
        <text class="ts" x="302" y="102" dominant-baseline="central">contains</text>
        <g class="c-purple">
          <rect x="220" y="120" width="140" height="36" rx="4" stroke-width="0.5"/>
          <text class="t" x="290" y="138" text-anchor="middle" dominant-baseline="central">users.email</text>
        </g>
        <line x1="290" y1="80" x2="180" y2="156" stroke="#993C1D" stroke-width="1" stroke-dasharray="3 3" marker-end="url(#arrow-ax)"/>
        <g class="c-coral">
          <rect x="40" y="158" width="180" height="36" rx="4" stroke-width="0.5"/>
          <text class="th" x="130" y="178" text-anchor="middle" dominant-baseline="central">User Activity Dashboard</text>
        </g>
        <text class="ts" x="155" y="118" text-anchor="middle" fill="#993C1D">referenced by</text>
      </svg>
      <div style="font-size: 12px; color: var(--text-secondary); margin-top: 8px;">3가지 관계: contains (parent-child), lineage (데이터 흐름), referenced (사용 관계). 단일 Asset이 아니라 자체 그래프.</div>
    </div>
  `,
  link: () => `
    <div class="cmap-supplement-label">Tag Propagation — 직접 link과 lineage propagation 두 단계</div>
    <svg class="cmap-svg" viewBox="0 0 680 280" role="img">
      <defs>
        <marker id="arrow-link" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
          <path d="M2 1L8 5L2 9" fill="none" stroke="context-stroke" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        </marker>
      </defs>
      <g class="c-blue">
        <rect x="240" y="15" width="200" height="50" rx="8" stroke-width="0.5"/>
        <text class="th" x="340" y="35" text-anchor="middle" dominant-baseline="central">"Email Address" Term</text>
        <text class="ts" x="340" y="53" text-anchor="middle" dominant-baseline="central">Classification: PII</text>
      </g>
      <line x1="285" y1="65" x2="180" y2="105" stroke="#A32D2D" stroke-width="1" marker-end="url(#arrow-link)"/>
      <line x1="340" y1="65" x2="340" y2="105" stroke="#A32D2D" stroke-width="1" marker-end="url(#arrow-link)"/>
      <line x1="395" y1="65" x2="500" y2="105" stroke="#A32D2D" stroke-width="1" marker-end="url(#arrow-link)"/>
      <text class="ts" x="100" y="90" fill="#A32D2D">① 직접 link 자산 → 자동 PII</text>
      <g class="c-purple">
        <rect x="80" y="110" width="180" height="40" rx="6" stroke-width="0.5"/>
        <text class="t" x="170" y="126" text-anchor="middle" dominant-baseline="central">users.email</text>
        <text class="ts" x="170" y="142" text-anchor="middle" dominant-baseline="central">PII (자동, 확정)</text>
      </g>
      <g class="c-purple">
        <rect x="270" y="110" width="140" height="40" rx="6" stroke-width="0.5"/>
        <text class="t" x="340" y="126" text-anchor="middle" dominant-baseline="central">orders.contact</text>
        <text class="ts" x="340" y="142" text-anchor="middle" dominant-baseline="central">PII (자동, 확정)</text>
      </g>
      <g class="c-purple">
        <rect x="420" y="110" width="180" height="40" rx="6" stroke-width="0.5"/>
        <text class="t" x="510" y="126" text-anchor="middle" dominant-baseline="central">leads.email</text>
        <text class="ts" x="510" y="142" text-anchor="middle" dominant-baseline="central">PII (자동, 확정)</text>
      </g>
      <line x1="170" y1="150" x2="170" y2="190" stroke="#3B6D11" stroke-width="1" marker-end="url(#arrow-link)"/>
      <line x1="340" y1="150" x2="340" y2="190" stroke="#3B6D11" stroke-width="1" marker-end="url(#arrow-link)"/>
      <line x1="510" y1="150" x2="510" y2="190" stroke="#3B6D11" stroke-width="1" marker-end="url(#arrow-link)"/>
      <text class="ts" x="100" y="175" fill="#3B6D11">② lineage 따라 derived 자산 → PII 후보 (추천)</text>
      <g class="c-amber">
        <rect x="80" y="195" width="180" height="40" rx="6" stroke-width="0.5" stroke-dasharray="4 4"/>
        <text class="t" x="170" y="211" text-anchor="middle" dominant-baseline="central">users_clean.email_hash</text>
        <text class="ts" x="170" y="227" text-anchor="middle" dominant-baseline="central">PII 후보 (검토 대기)</text>
      </g>
      <g class="c-amber">
        <rect x="270" y="195" width="140" height="40" rx="6" stroke-width="0.5" stroke-dasharray="4 4"/>
        <text class="t" x="340" y="211" text-anchor="middle" dominant-baseline="central">contact_anon</text>
        <text class="ts" x="340" y="227" text-anchor="middle" dominant-baseline="central">PII 후보 (검토 대기)</text>
      </g>
      <g class="c-amber">
        <rect x="420" y="195" width="180" height="40" rx="6" stroke-width="0.5" stroke-dasharray="4 4"/>
        <text class="t" x="510" y="211" text-anchor="middle" dominant-baseline="central">leads_marketing.lead_h</text>
        <text class="ts" x="510" y="227" text-anchor="middle" dominant-baseline="central">PII 후보 (검토 대기)</text>
      </g>
      <text class="ts" x="340" y="270" text-anchor="middle">① 직접 propagation은 즉시 적용. ② lineage propagation은 검토 후 확정.</text>
    </svg>
  `
};

function showConcept(id) {
  const c = cmapData[id];
  if (!c) return;
  document.querySelectorAll('.cmap-svg .node').forEach(n => n.classList.remove('active'));
  const el = document.querySelector('[data-id="' + id + '"]');
  if (el) el.classList.add('active');
  
  const props = c.properties.map(p => '<li>' + p + '</li>').join('');
  const rel = c.related.map(r => 
    '<li><span class="cmap-link" onclick="showConcept(\'' + r.id + '\')">' + cmapData[r.id].title + '</span> — ' + r.verb + '</li>'
  ).join('');
  
  document.getElementById('cmap-desc').innerHTML = 
    '<h3 class="cmap-title">' + c.title + '</h3>' +
    '<p class="cmap-def">' + c.def + '</p>' +
    '<div class="cmap-section"><div class="cmap-label">주요 속성</div><ul>' + props + '</ul></div>' +
    '<div class="cmap-section"><div class="cmap-label">관련 개념</div><ul>' + rel + '</ul></div>' +
    '<div class="cmap-section"><div class="cmap-label">예</div><p>' + c.example + '</p></div>';
  
  const supEl = document.getElementById('cmap-supplement');
  if (supplements[id]) {
    supEl.innerHTML = supplements[id]();
    supEl.style.display = 'block';
  } else {
    supEl.innerHTML = '';
    supEl.style.display = 'none';
  }
}

// Chapter navigation
document.querySelectorAll('.nav-item').forEach(item => {
  item.addEventListener('click', e => {
    e.preventDefault();
    const ch = item.dataset.chapter;
    document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
    document.querySelectorAll('.chapter').forEach(c => c.classList.remove('active'));
    item.classList.add('active');
    document.querySelector('.chapter[data-chapter="' + ch + '"]').classList.add('active');
    window.scrollTo({ top: 0, behavior: 'smooth' });
  });
});

// Signal data (Chapter 2) — source-based
const signalData = {
  db: {
    title: "DB",
    subtitle: "데이터베이스 시스템",
    access: "항상 존재",
    def: "모든 RDBMS가 기본으로 제공하는 시스템 카탈로그. information_schema, pg_catalog 등 — 별도 통합 없이 접근 가능.",
    signals: [
      "스키마 구조 — 테이블·컬럼 이름, 데이터 타입, NULL 허용 여부",
      "관계 정보 — PK, FK, 제약 조건",
      "명명 패턴 — `_dt`, `_cd`, `_id` 접미사에서 포맷 추론 가능",
      "쿼리 로그 — DB 레벨 로깅이 활성화된 경우 접근 패턴·빈도 수집 가능",
      "뷰·저장 프로시저 — 일부 변환 로직 포함"
    ],
    augments: "컬럼의 형태·관계, 명명 패턴으로 추론 가능한 포맷, 기본적인 사용 빈도 (로깅 활성화 시)",
    limits: "이름·타입만으론 의미 추론에 한계. 동음이의어·다의어 구분 불가 (`status`가 어떤 상태인지 알 수 없음). 비즈니스 맥락 없음. 단독으로는 가장 얕은 정보 수준."
  },
  catalog: {
    title: "Catalog",
    subtitle: "데이터 카탈로그 시스템",
    access: "선택적",
    def: "Atlan, DataHub, Collibra 등. DB 신호를 수집하고 다른 소스들을 통합해 enriched 메타데이터를 제공하는 레이어. 없는 회사도 많음.",
    signals: [
      "DB 구조 신호의 enriched 버전 (수동 description, 태그, classification 추가)",
      "SQL parsing으로 추출한 lineage 그래프 (upstream·downstream 관계)",
      "ETL 도구 메타데이터 통합 (Airflow, Glue 등)",
      "OpenLineage 등 표준 lineage 포맷",
      "Glossary term-asset 연결 (비즈니스 용어 매핑)",
      "PII 등 classification 및 tag propagation"
    ],
    augments: "lineage 관계, 비즈니스 glossary 연결, PII 등 분류 태그, 수동으로 작성된 description",
    limits: "카탈로그 시스템 자체가 없으면 해당 없음. 있어도 description 공백이 많음 — 이게 이 프로젝트의 출발점. lineage는 SQL parsing 가능한 범위로 제한됨."
  },
  code: {
    title: "Code",
    subtitle: "소스코드 저장소",
    access: "통합 필요",
    def: "Git 저장소에 담긴 애플리케이션 코드. 데이터를 다루는 코드 안에 비즈니스 의미가 살아있음.",
    signals: [
      "ORM 매핑 — 테이블이 어떤 객체에 대응되는지 (JPA, SQLAlchemy 등)",
      "Enum 정의 — 코드값의 실제 의미 (예: status=1 → 'PENDING')",
      "i18n 라벨 — UI에 표시되는 텍스트 (컬럼명이 화면에서 어떻게 보이는지)",
      "변수·함수명·로직 — 컬럼을 어떻게 다루는지, 어떤 조건으로 분기하는지",
      "README, docstrings, code comments — 코드에 남긴 운영 맥락",
      "dbt models, Airflow DAGs — 변환 파이프라인 정의",
      "Git 커밋 메시지 — 변경의 배경과 의도"
    ],
    augments: "컬럼의 비즈니스 의도, 코드값의 실제 의미, UI 표시 라벨, 파이프라인 변환 로직, 변경 이유",
    limits: "Git 저장소 접근 + 별도 파싱 파이프라인 필요. 코드 품질에 따라 신호 밀도 차이 큼 (주석 없는 코드, 모호한 변수명). 코드와 DB 스키마 변경이 항상 동기화되지 않음."
  },
  bi: {
    title: "BI Tools",
    subtitle: "비즈니스 인텔리전스 도구",
    access: "통합 필요",
    def: "Looker, Tableau, Metabase, Power BI 등. 데이터를 분석·시각화하는 도구에서 실제 사용 패턴을 수집.",
    signals: [
      "어떤 컬럼이 어떤 대시보드에 사용되는지 (컬럼의 실제 활용처)",
      "필터·집계에 자주 쓰이는 컬럼 (비즈니스적으로 중요한 컬럼 식별)",
      "접근 부서·팀 — 누가, 어떤 목적으로 쓰는지",
      "사용 빈도·추세 (시간에 따른 중요도 변화)"
    ],
    augments: "컬럼의 실제 비즈니스 활용처, 어떤 부서가 관심 있는 컬럼인지, 중요도 우선순위 힌트",
    limits: "도구마다 연동 방식이 다름. 사용 빈도가 곧 중요도는 아님 (자주 쓰여도 잘못 쓰이는 컬럼일 수 있음). 신규 데이터는 이력 없음. BI 없이 SQL 직접 사용하는 팀의 패턴은 잡히지 않음."
  },
  collaboration: {
    title: "Collaboration",
    subtitle: "협업·지식 관리 도구",
    access: "통합 필요",
    def: "Slack, Jira, Confluence, Notion 등. 사람들이 데이터에 대해 나눈 대화와 문서에서 맥락을 수집.",
    signals: [
      "Slack — 데이터 관련 채널 대화, 질문과 답변, 장애 대응 기록",
      "Jira — 스키마 변경 티켓, 데이터 버그 리포트, 요구사항 문서",
      "Confluence·Notion — 데이터 정의 문서, 운영 가이드, 의사결정 기록",
      "과거에 수동으로 작성된 설명들"
    ],
    augments: "tribal knowledge (문서화되지 않은 암묵적 지식), 비즈니스 의사결정 맥락, '왜 이렇게 설계됐는가' — 가장 풍부한 맥락이지만 접근이 가장 어려움",
    limits: "비정형이라 자동 의미 추출 어려움. 흩어져 있고 최신성·정확성 보장 안 됨. 관련 내용 찾는 것 자체가 어려움. 노이즈 비율이 높음."
  }
};

function showSignal(id) {
  const s = signalData[id];
  if (!s) return;
  document.querySelectorAll('.signal-list-item').forEach(el => el.classList.remove('active'));
  const item = document.querySelector('.signal-list-item[data-signal="' + id + '"]');
  if (item) item.classList.add('active');

  const sigs = s.signals.map(sig => '<li>' + sig + '</li>').join('');

  document.getElementById('signal-panel').innerHTML =
    '<h3 class="sp-title">' + s.title + '</h3>' +
    '<p class="sp-def"><span style="font-size:12px;color:var(--text-tertiary);margin-right:8px">' + s.subtitle + ' · ' + s.access + '</span><br>' + s.def + '</p>' +
    '<hr class="sp-divider">' +
    '<div class="sp-section"><div class="sp-label">얻을 수 있는 신호</div><ul style="margin:0;padding-left:18px;line-height:1.8;color:var(--text-primary);font-size:13px">' + sigs + '</ul></div>' +
    '<div class="sp-section"><div class="sp-label">증강 가능한 DB 정보</div><div class="sp-body">' + s.augments + '</div></div>' +
    '<div class="sp-section"><div class="sp-label">한계 · 주의사항</div><div class="sp-body">' + s.limits + '</div></div>';
}

// Ch4 tab switching
document.querySelectorAll('.aug-tab').forEach(tab => {
  tab.addEventListener('click', () => {
    document.querySelectorAll('.aug-tab').forEach(t => t.classList.remove('active'));
    document.querySelectorAll('.aug-map').forEach(m => m.classList.remove('active'));
    tab.classList.add('active');
    document.getElementById('aug-map-' + tab.dataset.map).classList.add('active');
  });
});

// Initial state
showConcept('term');
showSignal('db');

// ch4 panel data
const ch4Data = {
  desc: {
    db: { title: 'DB', sections: [
      { label: 'raw', body: 'TAX_EXMP_FLG · CHAR(1) · 테이블: LOAN_APPL_HIST' },
      { label: 'TAX_EXMP_FLG', body: '"TAX(세금)" + "EXMP(exempt, 면제)" + "FLG(flag)" → 세금 면제 여부를 나타내는 플래그 컬럼으로 추정' },
      { label: 'CHAR(1)', body: '단일 문자 코드값 체계. Y/N 이진 또는 다중 상태 코드일 가능성. 값이 무엇인지는 아직 알 수 없음.' },
      { label: 'LOAN_APPL_HIST', body: '대출(LOAN) 신청(APPL) 이력(HIST) 테이블. 이 컬럼은 대출 신청 프로세스와 연관된 속성.' }
    ]},
    code: { title: 'Code', sections: [
      { label: 'raw', body: 'enum TaxExemption { Y("면세"), N("과세"), P("부분면세"), X("해당없음") }' },
      { label: 'Y = "면세"', body: '해당 대출 신청 건 전체가 세금 면제 대상' },
      { label: 'N = "과세"', body: '정상적으로 세금이 부과되는 건' },
      { label: 'P = "부분면세"', body: '일부 항목만 면제되는 복합 케이스' },
      { label: 'X = "해당없음"', body: '세금 부과 자체가 적용되지 않는 건 (면세도 과세도 아닌 범주 외)' }
    ]},
    catalog: { title: 'Catalog', sections: [
      { label: 'raw', body: '도메인: LOAN · lineage: 세금계산모듈 → LOAN_APPL_HIST.TAX_EXMP_FLG 참조' },
      { label: 'LOAN 도메인', body: '이 컬럼은 대출 업무 영역의 데이터. Finance 도메인이 아니라 대출 신청 처리에 종속.' },
      { label: '세금계산모듈 참조', body: '실제 세금 계산 로직이 이 컬럼을 입력값으로 읽음. 단순 기록용이 아니라 프로세스에 직접 개입하는 컬럼.' }
    ]},
    result: { title: '컬럼 Description — 최종 결과', sections: [
      { label: '세 소스 조합', body: 'LOAN_APPL_HIST(대출 신청) + TAX_EXMP_FLG(세금 면제) + CHAR(1)(단일 코드) + enum(Y/N/P/X 의미) + lineage(세금 계산 활용)' },
      { label: '생성된 Description', body: '"대출 신청 건의 세금 면제 상태 코드. Y=면세 · N=과세 · P=부분면세 · X=해당없음"' },
      { label: 'Confidence High 근거', body: 'Enum으로 모든 코드값 의미 확정. 추측이 아닌 코드 정의 기반. Lineage로 비즈니스 목적도 확인.' },
      { label: 'Code 없을 때', body: '"세금 관련 CHAR(1) 코드" — 값이 몇 개인지, 각각 무슨 의미인지 알 수 없음. Confidence Low.' }
    ]}
  },
  term: {
    code: { title: 'Code', sections: [
      { label: 'raw', body: 'TaxExemption 클래스 — TaxCalcService.java · LoanApplValidator.java · ReportService.java 3개 파일에서 반복 참조' },
      { label: '반복의 의미', body: '계산(TaxCalcService), 검증(Validator), 리포팅(ReportService) 등 서로 다른 관심사에서 동일 개념이 쓰임 → 시스템 전반에 걸친 핵심 비즈니스 개념' },
      { label: 'TaxExemption', body: '"Tax(세금)" + "Exemption(면제)" → 코드에서 사용하는 영문 개념명. 한국어 Term 이름의 기반이 됨.' }
    ]},
    db: { title: 'DB', sections: [
      { label: 'raw', body: 'TAX_EXMP_FLG · TAX_EXMP_RSN_CD · TAX_EXMP_HIST — 동일 prefix TAX_EXMP_ 반복' },
      { label: 'TAX_EXMP_FLG', body: '면제 여부 (Y/N/P/X)' },
      { label: 'TAX_EXMP_RSN_CD', body: '면제 사유 코드 — 왜 면제인지' },
      { label: 'TAX_EXMP_HIST', body: '변경 이력 — 면제 상태가 언제 어떻게 바뀌었는지' },
      { label: 'prefix 패턴의 의미', body: '하나의 비즈니스 개념("세금면제")이 여부 · 사유 · 이력 세 가지 속성으로 분화. Term 하나에 세 컬럼이 연결됨.' }
    ]},
    bi: { title: 'BI', sections: [
      { label: 'raw', body: '"세금면제율" 지표 · "세금면제 건수" 리포트 컬럼' },
      { label: '"세금면제율"', body: '비즈니스 사용자가 이 개념을 "세금면제"라는 한국어로 부름. 기술 코드의 TaxExemption과 동일 개념.' },
      { label: 'Term 이름 확정', body: 'Code(TaxExemption) + BI("세금면제율") 모두 같은 개념 → Term 이름: "세금면제"' }
    ]},
    result: { title: 'Glossary Term — 최종 결과', sections: [
      { label: 'Term 이름', body: '"세금면제" — BI 사용어 기준 (비즈니스 현장 언어)' },
      { label: '정의', body: '특정 거래 또는 항목에 세금이 부과되지 않는 상태' },
      { label: '연결 자산', body: 'TAX_EXMP_FLG · TAX_EXMP_RSN_CD · TAX_EXMP_HIST — DB prefix 패턴 기반' },
      { label: '발견 방식', body: '하나의 Term을 작성한 게 아니라, 여러 소스에 흩어진 같은 개념을 패턴 인식으로 발견.' }
    ]}
  },
  cls: {
    db: { title: 'DB', sections: [
      { label: 'raw', body: 'CUST_EMAIL · VARCHAR(100)' },
      { label: 'CUST_EMAIL', body: '"CUST(고객)" + "EMAIL(이메일)" → 고객 이메일 주소 컬럼. _EMAIL suffix는 이메일 주소 패턴의 강한 신호.' },
      { label: 'VARCHAR(100)', body: '이메일 주소 길이에 적합한 타입. 이메일 형식임을 간접 지지.' },
      { label: '이 시점 판단', body: 'PII 후보로 분류. 컬럼명 패턴만으로는 확정 불가 — Code 확인 필요.' }
    ]},
    code: { title: 'Code', sections: [
      { label: 'raw', body: 'customerEmail 변수 · @PersonalInfo 어노테이션 · 주석: "// 개인정보: 이메일 주소"' },
      { label: '@PersonalInfo', body: '개발자가 코드에서 명시적으로 개인정보로 마킹. DB 컬럼명 추측이 아닌 코드 레벨의 직접 증거.' },
      { label: '주석', body: '"개인정보: 이메일 주소" → 의도적으로 남긴 메타데이터. PII 확정 근거 확보.' },
      { label: '이 시점 판단', body: 'DB 패턴 + Code 어노테이션 두 가지 증거 → CUST_EMAIL PII 확정.' }
    ]},
    catalog: { title: 'Catalog (Lineage)', sections: [
      { label: 'raw', body: 'lineage: CUSTOMER_DIM.CUST_EMAIL → ETL → MARKETING_REPORT.EMAIL_MASKED' },
      { label: 'ETL 파생 관계', body: 'CUST_EMAIL이 ETL을 통해 EMAIL_MASKED로 변환됨. 마스킹 처리가 됐지만 원본이 PII에서 파생.' },
      { label: 'EMAIL_MASKED', body: '이름에 MASKED가 붙어 마스킹된 것처럼 보이지만, PII에서 파생됐으므로 후보로 고려 필요.' },
      { label: '이 시점 판단', body: 'EMAIL_MASKED를 PII 후보로 제안. 마스킹 수준에 따라 판단이 달라지므로 스튜어드 검토 필요.' }
    ]},
    out_confirmed: { title: 'CUST_EMAIL — PII 확정', sections: [
      { label: '확정 근거', body: 'DB: _EMAIL suffix 패턴 + Code: @PersonalInfo 어노테이션 — 두 독립 소스에서 일치' },
      { label: '부여 방식', body: 'Link를 통해 즉시 확정. 이미 연결된 자산에 자동 전파. 별도 검토 없이 적용.' }
    ]},
    out_candidate: { title: 'EMAIL_MASKED — PII 후보', sections: [
      { label: '후보 근거', body: 'Lineage: CUST_EMAIL(PII 확정)에서 ETL을 통해 파생. 직접 확인은 아님.' },
      { label: '왜 후보인가', body: '마스킹이 됐더라도 원본이 개인정보이면 파생물도 개인정보일 수 있음. 마스킹 수준(단방향 해시인지, 복호화 가능한지 등)에 따라 다름.' },
      { label: '부여 방식', body: '후보로 제안 → 검토 큐 → 스튜어드가 마스킹 수준 확인 후 확정 또는 제외.' }
    ]}
  }
};

function showCh4Panel(mapId, srcId) {
  const data = (ch4Data[mapId] || {})[srcId];
  if (!data) return;
  const panel = document.getElementById('ch4-panel');
  let html = '<h3 class="sp-title">' + data.title + '</h3><hr class="sp-divider">';
  data.sections.forEach(s => {
    html += '<div class="sp-section"><div class="sp-label">' + s.label + '</div><div class="sp-body">' + s.body + '</div></div>';
  });
  panel.innerHTML = html;
}

document.querySelectorAll('.aug-src').forEach(el => {
  el.addEventListener('click', function() {
    const mapId = this.dataset.map;
    const srcId = this.dataset.src;
    document.querySelectorAll('.aug-src[data-map="' + mapId + '"]').forEach(e => e.classList.remove('active'));
    this.classList.add('active');
    showCh4Panel(mapId, srcId);
  });
});
</script>

</body>
</html>