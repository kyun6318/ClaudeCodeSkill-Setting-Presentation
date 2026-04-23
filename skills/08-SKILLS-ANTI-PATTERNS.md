# Skills Anti-Patterns & Failure Cases — 안티패턴과 실패 사례

## 이 문서의 목적

> 스킬은 올바르게 사용하면 품질을 보장하지만, 잘못 사용하면 **시간 낭비, 토큰 폭발, 파이프라인 붕괴**를 일으킨다.
> 이 문서는 실제로 발생할 수 있는 실패 패턴을 분류하고, 각각의 원인과 방지법을 정리한다.

---

## 철칙 (Iron Laws) 요약

어떤 상황에서도 위반해서는 안 되는 절대 규칙:

| # | 철칙 | 스킬 | 위반 시 결과 |
|---|------|------|------------|
| 1 | 증거 없이 완료 주장 금지 | /pre-completion-verifier | 거짓 완료 → 후속 Phase 실패 |
| 2 | 근본 원인 조사 없이 수정 금지 | /systematic-debugger | 증상만 고침 → 재발 |
| 3 | Frozen Metric 분리 위반 금지 | /self-improvement-loop | 자기 평가 조작 → 루프 무의미화 |
| 4 | 반드시 `docs/planning/06-tasks.md`에 작성 | /task-decomposer | 오케스트레이터가 파일을 못 찾음 |
| 5 | TDD 3단계(RED→GREEN→REFACTOR) 강제 | /dependency-auto-executor | 품질 게이트 실패 |
| 6 | 오케스트레이터만 merge/push | /dependency-auto-executor | branch 충돌, 코드 손실 |
| 7 | Phase 1+ 태스크는 git worktree 필수 | /dependency-auto-executor | branch 오염, merge 혼란 |
| 8 | Expert 호출 max_turns ≤ 12 | /dependency-auto-executor | 컨텍스트 폭발 |
| 9 | Drift Anchor를 Phase 0.3에서 생성 | /requirements-dialogue-anchored | 범위 이탈 방치 |
| 10 | grep 필터링 금지 (tail/head 사용) | /token-compressor | 한국어 에러 메시지 유실 |

---

## 카테고리 1: 파이프라인 순서 위반

### AP-01: Phase를 건너뛰고 실행

```
❌ 잘못된 사용
  아이디어만 있는 상태에서 바로 /dependency-auto-executor 실행

✅ 올바른 사용
  /requirements-dialogue → /task-decomposer → /dependency-auto-executor
  (또는 /full-pipeline-orchestrator-pipeline으로 전체 자동화)
```

**증상**: "TASKS.md가 없습니다" 에러, 또는 빈 상태에서 의미 없는 태스크 생성
**원인**: dependency-auto-executor는 `docs/planning/06-tasks.md`를 **반드시 입력으로 요구**
**방지**: /full-pipeline-orchestrator-pipeline을 사용하면 순서가 자동 강제됨

---

### AP-02: 기획 없이 autonomous-build-harness 실행 + planning skip

```
❌ 잘못된 사용
  /autonomous-build-harness → Phase 0에서 "Skip planning" 선택 → docs/planning/ 없음

✅ 올바른 사용
  /autonomous-build-harness → Phase 0에서 "requirements-dialogue" 또는 "Auto-planner" 선택
  (docs/planning/ 이미 있는 경우에만 Skip 가능)
```

**증상**: builder가 spec 없이 자유형 코딩 → 요구사항과 무관한 결과물
**원인**: autonomous-build-harness의 builder는 spec.md를 기반으로 빌드. spec이 없으면 AI가 추측
**방지**: Phase 0의 AskUserQuestion에서 반드시 기획 방법을 선택

---

### AP-03: 오케스트레이터에서 TASKS.md 경로 오류

```
❌ 잘못된 경로
  /TASKS.md  또는  docs/planning/TASKS.md  또는  tasks.md

✅ 유일한 정답
  docs/planning/06-tasks.md
```

**증상**: dependency-auto-executor가 "태스크를 찾을 수 없습니다" → 빈 실행
**원인**: Canonical Contract에서 경로가 고정. 다른 경로는 인식하지 않음
**방지**: task-decomposer가 자동으로 올바른 경로에 생성. 수동 생성 시 경로 확인 필수

---

### AP-04: 전문가(Specialist)가 직접 merge/push

```
❌ 잘못된 흐름
  dependency-auto-executor → specialist 에이전트 호출 → specialist가 git merge

✅ 올바른 흐름
  dependency-auto-executor → specialist 에이전트 호출 → specialist가 TASK_DONE 출력
  → orchestrator가 merge/push
```

