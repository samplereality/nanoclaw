# Memory migration record — 2026-07-21

Run of `/migrate-memory` during the `/update-nanoclaw` upgrade **2.1.23 → 2.1.53**
(provider-agnostic memory breaking change). Migrated legacy per-group memory into
the OKF v0.1 `memory/` tree + `instructions.prepend.md`. Content-blind staging,
then harness-side organization. No symlinks found → no quarantine.

The migrated data lives in the gitignored group folders (`groups/<folder>/memory/`,
`groups/<folder>/instructions.prepend.md`); this file is the record of what moved.

## Maintenance window

- Host service `nanoclaw.service` stopped (froze the 60s sweep so scheduled tasks
  couldn't wake a memoryless agent), `main` container (`nanoclaw-v2-main-…`)
  stopped, migration performed, host restarted with the new `dist/` code.
- No `ncl tasks` series were paused (old host predated the command). Legacy
  recurring `messages_in` survived and resume via the sweep: every-5-min Pi poller
  (series `task-1778208055029-bv56zv`), weekly Bluesky report Tue 8am ET
  (series `task-1781148069917-9bejip`).

## Source → destination

### Group `main` (`ag-1777037518807-fdd2hw`)

| Source (staged) | Outcome |
|---|---|
| `CLAUDE.md` (438 B, composed boilerplate) | Classified boilerplate → discarded (regenerates at spawn) |
| `CLAUDE.local.md` (19,454 B) | Distilled → `instructions.prepend.md` (ProfClaw persona, concise voice, no-double-send rule) + memory concepts (below); v1 system-reference boilerplate (lines 82–442) and the `@./.claude-global.md` include intentionally omitted as obsolete → discarded |
| `.claude-shared/projects/-workspace-agent/memory/feedback_no_duplicate_messages.md` | → `memory/no-duplicate-messages.md` (type: feedback) → discarded |
| `.claude-shared/projects/-workspace-agent/memory/MEMORY.md` | Index pointer, folded into the above → discarded |

New `main` memory tree:
- `memory/index.md` — Core Memory (Mark Sample; telegram-mg-17770; Bluesky bots; frontend-engineer sub-agent) + Map
- `memory/accounts.md` (reference) — Bluesky, AgentMail, Gmail SMTP, Google OAuth, X/Twitter
- `memory/bluesky-bots.md` (project) — weekly report, dashboard, 2026-07-07 follower snapshot
- `memory/location-fact-workflow.md` (workflow) — location_fact trigger handling
- `memory/workspace-files.md` (reference) — key workspace files
- `memory/no-duplicate-messages.md` (feedback)
- `memory/system/{index,definition}.md` (templates)

### Group `frontend-engineer` (`ag-1782940020344-s3sp1z`)

| Source (staged) | Outcome |
|---|---|
| `CLAUDE.md` (438 B, composed boilerplate) | Discarded |
| `CLAUDE.local.md` (421 B) | → `instructions.prepend.md` (frontend-engineer role) + Core Memory fact (sub-agent of ProfClaw, builds the Bluesky bot dashboard) → discarded |

New `frontend-engineer` memory tree: `memory/index.md` (Core Memory + Map) +
`memory/system/{index,definition}.md`.

## Verification

- Both trees well-formed: every non-reserved concept has a `type:`; Core Memory
  holds facts; standing behavior in `instructions.prepend.md`; all concepts
  reachable from `index.md` Map.
- Staging (`.memory-migration-staging/`) removed after operator approval;
  auto-memory source cleared.
- Host restarted, `2.1.53`, service active; `ncl tasks` available; recurring
  tasks intact.
- **Live recall test: PASSED** (2026-07-21) — ProfClaw recalled migrated memory
  correctly after respawn on the new image.
