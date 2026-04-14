---
aliases:
  - "도메인 전문가의 판단 전략을 시스템 아키텍처로 전이하는 공학적 경로: FDE와 ACTA 방법론을 통한 AI 에이전트 설계 혁신 연구 보고서"
---
# 도메인 전문가의 판단 전략을 시스템 아키텍처로 전이하는 공학적 경로: FDE와 ACTA 방법론을 통한 AI 에이전트 설계 혁신 연구 보고서

인공지능(AI) 기술의 패러다임이 단순한 텍스트 생성에서 특정 목적을 자율적으로 수행하는 '에이전트'로 전환됨에 따라, 에이전트 설계의 핵심 역량은 기술적 정교함보다 도메인의 심층적인 이해로 이동하고 있다. 성공적인 에이전트는 단순히 도메인의 용어를 학습하는 수준을 넘어, 전문가가 복잡한 상황에서 내리는 전략적 판단의 메커니즘, 즉 도메인 이해의 3수준인 '전문가의 판단 전략'을 시스템 아키텍처에 내재화해야 한다. 이러한 전이 과정은 명문화된 문서만으로는 불가능하며, 전개 배치 엔지니어링(Forward Deployed Engineering, FDE)의 실전적 반복과 응용 인지 과업 분석(Applied Cognitive Task Analysis, ACTA)과 같은 정교한 추출 방법론을 필요로 한다. 본 보고서는 현장 엔지니어들의 실무 사례와 법률·의료 AI 개발의 초기 협업 과정을 분석하여, 전문가의 묵시적 지식이 어떻게 구체적인 설계 결정으로 연결되는지를 고찰한다.

## 제1장 전개 배치 엔지니어링(FDE)을 통한 도메인 지식의 공학적 포착

FDE는 단순한 기술 지원이나 고객 성공 관리자가 아닌, 고객의 문제 현장에 직접 뛰어들어 도메인의 '지면 진실(Ground Truth)'을 파악하고 이를 즉각적인 설계 변경으로 연결하는 하이브리드 기술 직군이다. 이들은 "문제가 요구사항 명세서로 해결될 수 있었다면 이미 해결되었을 것"이라는 철학 아래, 현장에서만 발견할 수 있는 비정형적 워크플로우를 포착한다.   

### 1.1 OpenAI와 아이오와 농가의 정밀 농업 피벗 사례

OpenAI의 FDE 팀이 존 디어(John Deere)와 협업하여 아이오와 농가에서 수행한 프로젝트는 연구실의 가설이 현장의 제약 조건과 충돌하며 어떻게 혁신적인 설계로 이어지는지를 보여주는 핵심 사례이다.   

- **사례명 / 출처:** OpenAI FDE 팀의 아이오와 농가 정밀 농업 프로젝트 / Sundeep Teki 블로그 및 OpenAI 실무 보고서    
    
- **핵심 내용:** OpenAI의 FDE 팀은 존 디어와 협력하여 농부들을 위한 AI 기반 잡초 제어 및 정밀 농업 시스템을 구축하기 위해 아이오와 현장에 직접 파견되었다. 이들은 농장의 실제 가동 환경에서 며칠간 상주하며 농부들의 의사결정 과정을 관찰했다. 초기에는 범용 LLM의 분석 능력이면 충분할 것으로 보았으나, 현장의 '계절적 마감 기한'과 '장비 운영의 미세한 제약'이 모델의 성패를 결정짓는다는 것을 발견했다.   
    
- **초기 가설 → 현장 발견 → 설계 변경의 흐름:**
    
    1. **초기 가설:** 범용 언어 모델에 정밀 농업 데이터를 입력하면 표준화된 분석과 처방만으로도 농가의 효율성을 개선할 수 있을 것이다.   
        
    2. **현장 발견:** 농부들은 자신의 토지와 특정 작물 상태에 최적화된 '개인화된 개입'을 요구하며, 파종 및 살포 시기의 좁은 창(window) 내에서 즉각적인 신뢰를 줄 수 있는 결과물을 원한다는 사실을 확인했다. 또한, 연구 환경에서는 드러나지 않았던 에어갭(Air-gapped) 환경의 데이터 거버넌스 마찰과 네트워크 지연 시간의 치명성을 목격했다.   
        
    3. **설계 변경:** 시스템을 범용 분석 도구에서 '개인화된 통찰 엔진'으로 재설계했다. 확률적 모델의 불확실성을 통제하기 위해 현장 데이터 기반의 전용 평가셋(Evals)과 가드레일을 도입했으며, 결과적으로 화학 물질 살포량을 70% 절감하는 정밀도를 달성했다.   
        
- **4장 서술에서 활용 가능한 방식:** 도메인의 3수준(판단 전략)이 단순한 지식 전달이 아닌 '현장 임베딩'을 통해 확보된다는 주장을 뒷받침한다. 특히 연구소의 벤치마크 점수보다 현장의 '효용성(Utility)'이 설계를 주도해야 함을 강조할 때 활용 가능하다.   
    

### 1.2 Palantir의 문제 분해와 멱등성 파이프라인 설계

Palantir가 개척한 FDE 모델은 엔지니어를 문제에 가장 가까운 곳에 배치하여 'Auftragstaktik(임무형 지휘)'이라는 군사적 개념을 기술 영역에 적용한 것이다.   

- **사례명 / 출처:** Palantir FDE의 911 응급 대응 최적화 및 Foundry 파이프라인 설계 / Palantir 기술 블로그 및 데이터 인터뷰 가이드    
    
- **핵심 내용:** 미국 주요 도시의 911 응급 대응 시간을 단축하려는 프로젝트에서 FDE들은 상황실에 상주하며 데이터가 생성되고 소모되는 실시간 과정을 추적했다. 이들은 단순히 데이터를 통합하는 것을 넘어, 현장 요원들이 불완전한 정보 속에서 어떻게 상황을 인지(Situation Awareness)하는지를 분석했다.   
    
- **초기 가설 → 현장 발견 → 설계 변경의 흐름:**
    
    1. **초기 가설:** 911 통화 기록, 교통 데이터, 앰뷸런스 GPS를 실시간 대시보드에 통합하면 최적의 경로 안내가 가능할 것이다.   
        
    2. **현장 발견:** 실제 현장 데이터는 지연 도착(Late Arrivals)이 빈번하고, 데이터 스키마가 예고 없이 변경(Schema Drift)되며, 요원들은 대시보드 수치보다 '신호의 신뢰도'를 더 중요하게 판단한다는 점을 발견했다. 초보적인 데이터 통합 설계로는 무결성이 깨진 정보만 제공할 위험이 컸다.   
        
    3. **설계 변경:** Palantir Foundry의 핵심 기능인 '온톨로지 기반 데이터 파이프라인' 설계로 이어졌다. 늦게 도착하는 데이터에도 결과가 변하지 않는 멱등성(Idempotency) 로직을 아키텍처 수준에서 강제하고, 데이터의 출처를 끝까지 추적할 수 있는 리니지(Lineage) 기능을 설계의 최우선 순위로 배치했다.   
        
