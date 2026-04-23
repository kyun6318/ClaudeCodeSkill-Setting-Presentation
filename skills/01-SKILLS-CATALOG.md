# Claude Code Skills Catalog

## 기본 스킬 (Claude Code Built-in) — 9개

### `update-config`
- Claude Code 하네스의 settings.json을 설정하는 스킬
- "앞으로 X할 때마다", "X 전에/후에" 같은 자동화 행동을 hooks로 등록
- 설정 파일 기반으로 Claude Code의 동작을 커스터마이징

### `keybindings-help`
- 키보드 단축키 커스터마이징 전용 스킬
- ~/.claude/keybindings.json 수정을 안내
- 키 리바인딩, 코드 단축키 추가, 제출 키 변경 등 지원

### `simplify`
- 변경된 코드의 재사용성, 품질, 효율성을 검토
- 발견된 문제를 자동으로 수정
- 코드 단순화 및 리팩토링에 특화

### `loop`
- 프롬프트나 슬래시 커맨드를 주기적으로 반복 실행
- 기본 10분 간격, 사용자 지정 가능 (예: /loop 5m /foo)
- 상태 폴링, 반복 모니터링에 활용

### `schedule`
- 원격 에이전트(트리거)를 cron 스케줄로 생성/관리
- 예약된 자동화 태스크 생성 및 실행
- 스케줄 기반 원격 에이전트 관리

### `claude-api`
- Claude API / Anthropic SDK 앱 빌드 전용
- 프롬프트 캐싱, Managed Agents, tool use 등 Claude 기능 구현 지원
- anthropic SDK import 감지 시 자동 트리거

### `claude-dashboard:setup`
- Claude Code 상태표시줄(status line) 설정 구성
- 대시보드 플러그인 초기 셋업
- 상태 모니터링 UI 설정

### `claude-dashboard:update`
- statusLine 경로를 최신 플러그인 버전으로 업데이트
- 대시보드 플러그인 버전 관리
- 자동 경로 갱신

### `claude-dashboard:check-usage`
- Claude, Codex, Gemini, z.ai 등 모든 AI CLI 사용량 확인
- 사용 제한 상태 체크 및 추천 제공
- 멀티 AI 도구 비용/사용량 통합 모니터링

---

## 사용자 추가 스킬 (~/.claude/skills/) — 65개

### 기획/브레인스토밍 계열

#### `requirements-dialogue`
- 소크라테스식 기획 컨설팅
- 10개 명확성 차원 x 40-50개 동적 질문으로 기획
- 경쟁분석/페르소나/비즈니스모델 검증 포함, 상황에 맞는 기획 문서 생성

#### `requirements-dialogue-anchored`
- requirements-dialogue + Drift Anchor 강제로 기획 이탈 방지
- objective/scope/constraint를 확정
- Terraform/GCP, Python ML, LLM API, 스크래핑 프로젝트 최적화

#### `task-decomposer`
- Domain-Guarded 화면 단위 태스크 구조로 TASKS.md 생성
- 화면 요구사항과 도메인 리소스 간 Interface Contract Validation
- 실행 가능한 태스크 목록 자동 생성

#### `auto-planner`
- 1-4문장 프롬프트를 5분 내 풀 제품 사양으로 자동 확장
- requirements-dialogue와 달리 비대화형 -- 질문 없이 자동 spec 생성
- Anthropic 하네스 아키텍처의 Planner Agent 구현

#### `idea-concretizer`
- 추상적 아이디어를 구체적 MVP로 변환
- AI 재귀적 사고로 3-4개 제안 생성
- 아이디어 발전 후 바로 개발 진행 가능

#### `co-brainstorming`
- AI + 사용자 공동 브레인스토밍
- AI 페르소나가 비판 없이 아이디어 발산
- co-brainstorming-proposal.md 생성 -> /requirements-dialogue 연결


### 오케스트레이션/파이프라인 계열

