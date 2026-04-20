## Agent 기능 요구사항 정의

- 삼성전자 전사시스템의 컬럼 설명을 생성해야 한다.
- 사전에 정의한 91개 표준항목과 매칭 시켜야 한다.
- 대상/목적/포맷 으로 분리하여 생성한 결과를 조합한다.
- 결과는 한글과 영문 2가지의 버전으로 생성한다.

  

## 활용 가능한 자료

- 시스템 정보 (N-IRP)
- DB 스키마 및 테이블 정의서 (DIS)
- 소스코드
- 전체 시스템들의 컬럼정보 (DCS)
- 행정안전부 제공 포맷
- 91개 표준항목

  

## 자료제공 시스템

- DCS
- Data Hub (DoXA, 비정형 자료 → 정형 자료 변환)
- N-IRP
- DIS (Big Data Center의 시스템)

  

## 자료저장 시스템 정의

### PostgreSQL

- 대상 컬럼정보, DB 스키마, 시스템 정보, 91개 표준항목

### Vector

- 용어집, 소스코드 분석 내용, 행안부 포맷

  

# LangGraph 설계

## 1. Workflow 정의

### 정보 로드 -> 대상 선정 -> 매핑 판단 -> 생성

ENDSTART

  

## 2. 단계별 업무 정의

STARTEND

### 데이터 준비

- 시스템 정보, 91개 표준항목 정보, 행안부 포맷 등 변하지 않는 기준정보 조회
- 이 정보는 state에 저장한다.
- 모두 python 코드에서 진행

### 매칭 판별

- 대상 컬럼의 91개 표준항목 매칭 여부 확인
- 매칭된 도메인 정보를 state에 저장
- 컬럼 Description 생성 단계로 이동
- 모두 python 코드에서 진행

### 도구 사용

- 용어집, DB 스키마 tool 사용
- 검색결과를 state에 저장
- tool 정의와 로직은 python 코드로 진행

### LLM 질의

- state 정보를 기반으로 LLM 에게 컬럼 Description 생성 질의

### 컬럼 Description 생성

- 정해진 포맷에 맞게 컬럼 Description 생성
- 결과를 json 파일로 저장

  

## 3. State 설계

### 여러 단계에 걸쳐 필요한 데이터는 state에 넣는다.

### 변하지 않는 데이터는 state에 넣는다.

- 시스템 / 91개 표준항목 / 행안부 포맷 / 대상컬럼 정보는 변경없이 사용한다.
- 용어집이나 DB 스키마 정보는 도구사용 노드에서 state에 저장하고 LLM 질의 노드에서 사용한다.

**AgentState**

|   |
|---|
|`from` `langchain_core.messages` `import` `AIMessage, HumanMessage`<br><br>`from` `langgraph.graph` `import` `add_messages`<br><br>`class` `AgentState(TypedDict):`<br><br>    `# 공통 참조 정보`<br><br>    `system_info:` `str`                `# 시스템 정보`<br><br>    `standard_91:` `List``[``dict``]`         `# 91개 표준항목`<br><br>    `format_guide:` `List``[``dict``]`      `# 행안부 포맷`<br><br>    `# 테이블 단위 정보 (대상 테이블 변경 시 갱신)`<br><br>    `table_name:` `str`                 `# 테이블명`<br><br>    `table_schema:` `List``[``dict``]`        `# DB 스키마`<br><br>    `target_columns:` `List``[``dict``]`      `# 대상 컬럼`<br><br>    `# 컬럼 단위 정보 (대상 컬럼 변경 시 갱신)`<br><br>    `current_column: Optional[``dict``]`  `# 현재 작업중인 컬럼 정보`<br><br>    `glossary:` `List``[``dict``]`            `# 용어집`<br><br>    `# 결과물`<br><br>    `mapping_domain_name: Optional[``dict``]`                     `# 91개 표중항목 매핑 시 도메인 이름`<br><br>    `# messages: Annotated[list[dict], add_messages]            # 사용자/LLM 의 메세지 결과를 담을 수 있는 변수, message를 담는 전용함수인 add_messages 사용`|

