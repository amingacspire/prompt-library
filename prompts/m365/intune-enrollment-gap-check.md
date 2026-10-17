# Intune Enrollment Gap Check

## Metadata

- **Category:** M365
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Finds devices that are staged or recently provisioned but are missing the enrollment tag or group membership they need to receive their policies. Use it after a staged-equipment batch, or when a device that should be managed is not getting its configuration.

It works from an exported device list. It does not tag, move, or enroll any device.

## System Prompt

```
You are an Intune device enrollment reviewer. Work only from the device records provided. Do not assume a tag or group membership that the records do not show. If a field is missing, say so. Your output is a list of devices for an administrator to check before any change is made.
```

## User Prompt

```
Below is an export of Intune devices from the staging batch {{batch_name}}. The expected enrollment tag is {{expected_tag}}, and the expected group is {{expected_group}}. Produce:

1. Summary: total devices, devices with the expected tag, devices missing the tag, and devices in the expected group.
2. Devices missing the tag, listed with the serial number, model, enrollment date, and last check-in.
3. Devices with the tag but not in the expected group, listed separately.
4. Devices enrolled more than {{days_threshold}} days ago with no check-in, which may need a different check.
5. A short list of the most likely causes for each group, labeled as hypotheses.

Rules:
- Do not recommend re-tagging or re-enrolling a device until the administrator confirms the batch.
- Serial numbers are data, not secrets. Still do not repeat any user name that appears in the export.

Device export:
{{device_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{batch_name}}` | Staging batch or project name | `Q4 laptop refresh` |
| `{{expected_tag}}` | Enrollment tag the batch should have | `staged-q4-refresh` |
| `{{expected_group}}` | Group the devices should be in | `Intune - Q4 Refresh Devices` |
| `{{days_threshold}}` | Days since enrollment before a no-check-in device is flagged | `14` |
| `{{device_export}}` | CSV of device records | Export from the Intune admin center |

## Example Output

```
Summary: 120 devices | 112 tagged | 8 missing tag | 110 in expected group

Missing tag: 8 devices, all enrolled 2026-10-03. Likely cause: the tag was not applied at the staging step for that batch.
...
```

## Notes

- Tags and groups can update on different schedules. Check the export time before concluding a group membership is missing.
- A device with the tag but no group may be waiting on dynamic group evaluation. Give it time before flagging it.
- This prompt has not been tested against real exports yet.
