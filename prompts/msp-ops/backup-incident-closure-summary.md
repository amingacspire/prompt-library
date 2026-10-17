# Backup Incident Closure Summary

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Consolidates several related backup-job incidents into one closure note. It groups the affected jobs, states the root cause and resolution, records verification, and lists follow-up items. Use it when one underlying problem, such as disabled jobs after a platform change, produced many tickets.

It writes from the notes you provide. Confirm the root cause and the verification result before you close the tickets.

## System Prompt

```
You are a technical writer for managed service operations. Write closure notes from the incident notes provided. Do not add a root cause, a verification result, or a follow-up item that the notes do not state. Mark any sentence you infer as Inferred. Keep the language plain and factual.
```

## User Prompt

```
Write one consolidated closure note for the backup incidents below. Use this structure:

1. Summary: two or three sentences describing the problem and its impact.
2. Affected incidents: a table with incident number, affected job or system, and date opened.
3. Root cause, as stated in the notes.
4. Resolution: what was changed, by whom, and when.
5. Verification: how the fix was confirmed, including the job status after the change.
6. Follow-up: open items, owners, and due dates if the notes give them.

Rules:
- Do not state a root cause the notes do not support. Write "Not determined" if the notes do not say.
- Replace any client name with {{client_placeholder}}.

Incident notes:
{{incident_notes}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{client_placeholder}}` | Placeholder used in place of client names | `[Client]` |
| `{{incident_notes}}` | Notes from each incident, pasted together | Ticket notes and job status history |

## Example Output

```
Summary: Scheduled backup jobs stopped running for 11 servers after a platform update on 2026-10-06. Jobs were re-enabled and verified on 2026-10-08.

Affected incidents: 11 (see table)
Root cause: Not determined. Notes state the jobs were disabled during the update, not why they stayed disabled.
...
```

## Notes

- Closing many tickets with one note is only accurate if every affected job is in the table. Check the table against the job list before closing.
- A fix that was not verified by a completed job run is not verified. Record the run result.
- Keep client names out of the consolidated note. Use the placeholder.
- This prompt has not been tested against real incident notes yet.
