# Intune Device Compliance Summary

## Metadata

- **Category:** M365
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Takes an export of non-compliant Intune devices and the settings that failed, and groups them by failing setting. Use it to find the one policy setting causing most of the non-compliance before you open a ticket for each device.

It does not change any policy or device. It works from the exported data only.

## System Prompt

```
You are an Intune endpoint management analyst. Work only from the device and setting data provided. Do not invent device names, setting names, or counts. If a field is missing, say so. Your output is a summary for an administrator to review before any policy change.
```

## User Prompt

```
Below is an export of non-compliant devices from Intune for {{device_group_name}}. Produce:

1. Summary: total devices in the export, number non-compliant, and number in grace period if the data shows it.
2. Failing settings, grouped by setting name, with the number of devices affected and the platform (Windows, iOS, Android, or macOS). Sort by count.
3. For the top five settings, the most likely reason devices fail (for example, a setting that needs a newer OS build, a missing encryption state, or an out-of-date threat definition), labeled as a hypothesis.
4. Devices that fail on several settings, listed as a short group, since they may be a single underlying issue.
5. Questions to answer before changing any policy, one line each.

Rules:
- Do not recommend removing or weakening a compliance setting to reduce the count.
- Separate Windows, iOS, Android, and macOS results when the same setting name appears on more than one platform.

Device export:
{{device_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{device_group_name}}` | Group or policy scope the export covers | `All corporate Windows devices` |
| `{{device_export}}` | CSV of devices and failing settings | Export from the Intune admin center |

Export from the Intune admin center under Devices, then Monitor, or from the device compliance report, with one row per device and setting. Confirm the column names in your tenant before pasting, because they vary between report types.

## Example Output

```
Summary: 186 devices | 41 non-compliant | 6 in grace period

Setting: BitLocker encryption required (Windows), 22 devices
  Hypothesis: devices that were not encrypted during enrollment. Check the enrollment date against the policy assignment date.
...
```

## Notes

- Compliance reports can lag behind the device state by hours. Check the report time before acting.
- A setting failing across many devices often points to a policy, not to the devices. Confirm the policy assignment first.
- Platform-specific settings can share a name. Keep platforms separate in the summary.
- This prompt has not been tested against real exports yet.
