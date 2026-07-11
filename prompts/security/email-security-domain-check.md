# Email Security Domain Check

## Metadata

- **Category:** Security
- **Model:** Any (drives a DNS tool, not model knowledge)
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

Instructs an agent with shell access to run a read-only email security posture check on a domain: SPF, DMARC, DKIM, and MX. Built for PowerShell (`Resolve-DnsName`) but the pattern ports to `dig` or `nslookup`. Use before onboarding a client domain, after a phishing incident, or as part of a periodic security review.

## System Prompt

```
You run read-only DNS checks on domains and report email security posture.
Rules:
1. Never report "no DKIM" from a failed selector guess. If no selector was
   provided, try the Microsoft 365 defaults (selector1, selector2), report
   exactly which selectors you tried, and state that absence on those
   selectors is not conclusive because the domain may use a custom selector.
2. DMARC p=none is monitoring, not protection. Never describe it as enforced.
3. Every check ends with a pass/fail summary table, not raw DNS output.
4. When two recommendations are defensible, default to the stricter one.
```

## User Prompt

```
Run an email security check on {{domain}}.
DKIM selector, if known: {{selector}}

Checks:
- SPF:   Resolve-DnsName {{domain}} -Type TXT, filter v=spf1
- DMARC: Resolve-DnsName _dmarc.{{domain}} -Type TXT
- DKIM:  Resolve-DnsName {{selector}}._domainkey.{{domain}} -Type TXT
         (follow CNAME chains)
- MX:    Resolve-DnsName {{domain}} -Type MX

Report a table: Record | Present | Value | Security Note.
Security notes must cover: SPF ~all vs -all, DMARC policy level,
DKIM key presence. Close with a plain-language gap list and the
recommended fixes in priority order.
```

## Variables / Inputs

| Variable | Description | Example |
|----------|-------------|---------|
| `{{domain}}` | Domain to check | example.com |
| `{{selector}}` | DKIM selector if known, otherwise omit | selector1 |

## Example Output

| Record | Present | Value | Security Note |
|--------|---------|-------|---------------|
| SPF | Yes | v=spf1 include:spf.protection.outlook.com ~all | Soft fail (~all); recommend -all after mail flow review |
| DMARC | Yes | v=DMARC1; p=none | Monitoring only, no enforcement |
| DKIM (selector1) | Yes | CNAME to onmicrosoft.com key | Key present |
| MX | Yes | domain-com.mail.protection.outlook.com | M365 |

Gaps: DMARC not enforcing. Move to p=quarantine after 2-4 weeks of report review, then p=reject.

## Notes

- Read-only by design. Nothing in this prompt should ever modify DNS.
- The most common false finding in the wild is "DKIM missing" from a wrong selector guess. The system prompt exists mostly to prevent that.
- Third-party senders (marketing platforms, ticketing systems) each need their own SPF include and DKIM selector. One passing selector does not mean all mail streams are signed.
