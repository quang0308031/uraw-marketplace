# URAW: Effort Ladder research for Claude Code

Version 0.1.0 (early; see "Known limits").

`/uraw` answers a question at the lowest sufficient effort. It proposes web-verified research only when a concrete self-check signal says the answer would otherwise be unverified, and only with your consent. Whether a claim is *settled* is decided by code, never by the model.

| Level | What happens |
|---|---|
| E0 | Direct answer, no tools |
| E1 | Goal / constraints / assumptions / unknowns, then self-check |
| E2 | E1 + claims checked by parallel web search, verbatim quotes, opposing queries |
| E3 | E2 + small experiments, each run approved by you |

## Requirements

- Claude Code (plugin support) and **Node.js 20 or later**. No `npm install`: the CLI is bundled in one file.
- No API keys. It uses Claude Code's own WebSearch and subagents.

## Install

From a local folder (try it first):

```bash
claude --plugin-dir ./uraw-plugin
```

Or add the folder to a marketplace of yours and install with `/plugin install`.
Because it is a plugin, the skill is namespaced: run it as `/uraw:uraw`
(or just `/uraw` if Claude Code lets the plugin skill be called by its short name).

## Use

```
/uraw:uraw [options] <question>
/uraw:uraw status | report [--out FILE] | stop | help
```

| Option | Meaning | Default |
|---|---|---|
| `--effort auto\|E0\|E1\|E2\|E3` | Starting level | `auto` |
| `--max E0..E3` | Never propose above this level | none |
| `--parallel N` (1-8) | Parallel web searches | 3 |
| `--checkpoint N` (1-100) | Searches before asking to extend | 12 |
| `--claims N` (1-6) | Claims to verify | 3 |
| `--topk N` (1-5) | Pages read per query | 2 |
| `--sensitive "a,b"` | Terms that must never appear in a query | none |
| `--primary "h1,h2"` | Hosts counted as primary sources | none |
| `--model haiku\|sonnet\|opus\|fable\|inherit` | Model for the helper subagents (never above the session's) | session |
| `--out FILE` | Also write the evidence report as Markdown | none |

Tip: without `--primary`, every source counts as secondary and a numeric or technical claim cannot become *settled*. Name the hosts you trust as primary (official docs, specs).

## What the code enforces

- Escalation to E2/E3 needs your explicit approval; a declined signal is never asked again.
- Only planned queries may be searched; sensitive terms and a per-task canary are redacted or block the query.
- Fetched page text is stored on disk and read only by a read-only subagent (`uraw-reader`); quotes are checked verbatim against the page.
- *Settled* needs 2+ independent sources, an opposing query, a primary source for technical claims, and a round that added nothing new.
- No subagent runs on a model above the session's (Sonnet session: Sonnet or Haiku only).
- Task state lives in `~/.uraw` (override with `URAW_HOME`).

## Known limits

- The plugin's hooks run only after `/uraw` has been invoked in a session.
- Claude Code does not (yet) let a hook replace WebSearch output, so search result text still reaches the main model. The evidence the answer relies on is still limited to quotes the reader subagent returned and the code verified.
- Hooks declared inside plugin-shipped agents may be ignored by Claude Code; the main skill hook enforces the same rules (planner has no tools, reader reads only page files).
- *Settled* is hard to reach: it requires a round that adds no new source, and web search keeps returning new pages.
- Tested with the bundled unit tests and two live runs on one question; not yet used widely.

## Remove

Disable or uninstall the plugin in `/plugin`. Delete `~/.uraw` to remove saved task ledgers.

## License

MIT. See the `LICENSE` file.
