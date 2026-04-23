# Why These Skills Exist — 51개 스킬의 존재 이유 역추적

## 핵심 테제

> **"막연한 아이디어를 프로덕션 코드로 변환하되,
> 추측 없이(기획), 환각 없이(검증), 낭비 없이(최적화), 멈춤 없이(자율) 완성한다."**

이 51개 스킬은 무작위로 늘어난 도구 모음이 아니다.
**AI 기반 소프트웨어 개발의 일관된 철학 체계**이며, 각 스킬은 구체적인 실패 경험에서 태어났다.

---

## 1. 근본 문제: AI 에이전트의 5가지 구조적 결함

| # | 결함 | 증상 | 결과 |
|---|------|------|------|
| 1 | **기획 부재** | "일단 만들어봐" → 요구사항 불일치 | 3배 재작업 |
| 2 | **숨겨진 가정** | AI가 암묵적으로 결정 → 의도와 괴리 | 잘못된 아키텍처 |
| 3 | **검증 없는 완료 선언** | "완성했습니다" → 실제론 빌드 실패 | 허위 진행률 |
| 4 | **컨텍스트 폭발** | 200개 태스크 → 160K 토큰 → 환각 | 세션 사망 |
| 5 | **일회성 생성** | 한 번 만들고 끝 → 엣지케이스 누락 | 불완전한 제품 |

**이 5가지 결함이 51개 스킬을 낳은 직접적 원인이다.**

---

## 2. 결함별 해결 스킬 맵

### 결함 1: "기획 없이 코딩하면 3배 재작업한다"

**발견 계기**: AI에게 "만들어줘"라고 하면 즉시 코딩을 시작하지만, 완성된 결과물이 원하던 것과 다르다. 수정을 거듭할수록 컨텍스트가 소모되고, 결국 처음부터 다시 만든다.

**해결 전략**: 코딩 전에 모호함을 0.15 이하로 낮춘다.

```
모호함 0.9 (막연한 아이디어)
    ↓ /idea-concretizer, /co-brainstorming — 아이디어 발산, 3-4개 MVP 후보
모호함 0.7 (방향 설정됨)
    ↓ /requirements-dialogue — 10개 명확성 차원 × 40-50개 질문
모호함 0.15 (구현 수준 명확)
    ↓ /task-decomposer — 실행 가능한 태스크 분해
모호함 0.05 (코딩 시작 가능)
```

**왜 requirements-dialogue 하나로 안 되는가?**
- requirements-dialogue는 대화형(40-50개 질문) → 시간이 오래 걸린다
- 빠른 프로토타이핑이 필요할 때 → `/auto-planner` (5분, 비대화형)
- 아이디어 자체가 불명확할 때 → `/idea-concretizer` 또는 `/co-brainstorming`이 먼저 필요
- 기획 문서 간 일관성 검증 → `/codex-review`가 정적 리뷰

**탄생 순서** (추정):
1. `/requirements-dialogue` — 최초의 기획 스킬
2. `/task-decomposer` — requirements-dialogue 출력을 실행 가능한 형태로 변환
3. `/idea-concretizer`, `/co-brainstorming` — requirements-dialogue 이전 단계 필요성 발견
4. `/codex-review` — 기획 품질 검증 필요성 발견
5. `/auto-planner` — 빠른 대안 필요성 발견
6. `/requirements-dialogue-anchored` — 기획 도중 범위 이탈(drift) 문제 해결

---

### 결함 2: "AI의 숨겨진 가정이 프로젝트를 망친다"

**발견 계기**: AI가 "React + Next.js로 만들겠습니다"라고 자연스럽게 결정하지만, 왜 그 선택인지 설명하지 않는다. 나중에 발견하면 이미 수천 줄이 커밋된 후다.

**해결 전략**: 가정을 투명하게 만들고, 의도만 남기고 나머지를 의심한다.

| 스킬 | 역할 | 철학적 근거 |
|------|------|------------|
| `/the-fool` | 기획의 사각지대를 공격 | 포퍼: 반증 가능성이 과학의 조건 |

**왜 철학인가?**

이것은 장식이 아니다. AI 에이전트의 가장 위험한 특성은 **"자신감 있게 틀린다"**는 것이다.

- `/the-fool`은 완성된 기획에 "이게 왜 실패할까?"를 묻는다 (포퍼의 반증주의)

---

### 결함 3: "AI가 '완성했습니다'라고 하지만 실제론 깨져있다"

