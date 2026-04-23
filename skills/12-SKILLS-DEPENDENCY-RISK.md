# Skills Dependency Risk — 의존성 리스크 분석

## 이 문서의 목적

> 스킬 하나가 깨지면 어디까지 영향을 받는가?
> 전체 시스템을 멈출 수 있는 단일 장애점(SPOF)은 어디인가?
> 장애 발생 시 우회 경로는 있는가?

---

## 의존성 그래프 (전체)

```
idea-concretizer ──┐
co-brainstorming ─┤
                  ├──→ requirements-dialogue ──→ (codex-review) ──→ task-decomposer
                            선택적                       │                 │
               requirements-dialogue-                    │                 │
               anchored                                  │                 │
                                                         │                 │
               auto-planner ──→ autonomous-build-harness ──────────┐      │
                                     ↕             │      │
                          (내장 evaluator/QA 루프)  │      │
                                                   │      │
                                                   │      │
                                                   │      │
                                                   │      │
                                                   │      ┌──────┘
                                                   │      ▼
                                           dependency-auto-executor
                                                   │
                                           pre-completion-verifier
                                                   │
                                               quality-gate
                                                   │  ↘ (실패 시)
                                               code-reviewer   powerqa
                                                   │            │ ↘ (3회 실패)
                                                   │      systematic-debugger
                                           auto-security-audit (조건부)
                                           ※ sync는 수동 (PR 생성 전/사용자 요청 시)
                                                   │
                                               self-improvement-loop
                                               self-improvement-loop-skills
```

---

## 장애 영향도 분류

### 등급 정의

| 등급 | 의미 | 영향 범위 |
|------|------|----------|
| 🔴 **Critical (SPOF)** | 이 스킬이 깨지면 전체 파이프라인 정지 | 10개 이상 스킬 영향 |
| 🟠 **High** | 주요 파이프라인 경로 차단, 우회 가능 | 5-9개 스킬 영향 |
| 🟡 **Medium** | 특정 기능 사용 불가, 대안 존재 | 2-4개 스킬 영향 |
| 🟢 **Low** | 독립 스킬, 깨져도 다른 스킬 무영향 | 0-1개 스킬 영향 |

---

## 🔴 Critical — 단일 장애점 (SPOF) 3개

### SPOF-1: `docs/planning/06-tasks.md` (파일)

**스킬이 아니라 파일이지만 가장 위험한 SPOF.**

```
task-decomposer ──출력──→ [docs/planning/06-tasks.md] ──입력──→ dependency-auto-executor
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | 파일 미생성, 경로 오류, 형식 불일치 |
| 영향 범위 | dependency-auto-executor, 품질 체인 전체 |
| 영향 스킬 수 | **10개+** (실행 + 검증 + 개선 전체) |
| 우회 경로 | /autonomous-build-harness (TASKS.md 불필요, spec.md 기반) |
| 복구 방법 | /task-decomposer 재실행, 경로 확인 (`docs/planning/06-tasks.md`) |

**왜 Critical인가**: 이 파일이 없으면 전체 실행 계층이 시작할 수 없다. 오케스트레이터(dependency-auto-executor)가 이 파일에 절대 의존.

---

### SPOF-2: `dependency-auto-executor` (스킬)

```
full-pipeline-orchestrator-pipeline ─→ [dependency-auto-executor] ──→ 품질 체인 전체
                                                  (pre-completion-verifier, quality-gate,
                                                  code-reviewer, auto-security-audit(조건부),
                                                  powerqa, systematic-debugger)
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | 의존성 파싱 실패, worktree 생성 실패, Phase 전환 오류 |
| 영향 범위 | full-pipeline-orchestrator-pipeline, 품질 체인 전체, 자가개선 |
| 영향 스킬 수 | **10개+** |
| 우회 경로 | /autonomous-build-harness (TASKS.md 불필요, 독립 빌드 루프) |
| 복구 방법 | 상태 복원, orchestrate-state.json 확인 |

