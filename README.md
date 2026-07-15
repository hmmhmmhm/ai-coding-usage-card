# AI Coding Usage Card

A self-hosted GitHub profile card that shows how many tokens (and how much API-equivalent money) you've burned across your AI coding tools — **Claude Code, Codex, Gemini CLI, Copilot CLI, OpenCode, and everything else [ccusage](https://github.com/ccusage/ccusage) detects**.

No external services. No accounts. No API keys. Everything runs on your machine against your own local logs, and the card is just an SVG committed to your profile repo.

**Live examples** — all four variants, auto-updating daily from my own usage:

`ai-usage-full.svg` (846×280)

<img width="100%" src="https://raw.githubusercontent.com/DGO0/DGO0/main/cards/ai-usage-full.svg" alt="full variant" />

`ai-usage-grass.svg` (846×190)

<img width="100%" src="https://raw.githubusercontent.com/DGO0/DGO0/main/cards/ai-usage-grass.svg" alt="grass variant" />

`ai-usage-half.svg` (423×195) &nbsp;·&nbsp; `ai-usage-half-grass.svg` (423×335)

<p align="left">
<img width="49%" align="top" src="https://raw.githubusercontent.com/DGO0/DGO0/main/cards/ai-usage-half.svg" alt="half variant" />
<img width="49%" align="top" src="https://raw.githubusercontent.com/DGO0/DGO0/main/cards/ai-usage-half-grass.svg" alt="half-grass variant" />
</p>

## Variants

One run generates all four — embed whichever you like:

| File | Size | Content |
|---|---|---|
| `ai-usage-full.svg` | 846×280 | Everything: totals, 4-currency cost, token mix, activity, per-tool split, 30-day chart |
| `ai-usage-half.svg` | 423×195 | ALL-TIME + COST only — pairs nicely with github-readme-stats cards |
| `ai-usage-half-grass.svg` | 423×335 | Half card + 26-week contribution-style usage grass |
| `ai-usage-grass.svg` | 846×190 | 52-week usage grass only (GitHub 잔디 for AI usage) |

## What it shows

| Block | Content |
|---|---|
| ALL-TIME | Total tokens + cache-hit ratio |
| COST | API-equivalent cost in USD + 3 currencies of your choice (live FX rates, free API) |
| TOKEN MIX | Output / input / cache read / cache write |
| ACTIVITY | Active days, avg per day, peak day, top model |
| BY TOOL | Per-tool cost split (Claude Code / Codex / Gemini / Copilot) |
| GRASS | Contribution-graph-style heatmap of daily cost |

> "API-equivalent" because subscription users (Claude Max etc.) don't actually pay per token — the number is what the same usage would cost on the API. That's the fun part.

## How it works

```
ccusage (local logs) ──► usage-card.mjs ──► SVG ──► GitHub contents API ──► your profile README
        ▲                                                                        ▲
   all detected CLIs                                              <img> pointing at the raw file
```

A scheduler (Task Scheduler / cron / launchd) runs the script once a day. Each run recomputes everything from your local logs, fetches fresh FX rates, and commits the regenerated SVG.

## Setup

**Requirements:** Node 18+, [GitHub CLI](https://cli.github.com/) logged in (`gh auth login`, `repo` scope is enough), and a profile repo (the public repo named after your username).

1. **Get the script**

   ```bash
   curl -O https://raw.githubusercontent.com/DGO0/ai-coding-usage-card/main/usage-card.mjs
   ```

2. **Edit the CONFIG block** at the top: set `repo` to `yourname/yourname`, pick your currencies. On Windows, absolute paths for `npx`/`gh` are recommended (scheduled tasks have a minimal PATH).

3. **Test run**

   ```bash
   node usage-card.mjs
   # [2026-07-15T...] card updated: 12.6B tokens | $13,190 | Claude Code $13,190 | Codex $0.04
   ```

4. **Embed in your profile README** (any of the four variants)

   ```html
   <img width="100%" src="https://raw.githubusercontent.com/YOURNAME/YOURNAME/main/cards/ai-usage-full.svg" alt="AI usage" />
   <img width="100%" src="https://raw.githubusercontent.com/YOURNAME/YOURNAME/main/cards/ai-usage-grass.svg" alt="AI usage grass" />
   ```

5. **Schedule it daily**

   **Windows** — no-flash wrapper + Task Scheduler:

   ```vbs
   ' usage-card.vbs
   CreateObject("Wscript.Shell").Run "cmd /c ""node ""C:\path\to\usage-card.mjs"" >> ""C:\path\to\usage-card.log"" 2>&1""", 0, False
   ```

   ```powershell
   schtasks /Create /TN "AIUsageCard" /TR 'wscript.exe "C:\path\to\usage-card.vbs"' /SC DAILY /ST 09:37 /F
   ```

   **macOS / Linux** — cron:

   ```cron
   37 9 * * * /usr/local/bin/node /path/to/usage-card.mjs >> /path/to/usage-card.log 2>&1
   ```

## Customization

- **Accent color** — `CONFIG.accent` (default: green on black; swap in your own hex)
- **Currencies** — any codes supported by [open.er-api.com](https://open.er-api.com) (`KRW`, `EUR`, `CNY`, `JPY`, `GBP`, …)
- **Layout** — it's ~80 lines of template SVG; move columns, add rows, go wild

## Gotchas (learned the hard way)

- **Escape `<` in any dynamic SVG text** (`&lt;`) — one raw `<` kills the whole card with `StartTag: invalid element name`.
- **Seeing a stale card right after a design change?** GitHub caches images aggressively. Append `?v=2` to the `<img>` URL to bust it. Daily data updates don't need this — the cache is only ~5 minutes.
- The script needs no `workflow` scope — it commits a plain file via the contents API, no Actions involved.

## 한국어 요약

로컬에 쌓인 AI 코딩 CLI 로그(ccusage가 감지하는 모든 툴)를 매일 집계해서, 누적 토큰·4개 통화 비용·툴별 분해·30일 차트를 담은 SVG 카드를 프로필 레포에 커밋하는 스크립트입니다. 외부 서비스·계정·API 키 전부 불필요.

1. `usage-card.mjs` 받기 → 상단 CONFIG에서 `repo`를 `본인아이디/본인아이디`로 수정
2. `node usage-card.mjs` 한 번 실행해 테스트
3. 프로필 README에 `<img width="100%" src="https://raw.githubusercontent.com/아이디/아이디/main/cards/ai-usage-full.svg" />` 삽입 (4종 중 원하는 파일명으로)
4. 작업 스케줄러(위 VBS 래퍼) 또는 cron으로 매일 실행 등록

전제 조건은 Node 18+, `gh auth login` 완료된 GitHub CLI뿐입니다.

## License

MIT
