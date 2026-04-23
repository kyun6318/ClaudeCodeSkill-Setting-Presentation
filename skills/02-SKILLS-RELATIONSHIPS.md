# Skills Relationships & Pipeline Architecture

## 전체 파이프라인 흐름도

```mermaid
graph TD
    %% ENTRY POINTS
    Entry["ENTRY POINTS (진입점 선택)<br/>• /full-pipeline-orchestrator-pipeline<br/>• /autonomous-build-harness<br/>• 개별 스킬 직접 진입"]
    
    %% Phase 1
    subgraph P1["Phase 1: BRAINSTORMING"]
        P1_1["/idea-concretizer (3-4개 MVP 제안)"]
        P1_2["/co-brainstorming (4 AI 페르소나 브레인스토밍)"]
        P1_Out>"출력: co-brainstorming-proposal.md 또는 선택된 MVP"]
    end
    
    %% Phase 2
    subgraph P2["Phase 2: PLANNING (기획)"]
        P2_1["/requirements-dialogue (심층 기획)"]
        P2_2["/requirements-dialogue-anchored (Drift Anchor)"]
        P2_3["/auto-planner (자동 spec)"]
        P2_Out>"출력: docs/planning/01-09, .claude/handoffs/handoff-*.json"]
    end

    %% Phase 2.5
    subgraph P25["Phase 2.5: REVIEW (선택적)"]
        P25_2["/council-codex (+ Codex MCP)"]
        P25_Out>"출력: docs/planning/council-report.md"]
    end

    %% Phase 3
    subgraph P3["Phase 3: SPECIFICATION (명세)"]
        P3_2["/task-decomposer (Domain-Guarded TASKS.md)"]
        P3_Out>"출력: specs/domain/resources.yaml, docs/planning/06-tasks.md"]
    end

    %% Phase 4
    subgraph P4["Phase 4: EXECUTION (실행)"]
        P4_A["Path A: 구조화 실행<br/>/dependency-auto-executor"]
        P4_B["Path B: Build-Eval 루프<br/>/autonomous-build-harness"]
        P4_Aux["보조: /spike"]
    end

    %% Phase 5
    subgraph P5["Phase 5: QUALITY GATES (품질 검증)"]
        P5_1["/pre-completion-verifier"]
        P5_2["/quality-gate (메트릭 측정)"]
        P5_2_F["/powerqa (실패 시 자동 수정)"]
        P5_3["/code-reviewer (Spec + Code Quality)"]
        
        P5_1 --> P5_2
        P5_2 -.Fail.-> P5_2_F
        P5_2 -.Pass.-> P5_3
        P5_Aux["보조: /systematic-debugger"]
    end

    %% Phase 6
    subgraph P6["Phase 6: IMPROVEMENT (자가개선)"]
        P6_1["/self-improvement-loop (자율 실험 루프)<br/>:fix, :debug, :learn, :security, :scenario, :predict, :ship, :plan"]
        P6_3["/self-improvement-loop-skills"]
        P6_4["/reflection"]
        P6_Out>"출력: 개선된 코드 + 실험 로그"]
    end

    Entry --> P1
    P1 --> P2
    P2 --> P25
    P25 --> P3
    P3 --> P4
    P4 --> P5
    P5 --> P6
```

---

## 오케스트레이터별 파이프라인 비교

### `/full-pipeline-orchestrator-pipeline` — Drift-Anchored 강화 파이프라인

```
requirements-dialogue-anchored → task-decomposer + council-codex → Execution Topology → dependency-auto-executor → quality-gate + sync + code-reviewer
```

| Phase | 스킬 | 특징 |
|-------|------|------|
| 1 | /requirements-dialogue-anchored | Drift Anchor 생성 (objective/scope/constraint) |
| 2 | /task-decomposer + /council-codex | Static Review Loop (최대 3라운드) |
| 3 | Execution Topology 정의 | 병렬 실행 전략 수립 |
| 3.5 | Gemini MCP 디자인 + 테스트 시나리오 | 디자인/로직 분리 |
| 4 | /dependency-auto-executor | 실행 + branch 격리 |
| 5 | /quality-gate → /sync → /code-reviewer | 3중 품질 검증 |

