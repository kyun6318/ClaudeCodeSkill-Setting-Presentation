# Skill Name

---

## 1. 스킬

| 카테고리 | 스킬 (Claude Code) | 1줄 역할 |
|---|---|---|
| 기획 | `/requirements-dialogue` | 1:1 대화로 요구사항을 수렴해 9개 기획 문서 생성 |
| 기획 | `/screen-spec-generator` | 화면 별 상세 명세(YAML v2.0) 생성 |
| 기획 | `/task-decomposer` | Domain-Guarded 화면 단위 태스크 구조로 TASKS.md 생성 |
| 기획 | `/auto-planner` | 짧은 프롬프트를 5분 내 풀 사양으로 자동 확장 |
| 아이디어 | `/idea-concretizer` | 추상 아이디어를 재귀 사고로 3-4개 MVP 제안으로 변환 |
| 아이디어 | `/co-brainstorming` | AI 4개 페르소나 + 사용자 공동 브레인스토밍 |
| 실행 | `/full-pipeline-orchestrator` | 전체 자동화 —  아이디어 → 기획 → 리뷰 → 명세 → 빌드 → 개선 |
| 실행 | `/dependency-auto-executor` | tasks.md 의존성 기반 자동 직렬/병렬 실행 |
| 실행 | `/autonomous-build-harness` | spec.md 기반 Build-Eval 피드백 루프로 자율 앱 생성 |
| 검증 | `/code-reviewer` | 2단계 리뷰(스펙 준수 → 코드 품질) |
| 검증 | `/quality-gate` | Phase 완료 후 품질 게이트 검사·메트릭·리포트 |
| 검증 | `/pre-completion-verifier` | 완료/통과 주장 전 검증 명령 실행 강제 |
| 개선 | `/self-improvement-loop` | Frozen Metric + 자율 실험 루프(코드 수정→측정→keep/revert) |
| 개선 | `/auto-error-fixer` | Build > Type > Test > Lint 우선순위 자동 수정 루프 |
| 개선 | `/auto-security-audit` | STRIDE + OWASP Top 10 자율 보안 감사 |
| 검증 | `/systematic-debugger` | 4단계 근본 원인 분석 프로세스 |
| 운영 | `/token-compressor` | 출력 압축 + 도구 제한 + 세션 학습 훅 |
| 운영 | `/cost-model-router` | 태스크 복잡도에 따라 모델 자동 선택해 비용 절감 |

---