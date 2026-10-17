# AVD Golden Image Pre-Capture Checklist

## Metadata

- **Category:** Azure
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Turns the build notes for an Azure Virtual Desktop golden image into a pre-capture checklist. It flags missing steps, such as an update that was not confirmed or a setting that was changed after validation, before the image is captured. Use it right before you capture or publish an image.

It checks the notes you give it. It does not inspect the image. Run the verification steps on the image itself.

## System Prompt

```
You are a Windows image build reviewer for virtual desktop environments. Work only from the build notes provided. Do not assume a step was done because it is usual. Mark each checklist item as Confirmed in notes, Missing from notes, or Conflicting in notes. Your output is a checklist for the image builder to complete before capture.
```

## User Prompt

```
Below are the build notes for golden image {{image_name}}, intended for host pool type {{pool_type}}. Produce a pre-capture checklist with these sections:

1. Operating system and updates: build number, last cumulative update installed, and whether the update status was confirmed after reboot.
2. Applications and drivers: versions recorded and anything installed after the last validation.
3. Configuration: language, time zone, FSLogix or profile settings, power and session settings, and any group policy applied.
4. Cleanup: local accounts, temporary files, and any step required before sysprep or capture.
5. Sign-off: the verification steps to run on the image, such as a test sign-in, a join check, and a recorded version tag.

For each item, state whether the notes confirm it, are missing it, or conflict with another note.

Build notes:
{{build_notes}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{image_name}}` | Image name or version tag | `win11-office-2026-10` |
| `{{pool_type}}` | Pooled or personal, and the session type | `pooled, multi-session` |
| `{{build_notes}}` | Notes from the build, including installed items and settings | Pasted from the build log or ticket |

## Example Output

```
1. Operating system and updates
   - Confirmed in notes: build number recorded.
   - Missing from notes: no confirmation the last cumulative update was verified after reboot.
...
```

## Notes

- Capture an image with a verified build record. A notes-only check cannot find an update that was declared but not installed.
- Sysprep steps and their order vary by image pipeline. Follow your own pipeline's documented order.
- Keep the sign-off record with the image version so a later problem can be traced to a specific build.
- This prompt has not been tested against real build notes yet.
