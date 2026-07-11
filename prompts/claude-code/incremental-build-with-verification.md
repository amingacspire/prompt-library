# Incremental Build with Per-Step Verification

## Metadata

- **Category:** Claude Code / Agentic Patterns
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

A build technique for long agentic tasks in fragile targets (low-code platforms, workflow builders, remote systems): break the build into small ordered increments, pair every increment with an expected observable result, verify before moving on, and cap retries per step. Came out of building automation workflows where a single failed step silently corrupted everything after it. The agent claimed success; the platform disagreed.

## Prompt Structure (per increment)

```
STEP {{n}} of {{total}}: {{one_small_change}}

Do exactly this change and nothing else.

Expected result after this step:
{{observable_check}}

Verify the expected result by {{verification_method}} before reporting.
Report format:
- STEP {{n}}: DONE | verified: <what you observed>
- STEP {{n}}: FAILED | expected <X>, observed <Y>

If FAILED: retry a maximum of {{max_retries}} times with a corrected
approach. If still failing, STOP the entire build and report. Do not
proceed to step {{n+1}} on top of a failed step.
```

## Design Rules

1. **One change per step.** If a step description contains "and", split it.
2. **Every step has an observable check.** "It should work" is not a check. "The API returns the new object with a non-null id" is.
3. **Verification reads from the system, not from the agent's own action log.** Re-query the platform, list the resource, diff the config. The agent believing it succeeded is the exact failure this pattern kills.
4. **Retry cap of 3, then hard stop.** Unlimited retries on a broken assumption just multiplies the damage.
5. **No forward progress over a failure.** Later steps assume earlier state. Building on a failed step produces wreckage that costs more to unwind than the stop costs.

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{one_small_change}}` | The single change for this step | Add a webhook trigger to the workflow |
| `{{observable_check}}` | What proves it landed | GET on the workflow shows trigger count = 1 |
| `{{verification_method}}` | How to observe it | API query, UI state read, config diff |
| `{{max_retries}}` | Retry cap before hard stop | 3 |

## Example Output

```
STEP 4: DONE | verified: workflow now shows 3 tasks, new task id 7f2a
STEP 5: FAILED | expected transition connecting task 3 to task 4,
observed no transition created (retry 1 of 3)
STEP 5: FAILED after 3 retries. STOPPING BUILD.
State: steps 1-4 verified good. Step 5 blocked: platform rejects
transition label over 255 chars. Needs human decision on label format.
```

## Notes

- Slower than letting the agent free-run. That is the point. Free-running through 20 steps and failing silently at step 6 costs a full rebuild plus diagnosis.
- Works best when the step list is authored before the build starts, from a design spec, so the agent executes a plan instead of improvising one.
- The hard-stop report must include verified-good state so the resume starts from step N, not from zero.
