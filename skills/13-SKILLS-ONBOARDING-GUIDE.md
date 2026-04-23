# Skills Onboarding Guide — 온보딩 가이드

## 이 문서의 대상

> 51개 스킬 생태계를 **처음 접하는 사람**이
> 압도당하지 않고, 단계적으로 시스템을 익히고, 실전에 투입하기 위한 가이드.

---

## 먼저 읽어야 할 문서

| 순서 | 문서 | 읽는 이유 | 소요 시간 |
|------|------|----------|----------|
| 1 | **이 문서 (13)** | 어디서부터 시작할지 | 10분 |
| 2 | **01-SKILLS-CATALOG** | 51개가 뭐가 있는지 훑기 | 15분 |
| 3 | **06-SKILLS-DECISION-GUIDE** | "내 상황에서 뭘 써야 하지?" | 10분 |
| 4 | **08-SKILLS-ANTI-PATTERNS** | "이것만은 하지 마라" | 15분 |

나머지 문서(02-05, 07, 09-12)는 **궁금할 때** 찾아보면 된다.

---

## 단계별 온보딩 (4주 과정)

### Week 1: 관찰자 — "있는 것 확인하기"

**목표**: 51개 스킬이 뭔지 파악. 아직 아무것도 실행하지 않는다.

#### Day 1-2: 스킬 목록 파악

```bash
# 설치된 스킬 목록 확인
ls ~/.claude/skills/

# 스킬 하나 열어서 구조 파악 (가장 작은 것부터)
cat ~/.claude/skills/typescript-pro/SKILL.md | head -30
cat ~/.claude/skills/python-pro/SKILL.md | head -30
```

**이해할 것**:
- 스킬 = SKILL.md 파일 하나가 핵심
- SKILL.md 안에 트리거 조건, 실행 규칙, 금지 사항이 모두 정의됨
- 스킬 이름 앞에 `/`를 붙이면 실행 (예: `/requirements-dialogue`)

#### Day 3-4: 3가지 범주 이해

| 범주 | 수 | 비유 | 예시 |
|------|---|------|------|
| 프롬프트 고정 | ~30개 | 요리 레시피 | /requirements-dialogue, /python-pro |
| 프로세스 강제 | ~11개 | 조리 매뉴얼 체크리스트 | /full-pipeline-orchestrator-pipeline, /dependency-auto-executor |
| 진짜 새 능력 | ~10개 | 새로운 조리 도구 | /self-improvement-loop, /autonomous-build-harness |

> **Week 1 끝나면**: "아, 이런 것들이 있구나" 수준이면 충분.

---

### Week 2: 초보자 — "독립 스킬 써보기"

**목표**: 안전하고 독립적인 스킬부터 실행해본다. 파이프라인은 아직 안 건드린다.

#### 추천 첫 실행 스킬 (위험도 0, 독립 실행)

| 순서 | 스킬 | 실행 방법 | 예상 결과 |
|------|------|----------|----------|
| ① | `/rag` | "OOO에 대해 문서 검색해줘" | Context7 기반 문서 검색 결과 |
| ② | `/reasoning` | 복잡한 질문 후 "추론해봐" | CoT/ToT 구조화된 사고 |
| ③ | `/python-pro` | Python 코드 작성 요청 | 3.11+ 베스트 프랙티스 적용 |
| ④ | `/typescript-pro` | TypeScript 코드 작성 요청 | strict 모드 패턴 적용 |
| ⑤ | `/systematic-debugger` | 버그 보여주고 "분석해줘" | 4단계 근본 원인 분석 |

**실행 방법**:
```
사용자: /rag
Claude: [rag 스킬 로드됨]
사용자: "이 프로젝트의 아키텍처에 대해 문서를 검색해줘"
```

#### 두 번째 실행 그룹 (토큰 최적화)

| 순서 | 스킬 | 실행 방법 | 체감 효과 |
|------|------|----------|----------|
| ⑥ | `/token-compressor` | 설정 후 일반 작업 | CLI 출력이 짧아짐 |
| ⑦ | `/cost-model-router` | 태스크 분류 확인 | 비용 인식 향상 |

> **Week 2 끝나면**: "스킬이 뭘 하는지 체감했다" 수준.

---

### Week 3: 중급자 — "기획 스킬 써보기"

**목표**: 기획 스킬을 실행하여 문서를 생성해본다.

#### 기획 스킬 실행 순서

