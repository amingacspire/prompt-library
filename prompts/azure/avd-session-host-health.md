# AVD Session Host Health Summary

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Turns an export of Azure Virtual Desktop session host state into a short issues list for the on-call engineer. Use it at the start of a shift or during an incident, when users report slow or failed sign-ins and you need to see which hosts need attention.

It does not restart, drain, or change hosts. It reads the export and suggests what to check.

## System Prompt

```
You are an Azure Virtual Desktop operations assistant. Work only from the session host data provided. Do not invent host names, statuses, or session counts. If a field is missing, say so. Your output is a triage list for a human operator, not a change plan.
```

## User Prompt

```
Below is an export of AVD session host state for host pool {{host_pool_name}}. Produce:

1. Summary: total hosts, hosts available, hosts unavailable, hosts in drain mode, and hosts with new sessions disabled.
2. Hosts needing attention, ordered by severity. For each: host name, the status or condition, how long it has been in that state if the data shows it, and the most likely cause to check first.
3. Patterns: if several hosts share a problem (agent version, OS build, or heartbeat gap), call the pattern out before the individual hosts.
4. Read-only checks to run next, as PowerShell commands that do not change state.
5. Anything in the export that looks stale or truncated.

Rules:
- Do not recommend draining, rebooting, or removing a host without the operator's decision.
- A host in drain mode may be intentional. Say so rather than treating it as a fault.

Session host export:
{{session_host_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{host_pool_name}}` | Host pool the export covers | `hp-office-prod` |
| `{{session_host_export}}` | CSV or JSON of session host state | Output of the export command below |

Suggested export, using the Az.DesktopVirtualization module:

```powershell
Get-AzWvdSessionHost -HostPoolName '<host-pool>' -ResourceGroupName '<resource-group>' |
    Select-Object Name, Status, UpdateState, AllowNewSession, Session, LastHeartBeat, AgentVersion, OSVersion |
    Export-Csv -Path .\session-hosts.csv -NoTypeInformation
```

Confirm the property names against your installed module version.

## Example Output

```
Summary: 12 hosts | 9 available | 2 unavailable | 1 in drain mode

Pattern: 2 unavailable hosts both run agent version 1.0.x and both lost heartbeat at the same time.
  Check the agent version and network path to the broker before examining hosts individually.
...
```

## Notes

- Heartbeat timestamps are only as current as the export. Check the export time before reading ages into the output.
- A host with AllowNewSession set to false may be in planned maintenance. Ask before escalating.
- The status values come from the service. Verify them against the current Microsoft documentation for your module version.
- This prompt has not been tested against real exports yet.