**증상**: branch 충돌, 코드 손실, Phase 간 코드 꼬임
**원인**: 여러 specialist가 동시에 merge하면 race condition 발생
**방지**: specialist의 프롬프트에 "MUST NOT merge" 명시. merge는 오케스트레이터 전용

---

## 카테고리 2: 기획 단계 오용

### AP-05: requirements-dialogue와 co-brainstorming을 동시에 사용

```
❌ 잘못된 사용
  "비판적으로 질문하면서 동시에 자유롭게 브레인스토밍해줘"

✅ 올바른 사용
  /co-brainstorming (비판 금지 발산) → 결과 선택 → /requirements-dialogue (비판적 수렴)
```

**증상**: AI가 아이디어를 내놓으면서 동시에 비판 → 아이디어가 위축됨
**원인**: co-brainstorming의 제1원칙 "판단 자제"와 requirements-dialogue의 본질 "비판적 질문"은 **정반대**
**방지**: 반드시 순차 실행. 발산(co-brainstorming) → 수렴(requirements-dialogue)

---

### AP-06: requirements-dialogue 모호함 임계값 무시

```
❌ 잘못된 사용
  모호함 점수가 0.4인데 "충분히 이해했으니 코딩 시작하자"

✅ 올바른 사용
  모호함 ≤ 0.15까지 질문 계속 → 이후 다음 단계(task-decomposer 등) 진입
```

**증상**: 구현 중 "이건 스펙에 없는데요?" 반복 → 재작업
**원인**: 모호함 0.4는 기능의 40%가 불명확하다는 의미
**방지**: requirements-dialogue의 Phase 전환 조건을 신뢰. 조기 종료 유혹에 빠지지 않기

---

### AP-07: Drift Anchor 없이 대규모 기획 진행

```
❌ 잘못된 사용
  /requirements-dialogue로 40개 질문 진행 → 중간에 범위가 "챗봇"에서 "AI 플랫폼"으로 확장

✅ 올바른 사용
  /requirements-dialogue-anchored → Phase 0.3에서 Drift Anchor 확정
  → 이후 질문이 앵커를 벗어나면 경고
```

**증상**: 기획 문서가 생성되었지만 초기 의도와 완전히 다른 제품이 됨
**원인**: 대화 중 자연스러운 범위 확장을 AI가 그대로 수용
**방지**: Terraform/ML/LLM 등 기술 프로젝트에서는 반드시 /requirements-dialogue-anchored 사용

---

### AP-08: ICV(Interface Contract Validation) 실패 무시

```
❌ 잘못된 사용
  task-decomposer: "Screen A needs 'user.avatar' but resources.yaml has no 'avatar' field"
  사용자: "나중에 추가하면 되지, 계속 진행해"

✅ 올바른 사용
  ICV 실패 → 즉시 중단 → 도메인 명세 수정 → 재실행
```

**증상**: 빌드 중 "이 데이터가 없습니다" → 전체 Phase 실패
**원인**: 명세와 도메인 리소스 간 불일치를 무시하면 빌드 시 100% 실패
**방지**: ICV 실패는 **STOP 시그널**. "나중에"는 없다

---

## 카테고리 3: 검증 단계 오용

### AP-09: 증거 없이 "완료" 선언

```
❌ 잘못된 사용
  AI: "구현을 완료했습니다. 테스트도 통과할 것입니다."
  (실제로 npm test를 실행하지 않음)

✅ 올바른 사용
  AI: npm test 실행 → 출력 전체 확인 → "12 tests passed, 0 failed" 증거 제시
  → "모든 테스트가 통과했습니다."
```

**증상**: 다음 Phase에서 "이전 Phase 코드가 깨져있습니다" 발견
**원인**: "should work"는 증거가 아님. 확인과 추측의 차이
**방지**: /pre-completion-verifier가 **명령어 실행 → 출력 읽기 → 판정**을 강제

---

### AP-10: powerqa와 systematic-debugger 순서 뒤바꿈

```
❌ 잘못된 사용
  에러 발생 → /systematic-debugger (30분 분석) → 사실은 단순 typo였음

  에러 반복 → /powerqa (5사이클 자동 수정) → 3회 동일 실패 → 원인 모름

✅ 올바른 사용
  에러 발생 → /powerqa (빠른 자동 수정 시도)
  → 3회 동일 실패 → /systematic-debugger (근본 원인 분석)
```

