---
name: codex-review
description: "Codex MCP 플러거블 정적 리뷰. 구현 계획에 대해 Static Review Loop(계획→리뷰→수정→재리뷰)를 수행. Codex MCP 있으면 활용, 없으면 Agent fallback."
trigger: "/codex-review, 계획 리뷰, static review, 정적 리뷰"
---

# codex-review: 구현 계획 정적 리뷰 위원회

> "계획이 코드가 되기 전에, 계획 자체를 심사한다."
> 멀티 에이전트 리뷰 + **Codex MCP Static Review Loop**.
> 구현 계획(TASKS.md)을 반복적으로 리뷰하여 실행 전 결함을 제거한다.

---

## Pipeline Context

| 항목 | 내용 |
|------|------|
| **파이프라인 위치** | forge-pipeline Phase 2 (구현 계획 리뷰) |
| **기반 스킬** | 멀티 에이전트 리뷰 계승 |
| **추가 기능** | Codex MCP 정적 리뷰 루프, 계획 문서 특화 리뷰 |
| **입력** | `docs/planning/06-tasks.md`, `docs/planning/00-drift-anchor.md`, `specs/` |
| **출력** | `docs/planning/codex-review-report.md`, 수정된 `06-tasks.md` |

---

## 활성화 트리거

- `/codex-review` 명령 입력 시
- `/forge-pipeline` Phase 2에서 자동 호출
- "계획 리뷰", "static review", "정적 리뷰" 키워드

---

## Phase 0: 환경 감지 및 Codex MCP 확인

### Step 1: Codex MCP 가용성 확인

```bash
# MCP 도구 목록에서 codex 관련 도구 탐색
# 가능한 패턴: mcp__codex__*, mcp__openai__*
```

```
if Codex MCP 도구 발견:
  MODE = "codex-enhanced"    # Codex MCP로 정적 분석
  사용 가능 도구 목록 기록
elif $CLABS_SOCKET 존재:
  MODE = "distributed"       # Clabs 분산 리뷰
else:
  MODE = "agent-fallback"    # Agent 서브에이전트 리뷰
fi
```

### Step 2: 필수 입력 파일 확인

```
필수:
- docs/planning/06-tasks.md (구현 계획)
- docs/planning/00-drift-anchor.md (Drift Anchor)

선택:
- specs/domain/resources.yaml
- specs/screens/*.yaml
- docs/planning/01-prd.md ~ 09-personas.md
```

없으면 AskUserQuestion으로 안내:
```
"구현 계획(06-tasks.md)이 없습니다. 먼저 /tasks-generator를 실행해주세요."
```

---

## Phase 1: Static Review Loop (핵심!)

> 계획 → 리뷰 → 수정 → 재리뷰를 최대 3회 반복

### Review Loop 구조

```
┌─────────────────────────────────────────────────────────────┐
│                    Static Review Loop                        │
│                                                             │
│  Iteration 1:                                               │
│  ├── 06-tasks.md 로드                                       │
│  ├── Reviewer 3명에게 리뷰 요청                             │
│  │   ├── Architect: 구조/의존성/병렬성 검증                 │
│  │   ├── Reviewer: 완전성/일관성/실행가능성 검증            │
│  │   └── DriftGuard: Drift Anchor 정합성 검증               │
│  ├── Codex MCP (available 시): 정적 분석                    │
│  │   ├── 태스크 간 의존성 순환 감지                         │
│  │   ├── 범위 중복/누락 분석                                │
│  │   └── 실행 순서 최적화 제안                              │
│  ├── 리뷰 결과 종합                                         │
│  └── Critical/Important 이슈 있으면 → 수정 → Iteration 2   │
│                                                             │
│  Iteration 2: (Critical 이슈 수정 후)                       │
│  ├── 수정된 06-tasks.md 재로드                              │
│  ├── 이전 이슈 해결 여부 확인                               │
│  ├── 새로운 이슈 탐색                                       │
│  └── 이슈 없으면 → Human Checkpoint                        │
│                                                             │
│  Iteration 3: (최대, 이후는 사용자 판단)                    │
│                                                             │
│  종료 조건:                                                 │
│  - Critical 이슈 0개                                        │
│  - Important 이슈 ≤ 2개 (minor는 무시)                     │
│  - 또는 3회 반복 완료                                       │
│                                                             │
│  Human Checkpoint ✋                                         │
│  └── 사용자가 최종 확인 후 다음 Phase로                     │
└─────────────────────────────────────────────────────────────┘
```

