---
name: thread-ledger
description: Flexible breadcrumb-based thread tracking with causal chains and snapshots for sidetracks, dependencies, and context preservation. Use when the user wants to log or update thread ledgers, causal breadcrumbs, or JSONL snapshots via natural language (e.g., "sidetracked").
---

# thread-ledger

Purpose
- Preserve the causal chain of work so the user never loses context about why a step exists.
- Provide a flexible breadcrumb and process tracking system, not a rigid task list.
- Keep narrative decisions and dependencies recoverable even when plans change.

Files
- Templates live in thread-ledger/references/ (threads.md, snapshots.jsonl).
- Runtime files live in the working repo, defaulting to ./threads/threads.md and ./threads/snapshots.jsonl.
- If runtime files do not exist, offer to initialize them by copying the templates.

Intake (natural language)
- Accept freeform updates; no special keywords required.
- Treat mentions of "sidetrack" or "sidetracked" as a new causal branch to record.
- If the update includes intent, blockers, or next actions, capture them in the thread fields.

Linking heuristics
- If a thread is named or clearly implied, link to that thread.
- If not, link to the last active thread in the runtime ledger (default: ./threads/threads.md).
- If multiple active threads are plausible, ask one targeted question with a recommended default.
- For sidetracks, insert the breadcrumb after the most recent breadcrumb that caused it.
- If no specific prior breadcrumb is referenced, append to the end of the chain.

Update behaviors
1) Thread create or update
   - Create a new thread when the update introduces a new outcome.
   - Otherwise update Outcome, Why now, Current blocker, Next action, Last snapshot.
2) Breadcrumb insert
   - Insert at any point in the chain (before_index or after_index).
   - Renumber the list; do not delete later breadcrumbs unless asked.
3) Snapshot append
   - Append a JSON object as a new line in snapshots.jsonl.
   - Update Last snapshot in threads.md.
4) Summary (optional)
   - Summarize recent snapshots and current thread state on request.

Response format to user
- Updated: <thread id> <what changed> (breadcrumb index or snapshot line)
- Linked: <why it was linked there>
- Next action: <if detected>
- Ambiguity: <only if needed, with one question and a recommended default>

threads.md format (template and runtime)
- Active Threads list with compact fields
- Detailed thread sections with numbered breadcrumb chains

snapshots.jsonl format (template and runtime)
- One JSON object per line with keys:
  timestamp, thread_id, changed, why, stuck, next, tags (optional)
- Example line:
  {"timestamp":"2026-02-07T14:00:00Z","thread_id":"T-001","changed":"Tested IdentitiesOnly; failures persist","why":"Trying to reproduce login failures","stuck":"Unsure if CA trust is set on target host","next":"Check sshd_config TrustedUserCAKeys on target","tags":["ssh","nixos","deploy"]}

Guardrails
- Preserve existing content; keep edits minimal and localized.
- Do not create Linear tasks unless explicitly instructed.