#### `full-pipeline-orchestrator-pipeline`
- 아이디어->기획->리뷰->명세->빌드->자가개선 풀 파이프라인
- 5단계 커스텀 파이프라인 오케스트레이터
- Drift-Anchored 기획(Opus) -> Codex 리뷰 -> cmux 병렬 실행(gemini)
- idea-concretizer/co-brainstorming -> requirements-dialogue -> council -> screen-spec-generator -> dependency-auto-executor 연결
- Terraform/Python ML/LLM API/Scraping 프로젝트 최적화

#### `dependency-auto-executor`
- TASKS.md 의존성 기반 자동 직렬/병렬 실행
- Phase 병합까지 완전 자동화
- 태스크 의존성 분석 후 최적 실행 순서 결정
- cmux 기반 병렬 에이전트 실행 + task별 독립 git branch 격리
- Codex MCP 연속 검증 루프 + task별 3종 문서 관리
- Sonnet 모델 활용, aggregate review cycle 포함

#### `autonomous-build-harness`
- requirements-dialogue(기획) -> Builder(구현) -> Evaluator(QA) 3-에이전트 아키텍처
- Build-Eval 피드백 루프 (1/3/5/custom 사이클, 기본 3)로 자율 앱 생성
- 기획 품질 + 하네스의 자율 빌드/QA 결합


### 리뷰/검증/품질 계열

#### `codex-review`
- Codex MCP 플러거블 정적 리뷰
- 여러 관점(CTO, UX, Security 등)에서 기획안을 심의
- 다각도 피드백으로 기획 품질 향상
- 계획->리뷰->수정->재리뷰 Static Review Loop
- Codex MCP 있으면 활용, 없으면 Agent fallback

#### `code-reviewer`
- 태스크 완료/주요 기능 구현/머지 전 코드 검토
- 2단계 리뷰: Spec Compliance -> Code Quality
- 명세 준수 + 코드 품질 이중 검증

#### `quality-gate`
- Phase 완료 후 품질 게이트 검사 및 메트릭 측정
- dependency-auto-executor와 연동
- 리포트 생성 및 통과/실패 판정

#### `pre-completion-verifier`
- 작업 완료/수정 주장 전 반드시 실행
- 커밋이나 PR 전에 검증 명령어 실행 후 출력 확인
- "항상 주장 전 증거" 원칙 강제

#### `powerqa`
- 자동 QA 사이클링 -- 테스트/검증/수정을 목표 달성까지 자동 반복
- 최대 5사이클, 동일 실패 3회 시 조기 종료
- 자율적 품질 보증 워크플로우

#### `sync`
- 명세(spec)와 코드의 동기화 상태 검증
- 드리프트 감지 및 불일치 리포트
- 자동 수정 제안 제공


### self-improvement-loop 계열

#### `self-improvement-loop`
- Karpathy self-improvement-loop 패턴의 범용 구현
- 3파일 구조 + Frozen Metric + 2계층 RSI 자율 실험 루프
- 어떤 도메인이든 최적화 대상과 메트릭 정의 후 자동 실행

#### `self-improvement-loop-skills`
- 모든 스킬에 적용 가능한 범용 자율 개선 엔진
- SKILL.md 자동 수정 -> 테스트 -> 메트릭 측정 -> keep/revert
- 2계층 RSI(Inner/Outer) 지원

#### 서브커맨드: `auto-error-fixer`
- 우선순위 기반 자동 에러 수정 루프
- Build > Type > Test > Lint 순서로 수정
- 에러 유형별 자동 트리아지

#### 서브커맨드: `auto-security-audit`
- STRIDE + OWASP Top 10 자율 보안 감사
- 위협 모델 생성 -> 자율 벡터 탐색
- 심각도별 리포트 생성


### 디버깅/보안 계열

#### `systematic-debugger`
- 버그/테스트 실패/예상치 못한 동작 시 수정 전 반드시 사용
- 4단계 근본 원인 분석 프로세스
- 체계적 디버깅으로 원인 파악 후 수정


### 언어/프레임워크 전문가 계열

#### `python-pro`
- Python 3.11+ 베스트 프랙티스 강제
- 타입 안전성, async 패턴, 프로덕션급 코드 품질
- 타입 힌트 추가, 테스트 설계 지원

#### `typescript-pro`
- TypeScript 5.x 베스트 프랙티스 강제
- 타입 안전성, 성능, 유지보수성 최적화
- 타입 시스템 설계, tsconfig 설정 지원

