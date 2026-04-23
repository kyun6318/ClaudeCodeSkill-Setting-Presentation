# Skills Demo Cards — 영상 직전 공유용 슬라이드 묶음

> `SKILLS-DEMO-PLAN.md`의 예제 시연에 사용할 각 스킬을, **영상을 재생하기 직전**에 한 장으로 요약해 보여주기 위한 카드 모음.
> 카드 구성 = ① 핵심 개념 / ② 구조 / ③ 코드(축약) / ④ 예제에서 드러낼 장면.
>
> 공개 범위: `PRESENTATION-CONTEXT.md` §5 — **프론트매터/스키마/섹션 헤더까지만, 프롬프트 본문과 내부 대화 규칙은 `(축약)` 처리**. 개인 경로는 `~/.claude/skills/<name>/` 수준으로만 표기.
>
> 발표 표기: 직관 이름(kebab-case). 실제 호출명은 `SKILL-NAME-MAPPING.md`로 변환.

---

## 0. 전체 카드 운영 원칙

### 0.1 왜 카드를 영상 앞에 두는가

- 영상은 **"흐름"**을, 카드는 **"왜 이 스킬이 있는가"**를 담당한다.
- 카드 없이 영상만 보여주면 청중은 파이프라인은 보지만 각 단계의 **설계 이유**를 잡지 못한다.
- 카드를 먼저 1분 이내로 설명 → 영상 구간 재생 → 재개 식의 **카드→영상→카드→영상** 리듬이 집중도를 유지한다.

### 0.2 카드 표준 레이아웃 (1슬라이드)

```
┌───────────────────────────────────────────────────────────┐
│  /스킬-직관-이름                             [파이프라인 단계] │
│  핵심 개념: 한 줄                                           │
│                                                            │
│  ┌── 구조 ──────────┐  ┌── 축약 코드 ─────────────────┐     │
│  │ 위치·입출력·트리거 │  │ ---                          │     │
│  │                   │  │ name: ...                    │     │
│  │                   │  │ description: ...             │     │
│  │                   │  │ ---                          │     │
│  │                   │  │ ## ... (섹션 헤더)           │     │
│  │                   │  │ (본문 축약)                   │     │
│  └───────────────────┘  └──────────────────────────────┘    │
│                                                            │
│  예제 장면: Before / After 한 줄씩                          │
└───────────────────────────────────────────────────────────┘
```

### 0.3 상세 카드 vs 묶음 카드

| 분류 | 카드 수 | 용도 |
|---|---|---|
| **상세 카드 (개별)** | 7장 | 영상 구간마다 직전에 1장씩 삽입 |
| **묶음 카드 (그룹)** | 5장 | 같은 역할군을 한 슬라이드에 압축, 짧게 언급만 |

상세 7 + 묶음 5 = **총 12장**. 실제 deck에는 **2장 · 5장 · 6장 · 8장에 분산 삽입**됨 (7장 일괄 합류 아님 — 아래 "영상과의 러닝 타임 매핑"과 `SKILLS-DEMO-PLAN.md §5` 참조).

---

# 상세 카드 (영상 구간 직전 1장씩)

---

## 카드 1. /requirements-dialogue `[기획 · 1단계]`

**핵심 개념**
"대충 말하면 AI가 알아서 결정한다"를 차단. 10개 명확성 차원을 **질문으로 되돌려** 사용자가 결정권을 되찾게 한다. Ambiguity가 임계치 아래로 내려갈 때까지 코드를 한 줄도 쓰지 않는다.

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 1단계 — 시작점 |
| 입력 | 사용자 아이디어, `.claude/memory/user-profile.md`, (선택) `co-brainstorming-proposal.md` |
| 출력 | `docs/planning/06-*.md` 9개 기획 문서 → `/screen-spec-generator`로 핸드오프 |
| 실제 스킬명 | `requirements-dialogue` |

**축약 코드** (`~/.claude/skills/requirements-dialogue/SKILL.md`)

```yaml
---
name: requirements-dialogue
description: 1:1 대화로 요구사항 수렴. 10개 명확성 차원 × 수렴 엔진 → 9개 기획 문서
---

# 핵심 워크플로우 (축약)
#  Phase 0.5  존재론적 탐색  "X란 무엇인가?"
#  Phase 0.7  경쟁 분석       SWOT, 차별화
#  Phase 0.8  페르소나 딥다이브 JTBD
#  Phase 0.9  비즈니스 모델 검증 CAC/LTV
#  Phase 1    핵심 기능 도출
#  Phase 2~   화면/스택/DB/컨벤션 결정
# Phase 전환 조건: Ambiguity Score ≤ 0.4
# 출력: docs/planning/06-*.md (9개 문서)
```