```
Step 1: 간단한 아이디어 준비
  예: "할 일 관리 웹앱을 만들고 싶다"

Step 2: 빠른 기획부터 (5분)
  /auto-planner
  → 입력: "할 일 관리 웹앱. 태그 분류, 우선순위, 마감일 알림"
  → 출력: docs/autonomous-build-harness/spec.md 자동 생성
  → 확인: spec 내용 읽어보기

Step 3: 심층 기획 (30분, 선택적)
  /requirements-dialogue
  → 40-50개 질문에 답하기
  → 출력: docs/planning/01-09 (9개 문서)
  → 확인: 각 문서 훑어보기

Step 4: 기획 리뷰 (선택적)
  /council-codex
  → 입력: docs/planning/ 기획 문서
  → 출력: council-report.md
```

#### 기획 결과물 확인 체크리스트

```
□ docs/planning/01-project-overview.md 생성됨?
□ docs/planning/06-screens.md에 화면 목록 있음?
□ 기획 문서 간 일관성 있음? (같은 기능이 다른 이름으로 등장하지 않음?)
□ 모호한 부분 없음? (requirements-dialogue의 모호함 점수 0.15 이하?)
```

> **Week 3 끝나면**: "기획 스킬이 어떤 문서를 만드는지 안다" 수준.

---

### Week 4: 실전 투입 — "파이프라인 돌려보기"

**목표**: 전체 파이프라인을 처음부터 끝까지 실행해본다.

#### 옵션 A: /autonomous-build-harness (빠른 경로, 권장)

```
/autonomous-build-harness
→ Phase 0: "Auto-planner" 선택 (5분 기획)
→ Phase 1: auto-planner가 spec 생성
→ Phase 2: spec → features.json + rubric.json 변환
→ Phase 3-4: builder가 빌드 → evaluator가 QA (사이클 수 선택: 1/3/5/custom)
→ Phase 5: 최종 리포트

소요: 1사이클 ~2시간, 3사이클(기본) ~5시간
결과: 동작하는 웹앱 + 리포트
※ 온보딩 시에는 1사이클(빠른 프로토타입)을 권장
```

**왜 autonomous-build-harness부터**: TASKS.md 없이 spec만으로 동작하므로 중간 단계가 적다.

#### 옵션 B: /full-pipeline-orchestrator-pipeline (전체 경로)

```
/full-pipeline-orchestrator-pipeline
→ Phase 0: "기획부터 시작" 선택
→ Phase 1: /requirements-dialogue 심층 기획 (30-60분)
→ Phase 2: /task-decomposer (태스크 분해)
→ Phase 3: /dependency-auto-executor 실행
→ Phase 4: 품질 게이트 및 자가개선

소요: 2-4시간
결과: 기획 문서 9개 + 명세 + 코드 + 품질 검증
```

**주의사항** (08-ANTI-PATTERNS에서):
- requirements-dialogue 후 세션이 길어지면 **핸드오프 사용** (AP-19)
- TASKS.md 경로는 반드시 `docs/planning/06-tasks.md` (AP-03)
- specialist가 merge하면 안 됨 — 오케스트레이터만 merge (AP-04)

> **Week 4 끝나면**: "전체 파이프라인을 한 번 돌려봤다" 수준.

---

## 스킬 학습 로드맵

### Level 1: 필수 스킬 (처음 익힐 5개)

이 5개만 알면 전체 효과의 ~70%를 얻는다 (09-ROI-ANALYSIS 참조):

| 우선순위 | 스킬 | 이유 | ROI |
|---------|------|------|-----|
| ★★★★★ | **token-compressor** | 설정만 하면 모든 세션에서 자동 절감 | ∞ (한계비용 0) |
| ★★★★★ | **cost-model-router** | 비용 인식 + 모델 라우팅 | 80-140x |
| ★★★★★ | **pre-completion-verifier** | "완료" 전 증거 확인 습관 | 50x |
| ★★★★☆ | **requirements-dialogue** | 재작업 70% → 15%로 감소 | 7x |
| ★★★★☆ | **dependency-auto-executor** | 태스크 자동 실행 + 품질 체인 | 시간 2.4x |

```
학습 순서:
  token-compressor (10분 설정)
  → cost-model-router (개념 이해)
  → pre-completion-verifier (습관 형성)
  → requirements-dialogue (기획 1회 실행)
  → dependency-auto-executor (실행 1회)
```

---

### Level 2: 핵심 스킬 (다음 익힐 9개)

| 스킬 | 카테고리 | 언제 필요한가 |
|------|---------|-------------|
| **full-pipeline-orchestrator-pipeline** | 오케스트레이션 | 아이디어→앱 전체 자동화 |
| **autonomous-build-harness** | 오케스트레이션 | 빠른 앱 생성 |
| **task-decomposer** | 명세 | 태스크 분해 |
| **quality-gate** | 검증 | 메트릭 기반 품질 판정 |
| **code-reviewer** | 검증 | 의미론적 코드 리뷰 |
| **powerqa** | 검증 | 테스트 자동 수정 |
| **kongkong2** | 최적화 | 정확도 향상 (자동 적용) |
| **council-codex** | 리뷰 | 다관점 기획 검증 |
| **self-improvement-loop** | 자가개선 | 자율 코드 개선 |

