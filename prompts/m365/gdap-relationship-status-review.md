# GDAP Relationship Status Review

## Metadata

- **Category:** M365
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews an export of granular delegated admin privilege (GDAP) relationships and flags those that are expiring, already expired, broader than the work needs, or not assigned to any group. Use it on a regular schedule, or before a renewal cycle, to find relationships that need action.

It does not renew, end, or change any relationship. It reports what the export shows.

## System Prompt

```
You are a Microsoft 365 partner administration reviewer. Work only from the relationship data provided. Do not invent tenant names, role assignments, or end dates. Flag what the data shows, and state what you cannot confirm from the export. Your output is a review list for an administrator to act on.
```

## User Prompt

```
Below is an export of GDAP relationships, taken on {{review_date}}. Produce:

1. Summary: total relationships, active, expiring within 30 days, expiring within 90 days, and expired.
2. Expired or expiring relationships, grouped by urgency band (expired, within 30 days, 31 to 90 days), with the customer tenant and the end date.
3. Relationships that grant a role broader than the work they support, such as a global administrator role where a narrower role is listed in the same group. State the reason for the flag.
4. Relationships with no access assignments, or with assignments to groups that the export does not show.
5. Data gaps: anything missing that would be needed to make a final decision, such as the service needed for each tenant.

Rules:
- Do not recommend ending or renewing a relationship. Recommend the review step.
- Mask customer tenant identifiers in your output, showing only the last four characters.

Relationship export:
{{gdap_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{review_date}}` | Date the export was taken | `2026-10-17` |
| `{{gdap_export}}` | CSV of relationships and their roles and assignments | Export from the partner center relationships view |

Confirm the column names in the current partner center export before pasting. They change between portal releases.

## Example Output

```
Summary: 84 relationships | 79 active | 3 expiring within 30 days | 2 expired

Expired: tenant ending 4f2a (end date 2026-10-02). Review whether the customer still needs the service.
...
```

## Notes

- A relationship that expired is not active, even if a group still lists it. Check the status column, not only the end date.
- Global administrator access is the broadest role. Flag it for review, not automatic removal.
- Export timing matters. A relationship renewed after the export will still show as expiring.
- This prompt has not been tested against real exports yet.
