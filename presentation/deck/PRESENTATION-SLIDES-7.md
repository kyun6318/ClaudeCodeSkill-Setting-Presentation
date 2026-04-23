# LLM Tools, Skills, and AI-Native Workflows

# 7장. 스킬을 어떻게 설정하고 사용하는가

### 7-1. 이 장의 전제

**슬라이드**
```
이 장은 Claude Code 기준의 구현 예시입니다.

핵심은 도구가 아니라:
  분리 원칙 — 무엇을 어디에 두는가
  운영 규율 — 어떤 경우에 어떤 스킬을 쓰는가

같은 원칙은 다른 도구에도 적용할 수 있습니다.

오늘 공유 범위는 슬라이드 안의 구조·포맷·축약 예시까지입니다.
```

*발표자 노트*
"지금부터 실제 설정 구조를 보겠습니다. Claude Code 기준이지만, 분리 원칙과 운영 규율은 어떤 AI 코딩 도구에도 적용됩니다. 외부로 가져가실 수 있는 파일은 오늘 슬라이드에 담긴 축약 예시까지입니다."

### 7-2. 설정 구조

**슬라이드**
```
~/.claude/
  ├── CLAUDE.md          ← 전역 운영 규칙 (모든 세션에 적용)
  ├── settings.json      ← MCP 연결, 도구 설정
  └── skills/
      ├── requirements-dialogue/
      │   └── SKILL.md   ← 스킬 정의 (트리거, 실행 규칙, 금지 사항)
      ├── dependency-auto-executor/
      │   └── SKILL.md
      └── ...

프로젝트별:
  ./CLAUDE.md            ← 이 프로젝트에만 적용되는 규칙
```

*발표자 노트*
"구조가 간단합니다. 전역 설정은 홈 디렉토리의 .claude 폴더에, 프로젝트별 설정은 프로젝트 루트의 CLAUDE.md에 넣습니다. 스킬은 각각 별도 폴더에 SKILL.md 파일 하나로 정의됩니다. 이 분리가 중요한 이유는 다음 슬라이드에서 설명하겠습니다."

### 7-2b. SKILL.md 축약 예시

**슬라이드**
````
# ~/.claude/skills/requirements-dialogue/SKILL.md  (축약)

```yaml
---
name: requirements-dialogue
description: 1:1 대화로 요구사항을 수렴해 9개 기획 문서를 만든다
---

# 활성화 트리거
- requirements-dialogue 명령 입력 시
- docs/planning/ 폴더가 비어있을 때

# 핵심 워크플로우 (축약)
## Phase 0.5  존재론적 탐색     "X란 무엇인가?"
## Phase 0.7  경쟁 분석         SWOT, 차별화
## Phase 1    핵심 기능 도출
## Phase 전환 조건: Ambiguity Score ≤ 0.4

# 금지 사항
- Ambiguity > 0.4 상태에서 코드 생성
- 사용자 결정 없이 스택 자동 선택
```

※ 실제 본문은 더 길지만 구조는 이 패턴을 따름.
````

*발표자 노트*
"SKILL.md는 생각보다 단순합니다. 프론트매터 두 줄(name·description)로 스킬이 식별되고, 그 아래에 트리거·워크플로우·금지 사항을 씁니다. '어떤 경우에 켜지고', '어떤 단계를 강제하며', '무엇을 하지 말아야 하는지'를 한 파일에 적는 겁니다. 이 포맷이 있어야 '다음에도 똑같이 동작한다'가 성립합니다."

### 7-3. 왜 분리하는가

**슬라이드**
```
합치면 생기는 문제:
  → CLAUDE.md 하나에 모든 규칙을 넣으면
    컨텍스트 비용이 올라간다 (항상 로드)
    수정이 어렵다
    재사용이 안 된다

분리하면:
  → 필요한 스킬만 로드
  → 스킬별로 독립 수정 가능
  → 다른 프로젝트에서 재사용
  → 팀원이 선택적으로 적용 가능
```

*발표자 노트*
"'하나의 큰 파일에 다 넣으면 안 되나요?'라고 할 수 있습니다. 할 수 있습니다. 하지만 그러면 모든 세션에 모든 규칙이 로드됩니다. 쓰지 않는 규칙까지 토큰을 소비합니다. 분리하면 필요할 때만 로드할 수 있습니다."
스킬 분리 근거 Skills Separation Rationale


### 7-4. MCP 구성

**슬라이드**
```json
// settings.json — 실제 사용 중인 MCP (축약, 명령·토큰은 마스킹)
{
  "mcpServers": {
    "codex":    { /* Claude 결과물을 제3자 관점으로 정적 검증 */ },
    "ollama":   { /* 요약·RAG·간단 코드 생성을 로컬 모델로 오프로드 */ },
    "context7": { /* 최신 라이브러리 문서를 실시간 참조 */ }
  }
}
```

