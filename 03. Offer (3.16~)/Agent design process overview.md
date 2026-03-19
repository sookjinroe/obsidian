① 페르소나 정의
   산출물: 페르소나 카드
        ↓
② 발화 가설 작성               ← 업무 시나리오 기준
   산출물: 설계 매트릭스 (발화 컬럼만 채움)
        ↓
③ 설계 전 Walk-through
   산출물: 설계 매트릭스 (요구사항 컬럼 추가)
        ↓
④ 계위 판단 트리 적용
   산출물: 설계 매트릭스 (구성요소·계위·플러그인 컬럼 추가)
        ↓
⑤ 발화 목록 보완               ← 구성요소 커버리지 기준으로 발화 추가
   산출물: 설계 매트릭스 (발화 행 추가)
        ↓
⑥ 설계 후 Walk-through ←────────────────┐
   산출물: 설계 매트릭스 (구멍 컬럼 추가) │
        ↓                                │
   심각 구멍 있음? ──Yes──→ 구성·요구사항 수정 ─┘
        │
       No
        ↓
⑦ 구성 확정
   산출물: 설계 매트릭스 (확정본)
        ↓
⑧ CF 설계서 작성
   산출물: CF 설계서 (SKILL.md 등)



```
설계 플러그인 (우리가 만드는 것)
├── 페르소나 정의
├── 발화 가설 작성
├── 설계 전 Walk-through → 요구사항 도출
├── 계위 판단 트리 적용
├── 설계 후 Walk-through → 구멍 발견
└── 설계 매트릭스 확정
        ↓
plugin-dev (공개)
├── 플러그인 폴더 구조 생성
├── commands/ · hooks/ · .mcp.json 작성
└── 각 구성요소 구현 가이드
        ↓
skill-creator (공개)
├── SKILL.md 초안 작성
├── description 최적화
└── eval 루프 (with/without 비교)
```

세 조각이 순서대로 연결됩니다. 특정 고객 커스텀 제외하면 반복 가능한 플러그인 빌드 파이프라인이 됩니다.

---

## "거의"인 이유 — 하나 남은 것

설계 플러그인이 **아직 존재하지 않습니다.**

우리가 만든 것은 문서 두 개입니다. 이걸 실제로 에이전트가 쓸 수 있는 플러그인으로 만드는 작업이 남아 있습니다.

```
현재 상태
├── agent_component_reference.md  ← 문서
└── agent_design_process.md       ← 문서

필요한 것
└── design 플러그인
    ├── skills/persona-builder/SKILL.md
    ├── skills/utterance-designer/SKILL.md
    ├── skills/design-walkthrough/SKILL.md
    └── skills/matrix-builder/SKILL.md
```

그리고 아이러니하게도 이 설계 플러그인 자체를 만들 때 skill-creator + plugin-dev를 쓰면 됩니다.

---

## 실제 순서

```
① agent_component_reference.md    완성 ✅
   agent_design_process.md         완성 ✅
        ↓
② 이 두 문서를 기반으로 설계 플러그인 제작
   → skill-creator + plugin-dev 활용
        ↓
③ 세 플러그인 조합 완성
   설계 플러그인 + skill-creator + plugin-dev
        ↓
④ 특정 고객 커스텀 작업
   (도메인 Knowledge, 스키마 온보딩 등)
```

②가 다음 단계입니다.