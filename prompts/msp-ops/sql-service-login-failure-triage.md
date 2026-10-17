# SQL Service Login Failure Triage

## Metadata

- **Category:** MSP Ops
- **Model:** Claude
- **Version:** 1.0
- **Last Tested:** Not yet tested (draft)
- **Author:** Andrew Minga

## Description

Sorts SQL Server login failure entries from the error log into likely causes: a wrong password, a disabled or missing login, a database access problem, a network or client issue, or a service principal name problem. It lists the read-only checks for each cause. Use it when a service account or application starts failing to connect to SQL Server.

It does not reset passwords, change logins, or restart services.

## System Prompt

```
You are a SQL Server operations assistant. Work only from the error log entries provided. Use the state and error codes in the entries to classify each failure, and say when a classification depends on a state value you cannot see. Do not invent error codes. Give read-only checks only.
```

## User Prompt

```
Below are SQL Server error log entries for login failures on instance {{instance_name}}, covering {{time_window}}. Produce:

1. Summary: total failures, count by login name, and count by client address (masked to the first two octets).
2. Each failure group classified by likely cause, with the error code and state from the entries that support the classification.
3. For each cause, one read-only check to run on the instance, such as querying sys.server_principals for the login or checking the login's default database.
4. Entries that do not fit a cause, listed as unclassified with the reason.
5. Whether the failures are continuous or in bursts, and what that suggests.

Rules:
- Do not suggest resetting a password or changing a login's status until the operator has confirmed the cause.
- Mask client addresses to the first two octets.

Error log entries:
{{error_log_entries}}
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{instance_name}}` | SQL Server instance | `SQL-APP-02` |
| `{{time_window}}` | Period the entries cover | `2026-10-15 00:00 to 06:00` |
| `{{error_log_entries}}` | Login failure lines from the SQL Server error log | Pasted from the error log |

Export the error log entries with the server's error log viewer or with `EXEC xp_readerrorlog 0, 1, N'Login failed'` in a read-only session, then paste the lines.

## Example Output

```
Summary: 214 failures | login svc_app: 198 | client 10.42.x.x: 198

Cause: state 8, wrong password. Check the login's password age and whether the application config changed on 2026-10-14.
Cause: state 16, login default database unavailable. Check the default database status.
...
```

## Notes

- State values in the error message are the strongest clue. The same error code can have several states.
- A burst of failures after a password change usually points to the application still using the old credential.
- Service principal name problems show up as Kerberos errors in the log, not login errors. Check the error text, not only the code.
- This prompt has not been tested against real logs yet.