**왜 Critical인가**: 파이프라인이 실행 단계에서 dependency-auto-executor에 위임. 이 스킬이 깨지면 **기획은 됐지만 실행이 안 되는** 상태.

---

### SPOF-3: `requirements-dialogue` (스킬)

```
full-pipeline-orchestrator-pipeline ─→ [requirements-dialogue] ──→ task-decomposer ──→ 실행 전체
autonomous-build-harness ────────→ [requirements-dialogue] (기획 옵션)
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | 질문 생성 실패, 명확성 점수 계산 오류, 문서 생성 실패 |
| 영향 범위 | full-pipeline-orchestrator-pipeline, autonomous-build-harness, task-decomposer, 이후 전체 |
| 영향 스킬 수 | **10개+** |
| 우회 경로 | /auto-planner (비대화형 대안) |
| 복구 방법 | 핸드오프 파일 확인, docs/planning/ 수동 생성 |

**왜 Critical인가**: 풀 파이프라인의 기획 단계를 담당. 다만 /auto-planner(비대화형), /autonomous-build-harness(기획 내장) 등 우회 경로가 존재하므로 "기획 자체 불가능"은 아님. requirements-dialogue 고유의 심층 기획이 불가능해지는 것이 핵심 영향.

---

## 🟠 High — 주요 경로 차단 4개

### HIGH-1: `task-decomposer`

```
requirements-dialogue ──→ [task-decomposer] ──→ docs/planning/06-tasks.md ──→ 실행 전체
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | ICV 실패, 태스크 형식 오류, 의존성 정의 오류 |
| 영향 스킬 수 | **10개+** (SPOF-1 파일 생성 불가 = 실행 전체 차단) |
| 우회 경로 | TASKS.md 수동 작성 (형식 준수 필요), /autonomous-build-harness |

---

### HIGH-2: `pre-completion-verifier`

```
dependency-auto-executor ──Phase 완료──→ [pre-completion-verifier] ──→ quality-gate ──→ code-reviewer ──→ ...
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | 검증 명령어 실행 실패, 출력 파싱 오류 |
| 영향 스킬 수 | **6개** (품질 체인 전체가 verification 이후에 실행) |
| 우회 경로 | 수동으로 `npm test` 실행 후 결과 확인, /powerqa 직접 실행 |

---

### HIGH-3: `quality-gate`

```
pre-completion-verifier ──→ [quality-gate] ──pass──→ code-reviewer
                               │
                               └──fail──→ powerqa ──→ systematic-debugger
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | 메트릭 수집 실패, 임계값 판정 오류 |
| 영향 스킬 수 | **5개** (code-reviewer, sync, powerqa, systematic-debugger, 후속 전체) |
| 우회 경로 | /code-reviewer 직접 실행 (메트릭 없이 의미론적 리뷰만) |

---

### HIGH-4: `autonomous-build-harness`

```
autonomous-build-harness ──→ [내장 빌드 루프] ←──feedback──→ [내장 QA 루프]
```

| 항목 | 값 |
|------|---|
| 장애 시나리오 | spec 읽기 실패, 기능 순서 결정 오류, 연속 빌드 중단 |
| 영향 스킬 수 | **1개** (전체 통합 파이프라인이므로 스킬 수로는 적지만 영향도 큼) |
| 우회 경로 | /dependency-auto-executor (TASKS.md 기반 실행으로 전환) |

---

## 🟡 Medium — 특정 기능 차단 6개

