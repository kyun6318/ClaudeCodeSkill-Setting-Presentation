# Codex MCP Setup Guide

## OpenAI Codex MCP 설정 방법

### 방법 1: Claude Code settings.json에 직접 추가

```json
// ~/.claude/settings.json 또는 프로젝트 .claude/settings.json
{
  "mcpServers": {
    "codex": {
      "command": "npx",
      "args": ["-y", "@openai/codex-mcp"],
      "env": {
        "OPENAI_API_KEY": "sk-..."
      }
    }
  }
}
```

### 방법 2: .mcp.json (프로젝트별)

```json
// 프로젝트 루트/.mcp.json
{
  "mcpServers": {
    "codex": {
      "command": "npx",
      "args": ["-y", "@openai/codex-mcp"],
      "env": {
        "OPENAI_API_KEY": "sk-..."
      }
    }
  }
}
```

### 검증

설정 후 Claude Code를 재시작하고:
1. MCP 도구 목록에서 `mcp__codex__*` 패턴 확인
2. `/codex-review --check-codex` 실행

### 대안: OpenAI API 직접 활용

Codex MCP가 아닌 OpenAI API를 직접 사용하는 경우:

```json
{
  "mcpServers": {
    "openai": {
      "command": "npx",
      "args": ["-y", "@anthropic-ai/openai-mcp"],
      "env": {
        "OPENAI_API_KEY": "sk-..."
      }
    }
  }
}
```

### 비용 참고

- Codex MCP 리뷰는 OpenAI API 토큰을 소비합니다
- 구현 계획 리뷰 1회당 약 2K-5K 토큰
- Static Review Loop 3회 반복 시 최대 ~15K 토큰

### Codex MCP 없이도 codex-review는 작동합니다

Agent fallback 모드에서:
- Architect/Reviewer/DriftGuard 3명의 Claude Agent가 리뷰
- 정적 분석은 수동 검증으로 대체
- 리뷰 품질은 유사하나, 자동 분석 기능 제한
