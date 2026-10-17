# Alert Noise Gating Review

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews alert rules, suppression rules, and alert volume data together. It finds rules that fire often with no action taken, rules that never fire, and gaps where a silent or stopped worker would go unnoticed. Use it during a monitoring review, or after a complaint about alert volume or a missed alert.

It does not change any rule. It produces a list for the team to review.

## System Prompt

```
You are a monitoring operations reviewer. Work only from the rule definitions and alert volume data provided. For each recommendation, state the evidence in the data that supports it. Do not recommend deleting a rule without the owner's review. Your output is a review list for the monitoring team.
```

## User Prompt

```
Review the alert rules and volume data below for {{monitoring_scope}}, covering {{period}}. Produce:

1. Summary: total rules, rules that fired in the period, rules that never fired, and total alerts.
2. The rules that fired most often, with the count and whether the data shows an action taken (acknowledged, ticketed, or resolved).
3. Rules that fired often with no action recorded. Label each as a candidate for tuning, not for removal.
4. Rules that never fired. Label them as possibly unused or possibly missing a condition, and state which.
5. Gaps: any worker, device, or service with no heartbeat or silence alert configured, based on the rule list.
6. Questions for the rule owners, one line each.

Rules:
- Do not recommend removing a rule that covers a safety or security condition without the owner's review.
- Do not claim an alert was not acted on if the data does not record the action. Say the record is missing.

Rule definitions:
{{rule_definitions}}

Alert volume data:
{{alert_volume}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{monitoring_scope}}` | Customer environment or platform in scope | `Managed network devices, all sites` |
| `{{period}}` | Period the volume data covers | `2026-09-01 to 2026-09-30` |
| `{{rule_definitions}}` | Export or list of alert rules and suppression rules | Pasted from the monitoring console |
| `{{alert_volume}}` | Alert counts by rule and by action taken | CSV export |

## Example Output

```
Summary: 96 rules | 61 fired | 35 never fired | 4,820 alerts

Candidate for tuning: "Port flap, access switches" fired 1,140 times, none acknowledged. Question for owner: does the team need this alert at this threshold?
...
```

## Notes

- A rule that never fires may be correct, or its condition may never match the current data. Check the condition before deciding.
- Volume is not the same as value. A rule that fires rarely can still be the one that matters.
- Heartbeat and silence alerts are the usual gap. A stopped worker sends no alert of its own.
- This prompt has not been tested against real rule exports yet.
