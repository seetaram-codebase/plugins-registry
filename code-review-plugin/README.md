# Code Review Plugin

An AI-assisted code review plugin for Claude Code that provides structured review methodology, security scanning, auto-linting, and GitHub integration.

## What's Inside

| Component | Purpose |
|---|---|
| `skills/review-pr/` | Structured code review methodology with a 5-phase process |
| `agents/security-reviewer/` | Specialized security-focused agent (read-only, OWASP Top 10) |
| `hooks/hooks.json` | Auto-lint on every file write (JS/TS, Python, Go, Rust) |
| `.mcp.json` | GitHub MCP server for PR access and inline comments |

## Quick Start

### Local testing

```bash
claude --plugin-dir ./code-review-plugin
```

### Use the code review skill

```
/code-review:review-pr
```

Or simply ask Claude to review code — the skill activates automatically when it detects a review task.

### Trigger the security agent

Ask Claude:
> "Run a security-focused review on the auth module"

The security-reviewer agent has restricted tool access (read-only) and focuses exclusively on vulnerability identification.

## Prerequisites

### For GitHub integration
Set your GitHub token as an environment variable:

```bash
export GITHUB_TOKEN=your_github_personal_access_token
```

The token needs `repo` scope for private repositories, or `public_repo` for public ones.

### For auto-lint hooks
The hooks auto-detect file types and run the appropriate linter:

| Language | Linter | Install |
|---|---|---|
| JavaScript/TypeScript | ESLint | `npm install -g eslint` |
| Python | Ruff (fallback: Black) | `pip install ruff` or `pip install black` |
| Go | gofmt | Included with Go |
| Rust | rustfmt | Included with Rust |

Hooks fail silently if the linter is not installed — they won't block your workflow.

## Plugin Structure

```
code-review-plugin/
├── .claude-plugin/
│   └── plugin.json            # Plugin manifest (name, version, description)
├── skills/
│   └── review-pr/
│       └── SKILL.md           # Code review methodology and instructions
├── agents/
│   └── security-reviewer/
│       └── AGENT.md           # Security-focused sub-agent definition
├── hooks/
│   └── hooks.json             # PostToolUse hooks for auto-linting
├── .mcp.json                  # GitHub MCP server configuration
└── README.md
```

## How the Review Skill Works

The review-pr skill follows a 5-phase methodology:

1. **Understand Context** — Read PR metadata, identify scope and purpose
2. **Read Systematically** — Tests first, then interfaces, then implementation
3. **Evaluate** — Assess correctness, readability, performance, and security
4. **Quality Gate** — Verify all files reviewed, at least one positive observation
5. **Structured Feedback** — Critical issues, suggestions, positive observations, verdict

## How the Security Agent Works

The security-reviewer agent:

- Has **read-only** tool access (Read, Glob, Grep) — it cannot modify files
- Maps the attack surface before reading code line by line
- Classifies findings against OWASP Top 10 (2021) and CWE references
- Reports findings with severity, location, attack scenario, and concrete fix
- Produces a risk assessment summary with prioritized recommendations

## Customization

### Adjust the review criteria
Edit `skills/review-pr/SKILL.md` to add domain-specific checks, change the feedback format, or adjust the quality gate thresholds.

### Change linter configuration
Edit `hooks/hooks.json` to use different linters, add new languages, or adjust lint flags.

### Add more agents
Create a new directory under `agents/` with an `AGENT.md` file following the same format as the security-reviewer.

## Version History

- **1.0.0** — Initial release with review skill, security agent, lint hooks, and GitHub MCP

## License

MIT
