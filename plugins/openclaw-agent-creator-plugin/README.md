# OpenClaw Agent Creator — Claude Code Plugin

Scaffold new [OpenClaw](https://docs.openclaw.ai) agents directly from Claude Code. Generates config diffs and complete workspace markdown files.

## Commands

| Command | Description |
|---------|-------------|
| `/openclaw-new-agent` | Interactively create a new OpenClaw agent with guided prompts |
| `/openclaw-list-agents` | Summarise all agents defined in your openclaw.json |

## Skill

The plugin also includes an **Agent Creator Skill** that Claude uses autonomously — if you describe a new agent need in any context, Claude will apply the skill to generate the scaffold without needing an explicit command.

## Installation

```bash
# From the plugin marketplace
/plugin install openclaw-agent-creator@<marketplace>

# Or from a local clone
/plugin marketplace add ./path/to/marketplace
/plugin install openclaw-agent-creator@local
```

## Usage

```
/openclaw-new-agent
```

You'll be prompted for:
- Agent ID and display name
- Purpose (one sentence)
- Channel binding
- Optional: model override, personality, specialised memory files

The plugin outputs:
1. JSON5 config diff to paste into `~/.openclaw/openclaw.json`
2. All workspace `.md` files with correct content and size guardrails
3. Setup instructions

## Plugin structure

```
openclaw-agent-creator-plugin/
├── .claude-plugin/
│   └── plugin.json
├── commands/
│   ├── openclaw-new-agent.md      # Main scaffolding command
│   └── openclaw-list-agents.md   # List existing agents
├── skills/
│   └── openclaw-agent-creator/
│       └── SKILL.md               # Autonomous skill for agent creation
└── README.md
```

## References

- [OpenClaw Docs](https://docs.openclaw.ai)
- [OpenClaw Architecture](https://docs.openclaw.ai/concepts/architecture)
- [Claude Code Plugins](https://code.claude.com/docs/en/plugins)
