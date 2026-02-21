---
name: openclaw-agent-creator
description: Create and scaffold new OpenClaw agents. Use this skill when asked to create, add, or set up a new OpenClaw agent, or when working with OpenClaw multi-agent configurations.
tools:
  - read
  - write
  - bash
version: "1.0.0"
---

# OpenClaw Agent Creator Skill

Use this skill to create a new OpenClaw agent. You will produce a config diff and a full workspace scaffold.

## When to invoke
- User says "create an OpenClaw agent", "add a new agent", "new OpenClaw agent", or describes a persona to add
- User provides OpenClaw agent requirements (purpose, channel, name)

## What you need to know before generating

Gather (or infer from context):
1. **Agent ID** — short slug, lowercase, hyphens OK (e.g. `research`, `support`, `dev`)
2. **Display name** — human-readable (e.g. "Research Assistant")
3. **Purpose** — one sentence describing what the agent does
4. **Channel binding** — which channel(s) route to this agent, and any accountId/peer filter
5. **Personality/tone** — defaults to "helpful and direct" if not specified
6. **Model** — omit to inherit from `agents.defaults`
7. **Specialised memory files** — 0–3 domain-specific files (e.g. PROJECTS.md, CONTACTS.md)

## Output 1: Config diff

Show only the two JSON5 fragments — never the full config:

```
════ Add to agents.list[] ═══════════════════════════════
{
  id: "<id>",
  name: "<name>",
  workspace: "~/.openclaw/workspace-<id>",
}

════ Add to bindings[] (before catch-all rules) ═════════
{
  agentId: "<id>",
  match: { channel: "<channel>" }
}
```

## Output 2: Workspace files

Generate markdown content for each file. Enforce these size limits strictly:

| File | Max |
|------|-----|
| AGENTS.md | 4 KB |
| SOUL.md | 2 KB |
| USER.md | 1 KB |
| TOOLS.md | 2 KB |
| HEARTBEAT.md | 1.5 KB |
| BOOT.md | 1 KB |
| BOOTSTRAP.md | 1.5 KB |
| memory files | 1 KB each |

**File generation rules:**
- AGENTS.md and USER.md: use the standard OpenClaw templates verbatim — no agent-specific content
- SOUL.md and TOOLS.md: use standard template, then append a `★ Agent-specific` section at the bottom
- HEARTBEAT.md: generate 3–5 proactive tasks derived from the agent's purpose
- BOOTSTRAP.md: write a first-run ritual appropriate to the agent's role
- BOOT.md: standard gateway-restart checklist

## Output 3: Setup instructions

After generating files, always provide:
```bash
mkdir -p ~/.openclaw/workspace-<id>
# copy the generated .md files into that directory
openclaw gateway restart
```

And remind: peer bindings must appear before channel-wide catch-all rules in `openclaw.json`.

## Key architecture facts

- Gateway runs at `ws://127.0.0.1:18789`
- Config file: `~/.openclaw/openclaw.json` (JSON5 format)
- Workspace root: `~/.openclaw/workspace/` (default), `~/.openclaw/workspace-<id>/` (per-agent)
- Skills precedence: workspace skills > managed skills > bundled skills
- Session logs: `~/.openclaw/agents/<agentId>/sessions/<SessionId>.jsonl`
- BOOTSTRAP.md runs once on first session; delete after completion
- HEARTBEAT.md runs every ~30 minutes when gateway is active