- **4장 서술에서 활용 가능한 방식:** 전문가의 판단 전략(수준 3)이 시스템의 안정성 아키텍처(수준 4)로 전환되는 구체적 메커니즘을 설명할 때 사용한다. 데이터의 양보다 '데이터의 오염과 지연에 대한 전문가적 대응'을 알고리즘화한 사례로 제시 가능하다.   
    

### 1.3 Salesforce의 Migration Factory와 현장 R&D

Salesforce의 제품 FDE 팀은 새로운 에이전틱(Agentic) 제품 혁신의 핵심 엔진으로 작동하며, 현장에서 얻은 통찰을 제품 로드맵에 즉각 반영하는 시스템을 갖추고 있다.   

- **사례명 / 출처:** Salesforce Global Migration Factory와 Agentforce 로드맵 설계 / Salesforce 채용 공고 및 제품 전략 문서    
    
- **핵심 내용:** Salesforce는 레거시 솔루션에서 차세대 에이전트 플랫폼인 Agentforce로 고객을 이주시키기 위해 'Migration Factory'를 설립했다. 이는 단순한 기술 지원 조직이 아니라, 이주 과정에서 발생하는 모든 기술적 장벽을 제품 자체의 기본 기능으로 변환하는 '현장 R&D' 조직이다.   
    
- **초기 가설 → 현장 발견 → 설계 변경의 흐름:**
    
    1. **초기 가설:** 표준 마이그레이션 가이드라인과 API 문서만 제공하면 고객사들이 자율적으로 에이전트 기반 시스템으로 전환할 수 있을 것이다.   
        
    2. **현장 발견:** 기업 내부의 부족 지식(Tribal Knowledge)과 문서화되지 않은 레거시 워크플로우가 에이전트 도입의 가장 큰 발목을 잡는다는 사실을 확인했다. 아키텍처 다이어그램에 없는 '현장 예외 처리'가 비즈니스의 핵심임을 발견한 것이다.   
        
    3. **설계 변경:** FDE들은 이러한 발견을 바탕으로 'Words to Workflows'와 같이 자연어로 워크플로우를 정의하고 자동화하는 기능을 Agentforce 플랫폼의 표준 기능으로 승격시켰다. 또한, 마이그레이션 에이전트가 레거시 데이터의 관계성을 스스로 학습하여 온톨로지를 구축하도록 설계를 고도화했다.   
        
- **4장 서술에서 활용 가능한 방식:** "현장이 곧 로드맵"이라는 주장을 뒷받침한다. 일회성 커스텀 개발이 어떻게 범용적인 제품 아키텍처로 진화하는지, 그리고 에이전트가 전문가의 '예외 처리 전략'을 어떻게 표준화하는지를 설명하는 데 유용하다.   
    

## 제2장 법률 및 의료 도메인 전문가 협업과 제품 구체화 사례

Harvey와 Abridge는 법률과 의료라는 고도로 전문화된 영역에서 AI를 성공적으로 안착시킨 대표적 사례이다. 이들의 초기 과정은 도메인 전문가의 워크플로우를 미세하게 분해하고, 전문가가 가진 '신뢰의 기준'을 시스템의 핵심 설계로 치환하는 과정이었다.   

### 2.1 Harvey: 변호사의 추론 과정을 모방한 에이전트 체이닝

Harvey의 공동 창업자 윈스턴 와인버그는 변호사로서 자신이 직접 겪은 비효율을 해결하기 위해 AI 연구원과 함께 법률 워크플로우의 '공학적 해체'를 시도했다.   

- **사례명 / 출처:** Harvey AI의 초기 실험과 Allen & Overy(A&O)와의 공동 빌딩 사례 / Contrary Research 및 Artificial Lawyer 인터뷰    
    
- **핵심 내용:** Harvey는 초기 100개의 법률 질문에 대한 GPT-3의 답변을 변호사들에게 검증받는 과정에서, 변호사들이 단순히 '정확한 답'이 아니라 '추론의 근거'와 '검증 가능한 구조'를 원한다는 점을 명확히 인지했다. 이를 위해 대형 로펌 A&O와 파트너십을 맺고 변호사들을 시스템의 '공동 설계자(Co-Builder)'로 참여시켰다.   
    
- **초기 가설 → 현장 발견 → 설계 변경의 흐름:**
    
    1. **초기 가설:** 지능이 높은 단일 모델이 법률 문서를 읽고 요약해주는 것만으로도 변호사의 업무 시간을 획기적으로 줄일 수 있을 것이다.   
        
    2. **현장 발견:** 전문가들은 답변의 '환각'을 극도로 경계하며, 답변의 각 주장을 개별 판례나 규정과 1:1로 매칭할 수 있는 도구를 원했다. 또한, 변호사 업무는 단순히 답을 내는 것이 아니라 '준비-검토-수정'의 반복적 워크플로우로 구성되어 있음을 확인했다.   
        
    3. **설계 변경:** '에이전틱 워크플로우'를 아키텍처의 기본 단위로 삼았다. 클라이언트 요청을 소규모 작업으로 분해하고, 각 단계마다 서로 다른 전문 모델(조항 추출 모델, 리스크 점수 모델 등)을 배치하여 체인 형태로 연결했다. 또한, 생성된 모든 텍스트에 대해 'Shepardization(판례 인용 검증)'이 가능하도록 원본 소스에 대한 소급 링크(Inline Citations)를 UI에 강제 배치했다.   
        
- **4장 서술에서 활용 가능한 방식:** 도메인 이해의 3수준(판단 전략)이 어떻게 시스템의 모듈화(수준 4)로 이어지는지를 설명한다. 전문가의 추론 단계를 모델의 체인 구조로 치환한 사례로 강조할 수 있다.   
    

### 2.2 Abridge: 소아과 의사의 비정형 대화와 구조적 기록의 결합

Abridge는 의사-환자 간의 대화가 의료의 '지면 진실'이라는 점에 집중하며, 전문가의 대화 속에 숨겨진 임상적 신호를 포착하기 위한 설계를 진행했다.   