#### `golang-pro`
- Go 1.22+ 전문 지식 제공
- 동시성 패턴, 시스템 프로그래밍, 클린 아키텍처
- Go 코드 작성/리뷰/구조화 지원

#### `react-19`
- React 19 프레임워크 전문
- 컴포넌트와 hooks 기반 UI 빌드
- 최신 React 패턴 적용

#### `fastapi-latest`
- FastAPI 모던 Python 웹 프레임워크 전문
- 자동 OpenAPI 문서 생성 API 빌드
- 최신 FastAPI 패턴 및 비동기 처리

#### `kubernetes-specialist`
- K8s 클러스터 운영, 매니페스트 작성, 배포 전략
- Helm 차트 설정, Pod 디버깅
- 쿠버네티스 트러블슈팅 전문

#### `terraform-engineer`
- 멀티클라우드 환경 IaC(Infrastructure as Code) 설계/관리
- HCL 모듈 작성, 인프라 변경 계획
- IaC 설정 리뷰

#### `database-optimizer`
- MySQL/PostgreSQL 쿼리 최적화 및 인덱스 설계
- EXPLAIN 출력 분석, 스키마 설계
- DB 성능 튜닝 전문


### 유틸리티/기타 계열

#### `reasoning`
- Chain of Thought, Tree of Thought, ReAct 등 추론 기법 제공
- 복잡한 문제 해결을 위한 구조화된 사고
- 다양한 추론 프레임워크 지원

#### `reflection`
- 에이전트가 자신의 출력을 비판적으로 검토
- 반복 개선하는 자기 성찰 패턴
- 출력 품질 자율 향상

#### `memory`
- 세션 간 학습 지속 및 프로젝트 지식 축적
- .claude/memory/에 구조화된 메모리 저장
- 장기 기억 관리

#### `token-compressor`
- 자체 완결형 토큰 절감 시스템
- 출력 압축 + 에이전트 도구 제한 + 세션 학습 훅
- 60-90% 토큰 사용량 절감

#### `cost-model-router`
- AI 비용 최적화 라우팅
- 태스크 복잡도에 따라 적절한 모델/에이전트 자동 선택
- 40-70% 비용 절감

#### `session-report`
- 세션 종료 시 구조화된 작업 리포트 생성
- git log/diff + 세션 데이터 -> 변경 분석 -> 메트릭 요약
- 마크다운 리포트 출력

#### `goal-setting`
- TASKS.md 기반 목표 관리 및 진행 상황 모니터링
- quality-gate, dependency-auto-executor와 연동
- 목표 추적 대시보드

#### `spike`
- 불확실 영역 탐색 전용 모드
- 품질 게이트 완화 + 빠른 프로토타이핑
- 결과 리포트 생성으로 탐색 결과 정리

#### `a2a`
- 에이전트 간 구조화된 통신 및 협업 프로토콜
- Request/Response, Handoff, Broadcast 패턴
- 멀티 에이전트 시스템 통신 표준

#### `desktop-bridge`
- Claude Desktop(설계) + Claude Code CLI(구현)를 GitHub Issue로 연결
- publish/implement 두 모드 지원
- 하이브리드 워크플로우 브릿지

#### `chrome-browser`
- Claude Code의 Chrome 브라우저 제어
- 로컬 웹앱 테스트, UI 디버깅, 폼 자동화
- 멀티 사이트 워크플로우 브라우저 자동화

#### `rag`
- Context7 MCP로 최신 라이브러리 문서 검색
- 검색 후 정확한 코드 생성
- 모든 전문가 에이전트에서 자동 활용

#### `kongkong2`
- 입력 복제(Query Repetition) 기법으로 정확도 향상
- 모든 에이전트에 자동 적용
- LLM 응답 품질 향상 기법

#### `the-fool`
- 도메인 무관 비판적 추론 (5가지 분석 모드)
- 가정 탐색, 반대 논증, 실패 시나리오, 레드팀 공격, 증거 검증
- 계획/설계/결정의 사각지대를 소크라테스적 질문으로 탐색