**증상**: 단순 에러에 과도한 분석, 또는 복잡한 에러에 반복 수정만
**원인**: powerqa는 "일단 고쳐봐", debugging은 "이해한 후 고쳐". 순서가 중요
**방지**: 품질 체인에서 검증 실패 시 powerqa가 자동 호출되고, 동일 유형 실패가 3회 반복되면 systematic-debugger로 에스컬레이션. 이 흐름은 조건부로 트리거되며 항상 실행되는 것은 아님

---

### AP-11: 품질 게이트 체인 순서 변경

```
❌ 잘못된 순서
  code-reviewer → verification (리뷰 후 빌드 확인)

✅ 올바른 순서 (자동 체인)
  verification → quality-gate → code-reviewer
  ※ sync는 자동 체인이 아닌 수동 검증
```

**증상**: 빌드가 깨진 코드를 리뷰 → 리뷰 시간 낭비
**원인**: 빌드 통과(verification) 전에 리뷰하는 것은 의미 없음
**방지**: dependency-auto-executor가 품질 체인 순서를 자동 강제

---

### AP-12: quality-gate 메트릭 임계값을 임의로 완화

```
❌ 잘못된 사용
  "커버리지 30%인데 괜찮지 않을까? 40%로 낮추자"

✅ 올바른 사용
  커버리지 < 70% → Phase 블록 → 테스트 추가 → 재측정
```

**증상**: 품질 부채 누적 → 후반 Phase에서 연쇄 실패
**원인**: 임계값은 "제안"이 아니라 "게이트". 낮추면 후속 검증이 무의미해짐

---

## 카테고리 4: 자가개선 루프 오용

### AP-13: Frozen Metric을 에이전트가 수정

```
❌ 잘못된 사용
  self-improvement-loop 루프 중 에이전트가 eval/metrics.py를 수정하여 점수를 올림

✅ 올바른 사용
  prepare.* (평가 코드) — 절대 수정 불가
  target.* (대상 코드) — 에이전트만 수정
  program.md (전략) — Outer Loop에서만 수정
```

**증상**: 메트릭 점수가 올라가지만 실제 코드 품질은 변화 없음
**원인**: 시험과 답을 모두 고칠 수 있으면 루프는 무의미
**방지**: 3파일 분리가 구조적으로 강제. eval/ 디렉토리 수정 시 guard가 revert

---

### AP-14: self-improvement-loop를 부적합한 도메인에 적용

```
❌ 부적합한 경우
  - 메트릭을 자동 측정할 수 없는 도메인 (예: "디자인이 예쁜가?")
  - 실험을 자동 실행할 수 없는 도메인 (예: 수동 하드웨어 테스트)
  - keep/revert 판단이 모호한 도메인 (예: "더 나은 UX" 주관적)

✅ 적합한 경우
  - 테스트 통과율 (자동 측정 ✅, 자동 실행 ✅, 판단 명확 ✅)
  - 빌드 성공 여부 (자동 측정 ✅, 자동 실행 ✅, 판단 명확 ✅)
  - 보안 취약점 수 (자동 측정 ✅, 자동 실행 ✅, 판단 명확 ✅)
```

**증상**: 루프가 돌지만 개선 없음, 토큰만 소비
**원인**: self-improvement-loop의 Fitness Check 3조건 미충족
**방지**: 시작 전 자문 — "메트릭이 자동 측정 가능한가? 실험이 자동 실행 가능한가? 판단이 명확한가?"

---

## 카테고리 5: 최적화 역효과

### AP-15: token-compressor에서 grep으로 출력 필터링

```
❌ 잘못된 사용
  npm test 2>&1 | grep -i "error"
  → 한국어 에러 "모듈을 찾을 수 없습니다" 유실

✅ 올바른 사용
  npm test 2>&1 | tail -20
  → 마지막 20줄에 모든 에러 포함 (언어 무관)
```

**증상**: 테스트 통과로 보이지만 실제론 한국어/비영어 에러 존재
**원인**: grep은 영어 키워드만 매칭. 비영어 에러 메시지는 필터링됨
**방지**: tail/head로 줄 수만 제한. 내용 필터링은 금지

---

### AP-16: cost-model-router에서 복잡한 태스크를 Haiku에 할당

```
❌ 잘못된 사용
  "이 리팩토링은 간단해 보이니까 Haiku로"
  → 실제로는 20개 파일 수정, 아키텍처 변경 포함

✅ 올바른 사용
  Complexity Check:
    lines_changed > 20?  → EXPENSIVE
    files_affected > 3?   → EXPENSIVE
    cyclomatic_delta > 2? → EXPENSIVE
    하나라도 YES → Opus/Sonnet 사용
```