| 스킬 | 깨지면 영향받는 스킬 | 우회 경로 |
|------|-------------------|----------|
| **full-pipeline-orchestrator-pipeline** | requirements-dialogue, task-decomposer, dependency-auto-executor 호출 자동화 불가 | 개별 스킬 수동 순차 실행 |
| **powerqa** | 자동 수정 루프 불가 | 수동 디버깅 + 수정 |
| **code-reviewer** | 의미론적 리뷰 불가 | /quality-gate (메트릭만) + 수동 리뷰 |
| **codex-review** | 정적 리뷰 루프 불가 | 리뷰 없이 진행 |
| **self-improvement-loop** | 자율 개선 루프 불가 | /powerqa + /reflection (수동 개선) |
| **cost-model-router** | 모델 라우팅 불가 (40-70% 비용 증가) | 전부 기본 모델로 실행 |

---

## 🟢 Low — 독립 스킬 28개

깨져도 다른 스킬에 영향 없음. 해당 기능만 사용 불가:

| 카테고리 | 스킬 (영향 범위 0-1) |
|---------|---------------------|
| 기획 보조 | idea-concretizer, co-brainstorming, the-fool |
| 기획 변형 | requirements-dialogue-anchored, auto-planner |
| 검증 독립 | sync, auto-security-audit, systematic-debugger |
| 자가개선 | self-improvement-loop-skills, reflection |
| 전문가 | python-pro, typescript-pro, golang-pro, react-19, fastapi-latest |
| 전문가 | kubernetes-specialist, terraform-engineer, database-optimizer |
| 유틸리티 | rag, memory, session-report, goal-setting, spike |
| 유틸리티 | chrome-browser, desktop-bridge, a2a, kongkong2, reasoning |

**관찰**: 51개 중 28개(55%)가 독립 스킬. 생태계의 절반 이상이 **격리된 단일 기능**이므로 전체 시스템의 복원력이 높다.

---

## 장애 전파 경로 (Failure Propagation Paths)

### 경로 1: 기획 장애 → 전체 파이프라인 정지

```
requirements-dialogue 장애
    ↓
task-decomposer 실행 불가 (입력 문서 부족)
    ↓
docs/planning/06-tasks.md 미생성
    ↓
dependency-auto-executor 실행 불가
    ↓
품질 체인 실행 불가
    ↓
self-improvement-loop 실행 불가
    ↓
★ 전체 파이프라인 정지 (영향: 10개+ 스킬)
```

**차단점**: requirements-dialogue → task-decomposer가 직렬 의존.
이 중 하나가 깨지면 이 경로는 멈추지만, 전체 파이프라인이 정지하는 것은 아님.

**우회 경로**:
- requirements-dialogue 장애 → /auto-planner 또는 /autonomous-build-harness(기획 내장)
- task-decomposer 장애 → TASKS.md 수동 작성 또는 /autonomous-build-harness

---

### 경로 2: 실행 엔진 장애 → 코드 생성 불가

```
dependency-auto-executor 장애
    ↓
full-pipeline-orchestrator-pipeline 실행 위임 실패
    ↓
품질 체인 트리거 안 됨
    ↓
★ 코드는 기획됐지만 실행 안 됨
```

**완화**: /autonomous-build-harness (독립 실행 경로, TASKS.md 불필요).

---

### 경로 3: 품질 체인 장애 → 검증 없는 코드 생성

```
pre-completion-verifier 장애
    ↓
quality-gate 트리거 안 됨 (증거 없이 넘어감)
    ↓
code-reviewer 트리거 안 됨
    ↓
★ 코드는 생성되지만 검증 없이 merge
```

**이것은 "코드가 안 만들어지는" 것보다 위험하다.**
검증 없이 merge된 코드가 프로덕션에 나가면 막대한 비용 초래.

**완화**: 시스템 hooks에 lint/test를 등록해두면 스킬과 무관하게 실행.

---

### 경로 4: TASKS.md 파일 손상 → 실행 혼란

```
docs/planning/06-tasks.md 형식 오류 (Task ID 형식 불일치 등)
    ↓
dependency-auto-executor 의존성 파싱 실패
    ↓
잘못된 순서로 태스크 실행 또는 완전 정지
    ↓
★ 부분 실행 + 불일치 코드 (정지보다 위험할 수 있음)
```

