# URAW marketplace

A Claude Code plugin marketplace with one plugin, `uraw` (Effort Ladder research).

## Install

Push this folder to a git host, then in your shell (or with `/plugin` inside a session):

```bash
claude plugin marketplace add <owner>/<repo>      # GitHub shorthand, or a full git URL
claude plugin install uraw@uraw-tools
```

To try it from this folder without a git host:

```bash
claude plugin marketplace add ./uraw-marketplace
claude plugin install uraw@uraw-tools
```

Then run `/uraw:uraw help` in a session. Requires Node.js 20 or later; no API key.

## Update and remove

```bash
claude plugin marketplace update uraw-tools
claude plugin uninstall uraw
claude plugin marketplace remove uraw-tools
```

See `plugins/uraw/README.md` for options, what the code enforces, and known limits.
