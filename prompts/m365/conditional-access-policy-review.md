# Conditional Access Policy Review

## Metadata

- **Category:** M365
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

Reviews an exported set of Entra ID Conditional Access policies for coverage gaps, conflicts, and common misconfigurations. Use when auditing a tenant you inherited, before a security review, or after bulk policy changes. Do not use the output as the audit itself; it is a first-pass gap finder that tells a human where to look.

## System Prompt

```
You are a Microsoft Entra ID security reviewer. You analyze Conditional Access
policies for gaps and conflicts. You do not assume any policy exists unless it
appears in the provided export. When a gap depends on information not present
in the export (license level, named locations, group membership), say so
explicitly instead of guessing. Rank findings by risk. Flag any policy in
report-only mode as unenforced.
```

## User Prompt

```
Review the following Conditional Access policy export for {{tenant_description}}.

Check for:
1. Users or apps excluded from MFA enforcement, and whether a break-glass
   account exclusion is documented
2. Legacy authentication not blocked
3. Policies in report-only mode that look like they were meant to be enforced
4. Conflicting policies where one grant undermines another block
5. No device compliance or hybrid join requirement on privileged roles
6. Guest/external access not scoped

Policy export (JSON):
{{policy_export_json}}

Output: table of findings with columns Risk (High/Med/Low), Policy, Gap,
Recommended Fix. Then a short list of things you could NOT verify from this
export alone.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{tenant_description}}` | Brief tenant context | 200-seat healthcare client, E3 + P1 |
| `{{policy_export_json}}` | Output of Graph query to /identity/conditionalAccess/policies | JSON array |

## Example Output

| Risk | Policy | Gap | Recommended Fix |
|------|--------|-----|-----------------|
| High | (none) | No policy blocks legacy authentication | Create block policy for legacy auth clients |
| Med | Require MFA - All Users | 4 users excluded with no documented reason | Review exclusion list, document break-glass |

Things not verifiable from export: license level, named location definitions, group memberships behind sids.

## Notes

- Scrub tenant IDs and user UPNs from the export before pasting into any LLM tool not covered by your data processing agreement.
- The model cannot see group membership, so exclusion-by-group findings need manual follow-up.
- Report-only detection depends on the `state` field being present in your export.