**"조회는 한번에 하되, LLM에게 전달할 때는 현재 컬럼 정보 위주로 필터링해서 주어야 한다"**

  

Annotated[Type, Reducer_Function]

Python의 typing 모듈에서 제공하는 특별한 타입 힌트로, 기존 타입에 메타데이터를 추가할 수 있게 해줍니다.

  

LangGraph에서 사용자 또는 LLM, 아니면 둘 다의 메세지를 누적으로 관리하고 싶다면 add_messages 라는 함수를 통해 기존 메세지와 합칠 수 있고, 

add_messages 함수는 리스트로 된 Messages Type의 인자를 받아 병합하는 기능을 수행한다.

ex) [HumanMessage, AIMessage]

State 값 변경

State에 정의한 변수의 값은 기본적으로 "덮어쓰기"로 실행된다.

이 말은 곧 각 노드에서 State의 값에 접근하거나 업데이트를 할 때, 복사본을 받아와서 값을 변경한 후 원본 State에 덮어쓰기를 하는 것이다.

State를 여러개의 노드에서 병렬로 접근한다면 변수의 데이터 컨트롤에 유의해야 한다.

만약 데이터가 누적되는 list 형태의 변수에 자료를 추가할 경우, 노드 내에서 append를 하더라도 해당 데이터는 유실될 수 있다. (동일한 타이밍에 다른 노드에서 append한 값으로 "덮어쓰기" 가 될 수 있기 때문)

## 4. Node 구현

### 상태(state)를 어떻게 조작하고, LLM과 도구(Tool)를 어떻게 유기적으로 결합할 것인가

#### 노드 작성 방식

##### **상태 중심의 입력과 출력 (State in, State out)**

**노드는 반드시 전체 State를 인자로 받고, 업데이트할 정보만 딕셔너리 형태로 반환해야 한다.**

- Mapping Check Node에서는 State에서 전체 컬럼 중 대상 컬럼을 추출하고, 결과로 State에 업데이트 해야하는 결과만 반환한다.
    

##### 복합 로직의 캡슐화 (Encapsulation)

**노드 하나에 너무 많은 일을 시키지 말아야 하지만 응집도는 높아야 한다.**

- LLM_Gen_Node 내부에 단순히 llm.invoke만 넣지 말고, **[프롬프트 구성 -> LLM 호출 -> 응답 파싱 -> 예외 처리]**를 하나의 흐름으로 묶어 캡슐화 하자
    

  

### 에러 핸들링

#### **에이전트가 스스로 오류를 복구(Self-healing)하거나, 실패 지점을 기록하고 다음 단계로 안전하게 넘어가는 구조**를 설계해야 함

  

##### 1. 노드 레벨의 예외 처리 (Graceful Failure)

특정 컬럼에서 에러(예: LLM 응답 실패, 용어집 검색 오류)가 발생했을 때 전체 프로세스가 멈추지 않도록 하는 설계

- 전략: 노드 내부에서 에러를 잡아 State에 에러 메시지를 기록하고, 빈 결과값이라도 반환하여 루프가 유지되게 한다.
    

**구현 예시**

|   |
|---|
|`def` `llm_generate_node(state: AgentState):`<br><br>    `try``:`<br><br>        `# 정상 로직`<br><br>        `response` `=` `chain.invoke(...)`<br><br>        `return` `{``"final_descriptions"``: [{``"column"``:` `"..."``,` `"desc"``: response.content}]}`<br><br>    `except` `Exception as e:`<br><br>        `# 에러 발생 시 로그를 남기고 '실패' 상태를 전파`<br><br>        `error_msg` `=` `f``"Error at {state['current_idx']}: {str(e)}"`<br><br>        `return` `{``"final_descriptions"``: [{``"column"``:` `"..."``,` `"desc"``:` `"ERROR: 생성 실패"``}]}`|

##### 1-1. 노드 레벨의 예외 처리 + 다음 실행노드 지정

