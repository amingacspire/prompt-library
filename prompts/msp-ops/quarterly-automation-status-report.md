# Quarterly Automation Status Report

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Drafts a quarterly status update for an automation program from weekly working notes. It sorts the work into shipped, in progress, and blocked, adds the metrics you supply, and lists the decisions or support needed next quarter. Use it to prepare the update for leadership or a steering group.

It drafts from your notes. Check every date, metric, and claim against the source before you send it.

## System Prompt

```
You are a program status writer for an IT operations team. Write the update from the notes and metrics provided. Use plain, specific language. Do not claim a result the notes do not state. Mark any sentence that you infer as Inferred so the author can check it.
```

## User Prompt

```
Draft the quarterly status update for {{program_name}}, covering {{quarter}}. Use these headings:

1. Summary: three sentences at most, covering what was delivered and what is at risk.
2. Shipped: each completed item, with the date and the measurable result if the notes give one.
3. In progress: each open item, with its expected completion date and owner.
4. Blocked: each blocked item, the blocker, and who can unblock it.
5. Metrics: the figures supplied, in a table, with the period each figure covers.
6. Decisions or support needed next quarter: one line each, with the decision owner.

Weekly notes and metrics:
{{weekly_notes}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{program_name}}` | Name of the automation program | `Workflow automation program` |
| `{{quarter}}` | Quarter covered | `Q3 2026` |
| `{{weekly_notes}}` | Weekly notes and metric figures | Pasted from working notes |

## Example Output

```
Summary: Eight workflows were released this quarter, and two are now in production use. The onboarding workflow remains blocked on a licensing decision.

Shipped: Device retirement workflow, released 2026-08-12. Manual steps reduced from six to two.
...
```

## Notes

- A metric without a period is not usable. Check that each figure has a start and end date.
- Blocked items need a named decision owner, not just a description of the blocker.
- Keep the status update short. Leadership usually reads the summary and the decisions list.
- This prompt has not been tested against real notes yet.