**완화**: task-decomposer의 ICV(Interface Contract Validation)가 형식 검증.
하지만 수동 편집 시 ICV를 거치지 않으므로 위험.

---

## 장애 시나리오 × 우회 전략 매트릭스

### 시나리오 1: "requirements-dialogue가 깨졌다"

| 영향 | 우회 전략 |
|------|----------|
| full-pipeline-orchestrator-pipeline 실행 불가 | /auto-planner로 대체 (5분, 비대화형) |
| autonomous-build-harness 기획 옵션 하나 사라짐 | "Skip planning" 또는 /auto-planner 선택 |

**복구 시간**: 즉시 (auto-planner로 전환)
**품질 손실**: 중간 (심층 기획 → 빠른 기획으로 격하)

---

### 시나리오 2: "dependency-auto-executor가 깨졌다"

| 영향 | 우회 전략 |
|------|----------|
| full-pipeline-orchestrator-pipeline 실행 불가 | /autonomous-build-harness로 전환 (spec 기반 빌드) |
| 품질 체인 자동 트리거 안 됨 | 개별 스킬 수동 실행 (pre-completion-verifier → quality-gate → code-reviewer) |

**복구 시간**: 중간 (autonomous-build-harness 전환 시 spec 재구성 필요할 수 있음)
**품질 손실**: 낮음 (autonomous-build-harness도 Build-Eval 품질 루프 보유)

---

### 시나리오 3: "token-compressor가 깨졌다"

| 영향 | 우회 전략 |
|------|----------|
| CLI 출력 60-90% 더 소비 | 수동으로 `--stat`, `--short`, `tail -20` 적용 |
| 비용 증가 | cost-model-router는 독립이므로 모델 라우팅은 유지됨 |

**복구 시간**: 즉시 (수동 규칙 적용)
**품질 손실**: 없음 (비용만 증가)

---

### 시나리오 4: "품질 체인 전체가 깨졌다"

| 영향 | 우회 전략 |
|------|----------|
| 검증 없이 코드 merge | hooks에 `npm test && npm run lint` 등록 (시스템 레벨 게이트) |
| 메트릭 수집 불가 | 수동으로 `npm test -- --coverage` 실행 |

**복구 시간**: 즉시 (hooks가 백업 역할)
**품질 손실**: 중간 (자동화된 다단계 → 수동 단일 단계)

---

## 복원력 설계 분석

### 설계상 내장된 복원력

| 설계 요소 | 복원력 기여 |
|----------|-----------|
| **2개 오케스트레이터** | 하나(dependency-auto-executor)가 깨져도 다른 경로(autonomous-build-harness)로 실행 가능 |
| **핸드오프 프로토콜** | 중간 단계 결과가 파일로 저장되어 재시작 가능 |
| **독립 스킬 55%** | 28/51개가 독립이므로 전체 장애 확률 낮음 |

### 복원력이 부족한 영역

| 취약점 | 이유 | 개선 방안 |
|--------|------|----------|
| **기획 직렬 체인** | requirements-dialogue→task-decomposer 직렬 | 중간 결과 캐싱 + 재시작 지점 표시 |
| **TASKS.md 단일 파일** | 전체 실행 계층이 이 파일 하나에 의존 | 형식 검증 자동화 + 백업 |
| **dependency-auto-executor 복잡도** | 의존성 파싱, worktree, 품질 체인이 하나의 스킬에 집중 | 관심사 분리 검토 |

---

## SPOF 제거 전략

### 전략 1: 기획 체인 병렬화

```
현재 (직렬):
  requirements-dialogue → task-decomposer
  (하나가 깨지면 이후 전부 정지)

개선 (선택적 진입):
  requirements-dialogue ─┬→ task-decomposer (표준 경로)
                         │
  auto-planner ──────────┤ (우회 경로)
                         │
  수동 spec 작성 ────────┤ (부분 우회)
```

