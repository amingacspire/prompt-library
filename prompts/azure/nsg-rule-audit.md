# Azure NSG Rule Audit

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Reviews an export of network security group rules and flags rules that are too broad, unused or likely dead, and rules that shadow or are shadowed by others. Use it during a network review or before a customer handover. It is a read-only analysis of pasted data.

Do not use it to decide which rules to delete. Rule intent depends on workloads the export does not show.

## System Prompt

```
You are an Azure network security reviewer. Work only from the rules provided. Do not assume a rule's purpose from its name alone. Flag what the data shows and state what you cannot confirm. Your output is a draft for an engineer to review.
```

## User Prompt

```
Below is an export of NSG security rules. Review it and produce:

1. Summary: number of NSGs, number of custom rules, and how many rules you flagged.
2. Overly broad rules: any Allow rule with a source of Any, Internet, or 0.0.0.0/0 on a non-web port, or with a destination port range of * . Give NSG, rule name, priority, and why it is broad.
3. Shadowed rules: any rule that can never match because a lower-numbered rule with the same direction covers its traffic. Give both rule names.
4. Possibly unused rules: Deny rules that duplicate the default deny, and Allow rules to ports or prefixes that look retired. Label each as a possibility, not a finding.
5. Items to confirm with the owner before any change, one line each.

Rules:
- Do not recommend deleting a rule. Recommend reviewing it.
- Treat the default rules (priority 65000 and above) as context, not as findings.
- If the export looks incomplete, say so before giving counts.

NSG rule export:
{{nsg_rule_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{nsg_rule_export}}` | CSV of NSG rules | Output of the export command below |

Suggested export, using the Az.Network module:

```powershell
Get-AzNetworkSecurityGroup -ResourceGroupName '<resource-group>' | ForEach-Object {
    $nsg = $_
    $nsg.SecurityRules | Select-Object @{n='NSG';e={$nsg.Name}}, Name, Priority, Direction, Access, Protocol, SourceAddressPrefix, SourcePortRange, DestinationAddressPrefix, DestinationPortRange
} | Export-Csv -Path .\nsg-rules.csv -NoTypeInformation
```

Confirm the property names against your installed Az.Network version.

## Example Output

```
Summary: 6 NSGs | 84 custom rules | 9 flagged

Overly broad: web-tier NSG, rule "allow-any-rdp", priority 300.
  Source: Internet. Destination port: 3389. Review whether RDP should be open to the internet.
...
```

## Notes

- Priority order matters. A rule is only shadowed if a lower number covers the same traffic, so check direction and protocol as well as ports.
- Service tags such as Internet and VirtualNetwork are not the same as 0.0.0.0/0. Read the tag as written.
- Effective security rules on a NIC or subnet can differ from the NSG alone. Confirm with effective rules if a finding matters.
- This prompt has not been tested against real exports yet.