**예제 장면** (영상 0:00~0:40)
- Before: "주간 노트 허브 만들어줘" → AI가 Firebase + Clerk로 시작
- After: 10개 차원 질문 → 사용자가 Supabase + 이메일 OTP 선택

---

## 카드 2. /hidden-assumption-surfacer `[메타 · 기획 보조]`

**핵심 개념**
AI가 대화 중 암묵적으로 쌓는 가정을 `[stated] / [inferred] / [assumed] / [uncertain]` 4유형으로 분류해 **`hidden-assumption-surfacer.md`** 라는 파일로 가시화한다. 신뢰 등급(ESTABLISHED / WORKING / OPEN)으로 우선순위 관리.

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 메타 — 기획 전/중/후 어디서든 호출 |
| 입력 | 현재 세션 대화, 기존 `hidden-assumption-surfacer.md` |
| 출력 | 갱신된 `hidden-assumption-surfacer.md` |
| 실제 스킬명 | `hidden-assumption-surfacer` |

**축약 코드**

```yaml
---
name: hidden-assumption-surfacer
description: AI의 숨겨진 가정을 4유형으로 분류, 신뢰 등급 관리
disable-model-invocation: true
---

# 가정 분류 (축약)
#  [stated]     사용자가 명시함
#  [inferred]   대화 맥락에서 추론
#  [assumed]    AI가 임의로 깐 것  ← 가장 위험
#  [uncertain]  불확실, 질문 필요
#
# 신뢰 등급: ESTABLISHED > WORKING > OPEN
# 출력 파일: hidden-assumption-surfacer.md (명시적 기록)
```

**예제 장면** (영상 0:40~1:10)
- Before: 같은 스펙을 두 번 넣었을 때 결과가 다름 (드리프트)
- After: `[assumed]` 항목 5개가 먼저 쏟아져 나옴 → 사용자가 승격/기각

---

## 카드 3. /screen-spec-generator `[명세 · 2단계]`

**핵심 개념**
"화면이 주도하되, 도메인이 방어한다". 화면은 **무엇이 필요한지**만 YAML로 선언(`data_requirements`), 백엔드는 **어떻게 제공할지** 독립 결정. v2.0에서 화면당 400줄 → **100줄**로 축약.

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 2단계 |
| 입력 | `docs/planning/06-screens.md`, `.claude/handoffs/handoff-*.json` |
| 출력 | `specs/screens/*.yaml`, `specs/domain/resources.yaml` |
| 실제 스킬명 | `screen-spec-generator` |

**축약 코드** (스킬 프론트매터 + 생성되는 YAML 예시)

```yaml
---
name: screen-spec-generator
description: 화면별 상세 명세 YAML v2.0. data_requirements로 도메인과 느슨 결합
---
# Phase 0~5: 핸드오프 확인 → 기획 확인 → 화면 명세 → 공통 추출 → 커버리지 검증 → (선택) Stitch 목업
```

```yaml
# 생성물 예시: specs/screens/note-list.yaml
name: note-list
position: main
layout: sidebar-main
data_requirements:
  - { resource: notes, fields: [id, title, tags, updated_at], filter: {by: user_id} }
events:
  - on: click_item           → navigate: note-detail
  - on: submit_search_query  → filter: notes
tests:
  - 빈 상태에서 Empty 뷰 노출
  - 태그 필터 다중 선택 시 AND 결합
  - 검색어 디바운스 300ms
```

**예제 장면** (영상 1:10~1:40)
- Before: 명세 없이 구현 → 4번째 화면에서 스키마가 어긋남
- After: `resources.yaml`이 먼저 존재 → 모든 화면이 같은 리소스를 참조

---

## 카드 4. /task-decomposer `[태스크 · 3단계]`

**핵심 개념**
Domain-Guarded 분해. 태스크를 `P2-R1(Resource)` / `P2-S1(Screen)`로 **분리**해, 화면 요구와 도메인 리소스 간 **Interface Contract Validation**을 자동 수행한다. 출력 경로는 **고정** (`docs/planning/06-tasks.md`).

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 3단계 |
| 입력 | `specs/screens/*.yaml`, `specs/domain/resources.yaml` |
| 출력 | `docs/planning/06-tasks.md` (Canonical Contract) |
| 실제 스킬명 | `task-decomposer` |

