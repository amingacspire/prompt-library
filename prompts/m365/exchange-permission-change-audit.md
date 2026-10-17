# Exchange Permission Change Audit

## Metadata

- **Category:** M365
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews an audit log export of Exchange Online permission changes, such as Full Access, Send As, Send on Behalf, and role group membership changes. It lists who made each change, to which mailbox, and flags changes that look unexpected. Use it after an automated permissions workflow runs, or during a periodic access review.

It does not reverse any change. It reports what the log shows and what needs a person's review.

## System Prompt

```
You are a Microsoft 365 access reviewer. Work only from the audit records provided. Do not infer intent that the records do not show. Flag changes that do not match a documented request, and label each flag as needing review. Your output is an audit summary for an administrator.
```

## User Prompt

```
Below is an export of Exchange Online permission change records for {{review_period}}. Produce:

1. Summary: total changes, count by type (Full Access, Send As, Send on Behalf, role group membership), and count by initiator.
2. Changes made by a human account rather than an automated workflow, listed with the actor, target mailbox, and time.
3. Changes to shared mailboxes or to mailboxes of executives or privileged accounts, flagged for review.
4. Changes outside the approved change window, if {{change_window}} was supplied. Otherwise state that no window was given.
5. Changes you could not classify, with the reason.

Rules:
- Do not state that a change was unauthorized. State that it needs review against the request record.
- Mask any user principal name except the domain.

Audit export:
{{audit_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{review_period}}` | Period the export covers | `2026-10-01 to 2026-10-15` |
| `{{change_window}}` | Approved change window, optional | `Mon-Fri 08:00-18:00` |
| `{{audit_export}}` | CSV of permission change records | Output of the export below |

Suggested export from Exchange Online PowerShell, using Search-UnifiedAuditLog with the operations filter for the permission changes you need. Review the output columns in your tenant before relying on them.

## Example Output

```
Summary: 61 changes | 44 Full Access | 12 Send As | 5 role group

Flagged for review: Full Access granted to shared mailbox "Finance Team" by a human account on 2026-10-09 at 22:14.
...
```

## Notes

- Audit logs can have a delay before records appear. A short export may be missing recent changes.
- Automated workflows can make changes that look human. Compare against the workflow's run log before flagging a person.
- Keep the export and the summary together as the audit record.
- This prompt has not been tested against real logs yet.
