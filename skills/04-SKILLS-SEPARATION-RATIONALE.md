# Skills Separation Rationale — 각 스킬이 별도로 존재하는 이유

## 서론

51개 스킬은 5가지 구조적 결함에 대응한다. 그런데 왜 결함당 하나가 아니라 여러 개인가?

> **각 스킬은 환원 불가능한(irreducible) 차원에서 차이가 있어 병합할 수 없다.**

하나로 합치면 반드시 한쪽의 설계 원칙이 훼손된다.
이 문서는 5가지 결함별로 "왜 이 스킬이 저 스킬에 합쳐질 수 없는가"를 한 문장씩 증명한다.

---

## 결함 1: 기획 부재 — 5개 스킬

### 핵심 분화 축

```
                        대화형
                          │
            requirements-dialogue ─────┼───── co-brainstorming
           (비판적 질문)    │    (비판 금지 발산)
                          │
         requirements-dialogue-anchored │
          (범위 고정 강제)  │
                          │
    ───────────────────────┼──────────────── 사용자 참여
                          │
            auto-planner ──┼───── idea-concretizer
           (비대화, 5분)    │    (AI 혼자 사고)
                          │
                        비대화형
```

### 개별 분리 이유

| 스킬 | 가장 유사한 스킬 | 병합 불가 이유 |
|------|----------------|---------------|
| **requirements-dialogue** | co-brainstorming | requirements-dialogue는 **비판적 질문**으로 모호함을 공격한다. co-brainstorming은 **비판 금지**(Osborn 원칙 1)로 아이디어를 발산한다. 정반대의 대화 철학이므로 하나로 합치면 둘 다 작동하지 않는다. |
| **requirements-dialogue-anchored** | requirements-dialogue | requirements-dialogue는 기획 중 범위가 자연스럽게 확장될 수 있다. requirements-dialogue-anchored는 Phase 0.3에서 **objective/scope/constraint를 불변(immutable)으로 확정**하여 이후 모든 질문이 이 앵커를 벗어나면 경고한다. 앵커가 선택적이면 무시되므로 별도 스킬이어야 강제력이 생긴다. |
| **auto-planner** | requirements-dialogue | requirements-dialogue는 40-50개 질문으로 30-60분 대화가 필요하다. auto-planner는 **비대화형으로 5분 안에 spec을 생성**한다. 상호작용 모델이 정반대이므로 병합하면 어느 쪽도 최적이 아닌 타협안이 된다. |
| **idea-concretizer** | co-brainstorming | idea-concretizer는 **AI 혼자 재귀적으로 사고**하여 3-4개 MVP를 제안한다. 사용자는 결과만 선택한다. co-brainstorming은 **4 AI 페르소나 + 사용자가 함께** 브레인스토밍한다. "혼자 생각"과 "함께 생각"은 다른 창의적 과정이다. |
| **co-brainstorming** | requirements-dialogue | co-brainstorming의 제1원칙은 **"판단 자제"**(비판 금지)이다. requirements-dialogue의 본질은 **"무지의 자각"**(비판적 질문)이다. 같은 스킬 안에서 "비판하지 마"와 "비판적으로 질문해"를 동시에 수행할 수 없다. |

### 분화의 환원 불가능한 3축

1. **상호작용 모델**: AI 혼자(idea-concretizer) / AI+사용자(co-brainstorming, requirements-dialogue) / 비대화(auto-planner)
2. **대화 철학**: 비판적(requirements-dialogue) / 비판 금지(co-brainstorming)
3. **파이프라인 위치**: 아이디어 발산(idea-concretizer/co-brainstorming) → 심층 기획(requirements-dialogue)

---

## 결함 2: 오케스트레이터의 분화

### 오케스트레이터 핵심 분화 축

```
기획 없음 ◄──────────────────────────────► 기획 포함
    │                                          │
    dependency-auto-executor          autonomous-build-harness          full-pipeline-orchestrator-pipeline
    (TASKS.md 필요)     (spec.md만 필요)    (아이디어만 필요)
```

### 개별 분리 이유

| 스킬 | 가장 유사한 스킬 | 병합 불가 이유 |
|------|----------------|---------------|
| **full-pipeline-orchestrator-pipeline** | autonomous-build-harness | full-pipeline-orchestrator-pipeline은 **아이디어→기획→리뷰→명세→빌드→자가개선** 전체를 관통하는 메타 오케스트레이터다. autonomous-build-harness는 기획과 빌드-평가 루프만 담당한다. 역할 범위가 완전히 다르다. |
| **autonomous-build-harness** | dependency-auto-executor | autonomous-build-harness는 **TASKS.md 없이 spec.md에서 직접 빌드**한다(태스크 분해 생략). dependency-auto-executor는 **반드시 TASKS.md(Phase/Task ID 구조)가 필요**하다. 입력 계약이 다르므로 병합 불가. |

