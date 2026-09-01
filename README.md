# cursorrules

Personal Cursor rules and agents — copy or symlink into any project.

## Layout

```
.cursor/
  rules/          # .mdc rule files (alwaysApply or glob-scoped)
  agents/
    topnotes/     # Topnotes-specific subagents (backlog, auth playbooks)
```

## Install agents in a project

Copy the whole project folder into your repo:

```bash
# from this repo
cp -r .cursor/agents/topnotes /path/to/topnotes-2/.cursor/agents/
```

Or symlink to stay in sync while developing agents here:

```bash
ln -sf ~/Documents/code/cursorrules/.cursor/agents/topnotes \
  /path/to/topnotes-2/.cursor/agents/topnotes
```

Cursor discovers agents from `.cursor/agents/*.md` (flat) or nested paths depending on version — if nested agents are not picked up, copy individual `.md` files into `.cursor/agents/`.

## Install rules

```bash
cp .cursor/rules/*.mdc /path/to/your-project/.cursor/rules/
```

## Topnotes agents

| Agent | Use when |
|-------|----------|
| `backlog.md` | Picking up deferred product work, backlog tickets, “implement a backlog item” |
| `fix-google-oauth-callback.md` | Google OAuth lands on `/?code=` instead of `/auth/callback` (Supabase redirect / www vs apex) |
| `fix-password-recovery.md` | Password reset, forgot password, `otp_expired` recovery links (#42 playbook) |

Auth playbooks document fixes already shipped on Topnotes `main`; keep them as runbooks for regressions or similar Supabase/Next apps.
