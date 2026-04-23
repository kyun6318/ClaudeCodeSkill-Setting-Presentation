# Skills Decision Guide — 상황별 스킬 선택 가이드

## 사용법

> **"지금 나는 어떤 상황인가?"** 에서 출발하여 분기를 따라가면 적절한 스킬에 도달한다.

---

## 마스터 디시전 트리

```
시작
 │
 ├─ Q1. 지금 무엇이 있는가?
 │   │
 │   ├─ "아이디어만 있다" ──────────────────→ [A. 아이디어 단계]
 │   ├─ "기획 문서가 있다" (docs/planning/) ─→ [B. 명세 단계]
 │   ├─ "TASKS.md가 있다" ─────────────────→ [C. 실행 단계]
 │   └─ "아무것도 없다 / 잘 모르겠다" ──────→ [D. 탐색 단계]
 │
 ├─ Q2. 지금 무엇을 하려는가?
 │   │
 │   ├─ "품질을 검증하고 싶다" ────────────→ [E. 검증 단계]
 │   ├─ "버그를 고치고 싶다" ──────────────→ [F. 디버깅 단계]
 │   ├─ "코드를 개선하고 싶다" ────────────→ [G. 개선 단계]
 │   ├─ "비용/토큰을 줄이고 싶다" ─────────→ [H. 최적화 단계]
 │   └─ "Desktop/CLI 연결이 필요하다" ────→ [I. 통합 단계]
 │
 └─ Q3. 어떤 기술을 쓰고 있는가?
     │
     └─ "특정 언어/프레임워크 전문가가 필요하다" → [J. 전문가 단계]
```

---

## A. 아이디어 단계 — "아이디어만 있다"

```
아이디어가 있다
 │
 ├─ 전체 파이프라인을 자동으로 돌리고 싶다?
 │   └─ /full-pipeline-orchestrator-pipeline
 │
 ├─ 개별 단계를 직접 제어하고 싶다
 │   │
 │   ├─ 아이디어가 막연하다 (구체화 필요)
 │   │   │
 │   │   ├─ AI가 혼자 구체화해줬으면 → /idea-concretizer
 │   │   │   "3-4개 MVP 제안을 AI가 재귀적으로 생성"
 │   │   │
 │   │   └─ 함께 브레인스토밍하고 싶다 → /co-brainstorming
 │   │       "4 AI 페르소나 + 나, 비판 금지 발산"
 │   │
 │   └─ 아이디어가 충분히 구체적이다 (기획 진입)
 │       │
 │       ├─ 깊이 있는 대화형 기획 (30-60분)
 │       │   │
 │       │   ├─ 범위 이탈(drift)이 걱정된다 → /requirements-dialogue-anchored
 │       │   │   "Phase 0.3에서 앵커 확정, 이탈 시 경고"
 │       │   │
 │       │   └─ 표준 심층 기획 → /requirements-dialogue
 │       │       "10개 명확성 차원 × 40-50개 질문"
 │       │
 │       └─ 빠른 자동 기획 (5분, 대화 없음)
 │           └─ /auto-planner
 │               "1-4문장 → 비대화형 spec 자동 생성"
 │
 └─ 빠르게 앱을 만들고 싶다 (기획+빌드+QA 한 번에)
     └─ /autonomous-build-harness
         "auto-planner → builder → evaluator 3-에이전트 루프"
```

### A 단계 요약표

| 상황 | 스킬 | 소요 시간 | 사용자 참여 |
|------|------|----------|-----------|
| 전체 자동화 | /full-pipeline-orchestrator-pipeline | 2-4시간 | 최소 |
| 막연한 아이디어 구체화 | /idea-concretizer | 5-10분 | 결과 선택만 |
| 함께 브레인스토밍 | /co-brainstorming | 15-30분 | 적극 참여 |
| 심층 대화형 기획 | /requirements-dialogue | 30-60분 | 질문 응답 |
| 범위 고정 기획 | /requirements-dialogue-anchored | 30-60분 | 질문 응답 |
| 빠른 자동 기획 | /auto-planner | 5분 | 없음 |
| 빠른 앱 생성 | /autonomous-build-harness | 1-2시간 | 최소 |