### 분화의 환원 불가능한 3축

1. **입력 계약**: 아이디어(full-pipeline-orchestrator-pipeline) / spec.md(autonomous-build-harness) / TASKS.md(dependency-auto-executor)
2. **메모리 아키텍처**: 전체 컨텍스트(dependency-auto-executor)
3. **실패 처리**: 5단계 에스컬레이션 후 skip(dependency-auto-executor) / Build-Eval N사이클(autonomous-build-harness, 사용자 선택)

---

## 결함 3: 검증 없는 완료 선언 — 6개 스킬

### 핵심 분화 축: "무엇이 잘못되었는가?"의 6가지 유형

```
타이밍 ──────────────────────────────────────────────────
  사후     │  pre-completion-verifier (증거 확인)
  1-pass   │  quality-gate (메트릭 측정)
  게이트   │  code-reviewer (의미론적 분석)
           │  sync (명세-코드 일치)
─ ─ ─ ─ ─ ┼ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
  반복     │  powerqa (자동 수정 루프)
  수정     │  systematic-debugger (근본 원인 분석)
```

### 개별 분리 이유

| 스킬 | 가장 유사한 스킬 | 병합 불가 이유 |
|------|----------------|---------------|
| **pre-completion-verifier** | quality-gate | pre-completion-verifier는 **테스트/빌드/린트를 실행하여 pass/fail만 판정**한다(게이트 역할). quality-gate은 **자체적으로 메트릭을 측정하고 기준값 대비 리포트를 생성**한다. "통과했는가?" 판정과 "얼마나 좋은가?" 수치화는 목적이 다르다. |
| **quality-gate** | code-reviewer | quality-gate은 **정량적 메트릭**(커버리지 %, 복잡도 점수, lint 에러 수)을 측정한다. code-reviewer는 **git diff를 읽고 의미론적으로 분석**한다. 숫자 측정과 의미 분석은 다른 인지 작업이다. |
| **code-reviewer** | sync | code-reviewer는 **"코드가 스펙의 의도를 구현했는가?"**를 판단한다(의미 비교). sync는 **"스펙의 구조가 코드의 구조와 일치하는가?"**를 검증한다(스키마 비교). 의도 vs 구조는 다른 검증 차원이다. |
| **sync** | code-reviewer | sync는 스펙과 실제 구조를 **기계적으로 대조**한다. code-reviewer는 인간 리뷰어처럼 **맥락적으로 판단**한다. |
| **powerqa** | pre-completion-verifier | powerqa는 **실패 시 자동으로 수정하고 재실행**한다. verifier는 **1회 실행하고 pass/fail만 보고**한다. powerqa는 루프, verifier는 게이트다. |
| **systematic-debugger** | powerqa | systematic-debugger은 **수정 전에 근본 원인 분석**을 강제한다. powerqa는 **분석 없이 즉시 수정을 시도**한다. 디버깅은 "이해한 후 고친다", powerqa는 "고치면서 이해한다". |

### 분화의 환원 불가능한 3축

1. **타이밍**: 1-pass 게이트(verifier, quality-gate, code-reviewer, sync) / 반복 수정(powerqa, debugging)
2. **검증 대상**: 실행 결과(verifier) / 정량 메트릭(quality-gate) / 의미론(code-reviewer) / 구조 일치(sync)
3. **행동 모드**: 판정만(verifier, quality-gate) / 판정+수정(powerqa) / 분석+이해(systematic-debugger)

---

## 결함 4: 컨텍스트 폭발 — 3개 스킬

### 핵심 분화 축

```
제어 대상
    │
    ├── 출력 크기 ────── token-compressor (CLI 출력 압축)
    │
    ├── 모델 선택 ────── cost-model-router (복잡도별 라우팅)
    │
    └── 입력 정확도 ──── kongkong2 (2-pass 읽기)
```

### 개별 분리 이유

| 스킬 | 가장 유사한 스킬 | 병합 불가 이유 |
|------|----------------|---------------|
| **token-compressor** | cost-model-router | token-compressor는 **"도구를 어떻게 호출하는가"를 최적화**한다. cost-model-router는 **"어떤 모델을 사용하는가"를 최적화**한다. 행동 규칙과 라우팅 전략은 다른 제어 표면이다. |
| **cost-model-router** | token-compressor | cost-model-router는 **모델 선택이라는 선행 결정**을 내린다. token-compressor는 **선택된 모델의 실행을 최적화**한다. 선택→실행 순서가 있으므로 분리되어야 한다. |
| **kongkong2** | token-compressor | kongkong2는 정확도를 위해 **입력 토큰을 의도적으로 증가**시킨다. token-compressor는 **출력 토큰을 감소**시킨다. 방향이 정반대다. |