- **사례명 / 출처:** Abridge의 소아과 웰 비짓(Pediatric Well Visit) 전용 노트 유형 개발 / Abridge 공식 블로그 및 Time지 선정 발명 사례    
    
- **핵심 내용:** 소아과 의사들은 성인 진료와 달리 발달 단계, 예방 접종, 보호자와의 정서적 교감 등 비정형적 대화 비중이 매우 높다. Abridge 개발팀은 24개 의료 시스템의 소아과 의사들로부터 수십만 건의 피드백을 받아, 표준적인 의료 기록 템플릿이 이들의 판단 전략을 담아내지 못한다는 점을 파악했다.   
    
- **초기 가설 → 현장 발견 → 설계 변경의 흐름:**
    
    1. **초기 가설:** 표준적인 SOAP(주관적, 객관적, 평가, 계획) 형식으로 진료 내용을 요약해주면 모든 전공의가 만족할 것이다.   
        
    2. **현장 발견:** 소아과 의사들은 "9개월 영아"라는 나이 정보와 "식단, 수면, 구강 건강"이라는 대화 주제가 밀접하게 연결되어야 하며, 기록 자체가 환자와의 '개인적인 서사'를 포함해야 한다고 강조했다. 기존의 일률적인 요약 방식은 임상적 맥락을 상실하게 만들었다.   
        
    3. **설계 변경:** '문맥 추론 엔진(Contextual Reasoning Engine)'을 도입했다. 의사의 전공과 환자의 연령을 EHR에서 자동으로 감지하고, 대화 내용에 따라 HPI(현병력)의 하위 섹션을 동적으로 생성하도록 설계했다(예: 사춘기 청소년 진료 시 성적 건강 섹션 자동 활성화). 또한, 의사가 AI의 요약을 신뢰할 수 있도록 노트의 모든 단어를 원본 오디오 타임스탬프와 대조할 수 있는 'Linked Evidence' 기능을 설계의 핵심으로 삼았다.   
        
- **4장 서술에서 활용 가능한 방식:** 전문가와 비전문가의 차이가 '맥락에 따른 유연성'에 있음을 강조할 때 사용한다. AI가 정해진 틀을 전문가에게 강요하는 것이 아니라, 전문가의 상황 판단에 맞춰 시스템이 스스로 변형되는 설계를 보여주는 사례이다.   
    

### 2.3 의료 및 법률 AI의 협업 분석 요약

|**항목**|**법률 AI (Harvey)**|**의료 AI (Abridge)**|
|---|---|---|
|**전문가 페인 포인트**|판례 오용 및 환각 위험|서류 업무로 인한 번아웃과 환자 소외|
|**분해된 업무 단계**|조항 추출 → 리스크 분석 → 답변 조합|대화 녹취 → 임상 신호 파악 → 구조적 노트 생성|
|**예상 밖의 발견**|모델 지능보다 보안 및 권한 체계가 더 중요|정교한 요약보다 '개인화된 서사'의 보존이 중요|
|**제품 설계 영향**|에이전틱 체인 및 샌드박스 보안 설계|문맥 추론 엔진 및 연결된 증거(Linked Evidence)|

  

## 제3장 전문가의 묵시적 지식이 설계에 반영된 구체적 메커니즘

전문가의 묵시적 지식(Tacit Knowledge)은 "우리가 말할 수 있는 것보다 더 많이 알고 있다"는 폴라니의 정의처럼, 명시적인 규칙으로 설명하기 어려운 직관과 통찰의 영역이다. 이를 시스템 설계로 옮기기 위해서는 인지 과학적 방법론인 ACTA가 결정적인 역할을 한다.   

### 3.1 ACTA 방법론을 통한 인지적 신호(Cues)의 추출

ACTA는 전문가들이 상황을 판단할 때 사용하는 미세한 단서인 '신호(Cues)'와 그에 따른 '정신적 시뮬레이션' 과정을 표면화한다.   

- **추출 방법:** '지식 감사(Knowledge Audit)' 세션에서 "비슷한 활력 징후를 보이지만 당신이 즉시 수술실로 보낼 환자와 그렇지 않은 환자의 차이는 무엇인가?"라는 질문을 던진다.   
    
- **발견된 지식:** 노련한 간호사는 혈압이나 맥박 수치가 정상이어도 환자가 호흡을 돕기 위해 무의식적으로 몸을 앞으로 숙이는 자세나, 피부의 미세한 톤 변화를 통해 '보상성 호흡 곤란' 상태임을 알아챈다. 초보자는 기계의 수치만 보느라 이를 놓친다.   
    
- **설계 반영:** 이러한 묵시적 지식은 에이전트의 '능력 매핑(Capability Mapping)'으로 이어진다. 단순히 수치 기반 알람을 울리는 것이 아니라, 카메라 데이터를 통해 환자의 '자세 패턴 인식' 기능을 추가하고, 활력 징후와 결합된 가중치 알고리즘을 설계에 반영한다.   
    

### 3.2 전문가 판단 전략의 시스템 반영 메커니즘 (표)

|**지식 유형**|**추출 방식 (ACTA)**|**시스템 설계 반영 형태**|**부재 시 발생할 설계 실수**|
|---|---|---|---|
|**상황 인식 (SA)**|시뮬레이션 인터뷰: "판단 시 무엇을 보는가?"|멀티모달 데이터 융합 및 우선순위 필터링|모든 데이터를 동일한 중요도로 나열하여 인지 과부하 유발|
|**예외 인식**|지식 감사: "평소와 다른 느낌의 사례는?"|비지도 학습 기반의 이상치 탐지 및 에스컬레이션 경로|정상 범위 내의 미세한 고장 신호(진동, 냄새 등)를 무시|
|**판단 규칙(Heuristics)**|소리 내어 생각하기: "왜 이 결정을 내렸는가?"|조건부 로직(If-Then) 및 에이전트의 사고 체인(CoT)|교과서적인 절차만 반복하여 복잡한 실전 상황 대응 실패|

  

### 3.3 소프트웨어 엔지니어링에서의 묵시적 지식과 에이전트

소프트웨어 개발 분야에서도 시니어 개발자의 묵시적 지식은 에이전트 설계의 성패를 가른다. 시니어 개발자는 코드의 기능적 완성도뿐만 아니라 '향후 유지보수의 용이성'이나 '문서화되지 않은 아키텍처적 관습'을 고려하여 코드를 작성한다.   

- **현상 분석:** 주니어 개발자는 에이전틱 AI에 과도하게 의존하거나 조심스럽게 회피하는 경향을 보이는 반면, 시니어는 AI를 '상세한 권한 위임'의 도구로 활용하며 자신의 foundational instincts를 사용하여 결과물을 조종한다.   
    
