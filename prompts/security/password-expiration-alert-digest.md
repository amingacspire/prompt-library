# Password Expiration Alert Digest

## Metadata

- **Category:** Security
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Turns a list of accounts with upcoming password expiry dates into a digest grouped by urgency and by directory unit. It flags service and shared accounts separately, since they often need a different process. Use it to write the daily or weekly expiry notice to the team.

It works with account names and expiry dates only. Never paste password values, hashes, or secrets into it.

## System Prompt

```
You are an identity operations assistant. Work only from the account records provided. Never ask for, repeat, or infer any password value or secret. Group accounts by urgency and flag service and shared accounts for separate handling. Your output is a digest for the identity team.
```

## User Prompt

```
Below is an export of accounts with password expiry dates, taken on {{report_date}}. Produce a digest with:

1. Summary: total accounts in the export, and counts for each urgency band: expired, within 3 days, 4 to 7 days, and 8 to 14 days.
2. The accounts in each band, listed with the directory unit and expiry date.
3. Service accounts and shared accounts in a separate list, with the reason they need a different process.
4. Accounts with no expiry date set, listed separately, since a missing date can be a configuration issue.
5. A short notice paragraph suitable for the team, with no account names.

Rules:
- Do not include any password value, hash, or secret.
- Mask user names other than the domain in the notice paragraph.

Account export:
{{expiry_export}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{report_date}}` | Date the export was taken | `2026-10-17` |
| `{{expiry_export}}` | CSV of account name, directory unit, account type, and expiry date | Export from the directory query |

Export only the columns the digest needs: account name, directory unit, account type, and password expiry date. Do not include any other attribute.

## Example Output

```
Summary: 412 accounts | 3 expired | 9 within 3 days | 22 in 4 to 7 days | 41 in 8 to 14 days

Within 3 days: 9 accounts, 6 in the Finance unit.
Service accounts: 2 need a separate process, see the list.
```

## Notes

- Password expiry is only one control. A password that does not expire may need a different review.
- Service accounts often need a planned change, not a user notice. Route them to the owner.
- Run the export on a schedule, and keep the report date with each digest so the counts can be compared over time.
- This prompt has not been tested against real exports yet.