**증상**: Haiku가 불완전한 리팩토링 → 수동 수정 → 결국 더 비쌈
**원인**: "간단해 보이는" 것과 실제 복잡도는 다름. 3가지 기준 중 하나라도 초과하면 EXPENSIVE
**방지**: cost-model-router의 복잡도 임계값 자동 분류를 신뢰

---

### AP-17: token-compressor와 kongkong2의 목적 혼동

```
❌ 잘못된 사용
  "토큰을 아끼라고 했잖아! kongkong2는 토큰을 늘리잖아? 꺼야지!"

✅ 올바른 이해
  token-compressor: 출력 토큰을 줄인다 (낭비 제거)
  kongkong2: 입력 토큰을 늘린다 (정확도 향상)
  → 둘은 다른 방향이지만 동시에 사용 가능
  → 입력을 정확히 읽어서(kongkong2) 출력을 간결하게(token-compressor)
```

**증상**: kongkong2를 끄면 요구사항 누락 증가 → 재작업 → 결국 토큰 더 소비
**원인**: "토큰 절감"의 대상이 다름. 입력 정확도와 출력 압축은 별개
**방지**: 둘은 **보완 관계**. 함께 사용이 기본

---

## 카테고리 6: 스킬 조합 충돌

### AP-18: spike + self-improvement-loop 동시 실행

```
❌ 잘못된 사용
  /spike (품질 게이트 완화) + /self-improvement-loop (엄격한 Frozen Metric)

✅ 올바른 사용
  /spike (탐색) → 결과 확인 → /self-improvement-loop (개선)
  탐색과 개선은 순차적
```

**증상**: 품질 게이트가 완화된 상태에서 Frozen Metric이 측정 → 잘못된 기준선
**원인**: spike의 "게이트 완화"와 self-improvement-loop의 "게이트 엄격"은 정반대
**방지**: 탐색(spike)이 끝난 후에 개선(self-improvement-loop) 시작

---

### AP-19: requirements-dialogue 후 세션 분리 없이 dependency-auto-executor 진입

```
❌ 잘못된 사용
  /requirements-dialogue (40-50개 질문, ~30K 토큰 소비)
  → 같은 세션에서 /task-decomposer → /dependency-auto-executor
  → 컨텍스트 160K 초과 → 환각 시작

✅ 올바른 사용
  [Session 1] /requirements-dialogue → docs/planning/* + handoff.json → 세션 종료
  [Session 2] /task-decomposer → docs/planning/06-tasks.md → 세션 종료
  [Session 3] /dependency-auto-executor → 실행
```

**증상**: 후반 Phase에서 AI가 앞서 결정한 내용을 "잊어버림" → 일관성 붕괴
**원인**: 컨텍스트 윈도우에 모든 대화가 쌓이면 초기 정보가 밀려남
**방지**: 핸드오프 프로토콜 사용. `.claude/handoffs/handoff-*.json`으로 세션 간 컨텍스트 전달

---

## 카테고리 7: 구조/경로 위반

### AP-20: git worktree 대신 같은 branch에서 작업

```
❌ 잘못된 사용
  Phase 1 태스크를 main branch에서 직접 실행

✅ 올바른 사용
  git worktree add ./phase1 -b phase/1 main
  → phase1 디렉토리에서 작업 → 완료 후 merge
```

**증상**: Phase 간 코드가 섞임 → 품질 게이트가 다른 Phase 코드까지 평가
**원인**: Phase별 격리가 없으면 미완성 코드가 다른 Phase에 영향
**방지**: dependency-auto-executor가 Phase 1+ 에서 worktree를 자동 생성

---

### AP-21: 서브에이전트 중첩 호출

```
❌ 잘못된 사용
  orchestrator → specialist A → specialist B (2단계 중첩)

✅ 올바른 사용
  orchestrator → specialist A (완료)
  orchestrator → specialist B (완료)
  (항상 1단계만)
```

**증상**: 프로세스 포크 실패, 에이전트 응답 없음
**원인**: Claude Code는 서브에이전트가 다른 서브에이전트를 호출할 수 없음 (1레벨 제한)
**방지**: 모든 specialist 호출은 메인 오케스트레이터에서 직접

---

## 카테고리 8: autonomous-build-harness 특유의 함정

### AP-22: autonomous-build-harness Phase 0에서 임계값 미설정

