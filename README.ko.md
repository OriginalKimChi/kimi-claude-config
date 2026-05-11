# kimi-claude-config (한국어)

[English](README.md) · 한국어

[Kimi Code MCP](https://github.com/howardpen9/kimi-code-mcp) 서버를 Claude Code 에서 더 편하게 쓰기 위한 개인 설정 모음.

포함 항목:
- **Slash 커맨드 5개** — `/kimi-analyze`, `/kimi-query`, `/kimi-implement`, `/kimi-review`, `/kimi-resume`. 각각 `kimi-code` MCP 서버의 해당 툴로 요청을 그대로 넘기는 얇은 래퍼.
- **서브에이전트 1개** — `kimi-rescue`. 긴 컨텍스트 분석 / 적대적 코드 리뷰 / 자율 파일 수정을 Kimi (Moonshot K2.6, 256K 컨텍스트) 에게 위임. `codex-rescue` 의 Kimi 버전.

이 파일들은 `~/.claude/` 아래에 자리잡으면 Claude Code 가 자동으로 인식합니다.

## 사전 준비

```bash
# 1) 런타임
brew install node uv

# 2) Kimi CLI (Python) — 첫 실행에서 Moonshot API 키 로그인
uv tool install kimi-cli
kimi

# 3) Kimi MCP 서버 (GitHub 포크에서 설치)
npm i -g github:OriginalKimChi/kimi-code-mcp
# 또는 원본:
# npm i -g github:howardpen9/kimi-code-mcp
```

`~/.claude/mcp.json` 에 MCP 서버 등록:

```json
{
  "mcpServers": {
    "kimi-code": { "command": "kimi-mcp-server" }
  }
}
```

## 커맨드 + 에이전트 설치

```bash
# 1) 레포 clone
git clone https://github.com/OriginalKimChi/kimi-claude-config.git ~/kimi-claude-config

# 2) symlink — git pull 로 업데이트 받으려면 이 방식 권장
mkdir -p ~/.claude/commands ~/.claude/agents
ln -sf ~/kimi-claude-config/commands/kimi          ~/.claude/commands/kimi
ln -sf ~/kimi-claude-config/agents/kimi-rescue.md  ~/.claude/agents/kimi-rescue.md

# 또는 그냥 복사
# cp -r ~/kimi-claude-config/commands/kimi         ~/.claude/commands/
# cp    ~/kimi-claude-config/agents/kimi-rescue.md ~/.claude/agents/
```

Claude Code 재시작하면 `/kimi-analyze`, `/kimi-query` 등이 slash 목록에 뜨고, `Agent` 툴로 `kimi-rescue` 호출이 가능해집니다.

## 새 컴퓨터에서 처음부터 — 전체 흐름

```bash
# (1) 런타임
brew install node uv

# (2) Kimi CLI + MCP 서버
uv tool install kimi-cli
npm i -g github:OriginalKimChi/kimi-code-mcp

# (3) Moonshot API 키 로그인 (컴퓨터마다 필수)
kimi

# (4) MCP 등록 — ~/.claude/mcp.json 에 아래 엔트리 추가
#   "kimi-code": { "command": "kimi-mcp-server" }

# (5) 이 레포 clone + symlink
git clone https://github.com/OriginalKimChi/kimi-claude-config.git ~/kimi-claude-config
mkdir -p ~/.claude/commands ~/.claude/agents
ln -sf ~/kimi-claude-config/commands/kimi          ~/.claude/commands/kimi
ln -sf ~/kimi-claude-config/agents/kimi-rescue.md  ~/.claude/agents/kimi-rescue.md

# (6) Claude Code 재시작
```

## 디렉토리 구조

```
commands/kimi/
  kimi-analyze.md     # 코드베이스 분석 (현재 프로젝트 컨텍스트 사용)
  kimi-query.md       # 일반 프로그래밍 질문 (코드베이스 컨텍스트 없음)
  kimi-implement.md   # 자율 파일 수정 (반드시 새 worktree 안에서 실행)
  kimi-review.md      # 적대적 코드 리뷰 (security / performance / maintainability)
  kimi-resume.md      # 이전 Kimi 세션 이어서 진행
agents/
  kimi-rescue.md      # 라우팅 서브에이전트 — 자연어 요청을 적절한 Kimi MCP 툴 하나로 포워딩
```

## 사용 예시

```text
# 코드베이스 분석
/kimi-analyze 이 백엔드에서 결제 처리 흐름이 어떻게 구성되어 있는지 매핑해줘

# 일반 질문 (코드베이스 안 봄)
/kimi-query Rust 의 lifetime elision 규칙을 예시와 함께 설명해줘

# 적대적 리뷰
/kimi-review backend/services/payment_service.py 를 security focus 로 리뷰

# 자율 수정 (worktree 에서 실행됨)
/kimi-implement frontend/app/checkout/page.tsx 의 카드 입력 폼에 만료일 validation 추가

# 이전 세션 이어가기
/kimi-resume 마지막 세션에서 발견한 N+1 쿼리 문제 더 깊게 파고들어줘
```

서브에이전트 `kimi-rescue` 는 메인 스레드가 무거운 작업을 Kimi 에게 넘기고 싶을 때 자동으로 호출됩니다 — 별도로 호출할 필요는 없어요.

## 라이센스

MIT.
