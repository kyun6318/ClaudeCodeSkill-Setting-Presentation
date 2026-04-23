# LLM Tools, Skills, and AI-Native Workflows

# 6장. 스킬 묶음은 어떤 문제를 해결하는가

### 6-1. 5가지 결함 → 해결 매핑

**슬라이드**
```
결함                    해결 스킬 (예시)
────────────────────────────────────────────────────────
① 기획 부재           requirements-dialogue, screen-spec-generator, task-decomposer
② 숨겨진 가정         hidden-assumption-surfacer, technical-premise-deconstructor
③ 검증 없는 완료      pre-completion-verifier, quality-gate
④ 컨텍스트 폭발       token-compressor, ultra-thin-executor
⑤ 일회성 생성         self-improvement-loop (Frozen Metric + 자율 개선)

각 스킬은 특정 실패 방식에 대한 체계적 대응이다.
```

*발표자 노트*
"2장에서 말씀드린 5가지 결함, 기억하시죠? 지금 제가 쓰고 있는 스킬은 약 60개 수준인데, 전부 그 결함들에 하나씩 대응하기 위해 만들어졌습니다. 무작위로 늘어난 도구 모음이 아니라, 구체적인 실패 경험에서 태어난 체계입니다."


### 6-2. 스킬 전체 범주 맵

**슬라이드**
```
[전체 스킬 범주 맵 이미지]

3가지 범주:
  ① 프롬프트 고정  — "매번 같은 품질 보장" (다수)
  ② 프로세스 강제  — "건너뛸 수 없게" (중간 규모)
  ③ 진짜 새 능력   — "구조적으로만 가능한 것" (소수)

오늘은 전체 목록 대신 범주와 대표 스킬 몇 개만 공개.
```

*발표자 노트*
"스킬의 세 가지 범주만 이해하시면 됩니다. 프롬프트 고정은 매번 500자짜리 지시를 'requirements-dialogue' 한 마디로 줄이는 것, 프로세스 강제는 AI가 자발적으로 건너뛰는 단계를 반드시 거치게 만드는 것, 진짜 새 능력은 프롬프트만으로는 구조적으로 불가능한 것들입니다."

### 6-3. 파이프라인 관점

**슬라이드**
```
아이디어 → 기획 → 명세 → 실행 → 검증 → 개선

  idea-concretizer, co-brainstorming        ← 아이디어 발산
  requirements-dialogue, auto-planner       ← 기획
  screen-spec-generator                      ← 화면 명세
  task-decomposer                            ← 태스크 분해
  dependency-auto-executor                   ← 실행
  quality-gate, code-reviewerer               ← 검증
  self-improvement-loop                      ← 자율 개선

각 단계를 스킬이 담당한다.
단계를 건너뛰면 그 단계의 결함이 나중에 나타난다.
```

*발표자 노트*
"파이프라인으로 보면 왜 이렇게 많은 스킬이 필요한지 이해하기 쉽습니다. 각 단계에는 고유한 실패 방식이 있고, 각 스킬은 그 단계의 실패를 방지합니다."

### 6-3b. 카드: task-decomposer — "화면이 주도하되, 도메인이 방어한다"

**슬라이드**
```
task-decomposer                              [태스크 · 3단계]
핵심: Domain-Guarded 분해 — Resource / Screen 태스크를 분리해 ICV 검증

구조                          축약 코드
─────────────────             ─────────────────────────────────
파이프라인: 3단계              ---
입력: specs/screens/*.yaml     name: task-decomposer
      specs/domain/*.yaml      ---
출력 경로 고정:                # Task ID 표준
  docs/planning/tasks.md    #   P0-T0.1   (Phase 0)
실제 스킬명: task-decomposer   #   P2-R1-T1  Resource 태스크
                               #   P2-S1-T1  Screen 태스크
                               #   P2-S1-V   검증 태스크
                               #
                               # 출력 경로 고정: docs/planning/tasks.md
                               # (루트에 두면 오케스트레이터가 못 읽음)

Before  화면·백엔드 태스크 섞여 의존 관계 불명확
After   P2-R1-* → P2-S1-* 순서가 자동 결정, 병렬 가능 묶음 식별
```

*발표자 노트*
"파이프라인의 '태스크 분해' 단계에 들어가는 스킬입니다. Resource와 Screen을 분리해서 적는 게 핵심입니다. 이렇게 하면 백엔드 태스크가 화면에 종속되지 않으면서도, 화면이 필요한 데이터는 계약으로 보장됩니다. 그리고 경로 규칙은 딱 하나 — 루트가 아니라 docs/planning/tasks.md. 이건 7장 안티패턴에서 다시 나옵니다."

### 6-4. 스킬의 수명

**슬라이드**
```
플랫폼이 흡수할 수 있는 스킬
  → 언어별 베스트 프랙티스 (python-pro 등)
  → 기본 검증 패턴 (verification 일부)
  → 토큰 최적화 (플랫폼 효율 개선 시)

사용자 고유성이 강한 스킬 (남는 것)
  → 사용자의 파이프라인 강제 (dependency-auto-executor, full-pipeline-orchestrator)
  → 사용자의 철학적 프레임 (requirements-dialogue, technical-premise-deconstructor)
  → 구조적 자가개선 (self-improvement-loop Frozen Metric)

→ "불필요해진다"가 아니라
   "플랫폼이 일부 흡수하지만, 고유한 것은 남는다"
```

*발표자 노트*
"모델이 좋아지면 이 스킬들이 필요 없어지지 않나?라는 질문이 있을 수 있습니다. 일부는 그렇습니다. 모델이 발전하면 언어별 패턴은 별도로 지시하지 않아도 잘 적용할 겁니다. 하지만 '이 순서로 이 방식으로 반드시 하라'는 사용자의 워크플로우 자체는 어떤 범용 제품도 내장할 수 없습니다. 그 부분은 남습니다."

> 참고 문서: [네이티브 vs 커스텀 스킬](../../skills/05-SKILLS-NATIVE-VS-CUSTOM.md) · [진화 로드맵](../../skills/10-SKILLS-EVOLUTION-ROADMAP.md)