### `/autonomous-build-harness` — 3-에이전트 Build-Eval 루프

```
requirements-dialogue/auto-planner → spec 변환 → [autonomous-build-harness] x N (사용자 선택: 1/3/5/custom) → final report
```

| Phase | 스킬 | 특징 |
|-------|------|------|
| 0 | 기획 방법 선택 | requirements-dialogue / auto-planner / skip |
| 1 | /requirements-dialogue 또는 /auto-planner | 기획 |
| 2 | Artifact 변환 | features.json, rubric.json, spec.md 생성 |
| 3-4 | /autonomous-build-harness | Build-Eval 피드백 루프 (1/3/5/custom 사이클, 기본 3) |
| 5 | Final report | 종합 리포트 |

---

## 품질 체인 (Quality Chain) — 자동 트리거 순서

각 Phase 완료 시 dependency-auto-executor가 자동으로 실행하는 순서 (sync는 수동 — PR 생성 전/사용자 요청 시):

```mermaid
graph TD
    V["pre-completion-verifier"]
    Q["quality-gate (메트릭 측정)"]
    CR["code-reviewer"]
    MERGE["Phase 병합"]
    P["powerqa (자동 수정, 최대 5사이클)"]
    RECALL["전문가 에이전트 재호출"]
    RECHAIN1["품질 체인 재실행"]
    RECHAIN2["품질 체인 재실행"]
    SD["systematic-debugger (근본 원인 분석)"]

    V --pass--> Q
    Q --pass--> CR
    Q --fail--> P
    
    CR --pass--> MERGE
    
    CR --fail--> RECALL
    RECALL --> RECHAIN1
    
    P --수정 성공--> RECHAIN2
    P --3회 동일 실패--> SD
```

---

## 스킬 간 의존 관계 맵

### 직접 호출 관계 (A → B = A가 B를 호출)

```mermaid
graph LR
    %% full-pipeline-orchestrator-pipeline
    FPOP["full-pipeline-orchestrator-pipeline"]
    FPOP --> RDA["requirements-dialogue-anchored"]
    FPOP --> TD2["task-decomposer"]
    FPOP --> CC["council-codex"]
    FPOP --> DAE["dependency-auto-executor"]
    FPOP --> QG["quality-gate"]
    FPOP --> SY["sync"]
    FPOP --> CR["code-reviewer"]

    %% autonomous-build-harness
    ABH2["autonomous-build-harness"]
    ABH2 --> RD2["requirements-dialogue"]
    ABH2 --> AP["auto-planner"]

    %% dependency-auto-executor
    DAE3["dependency-auto-executor"]
    DAE3 --> PCV["pre-completion-verifier"]
    DAE3 --> QG2["quality-gate"]
    DAE3 --> CR2["code-reviewer"]
    DAE3 --> PQA["powerqa (실패 시)"]

    %% others
    QG3["quality-gate"] --> PQA2["powerqa (실패 시)"]
    PQA3["powerqa"] --> SD["systematic-debugger (3회 실패 시)"]

    %% handoffs
    RD3["requirements-dialogue"] --핸드오프--> TD3["task-decomposer"]
```

### 데이터 흐름 (A ──output──→ B)

```mermaid
graph LR
    IC["idea-concretizer"] --".claude/docs/...-proposal.md"--> RD["requirements-dialogue"]
    CB["co-brainstorming"] --"co-brainstorming-proposal.md"--> RD
    
    RD --"docs/planning/01-09"--> CC["council-codex"]
    RD --".claude/handoffs/*.json<br/>(세션 분리)"--> TD["task-decomposer"]
    
    CC --"council-report.md (반영)"--> TD
    
    TD --"docs/planning/06-tasks.md"--> DAE["dependency-auto-executor"]
    
    AP["auto-planner"] --"features.json/rubric.json"--> ABH["autonomous-build-harness"]
```

---

## 선택적/조건부 실행 경로

### Skip Points (건너뛰기 가능 지점)