---

## B. 명세 단계 — "기획 문서가 있다"

```
docs/planning/ 이 있다
 │
 ├─ 기획 품질을 검증하고 싶다
 │   │
 │   ├─ 정적 분석 리뷰 → /codex-review
 │   │   "리뷰→수정→재리뷰 루프 (최대 3라운드)"
 │   │
 │   └─ 파괴적 비평/반증 → /the-fool
 │       "이 기획이 왜 실패할까? 5가지 공격 모드"
 │
 └─ TASKS.md를 만들어야 한다
     └─ /task-decomposer
         "YAML 명세 / 문서 → docs/planning/06-tasks.md"
```

### B 단계 요약표

| 상황 | 스킬 | 입력 | 출력 |
|------|------|------|------|
| 기획 리뷰 (정적 분석) | /codex-review | docs/planning/* | 리뷰 리포트 |
| 기획 공격 (파괴적) | /the-fool | 기획 문서 | 실패 시나리오 |
| 태스크 분해 | /task-decomposer | docs/planning/* | 06-tasks.md |

---

## C. 실행 단계 — "TASKS.md가 있다"

```
TASKS.md가 있다
 │
 └─ /dependency-auto-executor
     "의존성 기반 직렬/병렬 실행 + 품질 게이트"
```

### C 단계 요약표

| 상황 | 스킬 | 특징 |
|------|------|------|
| 표준 실행 | /dependency-auto-executor | 전체 컨텍스트, 실시간 재계획 |

---

## D. 탐색 단계 — "잘 모르겠다"

```
불확실하다
 │
 ├─ 기술적으로 가능한지 모르겠다
 │   └─ /spike
 │       "품질 게이트 완화 + 빠른 프로토타이핑"
 │       출력: docs/spikes/spike-{date}-{topic}.md
 │
 └─ 무엇을 만들어야 할지 모르겠다
     └─ /idea-concretizer 또는 /co-brainstorming
         "아이디어 발산부터 시작"
```

---

## E. 검증 단계 — "품질을 확인하고 싶다"

```
품질 검증이 필요하다
 │
 ├─ 무엇을 검증하려는가?
 │   │
 │   ├─ "빌드/테스트가 통과하는지"
 │   │   └─ /pre-completion-verifier
 │   │       "명령어 실행 → 전체 출력 확인 → 증거 기반 판정"
 │   │
 │   ├─ "코드 품질 메트릭"
 │   │   └─ /quality-gate
 │   │       "커버리지, 복잡도, lint, 타입 에러 정량 측정"
 │   │
 │   ├─ "스펙을 제대로 구현했는지"
 │   │   └─ /code-reviewer
 │   │       "2단계: 스펙 준수 → 코드 품질"
 │   │
 │   └─ "명세와 코드가 일치하는지"
 │       └─ /sync
 │           "spec ↔ 실제 코드 구조 대조"
 │
 ├─ 전체 품질 체인을 한 번에 돌리고 싶다
 │   └─ /dependency-auto-executor 내부 품질 체인 자동 실행
 │       "verification → quality-gate → code-reviewer"
 │       ※ sync는 자동 체인이 아닌 수동 검증
 │
 └─ 테스트 실패를 자동으로 고치고 싶다
     └─ /powerqa
         "테스트/빌드/lint 자동 수정 루프 (최대 5사이클)"
```

### E 단계 요약표

| 검증 대상 | 스킬 | 검증 방법 |
|----------|------|----------|
| 빌드/테스트 통과 | /pre-completion-verifier | 명령어 실행 + 출력 확인 |
| 정량 메트릭 | /quality-gate | 커버리지/복잡도/lint 수치 |
| 스펙 구현 여부 | /code-reviewer | git diff + spec 의미 비교 |
| 명세-코드 구조 일치 | /sync | 구조 대조 |
| 자동 수정 루프 | /powerqa | 5사이클 자동 fix |

---

## F. 디버깅 단계 — "버그를 고치고 싶다"

```
버그가 있다
 │
 ├─ 원인을 모르겠다
 │   └─ /systematic-debugger
 │       "4단계: 증거 수집 → 가설 → 실험 → 검증"
 │       ⚠️ 수정 전에 반드시 분석부터
 │
 ├─ 원인은 알겠는데 자동으로 고치고 싶다
 │   └─ /auto-error-fixer
 │       "Build > Type > Test > Lint 우선순위 자동 수정"
 │
 ├─ 과학적으로 추적하고 싶다
 │   └─ /self-improvement-loop:debug
 │       "가설 → 실험 → 증명/반증 사이클"
 │
 └─ 테스트가 실패하는데 빨리 통과시키고 싶다
     └─ /powerqa
         "자동 수정 루프 (최대 5사이클)"
```

---

## G. 개선 단계 — "코드를 개선하고 싶다"

```
코드를 개선하고 싶다
 │
 ├─ 어떤 종류의 개선인가?
 │   │
 │   ├─ 범용 자율 개선 (모든 도메인)
 │   │   └─ /self-improvement-loop
 │   │       "Frozen Metric + 2계층 RSI 자율 루프"
 │   │
 │   ├─ 스킬 자체 개선
 │   │   └─ /self-improvement-loop-skills
 │   │       "SKILL.md 수정 → 테스트 → keep/revert"
 │   │
 │   ├─ 보안 강화
 │   │   └─ /auto-security-audit
 │   │       "STRIDE + OWASP 자율 보안 감사"
 │   │
 │   ├─ 엣지케이스 발견
 │   │   └─ /self-improvement-loop:scenario
 │   │       "12차원 엣지케이스 탐색"
 │   │
 │   ├─ 문서화
 │   │   └─ /self-improvement-loop:learn
 │   │       "자율 코드베이스 문서화 엔진"
 │   │
 │   ├─ 배포 준비
 │   │   └─ /self-improvement-loop:ship
 │   │       "8단계 배포 워크플로우"
 │   │
 │   └─ 방금 생성한 출력을 즉시 개선
 │       └─ /reflection
 │           "3-pass 자기비판 (동기적, 현재 응답 내)"
 │
 └─ 복잡한 문제를 분석하고 싶다
     │
     ├─ 구조화된 추론 필요 → /reasoning
     │   "CoT, ToT, ReAct 중 적절한 기법 선택"
     │
     └─ 다관점 예측 필요 → /self-improvement-loop:predict
         "5인 페르소나 스웜 분석 → 토론 → 합의"
```

---

## H. 최적화 단계 — "비용/토큰을 줄이고 싶다"

```
비용이 걱정된다
 │
 ├─ CLI 출력이 너무 길다 (토큰 낭비)
 │   └─ /token-compressor
 │       "60-90% 토큰 절감: 출력 압축 + 도구 제한 + 세션 훅"
 │
 ├─ 비싼 모델을 쓸 필요 없는 태스크가 있다
 │   └─ /cost-model-router
 │       "40-70% 비용 절감: 복잡도별 Haiku/Sonnet/Opus 라우팅"
 │
 └─ 정확도를 높이고 싶다 (토큰 추가 투자)
     └─ /kongkong2
         "Query Repetition: 2-pass 읽기로 정확도 향상"
```

---

## I. 통합 단계 — "플랫폼 간 연결이 필요하다"

```
크로스플랫폼 파이프라인
 │
 └─ Claude Desktop에서 설계하고 CLI에서 구현하고 싶다
     └─ /desktop-bridge
         "GitHub Issue 기반 Desktop↔CLI 연결"
```

---

## J. 전문가 단계 — "특정 기술 전문가가 필요하다"

```
어떤 기술을 쓰고 있는가?
 │
 ├─ 언어
 │   ├─ Python 3.11+ ──→ /python-pro
 │   ├─ TypeScript 5.x ─→ /typescript-pro
 │   └─ Go 1.22+ ──────→ /golang-pro
 │
 ├─ 프론트엔드
 │   └─ React 19 ──────→ /react-19
 │
 ├─ 백엔드
 │   └─ FastAPI ───────→ /fastapi-latest
 │
 ├─ 인프라
 │   ├─ Kubernetes ────→ /kubernetes-specialist
 │   ├─ Terraform ─────→ /terraform-engineer
 │   └─ DB 최적화 ─────→ /database-optimizer
 │
 └─ 기타
     ├─ 최신 라이브러리 문서 → /rag
     ├─ 브라우저 자동화 ─────→ /chrome-browser
     ├─ 세션 리포트 ─────────→ /session-report
     └─ 세션 간 기억 ────────→ /memory
```

---

## 빠른 참조: 한 줄 선택 가이드

| 나의 상황 | 스킬 |
|----------|------|
| "아이디어를 앱으로 만들어줘" | `/full-pipeline-orchestrator-pipeline` |
| "빨리 풀스택 앱 만들어줘" | `/autonomous-build-harness` |
| "이거 만들 수 있을지 모르겠어" | `/spike` |
| "아이디어 좀 뽑아줘" | `/idea-concretizer` |
| "같이 브레인스토밍하자" | `/co-brainstorming` |
| "제대로 기획하자" | `/requirements-dialogue` |
| "빨리 기획해줘" | `/auto-planner` |
| "이 기획 검토해줘" | `/codex-review` |
| "이 기획 왜 실패할까?" | `/the-fool` |
| "태스크 분해해줘" | `/task-decomposer` |
| "TASKS.md 실행해줘" | `/dependency-auto-executor` |
| "빌드 통과하는지 확인해" | `/pre-completion-verifier` |
| "코드 품질 측정해줘" | `/quality-gate` |
| "코드 리뷰해줘" | `/code-reviewer` |
| "스펙이랑 코드 맞는지 확인" | `/sync` |
| "테스트 통과시켜줘" | `/powerqa` |
| "버그 원인 분석해줘" | `/systematic-debugger` |
| "자율적으로 개선해줘" | `/self-improvement-loop` |
| "에러 전부 고쳐줘" | `/auto-error-fixer` |
| "보안 취약점 찾아줘" | `/auto-security-audit` |
| "엣지케이스 찾아줘" | `/self-improvement-loop:scenario` |
| "토큰 아끼고 싶어" | `/token-compressor` |
| "비용 줄이고 싶어" | `/cost-model-router` |
| "세션 정리해줘" | `/session-report` |

---

## 유의사항

### 스킬 간 핸드오프 (세션 분리)

긴 작업은 세션을 나누는 것이 좋다:

```
[Session 1] /requirements-dialogue → docs/planning/* 생성 → 세션 종료
[Session 2] /task-decomposer → TASKS.md 생성 → 세션 종료
[Session 3] /dependency-auto-executor → 실행
```

핸드오프 파일(`.claude/handoffs/handoff-*.json`)이 세션 간 컨텍스트를 전달한다.

### 스킬 조합 금지 패턴

| 조합 | 문제 |
|------|------|
| /requirements-dialogue + /co-brainstorming 동시 | 비판적 질문 + 비판 금지 = 모순 |
| /spike + /self-improvement-loop 동시 | 게이트 완화 + 엄격 메트릭 = 상충 |
| /token-compressor + /kongkong2 목적 혼동 | 하나는 줄이고 하나는 늘림 (함께 쓸 수 있지만 목적 이해 필요) |

### 스킬 조합 권장 패턴

| 조합 | 효과 |
|------|------|
| /idea-concretizer → /requirements-dialogue → /codex-review | 발산 → 수렴 → 정적 검증 |
| /dependency-auto-executor + /token-compressor | 실행 효율 + 토큰 절감 |
| /powerqa → /systematic-debugger | 자동 수정 실패 시 근본 원인 분석 |
