---
name: to-prd-codex
description: Two-act PRD hardening. ACT 1 (you ↔ Claude) — Claude synthesizes the current conversation context and codebase understanding into a PRD draft written to PRD.md (no interview, no publishing yet). ACT 2 (Claude ↔ Codex) — OpenAI Codex adversarially reviews PRD.md in a read-only sandbox (VERDICT: APPROVED/REVISE), Claude revises and re-submits to the SAME Codex session until APPROVED or a MAX_ROUNDS cap. Only after explicit user sign-off is the PRD published to the project issue tracker. Use when you want a second model to attack a PRD before it becomes issues.
---

This skill produces a PRD exactly like `/to-prd`, then runs an adversarial Codex review on it BEFORE publishing to the issue tracker. Do NOT interview the user — synthesize what you already know. Catch conceptual and scope flaws here, where they are cheapest, before the PRD fans out into issues.

The issue tracker and triage label vocabulary should have been provided to you — run `/setup-matt-pocock-skills` if not.

---

## ACT 1 — Synthesize the PRD draft (you ↔ Claude)

Identical to `/to-prd`, with one difference: write the result to `PRD.md` as a DRAFT and do NOT publish to the tracker yet.

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary throughout the PRD, and respect any ADRs in the area you're touching.

2. Sketch out the major modules you will need to build or modify. Actively look for opportunities to extract deep modules that can be tested in isolation. A deep module encapsulates a lot of functionality behind a simple, testable interface that rarely changes. Check with the user that these modules match their expectations, and which modules they want tests written for.

3. Write the PRD using the template below to `PRD.md`. STOP before publishing — Act 2 reviews it first.

<prd-template>

## Problem Statement

The problem the user is facing, from the user's perspective.

## Solution

The solution to the problem, from the user's perspective.

## User Stories

A LONG, numbered list of user stories in the format:

1. As an <actor>, I want a <feature>, so that <benefit>

This list should be extremely extensive and cover all aspects of the feature.

## Implementation Decisions

- Modules to be built/modified and the interfaces being modified
- Technical clarifications from the developer
- Architectural decisions, schema changes, API contracts, specific interactions

Do NOT include specific file paths or code snippets (they go stale). Exception: if a prototype produced a snippet that encodes a decision more precisely than prose (state machine, reducer, schema, type shape), inline the decision-rich part and note it came from a prototype.

## Testing Decisions

- What makes a good test (test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (similar tests in the codebase)

## Out of Scope

What is out of scope for this PRD.

## Further Notes

Any further notes.

</prd-template>

---

## ACT 2 — Adversarial Codex review loop (Claude ↔ Codex)

### Prerequisites
- Codex CLI ≥ 0.130, authenticated via `codex login` (or `$CODEX_API_KEY` / `$OPENAI_API_KEY`).
- **Model: pin `gpt-5.5`** on every Codex call via `-c model="gpt-5.5"` (round 1 and every resume).
- If no auth is found, stop and tell the user: "No Codex authentication found. Run `codex login`, then re-run this skill." Do NOT skip the review silently.

### Configuration
| Parameter | Default | Purpose |
|-----------|---------|---------|
| MAX_ROUNDS | 5 | Hard termination cap |
| PRD_FILE | PRD.md | Draft PRD from Act 1 |
| LOG_FILE | PRD-REVIEW-LOG.md | Append-only review transcript |

### Review focus (what Codex is told to attack)
Tell Codex this is a PRD, not code, and to review at the requirements/conceptual layer:
- **Premises & hidden assumptions** — is the problem framed correctly? What is assumed but never stated?
- **Completeness** — missing user stories, actors, states, or flows; unhandled edge cases.
- **Internal contradictions** — user stories or implementation decisions that conflict.
- **Module decomposition** — are the proposed modules genuinely deep and testable in isolation, or shallow/leaky? Wrong boundaries?
- **Testing decisions** — do they test external behavior? Any module that should be tested but isn't?
- **Scope** — anything that should be Out of Scope but isn't (or vice versa); scope creep.
- Codex must end its reply with exactly one line: `VERDICT: APPROVED` or `VERDICT: REVISE`.

### Round 1 (new session)
Write the review prompt to a temp file (include the focus list above and instruct Codex to read `PRD.md`), then:

```bash
codex exec -s read-only --json -c model="gpt-5.5" "$(cat /tmp/to-prd-codex-prompt.txt)" \
  2>/dev/null | tee /tmp/to-prd-codex-out.jsonl >/dev/null
```

Extract `thread_id` from the `thread.started` JSONL event and the assistant's final message. If there is no `thread_id` or no verdict, treat it as an auth/model failure — stop and report.

### Rounds 2–MAX (resume the same session)
After arbitration and revising `PRD.md`, build the resume prompt so Codex sees what was decided and why — otherwise it re-raises rejected findings forever and never converges. Include, for each prior finding: ACTED (what changed in `PRD.md`) or REJECTED (the user's rationale from arbitration). Then:

```bash
codex exec resume "$THREAD_ID" -c sandbox_mode="read-only" -c model="gpt-5.5" --json \
  "$(cat /tmp/to-prd-codex-resume.txt)" \
  2>/dev/null > /tmp/to-prd-codex-out.jsonl
```

Where `/tmp/to-prd-codex-resume.txt` contains, e.g.:

```
I revised PRD.md. Here is how each of your prior findings was handled:
- <finding 1>: ACTED — <what changed>
- <finding 2>: REJECTED — <user's rationale>
Re-review PRD.md. For REJECTED findings, either accept the rationale or argue back with a stronger case; do not silently re-raise them. Also flag anything new. End with VERDICT: APPROVED or VERDICT: REVISE.
```

**Critical:** `resume` rejects `-s`; enforce read-only with `-c sandbox_mode="read-only"`.

### Each round
1. Append `## Round <n> — Codex` with the findings to `PRD-REVIEW-LOG.md`.
2. Read the final `VERDICT:` line.
3. **APPROVED** → converged; go to Act 3.
4. **REVISE** → **arbitration is the user's call, not Claude's.** For each finding, use the `AskUserQuestion` tool to ask the user whether to ACT on it or REJECT it. Give Claude's recommendation as the first option and a one-line take on whether the point is valid, but the user decides. Group related findings into a single question where it reads naturally; don't fire one question per trivial point. Then revise `PRD.md` per the ACTED decisions, record each decision (ACTED + what changed, or REJECTED + the user's rationale) in `PRD-REVIEW-LOG.md`, increment the round, and resume (feeding those decisions into the resume prompt per above).
5. Round > MAX_ROUNDS → deadlock: list unresolved points with Claude's counter-position and defer to the user.

---

## ACT 3 — Sign-off and publish

- On convergence (APPROVED): present the final PRD + a three-bullet summary of what the review changed + the round count. Request explicit user sign-off.
- On deadlock (MAX_ROUNDS): present unresolved points with your counter-position; the user decides.
- **Only after the user signs off**, publish `PRD.md` to the project issue tracker and apply the `ready-for-agent` triage label — no additional triage. Then it's ready for `/to-issues`.

---

## Hard constraints
- Act 1 always precedes Act 2; nothing is published before Act 3 sign-off.
- Codex runs read-only every round.
- The loop terminates at MAX_ROUNDS.
- The user arbitrates each REVISE finding via `AskUserQuestion` (ACT/REJECT); Claude recommends but does not decide. Decisions and rationale are logged and fed into the next resume prompt.
- Use the project's domain glossary and respect ADRs throughout.

## Trigger phrases
- "/to-prd-codex"
- "write a PRD then have codex review it before publishing"
- "PRD with a second-model adversarial pass"