**발견 계기**: AI가 "구현 완료했습니다"라고 선언하지만, 빌드가 실패하거나 테스트가 없거나 스펙과 다르다. 사용자가 직접 확인하기 전까지 알 수 없다.

**해결 전략**: "완료"를 선언하려면 반드시 증거를 제시해야 한다.

```
Phase 완료 선언
    ↓
/pre-completion-verifier — "증거 먼저, 주장은 나중"
    │  테스트 실행 결과, 빌드 성공 로그, git diff 확인
    ↓
/quality-gate — 메트릭 측정
    │  커버리지 ≥70%, 복잡도 ≤10, lint 에러 0
    │  실패 시 → /powerqa (자동 수정, 최대 5사이클)
    │            동일 실패 3회 → /systematic-debugger (근본 원인 분석)
    ↓
/code-reviewer — 2단계 리뷰
    │  1단계: 스펙 준수 여부 (spec compliance)
    │  2단계: 코드 품질 (code quality)
    ↓
Phase 병합 승인

※ /sync는 자동 체인이 아닌 수동 검증 (PR 생성 전 / 사용자 요청 시)
```

**왜 이렇게 많은 검증 단계인가?**

각 스킬은 서로 다른 유형의 실패를 잡는다:

| 스킬 | 잡는 실패 유형 |
|------|---------------|
| `/pre-completion-verifier` | "빌드가 깨졌는데 완료라 함" |
| `/quality-gate` | "커버리지 30%인데 괜찮다 함" |
| `/powerqa` | "테스트 실패를 수동으로 고쳐야 함" |
| `/systematic-debugger` | "같은 에러가 반복되는데 원인 모름" |
| `/code-reviewer` | "스펙에 없는 기능을 만듦" |
| `/sync` | "스펙은 바뀌었는데 코드는 그대로" |

**탄생 순서** (추정):
1. `/quality-gate` — 메트릭 기반 품질 게이트 도입
2. `/pre-completion-verifier` — "완료" 선언의 증거 요구
3. `/code-reviewer` — 자동 메트릭으로 못 잡는 설계 문제
4. `/powerqa` — 실패 시 수동 개입 없이 자동 수정
5. `/systematic-debugger` — powerqa로도 못 고치는 근본 원인
6. `/sync` — 기획-코드 괴리 감지

---

### 결함 4: "200개 태스크에서 컨텍스트가 폭발한다"

**발견 계기**: 프로젝트 규모가 커지면서 TASKS.md가 200개 이상의 태스크를 포함하게 되었다. 의존성 분석 + 상태 관리 + 실행 로그가 쌓이면 160K 토큰을 넘기며, AI가 환각을 시작 편집한다.

**해결 전략**: 토큰을 압축하고, 비용을 라우팅하고, 실행을 병렬화한다.

| 스킬 | 절감 효과 | 방법 |
|------|----------|------|
| `/token-compressor` | 60-90% 토큰 절감 | CLI 출력 압축 규칙 + 에이전트 도구 제한 + 세션 학습 훅 |
| `/cost-model-router` | 40-70% 비용 절감 | 복잡도별 모델 라우팅 (Haiku/Sonnet/Opus) |
| `/kongkong2` | 정확도 향상 | Query Repetition으로 2-pass 읽기 |

**구체적 압축 전략 (token-compressor)**:
```
BEFORE: git diff          → 전체 패치 출력 (수천 줄)
AFTER:  git diff --stat   → 파일별 변경 요약 (수십 줄)

BEFORE: npm test          → 전체 테스트 로그
AFTER:  npm test 2>&1 | tail -20  → 마지막 20줄만

BEFORE: 분석 에이전트에게 Write/Edit 허용
AFTER:  분석 에이전트는 Read/Grep/Glob만 허용 (실수 방지 + 토큰 절감)
```

---

### 결함 5: "한 번 생성하고 끝 → 엣지케이스 누락"

**발견 계기**: AI가 생성한 코드는 "행복 경로(happy path)"만 커버한다. 에러 핸들링, 동시성 문제, 보안 취약점은 한 번의 생성으로 잡히지 않는다.

**해결 전략**: 자율적으로 반복 개선하는 루프를 만든다.

**self-improvement-loop 핵심 설계 — Frozen Metric**:
```
┌──────────────────────────────────────────────┐
│  prepare.* (동결)  │  target.* (수정 가능)   │
│  ─────────────────  │  ──────────────────    │
│  평가 코드          │  실제 코드             │
│  절대 수정 불가     │  에이전트가 자유롭게    │
│                     │  수정 가능             │
│  → outer_score 측정 │  → 개선 시도           │
└──────────────────────────────────────────────┘
```

