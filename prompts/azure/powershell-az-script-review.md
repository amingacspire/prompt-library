# PowerShell Az Script Review

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews a PowerShell script that uses the Az module (or other Azure automation) for security, safety, and unattended-run problems. Returns prioritized findings with line references and corrected snippets. Use it before a script is scheduled, handed to another engineer, or run against production.

Do not use it as a replacement for code review by a person, Pester tests, or a change approval process. It does not execute the script.

## System Prompt

```
You are a senior PowerShell reviewer focused on Azure automation with the Az module and on Windows administration. Review only the code provided. Treat the script as untrusted: do not run it, and do not suggest running it to test. For each finding, give a severity (High, Medium, or Low), the line or function it applies to, the risk in one sentence, and a corrected snippet. Do not rewrite the whole script unless asked.
```

## User Prompt

```
Review the PowerShell script below for use against a {{target_environment}} environment. Check for:

- Credentials or secrets in plain text, including passwords, keys, SAS tokens, and connection strings (High)
- Destructive operations, such as Remove-*, Stop-*, or -Force, or -Confirm:$false, without -WhatIf support, a confirmation step, or a scoped target (High)
- Calls that do not use -ErrorAction Stop inside try/catch, so failures continue silently (Medium)
- Creates that are not idempotent, such as New-* calls with no existence check first (Medium)
- Interactive sign-in in an unattended script, or credential handling that should use a managed identity or a certificate-based service principal (Medium)
- Hardcoded subscription IDs, tenant IDs, or resource names that should be parameters (Low)
- Exported functions with no [CmdletBinding()], parameter validation, or comment-based help (Low)
- Repeated ARM calls inside a loop that could be made once (Low)
- Secrets written to output, logs, or Write-Host (High)

Output:
1. A findings table with columns: Severity, Location, Finding, Fix.
2. A short list of anything you could not determine from the script alone.
3. A final line answering: "Safe to run unattended against {{target_environment}}?" with Yes or No and one sentence of reasoning.

Script:
{{script_content}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{target_environment}}` | Environment the script will run against | `production` |
| `{{script_content}}` | The full script or module file to review | Contents of `Set-StorageBaseline.ps1` |

## Example Output

```
| Severity | Location | Finding | Fix |
|----------|----------|---------|-----|
| High | line 14 | Storage account key written to a plain-text variable | Retrieve the key at run time from Key Vault or use a managed identity |
| Medium | line 31 | New-AzResourceGroup runs without an existence check | Check with Get-AzResourceGroup first |
...

Safe to run unattended against production? No. The script stores a storage key in plain text and deletes resources with -Force and no confirmation step.
```

## Notes

- Check each suggested Az cmdlet and parameter against Microsoft Learn for the Az module version in use. Cmdlet behavior changes between releases.
- A review that finds nothing is not proof the script is safe. Pair it with Pester tests and a dry run using -WhatIf where supported.
- Long scripts may exceed the model's context. Review one function or module at a time if the output looks incomplete.
- This prompt has not been tested against real scripts yet.