**축약 코드**

```yaml
---
name: task-decomposer
description: Domain-Guarded 태스크 분해. 화면×리소스 Interface Contract Validation
---
# 실행 모드 (자동 감지)
#  specs/screens/*.yaml 존재 → Domain-Guarded 모드 (권장)
#  docs/planning/ 만      → 문서 기반 모드
#  둘 다 없음              → 코드 분석 모드
#
# Task ID 표준: P0-T0.1, P2-R1-T1 (Resource), P2-S1-T1 (Screen), P2-S1-V (Verify)
# 출력 경로 고정: docs/planning/06-tasks.md  ← 루트에 두면 오케스트레이터가 못 읽음
```

**예제 장면** (영상 1:40~2:00)
- Before: 화면과 백엔드 태스크가 섞여 의존 관계가 불명확
- After: `P2-R1-* → P2-S1-*` 순서가 자동 결정, 병렬 가능한 묶음도 식별

---

## 카드 5. /dependency-auto-executor `[실행 · 5단계]`

**핵심 개념**
`docs/planning/06-tasks.md`를 파싱 → 의존성 그래프 → **Git Worktree + TDD** 강제 → Phase 리뷰·QA·보안 게이트 → main 병합/원격 push까지 **한 루프로 닫는다**. 사람은 상태만 관찰한다(4장의 "오케스트레이터" 역할의 구체적 예).

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 5단계 — 실행 엔진 |
| 입력 | `docs/planning/06-tasks.md` |
| 출력 | 코드 + 테스트, `.claude/orchestrate-state.json`, `.claude/metrics/` |
| 품질 체인 | verification → quality-gate → code-reviewer → security → frontend |
| 실제 스킬명 | `dependency-auto-executor` |

**축약 코드 — 실행 체인**

```yaml
---
name: dependency-auto-executor
description: 06-tasks.md 의존성 기반 자동 직렬/병렬 실행. Phase 병합까지 완전 자동화
---
# 표준 실행 체인
#   태스크 선택 → 전문가 위임 → Worktree 준비(Phase 1+)
#   → TDD (RED → GREEN → REFACTOR) → 자체 검증 + 로컬 커밋
#   → Phase 리뷰/QA/보안 게이트 → main 병합 → 원격 push
#
# 핵심 규칙
#   - 병합/push는 오케스트레이터만 (전문가 에이전트는 구현·검증까지)
#   - 병렬은 같은 Phase × 다른 파일 × 최대 3~4개
#   - TASK_DONE:{task_id}:{commit_sha} 로만 완료 보고
```

**예제 장면** (영상 2:00~2:30)
- Before: 사람이 체크박스 찍으며 다음 태스크 수동 지시
- After: 병렬 3개 Worktree가 동시에 돌고, 사람은 Phase 게이트만 승인

---

## 카드 6. /pre-completion-verifier `[검증 · 품질 체인 1번]`

**핵심 개념**
**"완료" 주장은 증거로 대체된다.** 빌드/타입/테스트/런타임 명령을 실제로 실행하고 exit code와 출력을 확인한 뒤에만 주장할 수 있다. 품질 체인의 **가장 먼저** 호출되는 게이트.

**구조**

| 항목 | 내용 |
|---|---|
| 위치 | 품질 체인 1번 (→ quality-gate → code-reviewer → security → frontend) |
| 트리거 | Phase 완료 자동, 또는 `/pre-completion-verifier` 수동 |
| 실패 시 | powerqa 자동 수정 → 같은 Phase 브랜치로 TDD 재진입 |
| 실제 스킬명 | `pre-completion-verifier` |

**축약 코드 — Iron Law & Gate Function**

```yaml
---
name: pre-completion-verifier
description: 완료/통과 주장 전 반드시 검증 명령 실행. 항상 주장 전 증거
---
# Iron Law
#   ⛔ 새로운 검증 증거 없이 완료 주장 금지
#
# Gate Function (모든 주장 전 5단계)
#   1. IDENTIFY  이 주장을 증명하는 명령은 무엇인가
#   2. RUN       fresh, complete 실행
#   3. READ      전체 출력 + exit code + 실패 수
#   4. VERIFY    출력이 주장을 확인하는가
#   5. ONLY THEN 증거와 함께 주장
# 어느 단계든 건너뛰기 = 거짓말
```

