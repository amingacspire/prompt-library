# Azure Cost Anomaly Explainer

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Takes a daily cost export from Azure Cost Management and identifies the largest changes over a comparison window, with a likely cause for each and the questions to ask the resource owner. Use it when a monthly bill is higher than expected and you need a starting point for the conversation.

It does not confirm causes. The export shows what changed, not why.

## System Prompt

```
You are an Azure cost analyst. Work only from the cost data provided. Do not invent resource names, costs, or causes that the data does not support. Label every cause as a hypothesis. Your output is a draft for a human to review before it is shared.
```

## User Prompt

```
Below is a daily cost export for {{scope_name}}, covering {{period}} and compared against {{baseline_period}}. Produce:

1. Totals: spend in the period, spend in the baseline, and the change in amount and percent.
2. Top movers: the ten resources or services with the largest absolute change, each with its baseline cost, period cost, and change.
3. For each top mover, one or two plausible causes (for example, a new resource, a SKU change, a scale-out, a data transfer spike, or a reservation expiring), labeled as hypotheses.
4. Questions to ask the resource owner for each top mover. Keep them neutral and specific.
5. Data gaps: missing days, unlabeled resources, or costs with no tags that would limit the analysis.

Rules:
- Do not recommend shutting down or resizing a resource based on cost alone.
- If the totals in the export do not match the period you were given, say so first.

Cost export:
{{cost_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{scope_name}}` | Subscription, billing account, or resource group scope | `Subscription: Production` |
| `{{period}}` | Period being examined | `2026-09-01 to 2026-09-30` |
| `{{baseline_period}}` | Comparison period | `2026-08-01 to 2026-08-31` |
| `{{cost_export}}` | CSV of daily cost by resource or service | Export from Cost Management |

Export from the Azure portal under Cost Management, with daily granularity grouped by resource or service, and include the ResourceName, ServiceName, Date, and Cost columns.

## Example Output

```
Totals: period 41,200 | baseline 33,850 | change +7,350 (+21.7%)

Top mover: Virtual machines, East US (+3,900)
  Hypothesis: a scale-out or a SKU change. Daily cost steps up on 2026-09-11.
  Question for owner: Was capacity added on that date, and is it still needed?
...
```

## Notes

- Use the same currency and tax basis in both periods. A tax or currency change can look like a cost spike.
- Reservations and savings plans spread costs differently in amortized and actual views. Confirm which view the export uses.
- The export is a starting point. Confirm causes with the resource owner and the change log before acting.
- This prompt has not been tested against real exports yet.