```
❌ 잘못된 사용
  /autonomous-build-harness → Phase 0에서 max_cycles와 pass_threshold를 설정하지 않음
  → Build-Eval 루프가 종료 조건 없이 무한 반복

✅ 올바른 사용
  /autonomous-build-harness → Phase 0:
    max_cycles: 3
    pass_threshold: 7 (1-10 스케일)
  → 3사이클 내에 점수 7 이상이면 통과, 아니면 정지
```

**증상**: evaluator가 계속 "불합격" → builder가 계속 수정 → 무한 루프
**원인**: 종료 조건(max_cycles, pass_threshold)이 autonomous-build-harness-state.json에 기록되어야 함
**방지**: Phase 0의 AskUserQuestion이 반드시 이 값을 설정

---

### AP-23: evaluator 회의적 기본값 무시

```
❌ 잘못된 기대
  "builder가 만들었으니 기본적으로 동작하겠지"

✅ evaluator의 실제 태도
  "기능이 작동하지 않는다가 기본 가정"
  → 실제 클릭으로 증명될 때까지 불합격
```

**증상**: 사용자가 "왜 합격이 안 되지?"라고 의아해함
**원인**: evaluator는 의도적으로 회의적. 브라우저에서 실제 동작이 확인되어야만 합격
**방지**: 이것은 버그가 아니라 설계. "회의적 기본값"이 품질을 보장하는 메커니즘

---

## 안티패턴 심각도 매트릭스

| 심각도 | 안티패턴 | 결과 |
|--------|---------|------|
| 🔴 **치명적** | AP-03 (TASKS.md 경로 오류) | 파이프라인 완전 정지 |
| 🔴 **치명적** | AP-04 (specialist가 merge) | 코드 손실 가능 |
| 🔴 **치명적** | AP-08 (ICV 실패 무시) | 빌드 불가능한 코드 생성 |
| 🔴 **치명적** | AP-13 (Frozen Metric 수정) | 자가개선 루프 무의미화 |
| 🔴 **치명적** | AP-21 (서브에이전트 중첩) | 프로세스 포크 실패 |
| 🟠 **높음** | AP-01 (Phase 건너뛰기) | 불완전한 입력으로 빌드 |
| 🟠 **높음** | AP-06 (모호함 임계값 무시) | 3배 재작업 |
| 🟠 **높음** | AP-09 (증거 없이 완료) | 거짓 진행률 |
| 🟠 **높음** | AP-19 (세션 미분리) | 컨텍스트 초과, 환각 |
| 🟠 **높음** | AP-22 (임계값 미설정) | Build-Eval 무한 루프 |
| 🟡 **중간** | AP-02 (기획 없이 autonomous-build-harness) | 요구사항 불일치 |
| 🟡 **중간** | AP-05 (requirements-dialogue+co-brainstorming 동시) | 아이디어 위축 |
| 🟡 **중간** | AP-07 (Drift Anchor 없이) | 범위 이탈 |
| 🟡 **중간** | AP-10 (검증 순서 뒤바꿈) | 시간 낭비 |
| 🟡 **중간** | AP-11 (품질 체인 순서 변경) | 깨진 코드 리뷰 |
| 🟡 **중간** | AP-15 (grep 필터링) | 에러 유실 |
| 🟡 **중간** | AP-16 (복잡 태스크→Haiku) | 비용 역효과 |
| 🟢 **낮음** | AP-12 (메트릭 임계값 완화) | 품질 부채 누적 |
| 🟢 **낮음** | AP-14 (부적합 도메인 self-improvement-loop) | 토큰 낭비 |
| 🟢 **낮음** | AP-17 (optimizer+kongkong2 혼동) | 불필요한 비활성화 |
| 🟢 **낮음** | AP-18 (spike+self-improvement-loop 동시) | 잘못된 기준선 |
| 🟢 **낮음** | AP-20 (worktree 미사용) | Phase 간 코드 혼합 |
| 🟢 **낮음** | AP-23 (회의적 기본값 오해) | 기대치 불일치 |

---

## 빠른 자가 진단 체크리스트

스킬 사용 전 확인:

```
□ TASKS.md 경로가 docs/planning/06-tasks.md인가?
□ Phase 순서를 건너뛰지 않았는가?
□ 세션이 너무 길어지지 않았는가? (핸드오프 필요?)
□ 모호함 점수가 0.15 이하인가? (requirements-dialogue)
□ ICV가 통과했는가? (task-decomposer)
□ Frozen Metric을 에이전트가 건드리지 않았는가? (self-improvement-loop)
□ 완료 선언 전에 증거를 확인했는가?
□ specialist가 merge하지 않았는가?
□ Phase 1+ 태스크에 worktree를 사용했는가?
```
