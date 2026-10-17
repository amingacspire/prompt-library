# Automation Time-Saved Calculator

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Turns run counts and per-task time estimates into a time-saved table for each automated workflow. It states every assumption next to the figure it affects, so the total can be checked. Use it for a monthly review of automation value, or when someone asks what an automation saves.

It is a calculator, not a measurement. The figures are only as good as the manual-time estimates you supply.

## System Prompt

```
You are an operations analyst. Calculate time saved from the run counts and time estimates provided, and show the arithmetic for each line. State every assumption. Do not round figures in a way that increases the saving. If an estimate is missing, leave that line out of the total and say so.
```

## User Prompt

```
Calculate the time saved by the automations below for {{period}}. For each workflow, use the run count, the estimated manual minutes per run, and the automated minutes per run.

Workflow data:
{{workflow_data}}

Produce:

1. A table with columns: workflow, runs, manual minutes per run, automated minutes per run, minutes saved, and hours saved.
2. The total hours saved, with the lines that were left out and why.
3. A list of assumptions, one per line, such as the rate used for manual work and whether failed runs were counted.
4. A note on any workflow where the automated minutes were close to or above the manual minutes.

Rules:
- Count failed runs only if the data says the failed run still saved the manual step.
- Do not convert hours to a currency value unless a rate was given.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{period}}` | Period the figures cover | `September 2026` |
| `{{workflow_data}}` | Run counts and minute estimates for each workflow | CSV with workflow, runs, manual minutes, automated minutes |

## Example Output

```
| Workflow | Runs | Manual min/run | Automated min/run | Min saved | Hours saved |
|---|---|---|---|---|---|
| Onboarding checklist | 38 | 25 | 4 | 798 | 13.3 |
...
Total: 41.2 hours saved. Excluded: "Password reset notice" (no manual estimate given).
```

## Notes

- Manual-time estimates are usually optimistic. Time a few manual runs if the figure will be used for a business case.
- Automated minutes should include the time a person spends checking the output, not only the run time.
- A saved hour is not a freed hour. Say so if the figure is used to justify headcount.
- This prompt has not been tested against real run data yet.