엣지에서 지정한 다음 실행 노드 대신, **Command 객체**를 통해 오류가 발생했을 때 동적으로 다음으로 실행할 노드를 지정할 수 있다.

**구현 예시**

|   |
|---|
|`from` `langgraph.types` `import` `Command`<br><br>`def` `execute_tool(state: State)` `-``> Command[Literal[``"agent"``,` `"execute_tool"``]]:` `# 이동할 수 있는 노드 명시`<br><br>    `try``:`<br><br>        `result` `=` `run_tool(state[``'tool_call'``])`<br><br>        `return` `Command(update``=``{``"tool_result"``: result}, goto``=``"agent"``)`<br><br>    `except` `ToolError as e:`<br><br>        `# Let the LLM see what went wrong and try again`<br><br>        `return` `Command(`<br><br>            `update``=``{``"tool_result"``: f``"Tool error: {str(e)}"``},`    `# 오류를 리턴함으로써 LLM 에게 오류가 발생했음을 인지시킬 수 있음`<br><br>            `goto``=``"agent"`<br><br>        `)`|

  

##### 2. 조건부 엣지를 활용한 재시도 (Retry Loop)

LLM이 생성한 결과가 정의한 규칙에 맞지 않을 경우, 다시 생성 노드로 돌려보내는 전략 (컬럼 Description 생성 노드와 END 사이에 Validation 노드를 추가해야 함)

- 전략:  Validator Node를 두어 결과물의 품질을 검사하고, 미흡할 경우 다시 LLM Generate Node로 엣지를 연결
    
- 흐름:  LLM Generate Node → Validation Node → (불합격 시) → LLM Generate Node (다시 시도)
    

**구현 예시**

|   |
|---|
|`def` `validator_node(state: AgentState):`<br><br>    `desc` `=` `state[``"final_description"``]`<br><br>    `retry_cnt` `=` `state.get(``"retry_count"``,` `0``)`<br><br>    `# 예시 규칙: 설명이 "이다." 또는 "함."으로 끝나야 함`<br><br>    `if` `desc.endswith(``"이다."``)` `or` `desc.endswith(``"함."``):`<br><br>        `return` `{``"is_valid"``:` `True``}`<br><br>    `else``:`<br><br>        `# 실패 시 횟수 증가`<br><br>        `return` `{``"is_valid"``:` `False``,` `"retry_count"``: retry_cnt` `+` `1``}`|

  

##### 3. 에러발생 시 AgentState에 에러로그 저장

**구현 예시**

|   |
|---|
|`class` `AgentState(TypedDict):`<br><br>    `# ... 기존 필드 ...`<br><br>    `error_log: Annotated[``List``[``dict``], operator.add]` `# 에러가 발생한 컬럼명과 원인 누적`|

  

4. 네트워크 문제가 발생했을 때 재시도 설정 추가

**구현 예시**

|   |
|---|
|`from` `langgraph.types` `import` `RetryPolicy`<br><br>`# 기초 데이터 로드`<br><br>`workflow.add_node(`<br><br>    `"initialize"``,`<br><br>    `initialize_node,`<br><br>    `retry_policy``=``RetryPolicy(max_attempts``=``3``, initial_interval``=``1.0``)`<br><br>`)`|

## 5. 전체 연결

노드들을 간선으로 연결하여 그래프를 완성시킨다.

**구현 예시**