### Reviewer 역할 (3명)

| Reviewer | 관점 | 검증 항목 |
|----------|------|----------|
| **Architect** | 구조/설계 | 태스크 분해 적절성, 의존성 그래프 건전성, 병렬 실행 가능성, 기술 부채 위험 |
| **Reviewer** | 완전성/품질 | 누락 태스크, 중복 태스크, 완료 기준 명확성, 테스트 전략 적절성 |
| **DriftGuard** | Drift 방지 | Objective 정합성, Scope 초과/미달, Constraint 위반, Out-of-Scope 침투 |

### Codex MCP Enhanced Mode

Codex MCP 도구가 사용 가능할 때 추가 실행:

```
Codex MCP 정적 분석 항목:
1. 의존성 순환 감지 (DAG 검증)
   - 태스크 간 depends_on 관계를 그래프로 구성
   - 순환 발견 시 Critical 이슈
2. 범위 중복 분석
   - 두 태스크가 같은 파일/모듈을 수정하는 경우 감지
   - 병렬 실행 시 충돌 위험 경고
3. 실행 순서 최적화
   - Critical Path 분석
   - 병렬화 가능 그룹 식별
4. 코드 영향도 분석 (코드베이스 존재 시)
   - 각 태스크가 영향 주는 파일/함수 예측
   - 변경 범위 겹침 경고
```

### Agent Fallback Mode

Codex MCP 없을 때:

```javascript
// 3명의 Agent 서브에이전트 병렬 스폰
await Promise.all([
  Agent({ type: "general-purpose", description: "Architect Review", ... }),
  Agent({ type: "general-purpose", description: "Completeness Review", ... }),
  Agent({ type: "general-purpose", description: "DriftGuard Review", ... })
]);
```

---

## Phase 2: 교차 검증 및 토론

**구현 계획 특화 질문** 사용:

### Turn 1: 초기 리뷰 결과 수집

각 Reviewer의 리뷰 수집 후 종합.

### Turn 2: 교차 검증 (필수)

```
Architect에게:
"DriftGuard가 '{Drift 지적}'을 발견했습니다.
이 태스크 구조에서 Scope를 벗어나지 않으면서 
해당 기능을 어떻게 재구성할 수 있을까요?"

Reviewer에게:
"Architect가 '{병렬화 제안}'을 했습니다.
이렇게 병렬화하면 태스크 간 완전성이 유지되나요?
누락되는 연결점은 없나요?"

DriftGuard에게:
"Reviewer가 '{추가 태스크}'를 제안했습니다.
이 추가 태스크가 Drift Anchor의 Scope 내에 있나요?"
```

### Turn 3: 수렴 (필수)

```
"지금까지의 리뷰를 종합하면:
- 합의된 수정사항: {N}개
- 미해결 쟁점: {M}개

수정사항을 06-tasks.md에 반영하겠습니다."
```

---

## Phase 3: 계획 수정 및 재리뷰

### Step 1: 자동 수정 (합의 사항)

합의된 수정사항을 06-tasks.md에 반영:
- 태스크 추가/삭제/수정
- 의존성 관계 재구성
- 병렬 그룹 재정의

### Step 2: 재리뷰 (Iteration 2)

수정된 계획을 다시 리뷰:
- 이전 Critical/Important 이슈 해결 확인
- 수정으로 인한 새 이슈 탐색
- Drift Anchor 재검증

### Step 3: 종료 판정

```
if critical_issues == 0 && important_issues <= 2:
  → Phase 4 (Human Checkpoint)
elif iteration < 3:
  → Phase 3 재실행 (수정 + 재리뷰)
else:
  → 남은 이슈 목록과 함께 Phase 4로
fi
```

---

## Phase 4: Human Checkpoint (필수!)