### 분화의 환원 불가능한 축

**최적화 계층이 다르다**:
```
Layer 2: 라우팅 (cost-model-router) — 어떤 모델이 이 태스크를 실행할지
Layer 1: 행동 (token-compressor) — 선택된 모델이 도구를 어떻게 호출할지
Layer 0: 입력 (kongkong2) — 모델이 입력을 어떻게 읽을지
```

3개 레이어는 **동시에 적용 가능하지만 독립적으로 제어**되어야 한다.

---

## 결함 5: 일회성 생성 — 4개 스킬

### 핵심 분화 축

```
목적 ───────────────────────────────────────────────
    │
    ├── 발견 (미지 탐색) ────── spike
    │                            "모르는 것을 알기 위한 프로토타이핑"
    │
    ├── 성찰 (즉시 개선) ────── reflection
    │                            "방금 생성한 출력을 비판하고 개선"
    │
    └── 체계적 개선 ──────────── self-improvement-loop (+ skills)
                                 "Frozen Metric 기반 자율 실험 루프"

종료 조건 ──────────────────────────────────────────
    │
    ├── 시간 제한 ─────────── spike ("충분히 배웠다")
    ├── 품질 만족 ─────────── reflection (max 3회)
    └── 메트릭 수렴 ────────── self-improvement-loop (outer_score)
```

### 개별 분리 이유

| 스킬 | 가장 유사한 스킬 | 병합 불가 이유 |
|------|----------------|---------------|
| **self-improvement-loop-skills** | self-improvement-loop | skills는 **SKILL.md를 수정 대상으로, 스킬 테스트 케이스 통과율을 Frozen Metric으로** 사용한다. 일반 self-improvement-loop에는 스킬 테스트 러너가 없다. 전용 프레임워크가 필요하다. |
| **reflection** | self-improvement-loop | reflection은 **단일 응답 내에서 동기적으로**(최대 3-pass) 자기 출력을 비판하고 개선한다. self-improvement-loop는 **비동기적으로 git keep/revert 루프**를 돌린다. 시간 범위(temporal scope)가 다르다. |
| **spike** | self-improvement-loop | spike는 **품질 게이트를 의도적으로 완화**하고 빠르게 프로토타이핑한다. self-improvement-loop는 **Frozen Metric으로 품질을 엄격히 측정**한다. 탐색(exploration)과 개선(exploitation)은 다른 전략이다. |

### 분화의 환원 불가능한 3축

1. **시간 범위**: 즉시(reflection) / 단기(spike) / 장기(self-improvement-loop)
2. **종료 조건**: pass 횟수(reflection) / 학습 충분(spike) / 메트릭 수렴(self-improvement-loop)
3. **품질 태도**: 엄격(self-improvement-loop) / 완화(spike)

---

## 종합: 왜 51개가 환원 불가능한 최소 집합인가

### 5대 결함 × 환원 불가능한 축 = 51개

| 결함 | 스킬 수 | 핵심 분화 축 |
|------|--------|-------------|
| 기획 부재 | 5개 | 상호작용 모델 × 대화 철학 × 파이프라인 위치 |
| 오케스트레이션 | 3개 | 입력 계약 × 실패 처리 |
| 검증 없는 완료 | 6개 | 타이밍 × 검증 대상 × 행동 모드 |
| 컨텍스트 폭발 | 3개 | 최적화 계층 (라우팅/행동/입력) |
| 일회성 생성 | 4개 | 시간 범위 × 종료 조건 × 품질 태도 |
| **독립 전문가** (언어/프레임워크/유틸리티) | **30개** | 도메인 특수 지식 |
| **합계** | **51개** | |

### 병합 테스트: "이 두 스킬을 합치면 무엇을 잃는가?"

```
requirements-dialogue + co-brainstorming       = 비판적 질문과 비판 금지를 동시에 → 모순
pre-completion-verifier + powerqa   = 1-pass 게이트와 반복 수정 루프를 동시에 → 역할 혼란
token-compressor + kongkong2 = 토큰 줄이기와 토큰 늘리기를 동시에 → 상충
self-improvement-loop + spike     = 엄격한 메트릭과 게이트 완화를 동시에 → 모순
```

**어떤 두 스킬을 병합하더라도, 적어도 하나의 환원 불가능한 축에서 타협이 발생한다.**

이것이 51개가 **설계 원칙상 최소 필요 집합(minimal sufficient set)**인 이유다.
