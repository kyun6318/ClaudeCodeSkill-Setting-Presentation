# Skills Maturity Assessment — 스킬 성숙도 평가

## 평가 기준

| 지표 | 측정 방법 |
|------|----------|
| **문서 규모** | SKILL.md 파일 크기 (bytes) |
| **지원 파일 수** | 디렉토리 내 파일 수 |
| **하위 디렉토리** | references/, templates/, constitutions/, scripts/ 존재 여부 |
| **버전 표기** | 명시적 v1/v2/v3 표기 여부 |
| **교차 참조 수** | 다른 스킬에서 이 스킬을 참조하는 횟수 |
| **내용 깊이** | DEEP / MODERATE / SHALLOW |
| **완성도** | COMPLETE / PARTIAL / STUB |

---

## Tier 분류

### Tier 1: 엔터프라이즈급 (Enterprise-Grade) — 4개

대규모 문서, 다수의 지원 파일, references/ 디렉토리, 높은 교차 참조.

| 스킬 | 크기 | 파일 수 | 버전 | 교차참조 | 평가 |
|------|------|--------|------|---------|------|
| **fastapi-latest** | 456KB | 다수 | - | 54 | API 레퍼런스 집합체, 프레임워크급 문서 |
| **react-19** | 106KB | 다수 | - | 24 | 프레임워크 전체 문서 포함 |
| **self-improvement-loop** | 대형 | 다수 | v3.0 | 33 | 3세대 진화, 8개 서브커맨드, 2계층 RSI |
| **autonomous-build-harness** | 대형 | 다수 | - | 24 | 3-에이전트 아키텍처, Build-Eval 루프 |

**특징**: 단순 프롬프트가 아니라 **프레임워크 수준의 문서와 지원 자산**을 보유.

---

### Tier 2: 프로덕션급 (Production-Ready) — 15개

10-15KB 범위, references/ 디렉토리 보유, 안정적 API.

| 스킬 | 버전 | 특징 |
|------|------|------|
| **requirements-dialogue** | v2 | 핵심 기획 스킬, 10개 명확성 차원 |
| **requirements-dialogue-anchored** | v2 | Drift Anchor 강화 변형 |
| **task-decomposer** | - | Domain-Guarded 태스크 구조 |
| **full-pipeline-orchestrator-pipeline** | - | 5단계 모델 최적화 파이프라인 |
| **dependency-auto-executor** | - | 의존성 기반 실행 엔진 |
| **council-codex** | v2 | Codex MCP 정적 리뷰 루프 |
| **auto-planner** | - | 비대화형 5분 spec 생성 |
| **idea-concretizer** | - | AI 재귀적 MVP 제안 |
| **co-brainstorming** | - | 4 페르소나 브레인스토밍 |
| **the-fool** | - | 5모드 파괴적 비평 |
| **memory** | - | 5유형 세션 간 기억 |
| **desktop-bridge** | - | Desktop↔CLI 연결 |
| **token-compressor** | - | 3계층 토큰 절감 |
| **cost-model-router** | - | 복잡도 기반 모델 라우팅 |
| **powerqa** | - | 5사이클 자동 QA |

**특징**: 명확한 입출력 계약, 다른 스킬과의 통합 지점 정의, 실전 사용 가능.

---

### Tier 3: 기능적 (Functional) — 17개

전문 분야에 특화, 핵심 기능은 완성.

| 스킬 | 특징 | 비고 |
|------|------|------|
| **quality-gate** | 메트릭 기반 품질 게이트 | 기능적이지만 간결 |
| **code-reviewer** | 2단계 리뷰 | 핵심 기능 완성 |
| **pre-completion-verifier** | 증거 기반 완료 판정 | 단일 목적, 간결 |
| **sync** | 명세-코드 동기화 검증 | 단일 목적 |
| **systematic-debugger** | 4단계 근본 원인 분석 | 단일 목적, 완성 |
| **self-improvement-loop-skills** | 스킬 자체 개선 엔진 | - |
| **auto-error-fixer** | 우선순위 기반 자동 에러 수정 | - |
| **auto-security-audit** | STRIDE + OWASP 기반 자율 보안 감사 | - |
| **reflection** | 3-pass 자기비판 | 완성 |
| **reasoning** | CoT/ToT/ReAct | 완성 |
| **a2a** | 에이전트 통신 프로토콜 | 완성 |
| **goal-setting** | TASKS.md 진행률 모니터링 | 완성 |
| **session-report** | 세션 종료 리포트 | 유틸리티 |
| **spike** | 탐색 모드 | 유틸리티 |
| **chrome-browser** | 브라우저 자동화 | 외부 도구 연동 |
| **rag** | Context7 문서 검색 | - |
| **kongkong2** | Query Repetition | 좁은 범위, 완성 |

---

### Tier 4: 경량 전문가 (Lightweight Specialist) — 6개

5KB 미만, 최소한의 구조, 역할 정의에 집중.

| 스킬 | 크기 | 특징 |
|------|------|------|
| **typescript-pro** | ~3KB | 언어 패턴 규칙 집합 |
| **python-pro** | ~3KB | 언어 패턴 규칙 집합 |
| **golang-pro** | ~3KB | 언어 패턴 규칙 집합 |
| **database-optimizer** | ~4KB | DB 최적화 규칙 |
| **kubernetes-specialist** | ~4KB | K8s 패턴 규칙 |
| **terraform-engineer** | ~4KB | IaC 패턴 규칙 |