| 진입 조건 | 건너뛸 수 있는 Phase | 시작 스킬 |
|-----------|---------------------|----------|
| 아이디어만 있음 | 없음 | /idea-concretizer 또는 /co-brainstorming |
| 기획 문서 있음 (docs/planning/) | brainstorming | /task-decomposer |
| TASKS.md 있음 | brainstorming + planning + spec | /dependency-auto-executor |
| spec.md만 있음 | planning | /autonomous-build-harness |

### 세션 분리 (Handoff Protocol)

장시간 기획 작업 시 세션을 분리하여 컨텍스트 초과를 방지:

```mermaid
graph TD
    S1["[Session 1] /requirements-dialogue"]
    O1>"docs/planning/* + .claude/handoffs/handoff-*.json"]
    S2["[Session 2] /task-decomposer"]
    O2>"TASKS.md 생성"]
    S3["[Session 3] /dependency-auto-executor"]
    O3["실행"]

    S1 --> O1
    O1 --"(세션 종료, 새 세션 시작)"--> S2
    S2 --> O2
    O2 --"(세션 종료, 새 세션 시작)"--> S3
    S3 --> O3
```

---

## 병렬 실행 지점

| 스킬 | 병렬 방식 | 대상 |
|------|----------|------|
| /full-pipeline-orchestrator-pipeline Phase 4 | 모델별 분리 | Sonnet(로직) + Gemini(UI) |
| /self-improvement-loop:predict | 5인 페르소나 스웜 | 독립 분석 → 토론 → 합의 |

---

## 독립 스킬 (파이프라인 외부)

파이프라인에 속하지 않고 독립적으로 사용 가능한 스킬:

### 언어/프레임워크 전문가
| 스킬 | 역할 | 자동 트리거 조건 |
|------|------|----------------|
| /python-pro | Python 3.11+ 베스트 프랙티스 | .py 파일 작업 시 |
| /typescript-pro | TypeScript 5.x 타입 안전성 | .ts/.tsx 파일 작업 시 |
| /golang-pro | Go 1.22+ 동시성 패턴 | .go 파일 작업 시 |
| /react-19 | React 19 컴포넌트/hooks | React 컴포넌트 작업 시 |
| /fastapi-latest | FastAPI 비동기 API | FastAPI 코드 작업 시 |
| /kubernetes-specialist | K8s 매니페스트/배포 | k8s YAML 작업 시 |
| /terraform-engineer | IaC HCL 모듈 | .tf 파일 작업 시 |
| /database-optimizer | SQL 쿼리/인덱스 최적화 | DB 스키마/쿼리 작업 시 |

### 유틸리티
| 스킬 | 역할 | 사용 시점 |
|------|------|----------|
| /reasoning | CoT/ToT/ReAct 추론 | 복잡한 문제 분석 시 |
| /memory | 세션 간 지식 축적 | 프로젝트 학습 지속 시 |
| /session-report | 세션 종료 리포트 | 작업 완료 시 |
| /token-compressor | 토큰 절감 (60-90%) | 비용 최적화 시 |
| /cost-model-router | 모델 자동 선택 (40-70% 절감) | 태스크 실행 시 |
| /rag | Context7 최신 문서 검색 | 라이브러리 코드 생성 시 |
| /chrome-browser | 브라우저 자동화 | UI 테스트/디버깅 시 |
| /desktop-bridge | Desktop↔CLI 연결 | 하이브리드 워크플로우 시 |
| /a2a | 에이전트 간 통신 프로토콜 | 멀티 에이전트 협업 시 |
| /kongkong2 | Query Repetition 정확도 향상 | 전 에이전트 자동 적용 |
| /the-fool | 도메인 무관 비판적 추론 | 사각지대 탐색 시 |

---

## 요약: 추천 워크플로우

### 신규 프로젝트 (아이디어 → 배포)
```
/full-pipeline-orchestrator-pipeline  (전체 자동 오케스트레이션)
```

### 기획만 깊이 있게
```
/co-brainstorming → /requirements-dialogue → /council-codex
```

### 기획 완료 후 빠른 빌드
```
/autonomous-build-harness  (auto-planner로 빠른 spec → Build-Eval 루프)
```

### 기존 코드 자율 개선
```
/auto-error-fixer  (에러 수정)
/auto-security-audit  (보안 감사)
/self-improvement-loop:learn  (문서화)
```