**왜 Frozen Metric이 중요한가?**
- AI가 자기 점수를 매기면 → 점수를 올리기 위해 평가 기준을 낮춘다
- 평가 코드를 동결하면 → "실제로 좋아졌는가?"만 측정 가능
- 이것이 **신뢰할 수 있는 자기개선**의 전제조건

**2계층 RSI (Recursive Self-Improvement)**:
```
Inner Loop (RSI-1): 코드를 수정하여 메트릭 향상
Outer Loop (RSI-2): 전략(program.md)을 수정하여 Inner Loop 효율 향상
```

**8개 서브커맨드가 필요한 이유**:
| 서브커맨드 | 최적화 대상 | 왜 별도인가 |
|-----------|------------|-----------|
| `:fix` | 에러 수정 | Build > Type > Test > Lint 우선순위가 도메인 특수적 |
| `:debug` | 버그 원인 | 가설→실험→반증 사이클이 일반 코딩과 다름 |
| `:learn` | 문서화 | 코드 읽기와 문서 생성은 별도 스킬 |
| `:security` | 보안 | STRIDE + OWASP는 전문 지식 필요 |
| `:scenario` | 엣지케이스 | 12차원 탐색은 범용 개선과 다른 전략 |
| `:predict` | 예측 | 5인 페르소나 스웜은 독립적 패턴 |
| `:ship` | 배포 | 8단계 배포 프로세스 |
| `:plan` | 설정 | 셋업 위저드 |

---

## 3. 오케스트레이터가 4개인 이유

"dependency-auto-executor 하나로 충분하지 않나?"라는 질문에 대한 답:

| 오케스트레이터 | 전제 조건 | 강점 | 약점 |
|--------------|----------|------|------|
| `/full-pipeline-orchestrator-pipeline` | 아이디어만 있음 | 전체 파이프라인 자동화 | 오래 걸림 |
| `/autonomous-build-harness` | 없음 (requirements-dialogue/auto-planner/skip 선택 가능) | Build-Eval 피드백 루프 | 오케스트레이터 중 유일하게 기획부터 빌드까지 내장 |
| `/dependency-auto-executor` | TASKS.md 있음 | 의존성 기반 최적 실행 | 기획 없음 |

```
아이디어만 있다 → /full-pipeline-orchestrator-pipeline
기획 문서가 있다 → /autonomous-build-harness 또는 /dependency-auto-executor
TASKS.md가 있다 → /dependency-auto-executor
```

**각각은 서로 다른 "시작 상태"에 대한 최적 해법이다.**

---

## 4. 독립 스킬이 존재하는 이유

### 언어/프레임워크 전문가 (8개)

**문제**: "Python 코드 작성해줘"와 "Go 코드 작성해줘"는 완전히 다른 패턴이 필요하다.

- Python의 async/await ≠ Go의 goroutine
- React의 hooks ≠ Vue의 composition API
- FastAPI의 의존성 주입 ≠ Express의 미들웨어

**해결**: 각 언어/프레임워크의 최신 베스트 프랙티스를 스킬로 인코딩.
범용 "백엔드 만들어줘"보다 `/python-pro`가 Python 3.11+ 패턴을 정확히 적용한다.

### 유틸리티 (나머지)

| 스킬 | 존재 이유 |
|------|----------|
| `/rag` | 라이브러리 버전 변경 → 최신 문서 참조 필요 |
| `/chrome-browser` | 프론트엔드 결과를 눈으로 확인 → 브라우저 자동화 |
| `/memory` | 세션이 끝나면 학습 내용 소실 → 영속 메모리 |
| `/session-report` | "오늘 뭘 했지?" → 자동 리포트 |
| `/desktop-bridge` | Desktop(설계)과 CLI(구현) 분리 → GitHub Issue로 연결 |
| `/a2a` | 에이전트 간 통신 규약 부재 → 프로토콜 정의 |
| `/reasoning` | 복잡한 문제에 구조화된 사고 필요 → CoT/ToT/ReAct |
| `/reflection` | 한 번에 완벽한 출력 불가 → 자기 비판 루프 |
| `/kongkong2` | Google Research: 입력 반복이 비추론 태스크 정확도 향상 |
| `/spike` | 확실하지 않은 영역 → 품질 게이트 완화 + 빠른 탐색 |
| `/goal-setting` | TASKS.md 진행률 모니터링 |

