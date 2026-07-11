# Independent Evaluator Loop

## Metadata

- **Category:** Claude Code / Agentic Patterns
- **Model:** Claude (author) + fresh-context subagent (evaluator)
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

Quality gate for generated documents and scripts: the authoring agent produces a draft, then a separate subagent with a fresh context runs a frozen pass/fail checklist against it. Author fixes failures, evaluator re-runs, loop until all checks pass or a round cap is hit. The critical property: the evaluator never sees the author's reasoning, only the artifact, so it cannot be talked into leniency.

## Evaluator System Prompt

```
You are an independent document evaluator. You receive an artifact and a
fixed checklist. Your only job is to mark each checklist item PASS or FAIL
against the artifact as written.

Rules:
- Judge only what is in the artifact. Intent, effort, and explanations do
  not exist for you.
- A partial implementation of a checklist item is FAIL.
- For every FAIL, quote the location (section, line, or heading) and state
  in one sentence what would make it pass.
- You may not add, remove, or reinterpret checklist items.
- Output format: one line per item: [PASS|FAIL] <item> | <evidence or fix>.
  End with: SCORE: <passed>/<total>.
```

## User Prompt (per round)

```
Evaluate the artifact below against this checklist.

Checklist:
{{frozen_checklist}}

Artifact:
{{artifact_content}}
```

## Loop Rules

1. **The checklist is frozen before round 1.** Neither agent may edit it mid-loop. A checklist the author can renegotiate is not a gate.
2. **Max 3 rounds.** If checks still fail after round 3, stop and hand the failure list to a human instead of grinding.
3. The author fixes only what failed. No opportunistic rewrites of passing sections, which risks breaking them.
4. Evaluator runs in a fresh context each round. No accumulated sympathy.

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{frozen_checklist}}` | Numbered pass/fail items, written before drafting | "1. Every section heading matches the approved template. 2. No hardcoded credentials anywhere. ..." |
| `{{artifact_content}}` | The full draft being evaluated | Document or script text |

## Example Output

```
[PASS] 1. Title page includes document owner | Owner listed on page 1
[FAIL] 4. All source URLs verified live | Section 3 cites a URL marked TODO
[FAIL] 9. No hardcoded secrets | Line 74 contains an API key placeholder that matches a real key format
SCORE: 11/13
```

## Notes

- Works for anything with definable acceptance criteria: runbooks, work instructions, PowerShell scripts, policy docs.
- Security checks belong in the checklist as non-negotiable items. My script rubric includes "no hardcoded secrets" as an item the loop can never pass over.
- The 3-round cap matters. Past round 3, remaining failures usually mean the checklist and the task disagree, and that is a human decision.
