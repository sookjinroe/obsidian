
나이/직급: 31세, 경영기획팀 대리 3년차
회사: 국내 중견 제조업 계열사

업무 역량:
- ERP에서 원하는 데이터 뽑는 거 됨
- 엑셀 피벗, 기본 함수 능숙
- Claude, ChatGPT 일상적으로 씀
- 데이터 보는 눈 있음, 숫자 해석 가능

구조적 장벽:
- 영업/생산/재무 데이터가 각각 다른 시스템
- 통합해서 보려면 각각 뽑아서 수동으로 합쳐야 함
- 임기응변 질의("이거 지역별로 쪼개봐")에 시간이 걸림
- AI한테 물어봐도 우리 회사 데이터가 없음

페인포인트:
- 숫자는 있는데 "왜"를 설명하려면 한참 걸림
- 임원이 회의 중에 추가 질문하면 그 자리에서 답 못 함
- 정해진 리포트는 빠른데 새로운 관점 요청이 오면 처음부터


인터뷰 결과 정리입니다. 실제 인터뷰를 했다면 나왔을 내용을 페르소나 기반으로 구성했습니다.

---

## 인터뷰 결과 요약 — 박지수 (경영기획팀 대리)

### 현재 업무 방식 (As-Is)

```
월간 실적 보고 프로세스 (매월 5영업일 소요)

1일차: ERP에서 사업부별 매출/비용 데이터 추출
       → 시스템 3개 (영업/생산/재무) 각각 따로 뽑음

2일차: Excel에서 수동 통합 및 정제
       → VLOOKUP으로 붙이는 과정에서 오류 자주 발생

3일차: 전월 파일 열어서 비교 컬럼 수동 추가
       → 형식이 바뀌어 있으면 처음부터

4일차: 팀장 검토 → 추가 요청
       "이거 사업부별 말고 제품군별로도 봐줘"
       → 2일차부터 다시

5일차: PPT 작성 후 보고
```

---

### 발화 원문 (주요 발언)

> "숫자 뽑는 건 이제 빨라요. 근데 왜 이렇게 됐는지 설명하려면 또 한참이에요."

> "영업이랑 생산 데이터가 시스템이 달라서, 같이 보려면 두 개 뽑아서 제가 붙여야 해요."

> "팀장님이 회의 중에 '이거 왜 이래?' 하시면 '확인해볼게요' 밖에 못 해요. 그 자리에서 답이 안 나와요."

> "ChatGPT한테 물어보면 잘 대답하는데, 우리 데이터가 없잖아요. 연결이 안 되니까."

> "제품군별로 쪼개봐, 지역별로 봐, 이런 요청 오면 또 처음부터예요. 리포트 양식이 없으면 다 새 작업이에요."

---

### 핵심 페인포인트

|페인포인트|현재 소요 시간|빈도|
|---|---|---|
|다중 시스템 데이터 통합|반나절|매월|
|임기응변 질의 대응|1~2시간/건|주 2~3회|
|전월 비교 작업|1~2시간|매월|
|이상치 원인 파악|수시간~하루|매월 마감 시|

---

### 사용자가 원하는 것 (진짜 니즈)

표면 니즈와 진짜 니즈가 다릅니다.

```
표면 니즈 (본인이 말한 것)
"데이터 뽑는 게 더 빨랐으면"

진짜 니즈 (관찰된 것)
"임원 앞에서 즉각 답할 수 있는 사람이 되고 싶다"
→ 데이터 처리 속도가 아니라 신뢰성의 문제
```

---

### 에이전트 설계에 주는 시사점

**1. 연결이 핵심입니다** 여러 시스템을 넘나드는 것이 이 사람의 가장 큰 장벽입니다. Text-to-SQL 하나로는 부족하고, 크로스 시스템 조회가 첫 번째 가치입니다.

**2. 임기응변 질의에 강해야 합니다** 정해진 리포트 재생산이 아니라, 처음 보는 질문에도 즉시 답하는 것이 가치입니다. 발화 가설에 "정해진 형식 없는 질문" 비중을 높여야 합니다.