**특징**: 의도적으로 가볍다. 프롬프트 고정이 목적이므로 과도한 문서가 불필요.

---

## 버전 관리 현황

| 버전 | 스킬 | 의미 |
|------|------|------|
| **v3.0** | self-improvement-loop | 3세대 진화, 8개 서브커맨드, Guard + Chain 패턴 |
| **v2.x** | requirements-dialogue, requirements-dialogue-anchored, council-codex | 2세대, 안정화된 핵심 기획 스킬 |
| **v1.0** | self-improvement-loop-skills | 1세대, 초기 릴리스 |
| **미표기** | 나머지 37개 | 버전 추적 없음 |

---

## 중복/통합 분석

### 통합 후보 (MERGE Candidates) — 1쌍

#### 1. requirements-dialogue-anchored → requirements-dialogue `--anchored`

| 항목 | 현재 | 통합 후 |
|------|------|--------|
| 스킬 수 | 2개 | 1개 |
| 사용법 | `/requirements-dialogue-anchored` | `/requirements-dialogue --anchored` |
| 기능 손실 | 없음 | 없음 |
| 이점 | - | Phase 0.3 앵커를 선택적으로 활성화 |

**판정**: 통합 가능하나, 앵커 강제가 "선택적"이 되면 무시될 위험. **별도 유지도 합리적.**

---

## 개선 필요 스킬 (NEEDS-WORK) — 0개

> 현재 개선이 시급한 스킬은 없습니다.

---

## 폐기 후보 (DEPRECATE Candidates) — 0개

> 폐기 대상 스킬은 없습니다. 42개 사용자 추가 스킬 모두 최소한 기능적(Functional) 수준 이상을 달성했습니다.

---

## 고립 스킬 (Isolated Skills) — 3개

다른 스킬과의 교차 참조가 낮아 생태계 통합이 약한 스킬:

| 스킬 | 교차참조 | 상태 |
|------|---------|------|
| **the-fool** | 낮음 | 내용은 DEEP이나 생태계 연결 약함 |
| **chrome-browser** | 낮음 | 외부 도구 연동 (의도적 격리) |
| **kongkong2** | 3 | 자동 적용 패턴 (의도적 격리) |

**관찰**: 이 중 chrome-browser, kongkong2는 **의도적으로 격리**된 것(범용 패턴이므로 특정 스킬에 종속되면 안 됨). the-fool은 **통합 여지**가 있음.

---

## 성숙도 매트릭스 (전체 요약)

```
                    높은 통합도
                        │
      Tier 1            │            Tier 2
   (엔터프라이즈)         │         (프로덕션)
                        │
   fastapi-latest ●     │     ● requirements-dialogue
   react-19 ●           │     ● full-pipeline-orchestrator-pipeline
   self-improvement-loop ●       │     ● task-decomposer
   autonomous-build-harness ●            │     ● dependency-auto-executor
                        │     ● rag
 ───────────────────────┼──────────────────────
                        │
      Tier 4            │            Tier 3
   (경량 전문가)         │          (기능적)
                        │
   typescript-pro ●     │     ● quality-gate
   python-pro ●         │     ● code-reviewer
   golang-pro ●         │     ● trinity
   database-optimizer ● │     ● the-fool
   kubernetes-specialist│     ● reflection
   terraform-engineer ● │
                        │
                    낮은 통합도

          작은 규모 ◄────────────► 큰 규모
```

---

## 액션 아이템

### 중기 검토 (Needs Discussion)

| # | 액션 | 대상 | 고려사항 |
|---|------|------|---------|
| 1 | requirements-dialogue-anchored 통합 검토 | requirements-dialogue* | 강제성 약화 위험 |
| 2 | 고립 스킬 연결 강화 | the-fool | 다른 스킬에서 자동 호출 트리거 추가 |
| 3 | 스킬에 버전 표기 추가 | 전체 | 변경 추적 개선 |

### 장기 방향 (Strategic)

| # | 액션 | 효과 |
|---|------|------|
| 4 | references/ 미보유 스킬에 레퍼런스 추가 | 문서 품질 균일화 |
| 5 | 스킬별 테스트 케이스 표준화 | self-improvement-loop-skills로 자동 품질 관리 |
| 6 | 스킬 사용 통계 수집 (어떤 스킬이 자주 쓰이는가) | 데이터 기반 폐기/강화 판단 |

---

## 결론

### 전체 건강도: **우수 (Excellent)**

```
폐기 대상:           0개 / 42개  (0%)
개선 필요:           0개 / 42개  (0%)
통합 후보:           1쌍 / 42개  (2%)   — 검토 단계
프로덕션 이상:       19개 / 42개 (45%)  — Tier 1(4) + Tier 2(15)
엔터프라이즈급:       4개 / 42개  (10%)
기능적:             17개 / 42개 (40%)  — Tier 3
경량 전문가:          6개 / 42개  (14%)  — Tier 4
```

> 42개 커스텀 스킬 모두 기능적 수준 이상입니다. 생태계 안정화 및 자동화 품질이 매우 높습니다.
