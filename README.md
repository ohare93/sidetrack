# Sidetrack Thread Ledger

This repo provides a lightweight thread + breadcrumb system to preserve causal chains of work. It is designed for situations where tasks fork into sidetracks and you want to retain the "why" behind each step without turning everything into rigid tasks.

## How it works
- **Threads** capture high-level outcomes (what you are trying to achieve).
- **Breadcrumbs** capture the causal chain (why each step happened).
- **Snapshots** capture fresh state updates (what changed, why, stuck, next).

The system is intentionally flexible and uses natural language updates. It avoids over-structuring and focuses on keeping context discoverable.

## Repo layout
- `thread-ledger/SKILL.md`: OpenClaw/Codex skill definition (with YAML frontmatter).
- `thread-ledger/references/threads.md`: Template ledger file.
- `thread-ledger/references/snapshots.jsonl`: Template JSONL snapshot log.

## Runtime files
Runtime files live in the working repo where the agent is operating:
- `./threads/threads.md`
- `./threads/snapshots.jsonl`

If these files do not exist, the agent should offer to initialize them by copying the templates from `thread-ledger/references/`.

## Using with OpenClaw
1) Install or load the skill from `thread-ledger/SKILL.md`.
2) In your target repo, let the agent initialize `./threads/` from the templates on first use.
3) Provide natural language updates; no special keywords are required.

Example prompts:
- "I got sidetracked again; now I am working on SSH CA rollout."
- "I realized the deploy failed because IdentitiesOnly was missing."
- "Current blocker: the target host is missing TrustedUserCAKeys."
- "Summarize the last week of this thread."

Expected agent behavior:
- Link updates to the correct thread or breadcrumb chain.
- Insert breadcrumbs anywhere if you are correcting the record.
- Append snapshots for time-based updates.
- Ask one targeted question only if the thread is ambiguous.

## Using with other agents
Any agent can use the same files. The minimal contract is:
- Maintain a markdown ledger at `./threads/threads.md`.
- Append JSON lines to `./threads/snapshots.jsonl`.
- Keep updates small, localized, and reversible.

Suggested workflow:
1) Start of session: pick the active thread.
2) During work: add breadcrumbs when sidetracks happen.
3) End of session: append a snapshot.
