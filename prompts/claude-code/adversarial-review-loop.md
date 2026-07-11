# Adversarial Review Loop

## Metadata

- **Category:** Claude Code / Agentic Patterns
- **Model:** Any two models (author + independent reviewer)
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

A two-model review loop: one model authors the work, a second independent model critiques it, the author responds to every finding, and the cycle repeats until the reviewer approves or a round limit is hit. Catches issues the authoring model is blind to because it reviews its own reasoning. I run this between Claude Code and a second CLI model, but any pairing works.

## Reviewer System Prompt

```
You are a skeptical senior engineer doing a READ-ONLY review. Do NOT run any
commands. Do NOT create, edit, or delete any files. List every issue as:
[SEVERITY: Critical|High|Medium|Low] <finding>. Output ONLY your findings as
plain text. If no blockers remain, end your response with the word APPROVED.
```

## Context Document Template (sent to the reviewer each round)

```markdown
# Review Round [N]

## Task being worked on
[1-3 sentences: what is being implemented or fixed]

## Current approach
[Key design decisions, tools used, patterns applied]

## Files involved
[Key files and a brief description of changes]

## Key code snippets
[The most important changed code]

## Previous findings addressed (Round 2+)
[Each prior finding and the author's response]

## Focus area
[Optional: security, correctness, performance. Otherwise: general]
```

## Loop Rules

1. **Max rounds:** default 5. Stop early only on approval.
2. **Approval detection:** the last non-empty line of reviewer output must be exactly `APPROVED`. Checking the whole output for the word gives false positives when the reviewer quotes a prior round.
3. **No silent skips.** The author must respond to every finding with exactly one of:
   - `Fixed: [what changed]`
   - `Disputed: [reason]` (carries to next round for re-evaluation)
   - `Deferred: [reason]` (acknowledged, out of scope for this cycle)
4. **Dispute escalation:** if author and reviewer disagree on the same finding for 2+ rounds, stop and surface the dispute to a human.
5. **Security findings never die silently.** Critical security findings escalate to a human even if the author believes they are wrong.
6. **Always write a log**, even when interrupted: rounds completed, findings table with resolution status, final verdict.

## Example Output (round summary)

```
## Reviewer Round 2 findings
Critical: 0
High: 1
Medium/Low: 2

[H] Retry loop has no backoff; hammering the API on 429s.
Author response - Fixed: added exponential backoff, max 3 retries.
```

## Notes

- The reviewer must be a separate model or at minimum a fresh context. A model reviewing output that sits in its own context window inherits the same blind spots that produced the bugs.
- READ-ONLY is load-bearing. A reviewer that can edit files will start fixing instead of finding, and you lose the audit trail.
- Round limits prevent the failure mode where two models politely disagree forever.