```
선택 기준 — "이게 없으면 에이전트가 무엇을 못 하는가?"
  codex     → 독립적인 정적 검증 경로가 없음
  ollama    → 로컬 오프로드 경로가 없어 Claude 토큰을 다 써야 함
  context7  → 최신 라이브러리 정보에 접근 못함
```

*발표자 노트*
"MCP는 settings.json의 mcpServers 섹션에 추가합니다. 제가 실제로 붙인 건 세 개입니다. Codex는 Claude가 만든 결과를 제3자 관점에서 정적 검증시키려고, Ollama는 요약·RAG 같이 Claude 토큰을 굳이 써야 할 이유가 약한 작업을 로컬 모델로 오프로드하려고, Context7은 최신 라이브러리 문서를 실시간으로 참조하려고 붙였습니다. 선택 기준은 하나, '이게 없으면 에이전트가 어떤 경로를 못 가지는가'입니다. 있으면 좋은 게 아니라 없으면 구조가 안 돌아가는 것만 붙입니다."

### 7-4b. 프로젝트 CLAUDE.md 예시

**슬라이드**
```markdown
# ./CLAUDE.md  (프로젝트 루트, 축약 예시)

## 스택 (확정됨)
- Next.js 14 App Router + TypeScript
- Supabase (auth + DB), Tailwind + shadcn/ui

## 도메인 규칙
- 노트는 사용자 단위. Row Level Security 필수
- 태그는 정규화 없이 text[] 필드로 저장

## 운영 규칙
- 완료 전 pre-completion-verifier 의무
- Phase 1+ 구현은 Git Worktree에서만
- 에이전트 수정 금지: specs/domain/*.yaml

## 자주 쓰는 흐름
- 새 화면: screen-spec-generator → task-decomposer → dependency-auto-executor
- 버그: systematic-debugger 먼저, 수정은 그다음
```

*발표자 노트*
"프로젝트 CLAUDE.md는 이 프로젝트에서만 적용되는 규칙입니다. 전역 규칙은 홈 디렉토리에, 프로젝트 규칙은 프로젝트 루트에. 여기엔 스택 확정 내용, 도메인 규칙, 운영 규칙, 자주 쓰는 흐름을 적습니다. 팀원이 합류해도 이 파일 하나로 '이 프로젝트에서 AI에게 어떻게 지시해야 하는지'가 공유됩니다.

### 7-5. 스킬 사용 흐름

**슬라이드**
```
상황별 스킬 선택:

새 프로젝트 시작      → requirements-dialogue → dependency-auto-executor
빠른 프로토타입       → auto-planner → autonomous-build-autonomous-build-harness
기존 코드 개선        → auto-error-fixer
버그 원인 분석        → systematic-debugger
보안 점검             → auto-security-audit
완료 전 검증          → pre-completion-verifier

선택 기준: "지금 어느 단계인가?"
```

*발표자 노트*
"어떤 스킬을 쓸지 고를 때는 지금 어느 단계에 있는지를 먼저 파악합니다. 기획 단계면 requirements-dialogue, 구현 단계면 dependency-auto-executor, 개선 단계면 self-improvement-loop입니다. 헷갈리면 full-pipeline-orchestrator를 쓰면 전체 흐름을 자동으로 조율해줍니다."

스킬 선택 의사결정 가이드 Skills Decision Guide


### 7-6. 절대 피해야 할 안티패턴

**슬라이드**
```
🔴 치명적 안티패턴 TOP 3

  ① 증거 없이 완료 선언
     "완성했습니다" — 빌드 확인 전에 믿지 말 것
     → pre-completion-verifier 의무화

  ② 태스크 파일 경로 오류
     파일 경로는 정확히: docs/planning/06-tasks.md
     → 이 경로 이외의 위치에 두면 오케스트레이터가 인식 못함

  ③ Frozen Metric 수정
     평가 코드를 손대면 점수가 의미를 잃음
     → self-improvement-loop는 target 파일만 수정

나머지 안티패턴은 구두로 짧게 언급만.
```

*발표자 노트*
"안티패턴은 많지만 이 세 가지가 가장 치명적입니다. 첫 번째, 완료를 믿지 않는 것. AI가 '완성'이라고 해도 반드시 빌드와 테스트를 확인하세요. 두 번째, 경로 규칙은 정확하게. 파이프라인은 특정 경로를 기대하고, 다른 데 두면 그냥 동작 안 합니다. 세 번째, 자가개선 루프에서 평가 코드는 건드리지 않는 것. 평가 기준을 낮추면 점수가 올라가도 실제론 나빠지는 겁니다. 나머지는 Q&A에서 질문 나오면 짧게 덧붙이겠습니다."
