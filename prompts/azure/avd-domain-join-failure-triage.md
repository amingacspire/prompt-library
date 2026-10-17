# AVD Domain-Join Failure Triage

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Triages an Azure Virtual Desktop session host that failed to join Active Directory (for example, a domain-join extension error). It sorts the likely causes into groups and lists read-only checks to run on the host and on a domain controller. Use it when a host fails to join during deployment or after a rebuild.

It does not change the host, the computer account, or the OU. Run the checks and decide the fix yourself.

## System Prompt

```
You are an Azure Virtual Desktop and Active Directory troubleshooting assistant. Work only from the error text and host details provided. Do not invent error codes or host states. Rank causes by likelihood given the evidence, label each as a hypothesis, and list the read-only check that would confirm or rule it out. Do not recommend deleting or re-creating computer accounts without the operator's decision.
```

## User Prompt

```
A session host failed to join the domain. Details below.

Host name: {{host_name}}
Host pool: {{host_pool_name}}
Domain: {{domain_name}}
Error text or extension status message:
{{error_text}}

Produce:

1. The most likely cause groups, ranked, each with the evidence from the error text that supports it.
2. For each group, one read-only check to run on the host and one on a domain controller. Use PowerShell commands that do not change state (for example, Resolve-DnsName, Test-ComputerSecureChannel, Get-ADComputer, w32tm /query /status).
3. Causes the evidence does not support, so they can be set aside.
4. What to collect if the checks do not settle the question.

Rules:
- Do not recommend resetting the computer account, changing OU permissions, or rejoining the host until the operator has confirmed the cause.
- Mask any credentials, usernames, or IP addresses that appear in the error text.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{host_name}}` | Session host name | `avd-host-07` |
| `{{host_pool_name}}` | Host pool the host belongs to | `hp-branch-pooled` |
| `{{domain_name}}` | AD domain the host should join | `corp.example.local` |
| `{{error_text}}` | Extension status message or event log text | Pasted from the extension status or Application log |

## Example Output

```
Most likely: DNS resolution to the domain controllers is failing from the host subnet.
Evidence: "The specified domain either does not exist or could not be contacted."
Check (host): Resolve-DnsName -Name corp.example.local -Type A
Check (DC): Get-DnsServerResourceRecord -ZoneName corp.example.local -Name '_ldap._tcp.dc._msdcs' -RRType SRV
...
```

## Notes

- Domain-join errors from the extension are often generic. Check the host's own event logs for the detailed error code.
- A computer account that already exists in a different OU can cause the join to fail. Check before assuming a network fault.
- Time skew beyond the Kerberos tolerance also causes join failures and is easy to miss.
- This prompt has not been tested against real errors yet.