---

## 5. 진화 타임라인 (추정)

### 1세대: 기획 체계화
```
requirements-dialogue, task-decomposer
```
**동기**: "기획 문서가 매번 다르고 불완전하다"
**특징**: 10개 명확성 차원, Domain Guard

### 2세대: 실행 자동화
```
dependency-auto-executor, autonomous-build-harness, quality-gate
```
**동기**: "TASKS.md를 만들었는데 수동으로 실행해야 한다"
**특징**: 의존성 분석, Phase 기반 실행, 품질 게이트

### 3세대: 스케일링 위기 대응
```
token-compressor, cost-model-router
```
**동기**: "프로젝트가 커지니 토큰/비용이 폭발한다"
**특징**: 76% 토큰 절감, 40-70% 비용 절감, 모델 라우팅

### 4세대: 검증 강화
```
pre-completion-verifier, systematic-debugger, the-fool, powerqa
```
**동기**: "AI가 증거 없이 '완료'를 선언한다"
**특징**: 증거 기반 완료, 자동 수정 루프, 반증주의

### 5세대: 자율 개선
```
self-improvement-loop (8개 서브커맨드), self-improvement-loop-skills
```
**동기**: "한 번의 생성으로는 엣지케이스를 못 잡는다"
**특징**: Frozen Metric, 2계층 RSI, 자율 실험 루프

### 6세대: 크로스플랫폼 통합
```
desktop-bridge, a2a, full-pipeline-orchestrator-pipeline
```
**동기**: "개별 스킬은 좋지만 전체를 관통하는 흐름이 없다"
**특징**: 풀 파이프라인 오케스트레이션, 에이전트 간 통신

---

## 6. 설계 원칙 (Design Principles)

51개 스킬을 관통하는 5가지 원칙:

### 원칙 1: 확실성이 먼저다 (Certainty First)
> 코딩 전에 모호함을 0.15 이하로 낮춘다.
- requirements-dialogue: 10개 차원으로 명확성을 측정한다

### 원칙 2: 전략이 힘을 이긴다 (Metis over Bia)
> 소규모 팀이 지략으로 대규모 문제를 정복한다.
- dependency-auto-executor: 의존성 분석으로 최적 실행 순서
- cost-model-router: 적재적소에 적절한 모델 배치

### 원칙 3: 증거가 주장보다 먼저다 (Evidence Before Claims)
> "완료"를 선언하려면 반드시 실행 결과를 보여라.
- pre-completion-verifier: 테스트 통과 증거
- quality-gate: 정량적 메트릭 증거
- the-fool: 반증 가능성 검증

### 원칙 4: 낭비는 죄악이다 (Waste is Sin)
> 불필요한 토큰, 비용, 컨텍스트는 적극적으로 제거한다.
- token-compressor: CLI 출력 60-90% 압축
- cost-model-router: 모델별 40-70% 비용 절감

### 원칙 5: 시스템은 스스로 개선한다 (Recursive Self-Improvement)
> 일회성 생성이 아니라 반복적 개선 루프를 돌린다.
- self-improvement-loop: Frozen Metric + Inner/Outer RSI
- powerqa: 실패 시 자동 수정 (최대 5사이클)
- reflection: 자기 출력 비판 → 개선

---

## 7. 결론: 이 생태계의 본질

이 51개 스킬은 **"AI 에이전트가 실패하는 모든 방식"에 대한 체계적 대응**이다.

```
실패 유형          →  대응 계층           →  핵심 스킬
───────────────────────────────────────────────────────
잘못된 가정        →  투명성 계층         →  the-fool
불명확한 기획      →  기획 계층           →  requirements-dialogue, idea-concretizer, co-brainstorming
불완전한 명세      →  명세 계층           →  task-decomposer
비효율적 실행      →  오케스트레이션 계층  →  dependency-auto-executor
거짓 완료 선언     →  검증 계층           →  pre-completion-verifier, quality-gate, code-reviewer
토큰/비용 폭발     →  최적화 계층         →  token-compressor, cost-model-router
일회성 한계        →  자가개선 계층       →  self-improvement-loop, reflection
플랫폼 분절        →  통합 계층           →  desktop-bridge, a2a, full-pipeline-orchestrator-pipeline
```

각 스킬은 독립적으로도 유용하지만, **파이프라인으로 연결될 때 전체가 부분의 합보다 커진다.**

이것은 도구 모음이 아니라, **AI와 인간이 함께 소프트웨어를 만드는 방법론**이다.
