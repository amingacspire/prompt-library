# PowerShell Runbook Generator

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Turns a list of steps performed, and the commands used, into a repeatable runbook with prerequisites, verification steps, and a rollback path. Use it after a one-off change that should be repeatable, or to document a procedure before handing it to another technician.

It writes from what you give it. It does not check that the commands worked on your environment. Run every step in a test environment before you rely on the runbook.

## System Prompt

```
You are a technical writer for IT operations teams. Write runbooks from the steps provided. Do not add steps, commands, or parameters that were not given, except for clearly marked prerequisite checks and verification steps that you propose and label as proposed. Keep the language direct and the steps numbered.
```

## User Prompt

```
Write a runbook for the procedure below. The audience is a technician who has not done this before.

Use this structure:

1. Purpose and scope, in two or three sentences.
2. Prerequisites: access, module versions, and checks to run before starting. Mark any check you propose as "Proposed".
3. Procedure: numbered steps. Include the exact commands in code blocks, and state the expected result after each step.
4. Verification: how to confirm the change worked. Mark proposed checks as "Proposed".
5. Rollback: how to reverse each step, or a clear statement that rollback is not possible.
6. Known issues or cautions, drawn from the notes provided.

Replace any customer name, tenant ID, subscription ID, or hostname with a placeholder such as {{tenant_id}}.

Procedure notes:
{{procedure_notes}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{procedure_notes}}` | Steps performed, commands used, and any problems encountered | Pasted from the technician's notes or session transcript |

## Example Output

```
1. Purpose and scope
   Rotate the service account password for the backup job on file servers in scope.

2. Prerequisites
   - Access to the domain controller with account operator rights
   - Proposed: confirm the backup job is idle before starting
...
```

## Notes

- Commands that were run once in a live session may include values that should not be copied into a shared runbook. Review the placeholders before publishing.
- The "Proposed" label is there so a technician knows which steps came from the model and which came from the original work.
- Check any rollback step against the actual state of the system. A rollback that was never tested is a guess.
- This prompt has not been tested yet. Compare the output against a known-good runbook before using it.
