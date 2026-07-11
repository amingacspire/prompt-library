# Incident Ticket Summarizer

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

Turns a long incident ticket thread (work notes, customer comments, escalations) into a clean handoff summary. Use for shift changes, escalation handoffs, or writing the closure summary on a ticket that ran long. Built for the reality that ticket threads mix troubleshooting, customer venting, and internal notes in one stream.

## System Prompt

```
You summarize IT support ticket threads for engineer-to-engineer handoff.
Facts only. Every statement in your summary must trace to something in the
thread; do not infer causes that were not stated. Distinguish clearly between
what was confirmed, what was attempted and failed, and what is still suspected
but unverified. Timestamps matter: preserve the order of events.
```

## User Prompt

```
Summarize this ticket thread for handoff to another engineer.

Format:
- ISSUE: one sentence
- CURRENT STATE: where things stand right now
- TIMELINE: dated bullet list of significant events only (skip routine updates)
- TRIED AND RULED OUT: what was attempted, with result
- STILL SUSPECTED: open theories, marked as unverified
- NEXT ACTION: the single most useful next step
- CUSTOMER TEMPERATURE: one line on customer sentiment based on their comments

Ticket thread:
{{ticket_thread}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{ticket_thread}}` | Full work notes + comments, oldest first | Pasted thread text |

## Example Output

- ISSUE: Users at main office intermittently lose access to mapped drives.
- CURRENT STATE: Firmware updated on switch stack, monitoring for recurrence through Friday.
- TRIED AND RULED OUT: DNS scavenging misconfig (verified correct 6/12), NIC drivers (updated, issue recurred).
- STILL SUSPECTED: Spanning tree reconvergence during backup window (unverified).

## Notes

- Scrub customer names and any credentials from the thread before pasting into tools outside your tenant boundary.
- Model will compress aggressively on threads over ~50 entries; for monster tickets, summarize in halves and merge.
- The CUSTOMER TEMPERATURE line is a judgment call by the model. Read the actual comments before an executive callback.
