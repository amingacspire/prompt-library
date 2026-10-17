# EDR Exclusion Request Review

## Metadata

- **Category:** Security
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews a request to add an exclusion to an endpoint detection and response (EDR) product, such as a path, process, or file hash. It rates the scope and risk of the exclusion, asks whether a narrower option would work, and lists the questions the approver must answer. Use it before anyone adds an exclusion because an application is blocked or flagged.

It does not approve or create exclusions. The approver decides.

## System Prompt

```
You are a security operations analyst reviewing requests to exclude files or processes from endpoint detection. Work only from the request provided. Your default position is that the narrowest exclusion that solves the problem is best, and that a time limit and an owner are required. Do not recommend approval. Your output is a review note for the approver.
```

## User Prompt

```
Review the EDR exclusion request below.

Requester: {{requester_role}}
Application or business need: {{business_need}}
Requested exclusion type: {{exclusion_type}}
Requested exclusion value (path, process name, or hash): {{exclusion_value}}
Scope requested (devices or groups): {{scope}}
Duration requested: {{duration}}
Vendor or source of the software: {{software_source}}
Alert or block that triggered the request: {{alert_text}}

Produce:

1. Risk rating (Low, Medium, or High) with the reasons.
2. Whether the scope is as narrow as it could be. Suggest a narrower alternative if one exists, such as a signed-publisher rule, a specific file hash, or a single device group.
3. Whether the duration is justified. Suggest a time limit if none was given.
4. Questions the approver must answer before deciding, one line each.
5. Conditions to attach if the exclusion is approved, such as an owner, a review date, and logging.

Rules:
- Do not recommend approval or denial. Give a rating and the questions.
- If the requested exclusion covers a user profile, a system directory, or a broad folder, say so first.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{requester_role}}` | Role of the requester, not their name | `application owner, finance` |
| `{{business_need}}` | Why the exclusion is needed | `Line-of-business app blocked after update` |
| `{{exclusion_type}}` | Path, process, file hash, or extension | `Path` |
| `{{exclusion_value}}` | The value being excluded | `C:\Program Files\ExampleApp\` |
| `{{scope}}` | Devices or groups the exclusion would apply to | `Finance workstations group` |
| `{{duration}}` | Requested duration | `Permanent` |
| `{{software_source}}` | Vendor and how it was obtained | `Vendor installer, signed` |
| `{{alert_text}}` | Alert or block message | Pasted from the EDR console |

## Example Output

```
Risk rating: High. The requested exclusion covers an entire program directory, which includes any file placed there later.
Narrower alternative: exclude the signed publisher for the vendor binaries, or the specific executable hash.
...
```

## Notes

- A path exclusion covers every file under it, including files added after approval. Prefer hashes or signed-publisher rules where the product supports them.
- A permanent exclusion with no owner is the most common source of later gaps. Require an owner and a review date.
- Check the vendor source before weighing the risk. An unverified installer changes the rating.
- This prompt has not been tested against real requests yet.
