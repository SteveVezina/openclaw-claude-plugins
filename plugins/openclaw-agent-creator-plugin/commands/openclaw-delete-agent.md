---
description: Safely remove an OpenClaw agent — generates config removal diff and optional workspace cleanup commands
---

# /openclaw-delete-agent

You are helping the user remove an existing OpenClaw agent. You will produce a config removal diff and optional shell commands to clean up the workspace.

---

## Step 1 — Identify the agent

Ask the user which agent to delete (by ID or name). If they can share their `agents.list` and `bindings` from `~/.openclaw/openclaw.json`, read them. Otherwise ask them to paste the relevant sections.

---

## Step 2 — Confirm before proceeding

Show a summary of what will be removed and ask for explicit confirmation:

```
About to remove agent: <name> (id: <id>)

Config changes:
  ✕ Remove from agents.list[]: { id: "<id>", ... }
  ✕ Remove from bindings[]: { agentId: "<id>", ... }  (<n> binding(s))

Workspace: ~/.openclaw/workspace-<id>/
  → You can optionally delete this directory (see Step 4)

Session logs: ~/.openclaw/agents/<id>/sessions/
  → You can optionally archive or delete these

Proceed? (yes/no)
```

Do not generate output until the user confirms.

---

## Step 3 — Config removal diff

After confirmation, show only the fragments to remove — never the full config:

```
════ Remove from agents.list[] ═════════════════════════
{
  id: "<id>",
  name: "<n>",
  workspace: "~/.openclaw/workspace-<id>",
}

════ Remove from bindings[] ════════════════════════════
{ agentId: "<id>", match: { channel: "<channel>" } }
// ... (list all bindings for this agent)
```

---

## Step 4 — Workspace cleanup (optional)

Ask whether the user wants to keep or delete the workspace and session logs. Then provide the appropriate shell commands:

**Delete workspace permanently:**
```bash
rm -rf ~/.openclaw/workspace-<id>
```

**Archive workspace instead:**
```bash
tar -czf ~/openclaw-<id>-backup-$(date +%Y%m%d).tar.gz ~/.openclaw/workspace-<id>
rm -rf ~/.openclaw/workspace-<id>
```

**Delete session logs:**
```bash
rm -rf ~/.openclaw/agents/<id>
```

---

## Step 5 — Final reminder

- Run `openclaw gateway restart` after editing the config
- If this agent had a catch-all binding, check that remaining agents still have correct routing
- If another agent needs to absorb this agent's channels, suggest running `/openclaw-update-agent` next