**3. 회의 중 사용 시나리오가 중요합니다** 책상에서 미리 준비하는 용도보다, 회의 중 실시간으로 쓰는 시나리오가 더 강력합니다. 속도와 응답 신뢰성이 UX 설계의 핵심입니다.

**4. "왜"에 답해야 합니다** 숫자 조회가 아니라 원인 분석이 진짜 가치입니다. `drill-down`과 `anomaly-detect` Skills의 우선순위가 높아집니다.

---

### 발화 가설 목록 (수정)

이전 가설과 달리 현실적인 표현으로 재작성합니다.

```
즉흥적·모호한 질문 (실제 빈도 높음)
├── "3월 C사업부 왜 이렇게 나왔어"
├── "저번달이랑 얼마나 차이나"
├── "어느 사업부가 제일 문제야"
└── "이거 제품군별로도 쪼개봐"

맥락 의존 질문 (앞 대화 참조 필요)
├── "그럼 작년 같은 시기는?"
├── "거기서 비용은 어때"
└── "이거 좀 더 자세히"

출력 요구가 포함된 질문
├── "CFO 보고용으로 정리해줘"
├── "이거 차트로 보여줘"
└── "팀장님한테 보낼 수 있게 만들어줘"
```



## archive
![[Untitled-1773732232939.png]]
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>경영정보 에이전트 — 서비스 블루프린트</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',sans-serif;background:#fff;color:#1a1a1a;padding:24px}
@media(prefers-color-scheme:dark){body{background:#1a1a18;color:#e8e6de}}
.bp{padding:4px 0 12px}
.bp-tabs{display:flex;gap:8px;margin-bottom:10px}
.bp-tab{padding:6px 14px;border-radius:6px;border:1px solid #d0cec4;cursor:pointer;font-size:12px;color:#5f5e5a;background:transparent}
.bp-tab.on{color:#1a1a1a;border-color:#888780;background:#f1efe8;font-weight:500}
@media(prefers-color-scheme:dark){
  .bp-tab{border-color:#444441;color:#888780;background:transparent}
  .bp-tab.on{color:#e8e6de;border-color:#888780;background:#2c2c2a}
}
.bp-title{font-size:14px;font-weight:500;margin-bottom:2px}
.bp-persona{font-size:11px;color:#5f5e5a;margin-bottom:10px}
.bp-wrap{overflow-x:auto}
table{border-collapse:collapse;width:100%;min-width:640px;table-layout:fixed}
.h-corner{width:88px;background:#f1efe8;border:1px solid #d3d1c7;padding:0}
@media(prefers-color-scheme:dark){.h-corner{background:#2c2c2a;border-color:#444441}}
.h-step{font-size:10px;font-weight:500;color:#5f5e5a;text-align:center;padding:7px 2px;background:#f1efe8;border:1px solid #d3d1c7;line-height:1.4;vertical-align:middle}
@media(prefers-color-scheme:dark){.h-step{background:#2c2c2a;border-color:#444441;color:#888780}}
.r-label{font-size:10px;font-weight:500;padding:4px 6px;text-align:right;border:1px solid #d3d1c7;vertical-align:middle;white-space:nowrap;height:78px;width:88px;max-width:88px;overflow:hidden}
.r-label small{display:block;font-size:9px;font-weight:400;color:#888780;margin-top:2px}
@media(prefers-color-scheme:dark){.r-label{border-color:#444441}}
td.cell{border:1px solid #d3d1c7;height:78px;vertical-align:top;padding:0}
@media(prefers-color-scheme:dark){td.cell{border-color:#444441}}
.c-in{height:100%;padding:5px 4px;border-left:3px solid}
.c-out{height:100%;display:flex;align-items:center;justify-content:center}
.c-dot{width:4px;height:4px;border-radius:50%;background:#d3d1c7}
.c-io{font-size:9px;color:#888780;line-height:1.3;margin-bottom:3px}
.c-act{font-size:10px;line-height:1.45}

.lbl-user{color:#5f5e5a}.lbl-cmd{color:#534AB7}.lbl-skill{color:#0F6E56}.lbl-sub{color:#185FA5}.lbl-mcp{color:#854F0B}.lbl-hook{color:#5f5e5a}
@media(prefers-color-scheme:dark){.lbl-cmd{color:#AFA9EC}.lbl-skill{color:#5DCAA5}.lbl-sub{color:#85B7EB}.lbl-mcp{color:#EF9F27}}

.bc-user{border-left-color:#888780;background:#f1efe8}
.bc-cmd{border-left-color:#7F77DD;background:#EEEDFE}
.bc-skill{border-left-color:#1D9E75;background:#E1F5EE}
.bc-sub{border-left-color:#378ADD;background:#E6F1FB}
.bc-mcp{border-left-color:#BA7517;background:#FAEEDA}
.bc-hook{border-left-color:#888780;background:#f1efe8}
.c-act-user{color:#444441}.c-act-cmd{color:#3C3489}.c-act-skill{color:#085041}.c-act-sub{color:#0C447C}.c-act-mcp{color:#633806}.c-act-hook{color:#444441}
@media(prefers-color-scheme:dark){
  .bc-user{background:#2c2c2a}.bc-cmd{background:rgba(127,119,221,.18)}.bc-skill{background:rgba(29,158,117,.18)}
  .bc-sub{background:rgba(55,138,221,.18)}.bc-mcp{background:rgba(186,117,23,.18)}.bc-hook{background:#2c2c2a}
  .c-act-user,.c-act-cmd,.c-act-skill,.c-act-sub,.c-act-mcp,.c-act-hook{color:#c2c0b6}
}
</style>
</head>
<body>
<div class="bp">
  <div class="bp-tabs">
    <button class="bp-tab on" onclick="show('s1',this)">시나리오 1. 월간 실적 보고서 작성</button>
    <button class="bp-tab" onclick="show('s2',this)">시나리오 2. 예산 집행 현황 점검</button>
  </div>
  <div class="bp-title" id="meta-title"></div>
  <div class="bp-persona" id="meta-persona"></div>
  <div class="bp-wrap">
    <table id="grid"></table>
  </div>
</div>

<script>
const C={
  user: {label:'사용자',      sub:'발화·수신',   cls:'lbl-user', bc:'bc-user', ac:'c-act-user'},
  cmd:  {label:'명령어',      sub:'Commands',   cls:'lbl-cmd',  bc:'bc-cmd',  ac:'c-act-cmd'},
  skill:{label:'Skills',      sub:'자동 발동',   cls:'lbl-skill',bc:'bc-skill',ac:'c-act-skill'},
  sub:  {label:'서브에이전트', sub:'백그라운드',  cls:'lbl-sub',  bc:'bc-sub',  ac:'c-act-sub'},
  mcp:  {label:'MCP 서버',    sub:'외부 시스템', cls:'lbl-mcp',  bc:'bc-mcp',  ac:'c-act-mcp'},
  hook: {label:'Hooks',       sub:'pre / post', cls:'lbl-hook', bc:'bc-hook', ac:'c-act-hook'},
};
const ROWS=['user','cmd','skill','sub','mcp','hook'];

const D={
  s1:{
    title:'시나리오 1. 월간 실적 보고서 작성',
    persona:'사용자: 경영기획팀 담당자  ·  목적: 월간 사업부별 실적 취합 및 이상치 파악',
    steps:['① 실적\n데이터 요청','② SQL\n생성','③ 쿼리\n검증','④ DB\n조회','⑤ 이상치\n감지','⑥ 원인\n드릴다운','⑦ 리포트\n출력'],
    cells:{
      user: [{io:'발화→',act:'"지난달 사업부별 매출 보여줘"'},null,{io:'←확인',act:'SQL 내용 확인 후 승인'},null,null,null,{io:'←수신',act:'차트+리포트 최종 전달'}],
      cmd:  [{io:'발화→명령',act:'/조회 발동'},null,null,null,null,null,null],
      skill:[{io:'발화→설계',act:'text-to-sql schema-lookup 호출'},{io:'자연어→SQL',act:'사업부 매출 집계 쿼리 초안'},null,null,{io:'결과→감지',act:'anomaly-detect C사업부 -40%'},{io:'이상치→원인',act:'drill-down period-compare 발동'},null],
      sub:  [null,{io:'SQL→검증',act:'SQL검증 에이전트 조인·집계 체크'},{io:'SQL→제시',act:'검증 완료 쿼리 사용자 제시'},null,null,null,{io:'결과→출력',act:'리포트+시각화 병렬 실행'}],
      mcp:  [null,null,null,{io:'SQL→결과셋',act:'DB MCP 매출 테이블 쿼리'},null,{io:'SQL→결과셋',act:'DB MCP C사업부 상세 조회'},{io:'결과→파일',act:'시각화 MCP 차트+Excel'}],
      hook: [null,{io:'[pre]',act:'DML 차단 SELECT만 허용'},{io:'[pre]',act:'쿼리 확인 사용자 승인 대기'},{io:'[post]',act:'출처 첨부 sales_monthly'},null,{io:'[post]',act:'출처 첨부 dept_detail'},{io:'[post]',act:'결과 포맷 표+차트'}],
    }
  },
  s2:{
    title:'시나리오 2. 예산 집행 현황 점검',
    persona:'사용자: 경영기획팀 담당자  ·  목적: 부서별 예산 집행률 점검 및 초과 부서 파악',
    steps:['① 집행 현황\n요청','② SQL\n생성','③ 쿼리\n검증','④ DB\n조회','⑤ 초과 부서\n감지','⑥ 상세\n드릴다운','⑦ 담당자\n확인'],
    cells:{
      user: [{io:'발화→',act:'"부서별 예산 집행률 조회해줘"'},null,{io:'←확인',act:'SQL 승인'},null,null,null,{io:'←수신',act:'초과부서 목록+담당자 전달'}],
      cmd:  [{io:'발화→명령',act:'/비교 발동 예산 vs 집행'},null,null,null,null,null,null],
      skill:[{io:'발화→설계',act:'text-to-sql schema-lookup 예산·집행 매핑'},{io:'자연어→SQL',act:'집행률 계산 쿼리 생성'},null,null,{io:'결과→감지',act:'anomaly-detect 초과 부서 3곳'},{io:'이상치→상세',act:'drill-down 초과 항목별 분해'},null],
      sub:  [null,{io:'SQL→검증',act:'SQL검증 집행률 계산식 검증'},{io:'SQL→제시',act:'검증 완료 쿼리 제시'},null,null,null,{io:'결과→리포트',act:'리포트 초과 부서 요약'}],
      mcp:  [null,null,null,{io:'SQL→결과셋',act:'ERP MCP 예산·집행 테이블'},null,{io:'SQL→결과셋',act:'ERP MCP 초과 부서 내역'},{io:'결과→파일',act:'시각화 MCP 집행률 차트'}],
      hook: [null,{io:'[pre]',act:'DML 차단 SELECT만 허용'},{io:'[pre]',act:'쿼리 확인 사용자 승인 대기'},{io:'[post]',act:'출처 첨부 budget_plan budget_exec'},null,{io:'[post]',act:'출처 첨부 expense_detail'},{io:'[post]',act:'결과 포맷 표+차트'}],
    }
  }
};

function show(id,btn){
  document.querySelectorAll('.bp-tab').forEach(t=>t.classList.remove('on'));
  btn.classList.add('on');
  const s=D[id];
  document.getElementById('meta-title').textContent=s.title;
  document.getElementById('meta-persona').textContent=s.persona;
  const g=document.getElementById('grid');
  let h='<thead><tr><th class="h-corner"></th>';
  s.steps.forEach(st=>{h+=`<th class="h-step">${st.replace(/\n/g,'<br>')}</th>`;});
  h+='</tr></thead><tbody>';
  ROWS.forEach(rk=>{
    const rc=C[rk];
    h+=`<tr><td class="r-label ${rc.cls}">${rc.label}<small>${rc.sub}</small></td>`;
    s.cells[rk].forEach(cell=>{
      if(!cell){
        h+=`<td class="cell"><div class="c-out"><div class="c-dot"></div></div></td>`;
      } else {
        h+=`<td class="cell"><div class="c-in ${rc.bc}"><div class="c-io">${cell.io}</div><div class="c-act ${rc.ac}">${cell.act}</div></div></td>`;
      }
    });
    h+='</tr>';
  });
  h+='</tbody>';
  g.innerHTML=h;
}
show('s1',document.querySelector('.bp-tab.on'));
</script>
</body>
</html>

