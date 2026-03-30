<p align="center">
  <img src="harness_banner.png" alt="Harness Banner" width="600">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-1.0.1-brightgreen.svg" alt="Version">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Claude_Code-Plugin-purple.svg" alt="Claude Code Plugin">
  <img src="https://img.shields.io/badge/Codex_CLI-Skill-blue.svg" alt="Codex CLI Skill">
  <img src="https://img.shields.io/badge/Patterns-6_Architectures-orange.svg" alt="6 Architecture Patterns">
</p>

# Harness

**Agent Team & Skill Architect** — Works with both **Claude Code** and **OpenAI Codex CLI**

**English** | [한국어](README_KO.md) | [日本語](README_JA.md)

> Forked from [revfactory/harness](https://github.com/revfactory/harness). This version supports both Claude Code and Codex CLI from a single repository.

A meta-skill that designs domain-specific agent teams, defines specialized agents, and generates the skills they use.

## Installation

### Claude Code

```shell
# Via marketplace
/plugin marketplace add sumniy/codex-harness
/plugin install harness@harness

# Or direct copy
cp -r skills/claude-code/harness ~/.claude/skills/harness
```

### OpenAI Codex CLI

```
# Via $skill-installer
$skill-installer install https://github.com/sumniy/codex-harness/tree/main/skills/codex/harness

# Or direct copy
cp -r skills/codex/harness ~/.agents/skills/harness
```

## Repository Structure

```
harness/
├── .claude-plugin/                    # Claude Code plugin manifest
│   ├── plugin.json
│   └── marketplace.json
├── .codex-plugin/                     # Codex CLI plugin manifest
│   ├── plugin.json
│   └── marketplace.json
│
├── skills/
│   ├── claude-code/                   # ← Claude Code version (original)
│   │   └── harness/
│   │       ├── SKILL.md
│   │       └── references/
│   │           ├── agent-design-patterns.md
│   │           ├── orchestrator-template.md
│   │           ├── team-examples.md
│   │           ├── skill-writing-guide.md
│   │           ├── skill-testing-guide.md
│   │           └── qa-agent-guide.md
│   │
│   └── codex/                         # ← Codex CLI version (adapted)
│       └── harness/
│           ├── SKILL.md
│           ├── agents/openai.yaml
│           └── references/
│               ├── agent-design-patterns.md
│               ├── orchestrator-template.md
│               ├── team-examples.md
│               ├── skill-writing-guide.md
│               ├── skill-testing-guide.md
│               └── qa-agent-guide.md
│
├── README.md
├── README_KO.md
└── README_JA.md
```

## Platform Comparison

| | Claude Code | Codex CLI |
|---|---|---|
| **Skill path** | `skills/claude-code/harness/` | `skills/codex/harness/` |
| **Agent defs** | `.claude/agents/{name}.md` | `.codex/agents/{name}.toml` |
| **Skill defs** | `.claude/skills/{name}/skill.md` | `.agents/skills/{name}/SKILL.md` |
| **Execution mode** | Agent Teams (default) + Subagents | Subagents only (parallel via `max_threads`) |
| **Top model** | `model: "opus"` | `model = "o3"` / `gpt-5.4` |
| **Install cmd** | `/plugin install` | `$skill-installer install <url>` |
| **Invoke** | `/harness` or auto-trigger | `$harness` or auto-trigger |
| **Project docs** | `CLAUDE.md` | `AGENTS.md` |

## Key Features

- **6 Architecture Patterns** — Pipeline, Fan-out/Fan-in, Expert Pool, Producer-Reviewer, Supervisor, Hierarchical Delegation
- **Skill Generation** — Progressive Disclosure for efficient context management
- **Orchestration** — Inter-agent data passing, error handling, coordination protocols
- **Validation** — Trigger verification, dry-run testing, with-skill vs without-skill comparison

## 6-Phase Workflow

```
Phase 1: Domain Analysis
Phase 2: Team Architecture Design
Phase 3: Agent Definition Generation
Phase 4: Skill Generation
Phase 5: Integration & Orchestration
Phase 6: Validation & Testing
```

## Usage

**Claude Code:**
```
하네스 구성해줘
Build a harness for this project
```

**Codex CLI:**
```
$harness
하네스 구성해줘
Build a harness for this project
```

## Credits

- Original: [revfactory/harness](https://github.com/revfactory/harness) by [@revfactory](https://github.com/revfactory)
- Research: [revfactory/claude-code-harness](https://github.com/revfactory/claude-code-harness) — +60% quality improvement
- Harness 100: [revfactory/harness-100](https://github.com/revfactory/harness-100) — 100 pre-built harnesses across 10 domains

## License

Apache 2.0
