# Azure Policy Compliance Triage

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Takes an export of Azure Policy compliance state and turns it into a prioritized triage: non-compliant resources grouped by policy, the likely impact of each group, and the checks to run before anyone remediates. Use it when a subscription or management group shows a large non-compliance count and you need to decide where to start.

Do not use it to decide remediation automatically. It is a read-only analysis of data you paste in. It does not connect to Azure.

## System Prompt

```
You are an Azure governance analyst. Work only from the data provided. Do not invent resource names, policy definitions, compliance states, or effects that are not in the export. If a field is missing, say so. Your output is a draft for an engineer to review before any change is made.
```

## User Prompt

```
I am providing an export of Azure Policy compliance state for {{subscription_scope}}. Analyze it and produce the following:

1. Summary: total resources evaluated, and counts for compliant, non-compliant, and exempt.
2. Non-compliant resources grouped by policy definition, sorted by count (highest first). For each group, list the number of resources and the subscriptions or resource groups affected.
3. For each group, give a one-sentence description of the likely impact, and state whether remediation is likely automatic (DeployIfNotExists or Modify effect) or manual (Audit, Deny, or unknown effect).
4. The five most valuable next actions, in order of risk reduction. For each action, include a read-only PowerShell command that checks the current state of one example resource. Do not include a remediation command.
5. Data gaps: anything you would need in order to be certain of the findings, such as missing effect values, missing display names, or output that looks truncated.

Rules:
- Do not recommend disabling, excluding, or exempting a policy to clear non-compliance.
- Do not assume a resource is in production unless the data says so.
- If the export looks truncated, say so before giving any counts.

Policy compliance export:
{{policy_state_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{subscription_scope}}` | Subscription or management group the export covers | `Production subscription (non-prod excluded)` |
| `{{policy_state_export}}` | CSV or JSON export of policy state records | Output of `Export-Csv` on `Get-AzPolicyState` results |

Suggested export, using the Az.PolicyInsights module:

```powershell
Get-AzPolicyState -SubscriptionId '<subscription-id>' -Top 5000 |
    Select-Object ResourceId, PolicyDefinitionName, PolicyDefinitionAction, ComplianceState, PolicyAssignmentName |
    Export-Csv -Path .\policy-state.csv -NoTypeInformation
```

Confirm the property names against your installed Az.PolicyInsights version before relying on this export.

## Example Output

```
Summary: 1,240 evaluated | 1,102 compliant | 131 non-compliant | 7 exempt

Group 1: Storage accounts must disable public network access (46 resources, 3 subscriptions)
  Impact: Storage accounts may be reachable from the public internet.
  Remediation: Manual. Effect is Audit.
...
```

## Notes

- `PolicyDefinitionName` in the export is usually the policy GUID, not the display name. Map GUIDs to display names before grouping, or the groups will be hard to read.
- A large non-compliant count is often a few policies with many resources, not many separate problems. Group first, then prioritize.
- Verify each proposed remediation against the policy's current definition in the portal before acting on it.
- This prompt has not been tested against real exports yet.