|   |
|---|
|`from` `typing` `import` `TypedDict,` `List``, Optional, Annotated, Literal`<br><br>`import` `operator`<br><br>`from` `langgraph.graph` `import` `StateGraph, END`<br><br>`from` `langgraph.types` `import` `Command`<br><br>`class` `AgentState(TypedDict):`<br><br>    `# 공통 참조 정보`<br><br>    `system_info:` `str`<br><br>    `standard_91:` `List``[``dict``]`<br><br>    `format_guide:` `str`<br><br>    `# 작업 대상 및 현재 상태`<br><br>    `target_columns:` `List``[``dict``]`<br><br>    `current_idx:` `int`<br><br>    `# 중간 결과 및 최종 저장소`<br><br>    `mapping_result: Optional[``dict``]`<br><br>    `current_glossary:` `str`<br><br>`# 1. 시스템 정보 및 기초 데이터 로드`<br><br>`def` `initialize_node(state: AgentState):`<br><br>    `print``(``"---시스템 초기화 및 참조 데이터 로드---"``)`<br><br>    `return` `{`<br><br>        `"system_info"``:` `"공공기관 데이터 표준 가이드 v1.2"``,`<br><br>        `"standard_91"``: [{``"name"``:` `"USER_ID"``,` `"desc"``:` `"사용자 식별 번호"``,` `"format"``:` `"ID_FORMAT"``}],`<br><br>        `"format_guide"``:` `"설명은 '~이다.'로 끝나야 함."``,`<br><br>        `"current_idx"``:` `0`<br><br>    `}`<br><br>`# 2. 이번 회차에 처리할 컬럼 정보 세팅`<br><br>`def` `fetch_column_node(state: AgentState):`<br><br>    `idx` `=` `state[``"current_idx"``]`<br><br>    `if` `idx >``=` `len``(state[``"target_columns"``]):`<br><br>        `return` `Command(goto``=``END)` `# 모든 컬럼 처리 완료 시 종료`<br><br>    `print``(f``"---컬럼 분석 시작: {state['target_columns'][idx]['name']}---"``)`<br><br>    `return` `{``"mapping_result"``:` `None``,` `"current_glossary"``: ""}`<br><br>`# 3. 매핑 체크 및 분기 결정 (핵심 로직)`<br><br>`def` `mapping_check_node(state: AgentState)` `-``> Command[Literal[``"standard_gen"``,` `"fetch_tools"``]]:`<br><br>    `curr_col` `=` `state[``"target_columns"``][state[``"current_idx"``]][``"name"``]`<br><br>    `match` `=` `next``((s` `for` `s` `in` `state[``"standard_91"``]` `if` `s[``"name"``]` `=``=` `curr_col),` `None``)`<br><br>    `if` `match:`<br><br>        `print``(``">> 표준 항목 매핑 성공"``)`<br><br>        `return` `Command(update``=``{``"mapping_result"``: match}, goto``=``"standard_gen"``)`<br><br>    `else``:`<br><br>        `print``(``">> 매핑 실패: 외부 도구 조회 필요"``)`<br><br>        `return` `Command(goto``=``"fetch_tools"``)`<br><br>`# 4. 표준 항목 기반 생성 (Fast Track)`<br><br>`def` `standard_gen_node(state: AgentState):`<br><br>    `res` `=` `state[``"mapping_result"``]`<br><br>    `desc` `=` `f``"{res['desc']} 정보를 담고 있으며 포맷은 {res['format']}이다."`<br><br>    `result` `=` `{``"column"``: state[``"target_columns"``][state[``"current_idx"``]][``"name"``],` `"desc"``: desc}`<br><br>    `# 처리 완료 후 인덱스 증가 및 다음 컬럼 fetch로 이동`<br><br>    `return` `Command(`<br><br>        `update``=``{``"final_descriptions"``: [result],` `"current_idx"``: state[``"current_idx"``]` `+` `1``},`<br><br>        `goto``=``"fetch_column"`<br><br>    `)`<br><br>`# 5. 매핑 실패 시 도구 실행 (유사도 검색 등)`<br><br>`def` `fetch_tools_node(state: AgentState):`<br><br>    `curr_col` `=` `state[``"target_columns"``][state[``"current_idx"``]][``"name"``]`<br><br>    `# 여기서 Vector DB 검색 로직 수행 (예시 데이터)`<br><br>    `search_result` `=` `f``"용어 사전 검색 결과: {curr_col}은 사용자 계정을 의미함."`<br><br>    `return` `{``"current_glossary"``: search_result}`<br><br>`# 6. LLM 기반 생성`<br><br>`def` `llm_gen_node(state: AgentState):`<br><br>    `# 1. 현재 인덱스에 해당하는 컬럼 정보만 추출`<br><br>    `idx` `=` `state[``"current_idx"``]`<br><br>    `current_col` `=` `state[``"target_columns"``][idx]`<br><br>    `# 2. 프롬프트 템플릿 구성 (테이블 공통 정보 + 현재 컬럼 정보)`<br><br>    `prompt` `=` `ChatPromptTemplate.from_template(``"""`<br><br>    `당신은 데이터 관리 전문가입니다. 아래 정보를 바탕으로 컬럼 설명을 작성하세요.`<br><br>    `[테이블 공통 맥락]`<br><br>    `- 테이블명: {table_name}`<br><br>    `- 테이블 설명: {table_comment}`<br><br>    `- 시스템 정보: {system_info}`<br><br>    `[현재 분석 대상 컬럼 정보]`<br><br>    `- 컬럼명: {col_name}`<br><br>    `- 데이터 타입: {col_type}`<br><br>    `- 용어집 참고사항: {glossary}`<br><br>    `[작성 규칙]`<br><br>    `{format_guide}`<br><br>    `"""``)`<br><br>    `# 3. 모델 호출 (필요한 값만 매핑)`<br><br>    `llm` `=` `ChatOpenAI(model``=``"gpt-oss-120b"``)`<br><br>    `structured_llm` `=` `llm.with_structured_output(ColumnDescription)`<br><br>    `chain` `=` `prompt \| structured_llm`<br><br>    `# prompt template를 사용하면 state만 넘겨도 내부에서 알아서 매핑`<br><br>    `response` `=` `chain.invoke(state)`<br><br>    `# 4. 결과 누적 및 인덱스 증가 준비`<br><br>    `result` `=` `{`<br><br>        `"column_name"``: current_col[``"name"``],`<br><br>        `"description"``: response.content`<br><br>    `}`<br><br>    `return` `Command(`<br><br>        `update``=``{``"final_descriptions"``: [result],` `"current_idx"``: state[``"current_idx"``]` `+` `1``},`<br><br>        `goto``=``"fetch_column"`<br><br>    `)`<br><br>`# 1. 그래프 생성 (AgentState는 앞서 정의한 TypedDict)`<br><br>`workflow` `=` `StateGraph(AgentState)`<br><br>`# 2. 모든 노드 등록`<br><br>`workflow.add_node(``"initialize"``, initialize_node)`           `# 기초 데이터 로드`<br><br>`workflow.add_node(``"fetch_column"``, fetch_column_node)`       `# 대상 컬럼 추출`<br><br>`workflow.add_node(``"mapping_check"``, mapping_check_node)`     `# 91개 표준 매핑 체크`<br><br>`workflow.add_node(``"standard_gen"``, standard_gen_node)`       `# 표준 기반 즉시 생성`<br><br>`workflow.add_node(``"fetch_tools"``, fetch_tools_node)`         `# 용어집/유사도 검색(Tool)`<br><br>`workflow.add_node(``"llm_gen"``, llm_gen_node)`                 `# LLM 기반 추론 생성`<br><br>`# 3. 명시적 관계 연결 (Command가 없는 구간)`<br><br>`workflow.set_entry_point(``"initialize"``)`<br><br>`workflow.add_edge(``"initialize"``,` `"fetch_column"``)`<br><br>`workflow.add_edge(``"fetch_column"``,` `"mapping_check"``)`<br><br>`workflow.add_edge(``"fetch_tools"``,` `"llm_gen"``)`<br><br>`# 실행 객체 생성`<br><br>`app` `=` `workflow.``compile``()`<br><br>`# 테스트 실행`<br><br>`input_state` `=` `{`<br><br>    `"target_columns"``: [{``"name"``:` `"USER_ID"``}, {``"name"``:` `"REG_DT"``}]`<br><br>`}`<br><br>`for` `event` `in` `app.stream(input_state):`<br><br>    `print``(event)`|

- 표준에 매핑된 경우, 굳이 fetch_tools나 llm_gen을 거칠 필요없이 Command(goto=...) 를 사용하여 바로 standard_gen으로 보냄