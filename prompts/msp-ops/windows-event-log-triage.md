# Windows Event Log Triage

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Condenses an exported Windows System or Application event log into recurring errors and warnings, groups repeats, and suggests what to verify first. Use it when a server or workstation is misbehaving and the log is too long to read line by line.

It is a triage aid. It does not diagnose root cause. Event messages are often generic, so confirm each suspected cause before acting.

## System Prompt

```
You are a Windows systems triage assistant. Work only from the events provided. Group repeated events by provider and event ID. Do not invent causes that the event text does not support. Label each cause as a hypothesis to verify. Your output is a triage note for a technician.
```

## User Prompt

```
Below is an export of Windows event log entries from {{host_description}} covering {{time_window}}. Produce:

1. Summary: total entries reviewed, count of errors and critical entries, and count of warnings.
2. Recurring events: group by provider and event ID, with count, first and last occurrence, and one sample message. Sort by count.
3. For the top five groups, the most likely cause as a hypothesis, and the first check to run to confirm it.
4. Events that look unrelated to the reported problem, so they can be set aside.
5. A short list of read-only PowerShell commands to confirm the top hypotheses.

Rules:
- Do not suggest clearing logs, changing services, or rebooting without the technician's decision.
- If an event message contains what looks like a credential, token, or hostname outside the scope, mask it in your output.

Event export:
{{event_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{host_description}}` | The machine or role the log came from | `file server, Windows Server 2022` |
| `{{time_window}}` | Period the export covers | `last 48 hours before the 09:00 report` |
| `{{event_export}}` | CSV or text of event entries | Output of the export command below |

Suggested export:

```powershell
Get-WinEvent -FilterHashtable @{ LogName = 'System'; Level = 1, 2, 3; StartTime = (Get-Date).AddDays(-2) } |
    Select-Object TimeCreated, Id, ProviderName, LevelDisplayName, Message |
    Export-Csv -Path .\system-events.csv -NoTypeInformation
```

Repeat with `LogName = 'Application'` if the problem is application-level.

## Example Output

```
Summary: 412 entries | 38 errors | 0 critical | 74 warnings

Recurring: Disk, ID 153, 29 occurrences, 2026-10-10 02:14 to 2026-10-12 23:58
  Hypothesis: intermittent I/O retries on a disk. Check disk health and the controller firmware.
...
```

## Notes

- Many events repeat on a schedule and are noise. Check whether a group lines up with the reported symptom before escalating it.
- Message text is localized and can vary by build. Match on provider and event ID where you can.
- An export with no entries in the time window often means the window is wrong, not that the machine is healthy.
- This prompt has not been tested against real exports yet.