---

### Level 3: 고급 스킬 (필요할 때 익히는 8개)

| 스킬 | 언제 필요한가 |
|------|-------------|
| **requirements-dialogue-anchored** | 범위 이탈 방지 필요 |
| **auto-error-fixer** | 자동 에러 수정 |
| **auto-security-audit** | 보안 감사 |
| **sync** | 명세-코드 불일치 감지 |
| **spike** | 불확실 영역 탐색 |
| **reflection** | 출력 즉시 개선 |
| **a2a** | 에이전트 간 통신 |
| **desktop-bridge** | 데스크톱 도구 연결 |

---

### Level 4: 철학/유틸리티 (관심 있으면 익히는 17개)

| 카테고리 | 스킬들 |
|---------|--------|
| 철학적 기획 | the-fool |
| 브레인스토밍 | idea-concretizer, co-brainstorming |
| 언어 전문가 | python-pro, typescript-pro, golang-pro, react-19, fastapi-latest |
| 인프라 전문가 | kubernetes-specialist, terraform-engineer, database-optimizer |
| 유틸리티 | rag, memory, session-report, goal-setting, chrome-browser, reasoning |

---

## 핵심 개념 5가지

51개 스킬을 관통하는 개념. 이것만 이해하면 개별 스킬은 변형일 뿐:

### 1. 모호함 감소 (Ambiguity Reduction)

```
아이디어 (모호함 0.9) → 기획 (0.3) → 명세 (0.15) → 코드 (0.05)
```

- 모호함이 높으면 재작업 확률이 높다
- requirements-dialogue의 10개 명확성 차원이 이 감소를 측정한다
- **원칙**: 코딩 전에 모호함을 0.15 이하로 낮춰라

### 2. 품질 체인 (Quality Chain)

```
자동 체인: pre-completion-verifier → quality-gate → code-reviewer → auto-security-audit(조건부)
수동 검증: sync (PR 생성 전/사용자 요청 시)
```

- 각 단계가 다른 유형의 결함을 잡는다
- 순서가 중요하다 (빌드 안 되는 코드를 리뷰하는 건 낭비)
- dependency-auto-executor가 Phase 완료 시 자동 체인을 실행한다

### 3. Frozen Metric (동결 메트릭)

```
prepare.* (평가 코드) — 절대 수정 불가
target.* (대상 코드) — 에이전트가 수정
```

- AI가 자기 점수를 조작하지 못하게 평가와 대상을 분리
- self-improvement-loop의 핵심 원리
- **원칙**: 시험과 답을 모두 고칠 수 있으면 루프는 무의미

### 4. 증거 기반 완료 (Evidence-Based Completion)

```
"완성했습니다" ← 이것은 증거가 아님
npm test → "12 passed, 0 failed" ← 이것이 증거
```

- pre-completion-verifier의 핵심
- **원칙**: 증거 없이 완료를 주장하지 마라

### 5. 세션 분리 (Session Handoff)

```
[Session 1] /requirements-dialogue → handoff.json → 종료
[Session 2] /task-decomposer → handoff.json 읽기 → TASKS.md 생성
```

- 긴 작업은 세션을 나눠야 컨텍스트 초과를 방지
- `.claude/handoffs/handoff-*.json`이 세션 간 컨텍스트 전달
- **권장**: requirements-dialogue 후 컨텍스트가 클 때는 세션을 나누는 것이 좋다 (필수는 아니지만 긴 작업에서 권장)

---

## 자주 묻는 질문 (FAQ)

### Q1: "51개를 다 알아야 하나요?"

**아니요.** 5개만 알면 70%의 효과를 얻습니다.
token-compressor, cost-model-router, pre-completion-verifier, requirements-dialogue, dependency-auto-executor.
나머지는 필요할 때 06-DECISION-GUIDE를 보고 찾으면 됩니다.

### Q2: "어떤 스킬을 써야 할지 모르겠어요"

06-SKILLS-DECISION-GUIDE의 마스터 디시전 트리를 따라가세요.
첫 번째 질문: "지금 무엇이 있는가?" (아이디어/기획문서/TASKS.md/기존코드/없음)

### Q3: "/full-pipeline-orchestrator-pipeline과 /autonomous-build-harness 중 뭘 써야 하나요?"

- **처음이라면**: `/autonomous-build-harness` (더 간단, auto-planner로 5분 기획)
- **품질이 중요하면**: `/full-pipeline-orchestrator-pipeline` (requirements-dialogue 심층 기획)
- **빨리 만들고 싶으면**: `/autonomous-build-harness` (1-2시간)
- **제대로 만들고 싶으면**: `/full-pipeline-orchestrator-pipeline` (2-4시간)