- **설계적 제안:** 에이전트가 시니어의 판단을 흉내 내기 위해서는 코드베이스의 '묵시적 문맥(Implicit Context)'을 명시적으로 만들어주는 '컨텍스트 엔지니어링'이 필요하다. 예를 들어, MCP(Model Context Protocol) JSON 계약을 통해 에이전트가 소모할 명시적인 데이터 계약을 정의하는 행위는 전문가의 묵시적 의도를 시스템적으로 명시화하는 과정이다. 만약 이 과정이 없다면, 에이전트는 코드의 표면적인 기능만 이해하여 '동작은 하지만 유지보수는 불가능한' 코드를 대량 생산하는 치명적 실수를 저지르게 된다.   
    

## 제4장 설계 선택으로 이어지는 도메인 이해의 핵심 패턴 분석

조사된 사례들을 종합해볼 때, 도메인 이해의 3수준(전문가의 판단 전략)이 구체적인 수준 4(설계 선택)로 넘어가는 과정에는 일정한 논리적 패턴이 존재한다.

### 4.1 '신뢰의 지렛대' 배치 패턴

전문가는 AI의 지능 자체보다 자신이 최종 책임을 질 수 있도록 돕는 '검증 아키텍처'를 원한다.

- **패턴:** "답변을 믿으라"는 설계에서 "답변을 의심하고 검증하라"는 설계로 전환.   
    
- **설계 선택:** Harvey의 인라인 인용 시스템 , Abridge의 오디오-텍스트 하이퍼링크 , Palantir의 데이터 리니지 추적. 이러한 기능들은 모델의 정확도를 높이는 것이 아니라, 전문가의 '검토 시간'을 단축시키는 공학적 배려이다.   
    

### 4.2 '부족 지식의 명시화' 패턴

기업이나 특정 분과만의 독특한 규칙(Tribal Knowledge)을 시스템의 기본 상수로 취급한다.

- **패턴:** 범용 아키텍처 위에 도메인 전용 '메타데이터 레이어'를 구축.   
    
- **설계 선택:** Salesforce의 온톨로지 기반 마이그레이션 도구 , Abridge의 전공별 문맥 추론 엔진. 이는 에이전트가 "우리 팀은 항상 이렇게 일해왔다"는 전문가의 묵시적 관습을 이해하게 만드는 장치이다.   
    

### 4.3 '인간-에이전트 Agency 협상' 패턴

에이전트가 모든 일을 다 하는 것이 아니라, 인간 전문가가 개입해야 할 '결정적 순간'을 아키텍처가 식별하도록 만든다.

- **패턴:** 수동적 도구에서 능동적 파트너로의 전환.   
    
- **설계 선택:** Harvey Agents의 '협력적 에스컬레이션' 기능(복잡한 태스크 발견 시 인간 전문가에게 즉시 위임) , Palantir Foundry의 인간 참여형(Human-in-the-loop) 승인 게이트웨이.   
    

## 제5장 결론 및 에이전트 개발을 위한 전략적 제언

본 보고서의 분석을 통해 도메인 이해의 심층 수준이 성공적인 AI 에이전트 설계로 이어지기 위한 4가지 전략적 제언을 도출한다.

첫째, **현장을 제품 개발의 실험실로 삼아야 한다.** OpenAI와 Palantir의 사례에서 보듯, 엔지니어가 직접 도메인의 현장에 임베딩되어 "이 질문을 하기 전까지는 몰랐던 것"을 포착하는 과정이 설계의 피벗 포인트를 만든다. 요구사항 문서는 전문가의 판단 전략을 온전히 담아낼 수 없다.   

둘째, **정확도(Accuracy)보다 검증 가능성(Verifiability)에 투자하라.** 전문가들은 99%의 정확도보다 1%의 오류를 찾아내는 데 드는 비용에 더 민감하다. Abridge의 'Linked Evidence'와 같은 설계는 전문가가 자신의 직관을 AI 결과물에 대조해볼 수 있는 '공학적 투명성'을 제공한다.   

셋째, **묵시적 지식을 '구조화된 문맥(Context)'으로 치환하라.** ACTA 방법론을 통해 전문가의 미세한 판단 신호(Cues)를 추출하고, 이를 지식 그래프나 메타데이터 레이어 형태로 시스템에 주입해야 한다. 에이전트가 전문가의 '이유'를 이해하지 못하면, 결국 표면적인 모방에 그치게 된다.   

넷째, **에이전트를 '부족의 일원'으로 진화시켜라.** 모든 산업에 통용되는 범용 에이전트보다, 특정 로펌이나 특정 진료과의 고유한 워크플로우와 언어를 완벽하게 이해하는 에이전트가 더 강력한 해자(Moat)를 형성한다. 이를 위해 전문가를 시스템의 공동 설계자로 참여시키는 구조적 협업 모델이 필수적이다.   

결국 에이전트의 성공은 기술의 정교함이 아니라, 전문가의 머릿속에 들어있는 '판단 전략'이라는 보이지 않는 자산을 얼마나 우아하게 시스템 아키텍처로 번역해내느냐에 달려 있다. 본 보고서에서 제시된 FDE의 실천적 반복과 ACTA의 인지적 추출은 그 번역 과정을 성공으로 이끄는 가장 확실한 지도가 될 것이다.