**예제 장면** (영상 2:30~2:50)
- Before: AI "완료했습니다" → 빌드 돌려보면 타입 에러
- After: AI가 스스로 `npm run build`, `npm test` 실행 → 마지막 20줄을 증거 블록으로 첨부

---

## 카드 7. /self-improvement-loop `[자율 개선 · 7단계]`

**핵심 개념**
"일회성 생성"을 막는 **Frozen Metric + keep/revert 루프**. 3파일 원칙(`prepare.*` 불변 / `target.*` 에이전트 수정 / `program.md` 인간 지시)과 2계층 RSI(Inner=코드 개선, Outer=전략 개선). Guard로 다른 기능 파손 감시.

**구조**

| 항목 | 내용 |
|---|---|
| 파이프라인 | 7단계 — 수렴 이후 상시 |
| 입력 | 최적화 대상(`target.*`), 평가 코드(`prepare.*`), 디렉티브(`program.md`) |
| 출력 | keep 결정된 실험 결과 커밋들, 개선 궤적 그래프 |
| 실제 스킬명 | `self-improvement-loop` |

**축약 코드 — 3파일 원칙 + 2계층**

```yaml
---
name: self-improvement-loop
description: Karpathy self-improvement-loop 패턴. Frozen Metric + keep/revert 자율 실험 루프
---
# 3파일 원칙 (불변)
#   prepare.*    고정 인프라 (데이터/평가/유틸)  ← 수정 금지
#   target.*     실험 대상                    ← 에이전트만 수정
#   program.md   목표/제약/힌트               ← 인간(Outer에선 에이전트)
#
# 2계층 RSI
#   L0 meta_eval  outer_score 측정 (절대 불변)
#   L1 outer      전략/program.md
#   L1.5 eval     평가 인프라 (Frozen Metric)
#   L2 target     Inner Loop 수정 영역
#
# Verify vs Guard
#   Verify  "메트릭이 개선되었나?"   → 숫자
#   Guard   "다른 것이 깨졌나?"     → pass/fail
#   Guard 실패 → revert → rework(최대 2회) → discard
```

**예제 장면** (영상 2:50~3:15)
- Before: 검색 정확도를 개선하고 싶은데 무엇이 나아졌는지 알 수 없음
- After: 정답 라벨 50건 고정 → 인덱싱 전략 5회 변형 → 점수 오른 버전만 keep

---

# 묶음 카드 (역할군별 한 슬라이드 요약)

---

## 묶음 A. 아이디어 발산 `[아이디어]`

| 스킬 | 1줄 역할 | 언제 쓰나 |
|---|---|---|
| `/idea-concretizer` (`idea-concretizer`) | 추상 한 줄 → 구체 MVP 3~4개 제안 | 문제는 있는데 형태가 불분명할 때 |
| `/co-brainstorming` (`co-brainstorming`) | AI 4인 페르소나 + 사용자 공동 브레인스토밍 (Osborn 4원칙) | 제안을 더 확산시키고 싶을 때 |

**흐름 예시**: `/co-brainstorming` → 제안 10개 → `/idea-concretizer` → MVP 3개 → `/requirements-dialogue` 진입.

---

## 묶음 B. 메타 (가정·전제 해체) `[메타]`

| 스킬 | 1줄 역할 |
|---|---|
| `/hidden-assumption-surfacer` (`hidden-assumption-surfacer`) | **AI 가정의 가시화** — 상세 카드 2 참조 |
| `/technical-premise-deconstructor` (`technical-premise-deconstructor`) | 방법적 의심으로 기술 전제 해체 → 의도만 남기고 의도에서 코드 연역 |

**차이**: hidden-assumption-surfacer는 **대화 중 누적된 가정** 관리, technical-premise-deconstructor는 **프로젝트 시작 전 기술 선택 전제** 해체.

---

## 묶음 C. 실행 · 대체 경로 `[실행]`

| 스킬 | 언제 쓰나 |
|---|---|
| `/dependency-auto-executor` (`dependency-auto-executor`) | 표준 — 상세 카드 5 |
| `/full-pipeline-orchestrator` (`full-pipeline-orchestrator`) | 아이디어 → 빌드까지 한 방에 돌리고 싶을 때 |
| `/autonomous-build-autonomous-build-harness` (`autonomous-build-harness`) | spec만 있고 Build-Eval 루프를 맡기고 싶을 때 |
| `/ultra-thin-executor` (`ultra-thin-executor`) | 태스크 200개 이상 — 오토 컴팩팅 없이 |

**선택 기준**: 태스크 수 × 사람 개입 의지 × 루프 자율성.