```json
{
  "questions": [{
    "question": "구현 계획 리뷰가 완료되었습니다.\n\n**리뷰 결과:**\n- 리뷰 반복: {iteration}회\n- 해결된 이슈: {resolved}개\n- 남은 이슈: {remaining}개 (모두 Minor)\n\n**최종 계획 요약:**\n- 총 Phase: {N}개\n- 총 태스크: {M}개\n- 병렬 가능 그룹: {K}개\n- 예상 Critical Path: {path}\n\n최종 계획을 확인하고 다음 단계로 진행할까요?",
    "header": "Human Checkpoint - 구현 계획 최종 확인",
    "options": [
      {"label": "승인하고 진행", "description": "이 계획으로 실행 토폴로지 정의 (Phase 3)로 진행"},
      {"label": "추가 수정 필요", "description": "특정 부분 수정 후 재리뷰"},
      {"label": "전체 재리뷰", "description": "처음부터 다시 리뷰"},
      {"label": "중단", "description": "계획 수립 단계로 돌아가기"}
    ],
    "multiSelect": false
  }]
}
```

---

## Phase 5: 리포트 생성

```markdown
# codex-review Review Report

## 실행 요약
- **모드**: {codex-enhanced / distributed / agent-fallback}
- **리뷰 반복**: {N}회
- **총 이슈**: {발견} → {해결} (해결율 {N}%)

## Drift Anchor 정합성
- **Objective 정합**: ✅/❌
- **Scope 준수**: ✅/❌ (초과: {N}개, 미달: {M}개)
- **Constraint 준수**: ✅/❌

## 리뷰어별 핵심 발견

### Architect
- 구조 점수: {N}/10
- 핵심 발견: ...

### Reviewer
- 완전성 점수: {N}/10
- 핵심 발견: ...

### DriftGuard
- 정합성 점수: {N}/10
- 핵심 발견: ...

### Codex MCP (사용 시)
- 순환 의존성: {N}개 발견/해결
- 범위 중복: {N}개 경고
- 최적화 제안: {N}개

## 해결된 이슈
| # | 심각도 | 내용 | 해결 방법 |
|---|--------|------|----------|

## 미해결 이슈 (Minor)
| # | 내용 | 사유 |
|---|------|------|

## Human Checkpoint
- **결과**: {승인됨/추가수정/재리뷰}
- **승인자 코멘트**: {있으면}
```

Write to `docs/planning/codex-review-report.md`.

---

## Codex MCP 설정 가이드

Codex MCP가 미설정일 때 사용자에게 안내:

```
Codex MCP가 감지되지 않았습니다.

Codex MCP를 설정하면 구현 계획에 대한 정적 분석이 추가됩니다:
- 의존성 순환 자동 감지
- 코드 영향도 분석
- 범위 중복 경고

설정 방법은 references/codex-setup-guide.md를 참조하세요.

지금은 Agent fallback 모드로 진행합니다.
```

---

## 제약 조건

### MUST DO
- **Drift Anchor 대비 정합성 검증 필수 (DriftGuard)**
- **최소 1회 이상 리뷰 루프 실행**
- **Human Checkpoint 없이 다음 단계 진행 금지**
- 리뷰 결과를 codex-review-report.md에 기록

### MUST NOT DO
- **Codex MCP 없을 때 에러로 중단하지 않음 (graceful fallback)**
- **Human Checkpoint을 스킵하거나 자동 승인하지 않음**
- **3회 초과 리뷰 루프 (무한 루프 방지)**

---

## 참조 라우팅

| Topic | Reference | Load When |
|-------|-----------|-----------|
| Codex MCP 설정 | references/codex-setup-guide.md | Phase 0에서 Codex 미감지 시 |
| 리뷰 템플릿 | ../references/review-templates.md | Phase 1 리뷰 시 |
| 페르소나 | ../references/single-session-personas.md | Agent fallback 시 |

---

## 빠른 시작

```bash
# 단독 실행
/codex-review

# forge-pipeline에서 자동 호출
/forge-pipeline  → Phase 2에서 자동 실행

# Codex MCP 설정 확인
/codex-review --check-codex
```