[

![](https://t1.gstatic.com/faviconV2?url=https://hashnode.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

hashnode.com

Tech's secret weapon: The complete 2026 guide to the forward deployed engineer (role, salary, and interviews) - Hashnode

새 창에서 열기](https://hashnode.com/blog/a-complete-2026-guide-to-the-forward-deployed-engineer)[

![](https://t2.gstatic.com/faviconV2?url=https://www.peraspera.us/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

peraspera.us

How to Build Your 1st Forward Deployed Engineering Team - Per Aspera

새 창에서 열기](https://www.peraspera.us/forward-deployed/)[

![](https://t2.gstatic.com/faviconV2?url=https://tao-hpu.medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

tao-hpu.medium.com

Forward Deployed Engineers: AI's Answer to the SaaS Customization Paradox - Tao An

새 창에서 열기](https://tao-hpu.medium.com/forward-deployed-engineers-ais-answer-to-the-saas-customization-paradox-1223e6425b6f)[

![](https://t1.gstatic.com/faviconV2?url=https://www.sundeepteki.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

sundeepteki.org

The Forward Deployed AI Engineer: Architecting the Last Mile of AI ...

새 창에서 열기](https://www.sundeepteki.org/advice/forward-deployed-ai-engineer)[

![](https://t2.gstatic.com/faviconV2?url=https://www.datainterview.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

datainterview.com

Palantir Forward Deployed Engineer Interview Guide - Datainterview.com

새 창에서 열기](https://www.datainterview.com/blog/palantir-forward-deployed-engineer-interview)[

![](https://t1.gstatic.com/faviconV2?url=https://palantir.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

palantir.com

March 2026 • Announcements - Palantir

새 창에서 열기](https://palantir.com/docs/foundry/announcements/2026-03/)[

![](https://t3.gstatic.com/faviconV2?url=https://builtin.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

builtin.com

Forward Deployed Engineer Associate - Salesforce | Built In

새 창에서 열기](https://builtin.com/job/forward-deployed-engineer-associate/8848546)[

![](https://t3.gstatic.com/faviconV2?url=https://builtin.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

builtin.com

Product Forward Deployed Engineer - Senior - Salesforce | Built In

새 창에서 열기](https://builtin.com/job/product-forward-deployed-engineer-senior/8926795)[

![](https://t1.gstatic.com/faviconV2?url=https://jobs.technyc.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

jobs.technyc.org

Vice President, Global Migration Lead @ Salesforce | Tech:NYC Job Board

새 창에서 열기](https://jobs.technyc.org/companies/salesforce-2/jobs/70041428-vice-president-global-migration-lead)[

![](https://t1.gstatic.com/faviconV2?url=https://www.tealhq.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

tealhq.com

Vice President, Global Migration Lead @ Salesforce - Teal

새 창에서 열기](https://www.tealhq.com/job/vice-president-global-migration-lead_7ea1a9aabbd7a41f331bef45f461e6d2094ad)[

![](https://t1.gstatic.com/faviconV2?url=https://www.rocketlane.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

rocketlane.com

Forward Deployed Engineer (FDE): The Essential 2026 Guide - Rocketlane

새 창에서 열기](https://www.rocketlane.com/blogs/forward-deployed-engineer)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

harvey.ai

How Legal Teams are Working Better With 25,000+ Workflows - Harvey

새 창에서 열기](https://www.harvey.ai/blog/25000-custom-workflows)[

![](https://t2.gstatic.com/faviconV2?url=https://research.contrary.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

research.contrary.com

Report: Harvey Business Breakdown & Founding Story - Contrary Research

새 창에서 열기](https://research.contrary.com/company/harvey)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

abridge.com

Abridge General Counsel Tim Hwang on Principles for Building Trusted AI

새 창에서 열기](https://www.abridge.com/blog/building-trusted-healthcare-ai)[

![](https://t0.gstatic.com/faviconV2?url=https://medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

medium.com

How Harvey Built Trust in Legal AI: A Case Study for Builders | by Takafumi Endo | Medium

새 창에서 열기](https://medium.com/@takafumi.endo/how-harvey-built-trust-in-legal-ai-a-case-study-for-builders-786cc23c3b6d)[

![](https://t2.gstatic.com/faviconV2?url=https://en.wikipedia.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

en.wikipedia.org

Harvey (software) - Wikipedia

새 창에서 열기](https://en.wikipedia.org/wiki/Harvey_\(software\))[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

harvey.ai

How We Approach Design at Harvey

새 창에서 열기](https://www.harvey.ai/blog/how-we-approach-design-at-harvey)[

![](https://t0.gstatic.com/faviconV2?url=https://www.msba.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

msba.org

An Overview of Harvey AI's Features for Lawyers | Maryland State Bar Association

새 창에서 열기](https://www.msba.org/site/site/content/News-and-Publications/News/General-News/An_Overview_of_Harvey_AIs_Features_for_Lawyers.aspx)[

![](https://t1.gstatic.com/faviconV2?url=https://www.pittsburghlifesci.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

pittsburghlifesci.org

PLSA Case Study | Abridge - Pittsburgh life sciences

새 창에서 열기](https://www.pittsburghlifesci.org/case-studies/abridge-2)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

abridge.com

Pediatric Well Visit Note Type - Abridge

새 창에서 열기](https://www.abridge.com/blog/pediatric-well-visit-note-type)[

![](https://t2.gstatic.com/faviconV2?url=https://time.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

time.com

Abridge - The Best Inventions of 2024 - TIME

새 창에서 열기](https://time.com/collections/best-inventions-2024/7094840/abridge/)[

![](https://t2.gstatic.com/faviconV2?url=https://cdt.amegroups.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

cdt.amegroups.org

Transforming clinical documentation with ambient artificial intelligence (AI) scribes: a narrative review of technology, impact, and implementation - Cardiovascular Diagnosis and Therapy

새 창에서 열기](https://cdt.amegroups.org/article/view/148228/html)[

![](https://t2.gstatic.com/faviconV2?url=https://www.fiercehealthcare.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

fiercehealthcare.com

Abridge rolls out customized well visit note for pediatric primary care - Fierce Healthcare

새 창에서 열기](https://www.fiercehealthcare.com/ai-and-machine-learning/abridge-rolls-out-customized-well-visit-note-pediatric-primary-care)[

![](https://t0.gstatic.com/faviconV2?url=https://support.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

support.abridge.com

Pediatric Well Visit - Abridge

새 창에서 열기](https://support.abridge.com/hc/en-us/articles/46496154118291-Pediatric-Well-Visit)[

![](https://t3.gstatic.com/faviconV2?url=https://www.veroscribe.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

veroscribe.com

Abridge AI Scribe Review 2026: Pricing, Accuracy, and Limitations

새 창에서 열기](https://www.veroscribe.com/blog/abridge-review-2026)[

![](https://t2.gstatic.com/faviconV2?url=https://tao-hpu.medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

tao-hpu.medium.com

Harvey AI Hit $8 Billion. Its Tools Still Hallucinate in One of Every Six Queries. - Tao An

새 창에서 열기](https://tao-hpu.medium.com/harvey-ai-hit-8-billion-its-tools-still-hallucinate-in-one-of-every-six-queries-812d64182dc4)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

abridge.com

Transforming Clinical Documentation with Advanced AI | Abridge AI

새 창에서 열기](https://www.abridge.com/ai)[

![](https://t2.gstatic.com/faviconV2?url=https://www.reddit.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

reddit.com

Harvey, Legora - A discussion : r/legaltech - Reddit

새 창에서 열기](https://www.reddit.com/r/legaltech/comments/1rqb7tc/harvey_legora_a_discussion/)[

![](https://t2.gstatic.com/faviconV2?url=http://www.comp.mq.edu.au/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

comp.mq.edu.au

The Meaning of Tacit Knowledge - School of Computing

새 창에서 열기](http://www.comp.mq.edu.au/~richards/papers/TK_Meaning_v2.pdf)[

![](https://t2.gstatic.com/faviconV2?url=https://pmc.ncbi.nlm.nih.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

pmc.ncbi.nlm.nih.gov

The use of cognitive task analysis in clinical and health services research — a systematic review - PMC

새 창에서 열기](https://pmc.ncbi.nlm.nih.gov/articles/PMC8903544/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

researchgate.net

Applied cognitive task analysis (ACTA): a practitioner's toolkit for understanding cognitive task demands - ResearchGate

새 창에서 열기](https://www.researchgate.net/profile/Laura-Militello/publication/13466276_Applied_Cognitive_Task_Analysis_ACTA_A_Practitioner's_Toolkit_for_Understanding_Cognitive_Task_Demands/links/0c96052a08eeb295c6000000/Applied-Cognitive-Task-Analysis-ACTA-A-Practitioners-Toolkit-for-Understanding-Cognitive-Task-Demands.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://mysitasi.mohe.gov.my/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

mysitasi.mohe.gov.my

Advancing Applied Cognitive Task Analysis: Innovative Approaches and Interdisciplinary Insights - MySitasi!

새 창에서 열기](https://mysitasi.mohe.gov.my/uploads/get-media-file?refId=a739d33e-8c2f-42c9-b6a3-883c3a701d1b)[

![](https://t2.gstatic.com/faviconV2?url=https://railroads.dot.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

railroads.dot.gov

Cognitive Task Analysis | FRA - Federal Railroad Administration

새 창에서 열기](https://railroads.dot.gov/human-factors/elearning-attention/cognitive-task-analysis)[

![](https://t3.gstatic.com/faviconV2?url=https://lobehub.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

lobehub.com

applied-cognitive-task-analysis-acta... · LobeHub

새 창에서 열기](https://lobehub.com/skills/curiositech-windags-skills-applied-cognitive-task-analysis-acta-met)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

researchgate.net

Applied Cognitive Work Analysis: A pragmatic Methodology for Designing Revolutionary Cognitive Affordances | Request PDF - ResearchGate

새 창에서 열기](https://www.researchgate.net/publication/281570928_Applied_Cognitive_Work_Analysis_A_pragmatic_Methodology_for_Designing_Revolutionary_Cognitive_Affordances)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

researchgate.net

Applied Cognitive Task Analysis (ACTA): A Practitioner's Toolkit for Understanding Cognitive Task Demands - ResearchGate

새 창에서 열기](https://www.researchgate.net/publication/13466276_Applied_Cognitive_Task_Analysis_ACTA_A_Practitioner's_Toolkit_for_Understanding_Cognitive_Task_Demands)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

researchgate.net

A Cognitive Task Analysis for Developing a Clinical Decision Support System for Emergency Triage | Request PDF - ResearchGate

새 창에서 열기](https://www.researchgate.net/publication/393473789_A_Cognitive_Task_Analysis_for_Developing_a_Clinical_Decision_Support_System_for_Emergency_Triage)[

![](https://t2.gstatic.com/faviconV2?url=https://naturalisticdecisionmaking.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

naturalisticdecisionmaking.org

CTA in Effect Report - Naturalistic Decision Making

새 창에서 열기](https://naturalisticdecisionmaking.org/cta-in-effect-report/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

researchgate.net

From Junior to Senior: Allocating Agency and Navigating Professional Growth in Agentic AI-Mediated Software Engineering - ResearchGate

새 창에서 열기](https://www.researchgate.net/publication/400369631_From_Junior_to_Senior_Allocating_Agency_and_Navigating_Professional_Growth_in_Agentic_AI-Mediated_Software_Engineering)[

![](https://t0.gstatic.com/faviconV2?url=https://medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

medium.com

Making Context Explicit: Why AI Agents Force Better Architecture | Medium

새 창에서 열기](https://medium.com/@gthea/making-context-explicit-e2a172e0c80f)[

![](https://t2.gstatic.com/faviconV2?url=https://www.glean.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

glean.com

How to improve AI agent performance with context engineering - Glean

새 창에서 열기](https://www.glean.com/perspectives/how-to-improve-ai-agent-performance-with-context-engineering)[

![](https://t1.gstatic.com/faviconV2?url=https://github.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

github.com

waldo.BCTelemetryBuddy/docs/DesignWalkthrough.md at main · waldo1001/waldo ... - GitHub

새 창에서 열기](https://github.com/waldo1001/waldo.BCTelemetryBuddy/blob/main/docs/DesignWalkthrough.md)[

![](https://t1.gstatic.com/faviconV2?url=https://www.causaly.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

causaly.com

Context Graphs: A Prerequisite for Agentic AI in Enterprises - Causaly

새 창에서 열기](https://www.causaly.com/blog/context-graphs-for-agentic-ai)[

![](https://t1.gstatic.com/faviconV2?url=https://arxiv.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

arxiv.org

AI Phenomenology for Understanding Human-AI Experiences Across Eras - arXiv

새 창에서 열기](https://arxiv.org/html/2603.09020v1)[

![](https://t2.gstatic.com/faviconV2?url=https://www.reddit.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

reddit.com

We're Winston Weinberg + Gabe Pereyra, the co-founders of Harvey, AMA! - Reddit

새 창에서 열기](https://www.reddit.com/r/legaltech/comments/1pjb3z6/were_winston_weinberg_gabe_pereyra_the_cofounders/)

[

![](https://t1.gstatic.com/faviconV2?url=https://hnhiring.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://hnhiring.com/september-2019)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/the-conversations-shaping-legal-ai-next-chapter)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/practical-framework-for-legal-ai-roi)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/how-steve-johns-is-embedding-ai-into-everyday-legal-practice)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/shared-spaces-and-collaboration-in-harvey)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.abridge.com/)[

![](https://t1.gstatic.com/faviconV2?url=https://web.mit.edu/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://web.mit.edu/16.459/www/Militello98.pdf)[

![](https://t3.gstatic.com/faviconV2?url=https://www.spotium.de/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.spotium.de/solutions/implicit-knowledge-engine)[

![](https://t1.gstatic.com/faviconV2?url=https://atlan.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://atlan.com/know/types-of-ai-agent-memory/)[

![](https://t1.gstatic.com/faviconV2?url=https://www.zippia.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.zippia.com/lendingclub-careers-6758/jobs/)[

![](https://t1.gstatic.com/faviconV2?url=https://www.zippia.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.zippia.com/devops-engineer-vermont-jobs/)[

![](https://t2.gstatic.com/faviconV2?url=https://rajeshjain.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://rajeshjain.com/2025/06/12/progency-mckinsey-x-palantir-the-future-of-marketing-execution/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.shine.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.shine.com/job-search/senior-technical-assistant-jobs-in-nashik)[

![](https://t0.gstatic.com/faviconV2?url=https://legalinnovationspotlight.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://legalinnovationspotlight.com/winston-weinberg/)[

![](https://t3.gstatic.com/faviconV2?url=https://www.science.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.science.gov/topicpages/c/computer-based+cognitive+training.html)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/281412329_Macrocognition_mental_models_and_cognitive_task_analysis_methodology)[

![](https://t0.gstatic.com/faviconV2?url=https://www.semantic-web-journal.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.semantic-web-journal.net/system/files/swj3868.pdf)[

![](https://t2.gstatic.com/faviconV2?url=https://publications.ergonomics.org.uk/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://publications.ergonomics.org.uk/)[

![](https://t3.gstatic.com/faviconV2?url=https://dspace.mit.edu/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://dspace.mit.edu/bitstream/handle/1721.1/151880/ArmengolUrpi-armengol-phd-meche-2023-thesis.pdf?sequence=1&isAllowed=y)[

![](https://t1.gstatic.com/faviconV2?url=https://gerrystahl.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://gerrystahl.net/elibrary/tacit/tacit.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://www.emerald.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.emerald.com/gkmc/article/70/8-9/673/242473/Is-it-possible-to-share-tacit-knowledge-using)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/335133321_The_impact_of_Artificial_Intelligence_on_Design_Thinking_practice_Insights_from_the_Ecosystem_of_Startups)[

![](https://t2.gstatic.com/faviconV2?url=https://metacast.app/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://metacast.app/podcast/no-priors-artificial-intelligence-technology/jyrcnElu/how-harvey-ai-is-changing-the-legal-industry-with-winston-weinberg/BETSJoYe)[

![](https://t1.gstatic.com/faviconV2?url=https://anchor.fm/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://anchor.fm/s/4440da0/podcast/rss)[

![](https://t1.gstatic.com/faviconV2?url=https://www.anzam.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.anzam.org/wp-content/uploads/pdf-manager/1286_GORE_JULIE-373.PDF)[

![](https://t1.gstatic.com/faviconV2?url=https://www.mdpi.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.mdpi.com/2673-9585/6/1/1)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/264125152_Acquiring_and_Sharing_Tacit_Knowledge_in_Software_Development_Teams_An_Empirical_Study)[

![](https://t2.gstatic.com/faviconV2?url=https://journals.riverpublishers.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://journals.riverpublishers.com/index.php/JWE/article/view/5879/5487)[

![](https://t1.gstatic.com/faviconV2?url=https://issuu.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://issuu.com/structuremag/docs/december_structure_2025)[

![](https://t1.gstatic.com/faviconV2?url=https://www.stockholmresilience.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.stockholmresilience.org/download/18.889aab4188bda3f44912a32/1687863825612/SRC_Climate%20misinformation%20brief_A4_.pdf)[

![](https://t1.gstatic.com/faviconV2?url=https://health.ucdavis.edu/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://health.ucdavis.edu/news/headlines/pilot-program-in-emergency-medicine-department-trains-residents-to-use-ai-tool/2026/03)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.abridge.com/case-study/wvu-medicine)[

![](https://t1.gstatic.com/faviconV2?url=https://news.ycombinator.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://news.ycombinator.com/item?id=19797594)[

![](https://t1.gstatic.com/faviconV2?url=https://news.ycombinator.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://news.ycombinator.com/item?id=20867123)[

![](https://t3.gstatic.com/faviconV2?url=https://help.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://help.harvey.ai/articles/knowledge-sources-overview)[

![](https://t3.gstatic.com/faviconV2?url=https://purple.law/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://purple.law/blog/harvey-ai-review-2025/)[

![](https://t1.gstatic.com/faviconV2?url=https://www.gsdcouncil.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.gsdcouncil.org/blogs/harvey-ai-revolutionizing-professional-services-with-generative-intelligence)[

![](https://t2.gstatic.com/faviconV2?url=https://pmc.ncbi.nlm.nih.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://pmc.ncbi.nlm.nih.gov/articles/PMC12973079/)[

![](https://t3.gstatic.com/faviconV2?url=https://builtin.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://builtin.com/job/senior-director-clinical-success/8927441)[

![](https://t3.gstatic.com/faviconV2?url=https://himalayas.app/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://himalayas.app/companies/abridge/jobs/director-product-design-core-product)[

![](https://t3.gstatic.com/faviconV2?url=https://www.healthcaredive.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.healthcaredive.com/news/corewell-health-adopts-abridge-ai-documentation-tool/734672/)[

![](https://t1.gstatic.com/faviconV2?url=https://www.igi-global.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.igi-global.com/chapter/an-introduction-to-applied-cognitive-task-analysis/370574)[

![](https://t2.gstatic.com/faviconV2?url=https://pmc.ncbi.nlm.nih.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://pmc.ncbi.nlm.nih.gov/articles/PMC12992263/)[

![](https://t1.gstatic.com/faviconV2?url=https://www.constellationr.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.constellationr.com/insights/news/forward-deployed-engineers-promise-peril-ai-deployments)[

![](https://t2.gstatic.com/faviconV2?url=https://underdog.io/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://underdog.io/blog/abridge-careers)[

![](https://t3.gstatic.com/faviconV2?url=https://www.abridge.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.abridge.com/product)[

![](https://t3.gstatic.com/faviconV2?url=https://strategeos.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://strategeos.com/blog?blogcategory=Business+Consulting)[

![](https://t0.gstatic.com/faviconV2?url=https://theisrm.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://theisrm.org/documents/McAndrew%20%26%20Gore%20%282013%29%20Understanding%20Preferences%20in%20Experienced-Based%20Choice%20-%20A%20Study%20of%20Cognition%20%27In%20the%20Wild%27.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://www.emerald.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.emerald.com/tpm/article/31/7-8/700/1301868/Advancing-qualitative-methods-to-explore-team)[

![](https://t1.gstatic.com/faviconV2?url=https://arxiv.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://arxiv.org/html/2403.14344v1)[

![](https://t1.gstatic.com/faviconV2?url=https://simplify.jobs/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://simplify.jobs/p/b1992cb3-efbb-4e96-b3d6-d0e1f41c8246/Product-Manager)[

![](https://t0.gstatic.com/faviconV2?url=https://medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://medium.com/@Popstarlabs/doctors-as-ceos-and-founders-why-physician-led-brands-win-and-sometimes-lose-on-credibility-758f38cfa833)[

![](https://t3.gstatic.com/faviconV2?url=https://www.thinkbiotech.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.thinkbiotech.com/jcb/issues/Journal_of_Commercial_Biotechnology_Volume_26_Number_2.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/270960066_Working_Minds_A_Practitioner's_Guide_to_Cognitive_Task_Analysis)[

![](https://t0.gstatic.com/faviconV2?url=https://apps.dtic.mil/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://apps.dtic.mil/sti/trecms/pdf/AD1141308.pdf)[

![](https://t2.gstatic.com/faviconV2?url=https://www.artificiallawyer.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.artificiallawyer.com/2026/04/03/harveys-spectre-agent-points-to-law-firm-world-model/)[

![](https://t2.gstatic.com/faviconV2?url=https://gc.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://gc.ai/compare/gc-ai-vs-harvey)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/how-to-choose-best-legal-ai-platform)[

![](https://t3.gstatic.com/faviconV2?url=https://sourceforge.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://sourceforge.net/software/product/Syneos-Health/alternatives)[

![](https://t2.gstatic.com/faviconV2?url=https://www.scribd.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.scribd.com/document/994916811/Salesforce-AI-Agentforce-Deployment-Strategist)[

![](https://t1.gstatic.com/faviconV2?url=https://www.zippia.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.zippia.com/salesforce-careers-10060/jobs/atlanta-ga/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.100baggers.club/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.100baggers.club/en/reports/pltr)[

![](https://t0.gstatic.com/faviconV2?url=https://apps.dtic.mil/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://apps.dtic.mil/sti/tr/pdf/ADA335225.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://apps.dtic.mil/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://apps.dtic.mil/sti/pdfs/ADA335225.pdf)[

![](https://t2.gstatic.com/faviconV2?url=https://jobs.ashbyhq.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://jobs.ashbyhq.com/revin/8c25ce0d-e405-477d-acea-3cf6d0109356)[

![](https://t3.gstatic.com/faviconV2?url=https://gpt-trainer.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://gpt-trainer.com/blog/what+is+a+forward+deployed+engineer)[

![](https://t2.gstatic.com/faviconV2?url=https://www.prodapt.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.prodapt.com/success/ai-powered-video-analytics-solution-for-field-operations-2/)[

![](https://t1.gstatic.com/faviconV2?url=https://identosphere.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://identosphere.net/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.mexc.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.mexc.com/news/547242)[

![](https://t3.gstatic.com/faviconV2?url=https://www.geeklawblog.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.geeklawblog.com/tag/legal-ai)[

![](https://t0.gstatic.com/faviconV2?url=https://medium.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://medium.com/@Connected_Dots/there-is-something-about-harvey-948e6602ddf1)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/topic/Cognitive-Task-Analysis/publications)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/391504669_Upskilling_Entrepreneurship_Education_Deliberate_Practice_Standards_Cognitive_Task_Analysis_and_Harnessing_AI_for_Skill_Development)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/360118207_Examining_Physicians'_Explanatory_Reasoning_in_Re-Diagnosis_Scenarios_for_Improving_AI_Diagnostic_Systems)[

![](https://t0.gstatic.com/faviconV2?url=https://intuitionlabs.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://intuitionlabs.ai/pdfs/analysis-of-veeva-crm-adoption-in-the-pharmaceutical-industry.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://ascendix.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://ascendix.com/blog/best-salesforce-apps/)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/226761778_The_Continuing_Professional_Development_of_Teachers_Issues_of_Coherence_Cohesion_and_Effectiveness)[

![](https://t1.gstatic.com/faviconV2?url=https://vdoc.pub/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://vdoc.pub/documents/working-minds-a-practitioners-guide-to-cognitive-task-analysis-bradford-books-337bqumgpnlg)[

![](https://t0.gstatic.com/faviconV2?url=https://www.researchgate.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.researchgate.net/publication/358156556_PGxKnow_a_pharmacogenomics_educational_HoloLens_application_of_augmented_reality_and_artificial_intelligence)[

![](https://t0.gstatic.com/faviconV2?url=https://apps.dtic.mil/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://apps.dtic.mil/sti/tr/pdf/ADA385460.pdf)[

![](https://t3.gstatic.com/faviconV2?url=https://www.engineering.iastate.edu/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.engineering.iastate.edu/people/files/2026/04/2026-04-02_Dorneich_CV.pdf)[

![](https://t3.gstatic.com/faviconV2?url=https://sourceforge.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://sourceforge.net/software/product/PubPro/alternatives)[

![](https://t0.gstatic.com/faviconV2?url=https://static1.squarespace.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://static1.squarespace.com/static/602ed17146574c3cf7be9874/t/68c17eb40fdddb7b726dbe05/1757511348550/The+Link+Fall+2025.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://amostech.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://amostech.com/TechnicalPapers/2024/Machine-Learning-for-SDA/Fitzgerald.pdf)[

![](https://t1.gstatic.com/faviconV2?url=https://files.eric.ed.gov/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://files.eric.ed.gov/fulltext/ED677812.pdf)[

![](https://t1.gstatic.com/faviconV2?url=https://www.tellius.com/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.tellius.com/resources/blog/best-ai-platforms-for-pharma-field-force-effectiveness-in-2026-10-platforms-compared)[

![](https://t0.gstatic.com/faviconV2?url=https://intuitionlabs.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://intuitionlabs.ai/articles/veeva-crm-pharma-adoption)[

![](https://t1.gstatic.com/faviconV2?url=https://www.sundeepteki.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.sundeepteki.org/advice)[

![](https://t0.gstatic.com/faviconV2?url=https://apps.dtic.mil/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://apps.dtic.mil/sti/tr/pdf/ADA567929.pdf)[

![](https://t0.gstatic.com/faviconV2?url=https://www.mitre.org/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.mitre.org/sites/default/files/2021-11/prs-20-2210-cognitive-engineering-toolkit.pdf)[

![](https://t2.gstatic.com/faviconV2?url=https://run.unl.pt/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://run.unl.pt/bitstream/10362/191259/1/D0098.pdf)[

![](https://t3.gstatic.com/faviconV2?url=https://quanticle.net/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://quanticle.net/interesting_links.html)[

![](https://t3.gstatic.com/faviconV2?url=https://www.harvey.ai/&client=BARD&type=FAVICON&size=256&fallback_opts=TYPE,SIZE,URL)

새 창에서 열기](https://www.harvey.ai/blog/ai-use-cases-powering-daily-legal-work)