### Q4: "requirements-dialogue가 너무 오래 걸려요"

`/auto-planner`를 쓰세요. 5분 안에 비대화형으로 spec을 생성합니다.
품질은 requirements-dialogue보다 낮지만, 빠른 프로토타이핑에 적합합니다.

### Q5: "self-improvement-loop를 써보고 싶은데 복잡해 보여요"

서브커맨드부터 시작하세요:
- `/auto-error-fixer` — 에러를 자동으로 고쳐줌 (가장 간단)
- `/self-improvement-loop` — 셋업 위저드가 설정을 안내해줌

### Q6: "스킬이 깨졌어요 (에러가 나요)"

12-DEPENDENCY-RISK의 장애 대응 가이드를 보세요:
1. 어디서 멈췄는지 확인
2. 중간 결과(docs/planning/, specs/)가 있는지 확인
3. 우회 경로 선택 (autonomous-build-harness, auto-planner 등)

### Q7: "팀원에게 이 시스템을 소개하려면?"

이 순서로 공유하세요:
1. 이 문서 (13-ONBOARDING)
2. 01-CATALOG (뭐가 있는지)
3. 06-DECISION-GUIDE (뭘 쓸지)
4. Week 2 실습 (독립 스킬 체험)

### Q8: "새 스킬을 만들고 싶어요"

```
mkdir ~/.claude/skills/my-new-skill
cat > ~/.claude/skills/my-new-skill/SKILL.md << 'EOF'
---
name: my-new-skill
description: 한 줄 설명
user_instructions: "트리거 키워드"
---

# 스킬 이름

## 실행 규칙
...
EOF
```

SKILL.md만 작성하면 자동 인식됩니다. references/ 디렉토리를 추가하면 지원 문서도 포함 가능.

### Q9: "Cursor/Copilot과 같이 쓸 수 있나요?"

네. 11-ALTERNATIVES-COMPARISON에 조합 전략이 있습니다:
- **Claude Code(기획) + Cursor(병렬 실행)**: 깊이 + 속도
- **Claude Code(자동화) + Copilot(PR 리뷰)**: 자동화 + 팀 협업

### Q10: "이 시스템의 약점은 뭔가요?"

솔직하게:
- **초기 학습 곡선**: 51개 스킬 체계 이해에 시간 필요 (이 문서가 그래서 존재)
- **배포/운영 스킬 부재**: 코드까지는 훌륭, 프로덕션 이후는 공백 (10-ROADMAP)
- **CLI 기반**: IDE 통합 없음, 터미널에 익숙해야 함
- **Anthropic 모델 의존**: 기본적으로 Claude 모델만 사용

---

## 빠른 시작 치트시트

### 30초 안에 첫 스킬 실행

```bash
# Claude Code 시작
claude

# 가장 안전한 첫 실행
> /rag
> "이 프로젝트의 아키텍처 문서를 검색해줘"
```

### 5분 안에 토큰 최적화 적용

```bash
# token-compressor가 이미 설치되어 있으므로
# 일반 작업을 하면 자동으로 CLI 출력이 압축됨
claude

> git status 실행해줘
# → git status --short 로 자동 변환 (85% 절감)
```

### 1시간 안에 앱 생성

```bash
claude

> /autonomous-build-harness
# → "Auto-planner" 선택
# → "1 사이클 (빠른 프로토타입)" 선택
# → "할 일 관리 웹앱. 태그, 우선순위, 마감일 알림" 입력
# → ~2시간 후 동작하는 앱 + 리포트
```

---

## 졸업 기준

### "온보딩 완료"라고 할 수 있는 시점

```
□ Level 1 스킬 5개를 각각 1회 이상 사용해봤다
□ /autonomous-build-harness 또는 /full-pipeline-orchestrator-pipeline를 한 번 끝까지 돌려봤다
□ 06-DECISION-GUIDE를 보고 상황에 맞는 스킬을 선택할 수 있다
□ 08-ANTI-PATTERNS의 치명적(🔴) 5개를 기억하고 있다
□ "증거 없이 완료 주장 금지"를 체화했다
```

이 5가지를 충족하면, 나머지는 실전에서 필요할 때 배우면 된다.

---

## 다음 단계

온보딩 이후 깊이 있게 읽을 문서:

| 관심사 | 문서 |
|--------|------|
| "왜 이렇게 만들었지?" | 03-WHY, 04-SEPARATION-RATIONALE |
| "진짜 효과가 있나?" | 09-ROI-ANALYSIS |
| "앞으로 뭐가 추가되나?" | 10-EVOLUTION-ROADMAP |
| "다른 도구랑 비교하면?" | 11-ALTERNATIVES-COMPARISON |
| "깨지면 어떡하지?" | 12-DEPENDENCY-RISK |