**이미 부분적으로 존재**: autonomous-build-harness는 auto-planner를 통해 requirements-dialogue를 우회 가능.

---

### 전략 2: TASKS.md 형식 검증 자동화

```
현재:
  task-decomposer 출력 → (검증 없이) → dependency-auto-executor 입력

개선:
  task-decomposer 출력 → [형식 검증 hooks] → dependency-auto-executor 입력
                                │
                         실패 시 즉시 경고 + 형식 수정 제안
```

---

### 전략 3: 품질 체인 독립 진입점

```
현재:
  dependency-auto-executor Phase 완료 시에만 자동 실행

개선:
  /quality-check (독립 스킬)
    → pre-completion-verifier + quality-gate + code-reviewer를 수동으로도 실행 가능
    → dependency-auto-executor 장애 시 수동 품질 검증 가능
```

---

## 장애 대응 가이드

### "방금 파이프라인이 멈췄다" — 3단계 대응

```
Step 1: 어디서 멈췄는가?
  │
  ├─ 기획 단계 (requirements-dialogue/task-decomposer)
  │   → /auto-planner로 우회하거나, docs/planning/ 수동 작성
  │
  ├─ 실행 단계 (dependency-auto-executor)
  │   → /autonomous-build-harness로 전환, 또는 상태 복원 시도
  │
  ├─ 검증 단계 (품질 체인)
  │   → 개별 검증 스킬 수동 실행
  │
  └─ 자가개선 단계 (self-improvement-loop)
      → /reflection으로 대체, 또는 수동 코드 리뷰

Step 2: 중간 결과가 있는가?
  │
  ├─ docs/planning/spec.md 있음 → task-decomposer부터 재시작
  ├─ 06-tasks.md 있음 → dependency-auto-executor부터 재시작
  ├─ 핸드오프 파일 있음 → 해당 세션부터 재시작
  └─ 아무것도 없음 → 처음부터

Step 3: 우회 경로 선택
  │
  ├─ 자동화 유지: /autonomous-build-harness (기획+빌드+QA 한 번에)
  ├─ 수동 제어: 개별 스킬 순차 실행
  └─ 최소 경로: /auto-planner → /dependency-auto-executor (기획 5분 + 실행)
```

---

## 전체 리스크 요약

### 리스크 히트맵

```
         장애 확률
    낮음          높음
     │             │
높 ──┤  SPOF-1     │  (dependency-auto-executor 복잡도 높음)
음   │  SPOF-2     │
     │  SPOF-3     │
영   │             │
향 ──┤  HIGH-1~4   │
도   │             │
     │             │
낮 ──┤  독립 스킬   │  (28개, 깨져도 무영향)
음   │  28개       │
     │             │
```

### 숫자로 보는 복원력

| 지표 | 값 | 평가 |
|------|---|------|
| SPOF 수 | 3개 (파일 1 + 스킬 2) | 관리 가능 |
| High 리스크 스킬 | 4개 | 모두 우회 경로 존재 |
| 독립 스킬 비율 | 55% (28/51) | **양호** — 절반 이상 격리 |
| 우회 경로 보유율 | 100% (모든 SPOF/High에 대안 존재) | **양호** |

### 최종 판정

> **전체 시스템 복원력: 양호 (Good)**
>
> - SPOF 3개가 존재하지만, 모두 우회 경로가 있다.
> - 51개 중 28개(55%)가 독립 스킬이므로 연쇄 장애 범위가 제한적이다.
> - 핸드오프 파일이 장애 전파를 차단한다.
>
> **가장 큰 리스크**: dependency-auto-executor의 복잡도. 의존성 파싱 + worktree + 품질 체인 + merge가 하나의 스킬에 집중되어 있어, 이 스킬의 장애가 가장 넓은 영향을 미친다.
