---
description: Update an existing OpenClaw agent — modify config, workspace files, or channel bindings
---

# /openclaw-update-agent

You are helping the user update an existing OpenClaw agent. Ask them which agent they want to modify, then determine what needs to change.

---

## Step 1 — Identify the agent

Ask the user which agent to update (by ID or name). If they can share their `agents.list` from `~/.openclaw/openclaw.json`, read it. Otherwise ask them to paste the relevant section.

---

## Step 2 — Determine what to change

Ask which aspects they want to update. Accept any combination:

| Area | What changes |
|------|-------------|
| **Name** | Display name in config |
| **Model** | LLM model override |
| **Channel bindings** | Add, remove, or modify routing rules |
| **Workspace path** | Move workspace location |
| **SOUL.md** | Personality, purpose, tone, focus areas |
| **HEARTBEAT.md** | Proactive tasks |
| **TOOLS.md** | Specialised tool notes |
| **BOOTSTRAP.md** | Re-run a first-session ritual (recreate if deleted) |
| **BOOT.md** | Startup checklist |

---

## Step 3 — Output only diffs

For config changes, show only the modified JSON5 fragment — never the full config:

```
════ Update in agents.list[] (id: "<id>") ══════════════
{
  id: "<id>",
  name: "<updated-name>",           // changed
  workspace: "~/.openclaw/workspace-<id>",
  model: "anthropic/claude-opus-4", // added
}

════ Update in bindings[] ══════════════════════════════
// Remove:
{ agentId: "<id>", match: { channel: "telegram" } }

// Add:
{ agentId: "<id>", match: { channel: "telegram", accountId: "12345" } }
```

For workspace file changes, output only the **updated file** in full (not a line-level diff) with a note about what changed and why.

---

## Step 4 — Remind the user

- Run `openclaw gateway restart` after any config change
- If SOUL.md or TOOLS.md was updated, the change takes effect on the next session (no restart needed)
- If BOOTSTRAP.md was recreated, it will run once on the next session then should be deleted
- Keep all files within their size limits (SOUL.md ≤ 2 KB, HEARTBEAT.md ≤ 1.5 KB, etc.)