---

## 묶음 D. 검증 보조 `[검증]`

| 스킬 | 품질 체인 위치 |
|---|---|
| `/pre-completion-verifier` | 1번 — 상세 카드 6 |
| `/quality-gate` (`quality-gate`) | 2번 — 메트릭/리포트 |
| `/code-reviewerer` (`code-reviewer`) | 3번 — 스펙 준수 → 코드 품질 2단계 |
| `/systematic-debugger` (`systematic-debugger`) | 버그 발생 시 — 재현 → 가설 → 검증 → 수정 |

**규칙**: 품질 체인은 **순서**를 바꾸지 않는다. 스펙 준수 확인 전에 코드 품질을 보면 잘못된 것을 잘 만든 꼴.

---

## 묶음 E. 자율 개선 특화 + 공통 축소층 `[개선 · 운영]`

| 스킬 | 1줄 역할 |
|---|---|
| `/auto-error-fixer` (`auto-error-fixer`) | Build > Type > Test > Lint 우선순위로 자동 수정 루프 |
| `/auto-security-audit` (`auto-security-audit`) | STRIDE + OWASP Top 10 자율 탐색 |
| `/self-improvement-loop` (`self-improvement-loop`) | 범용 엔진 — 상세 카드 7 |
| `/token-compressor` (`token-compressor`) | 출력 압축 + 도구 제한 + 세션 학습 훅 |
| `/cost-model-router` (`cost-model-router`) | 태스크 복잡도에 따라 모델 자동 선택 (40~70% 절감 범위) |

---

# 영상과의 러닝 타임 매핑

카드→영상→카드→영상 리듬을 5장(사전 녹화 영상 2-3분) 안에 녹이는 배치.

| 구간 | 시간 | 내용 |
|---|---|---|
| 5-6 도입 발화 | 0:00~0:15 | "지금부터 4단계를 짧게 보실 겁니다" |
| **카드 1** (requirements-dialogue) | 0:15~0:45 | 한 줄 읽기 + "장면: Before/After" |
| 영상 구간 ① | 0:45~1:10 | /requirements-dialogue 10개 질문 → Supabase 결정 |
| **카드 3** (screen-spec-generator) | 1:10~1:35 | YAML 한 장 제시 |
| 영상 구간 ② | 1:35~2:00 | YAML 생성되는 화면 |
| **카드 5** (dependency-auto-executor) | 2:00~2:20 | 실행 체인 한 번 읽기 |
| 영상 구간 ③ | 2:20~2:50 | Worktree 3개 병렬, Phase 게이트 통과 |
| **카드 6** (pre-completion-verifier) | 2:50~3:10 | Iron Law 한 줄 |
| 영상 구간 ④ | 3:10~3:30 | 완료 선언 → 증거 블록 첨부 |
| 마감 | 3:30~3:45 | "이 흐름이 5가지 결함에 1:1 대응합니다 → 6장으로" |

> 카드 2·4·7은 **5장 외 다른 장**에서 소비한다.
> - 카드 2 (hidden-assumption-surfacer) → 2장 "숨겨진 가정" 사례 직후
> - 카드 4 (task-decomposer) → 6장 "파이프라인" 슬라이드에서 짧게
> - 카드 7 (self-improvement-loop) → 8장 "효과/한계" 직전 1장

---

# 공개 범위 체크리스트 (발표 전 최종 확인)

- [ ] 모든 카드의 "축약 코드" 블록에 **프롬프트 본문 전체가 들어가지 않음** — 섹션 헤더 + 의사 주석까지만
- [ ] 경로는 `~/.claude/skills/<name>/` 수준까지만, 사용자명·개인 디렉토리 노출 없음
- [ ] YAML 예시의 토큰/URL은 `...` 또는 마스킹 처리
- [ ] "실제 스킬명" 열은 카드에 남기되, 슬라이드 본문 설명은 **직관 이름**으로만 말함
- [ ] 7장 설정 슬라이드와의 중복 카드 없음 (7장은 `CLAUDE.md`, `settings.json`, 디렉토리 구조 — 이 카드는 개별 스킬 내용)

---

*이 문서: SKILLS-DEMO-CARDS.md | 연관: SKILLS-DEMO-PLAN.md, PRESENTATION-PLAN.md, PRESENTATION-OUTLINE.md, PRESENTATION-CONTEXT.md, SKILL-NAME-MAPPING.md, PRESENTATION-SLIDES-5.md~7.md*
