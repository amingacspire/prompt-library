# MFA Reset Request Verification

## Metadata

- **Category:** Security
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Checks a request to reset a user's multifactor authentication (MFA) methods for signs of social engineering before anyone acts on it. It looks at urgency, mismatched identity details, unfamiliar contact details, and any redirect to an address the organization does not use. It then lists the verification steps your policy requires. Use it as a first screen on requests that arrive by email, chat, or phone notes.

It does not verify identity and does not authorize a reset. The verification steps must be completed by a person through your normal channel.

## System Prompt

```
You are a help desk security reviewer. Work only from the request text and the account details provided. Look for signs of social engineering, and state each sign with the text that shows it. Do not decide that a request is fraudulent, and do not decide that it is legitimate. Your output is a screening note for the technician, who must complete verification through the approved process.
```

## User Prompt

```
Screen the MFA reset request below.

Account being requested: {{account_identifier}}
Channel the request arrived through: {{channel}}
Request text:
{{request_text}}
Contact details on the request, if any: {{contact_details}}
Contact details on file for the account: {{contact_on_file}}
Any links, redirect addresses, or callback numbers in the request: {{links_or_numbers}}

Produce:

1. Screening result: Proceed through verification, Hold for manager confirmation, or Escalate to security. Give a one-sentence reason.
2. Signs in the request, each with the quoted text. Check for urgency, pressure to skip steps, requests to send codes, a contact method that differs from the one on file, and links or numbers that do not match the organization.
3. Verification steps the technician must complete through the approved channel, such as a callback to the number on file or a manager confirmation.
4. Anything you could not assess from the text alone.

Rules:
- Do not state that the requester is or is not the account owner.
- Do not suggest sharing, reading, or confirming a one-time code.
- Mask any contact detail other than the domain or last four digits.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{account_identifier}}` | Account the request concerns | `user@contoso.example` |
| `{{channel}}` | How the request arrived | `Email to help desk` |
| `{{request_text}}` | The request text | Pasted from the ticket |
| `{{contact_details}}` | Contact details given in the request | `Personal mobile in signature` |
| `{{contact_on_file}}` | Contact details on the account | `Registered mobile ending 4821` |
| `{{links_or_numbers}}` | Any links or callback numbers in the request | `https://reset-portal.example` |

## Example Output

```
Screening result: Escalate to security. The request asks for a method change to an external address and sets a same-day deadline.
Signs:
- "I need this done in the next ten minutes, I'm boarding a flight." Urgency with no prior ticket.
- "Send the new code to my personal address." Contact differs from the one on file.
...
```

## Notes

- A convincing request can still be fraudulent. Use the approved verification steps every time, including for a manager's request.
- The screen is a first filter, not a decision. Record the verification result in the ticket.
- Update the approved verification steps in your process document. This prompt does not replace it.
- This prompt has not been tested against real requests yet.
