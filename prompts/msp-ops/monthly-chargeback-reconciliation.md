# Monthly Chargeback Reconciliation

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Compares this month's chargeback spreadsheet with last month's and flags variances, missing lines, duplicate lines, and totals that do not match. It lists the questions for the accounting team. Use it before chargeback invoices are issued.

It is an accounting check. It does not post, change, or approve any invoice.

## System Prompt

```
You are an accounting reconciliation assistant. Work only from the spreadsheet data provided. Show the arithmetic for every total you report. Do not round in a way that hides a variance. Flag every difference above the threshold, and every line you cannot match, for an accountant to review.
```

## User Prompt

```
Reconcile the chargeback data for {{billing_month}} against {{prior_month}}. Variance threshold: {{variance_threshold}}.

Current month data:
{{current_month_csv}}

Prior month data:
{{prior_month_csv}}

Produce:

1. Totals for each month, with the arithmetic shown, and the difference.
2. Lines whose amount changed by more than the threshold, listed with both amounts and the change.
3. Lines present in one month and not the other.
4. Possible duplicate lines in the current month, matched on the key fields you can see.
5. Questions for the accounting team, one line each.

Rules:
- Do not decide whether a variance is correct. State the difference and the question.
- Report every line you could not match. Do not drop them from the totals silently.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{billing_month}}` | Current billing month | `September 2026` |
| `{{prior_month}}` | Prior billing month | `August 2026` |
| `{{variance_threshold}}` | Amount or percentage that triggers a flag | `5 percent or 250.00` |
| `{{current_month_csv}}` | Current month chargeback lines | CSV with client, line item, quantity, rate, amount |
| `{{prior_month_csv}}` | Prior month chargeback lines | Same columns |

## Example Output

```
Totals: August 48,210.55 | September 49,630.10 | change +1,419.55

Above threshold: line "Managed endpoints" changed from 12,400.00 to 13,950.00 (+12.5 percent). Question: were 15 devices added in September?
...
```

## Notes

- Confirm that the spreadsheet columns match the pasted data before reading the totals. A shifted column changes every figure.
- A duplicate line may be a real second charge. Treat the flag as a question, not a finding.
- Keep the reconciliation with the invoice batch it covers.
- This prompt has not been tested against real spreadsheets yet.
