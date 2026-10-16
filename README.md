# Prompt Library

Battle-tested LLM prompts for IT operations. Written and maintained by [Andrew Minga](https://andrewminga.com), Manager of Customer Experience Engineering at C Spire Business. If prompts are your thing, the free browser-based [IT ops tools](https://andrewminga.com/tools/) probably are too.

Every prompt here has been used in real work. No theoretical prompts, no filler. Each one documents what it does, what model it was tested against, and what to watch out for.

## Categories

| Folder | Focus |
|--------|-------|
| [`prompts/m365`](prompts/m365) | Microsoft 365, Exchange Online, Entra ID, Intune |
| [`prompts/azure`](prompts/azure) | Azure infrastructure, AVD, cost management |
| [`prompts/security`](prompts/security) | Security review, incident triage, awareness content |
| [`prompts/msp-ops`](prompts/msp-ops) | MSP workflows, ticketing, client communication |
| [`prompts/claude-code`](prompts/claude-code) | Agentic coding, CLAUDE.md patterns, automation |

## Index

### Task Prompts

| Prompt | Category | Model Tested |
|--------|----------|--------------|
| [Conditional Access Policy Review](prompts/m365/conditional-access-policy-review.md) | M365 | Claude |
| [Intune Device Compliance Summary](prompts/m365/intune-compliance-summary.md) | M365 | Claude (untested draft) |
| [Azure Policy Compliance Triage](prompts/azure/azure-policy-compliance-triage.md) | Azure | Claude (untested draft) |
| [PowerShell Az Script Review](prompts/azure/powershell-az-script-review.md) | Azure | Claude (untested draft) |
| [NSG Rule Audit](prompts/azure/nsg-rule-audit.md) | Azure | Claude (untested draft) |
| [AVD Session Host Health Summary](prompts/azure/avd-session-host-health.md) | Azure | Claude (untested draft) |
| [Cost Anomaly Explainer](prompts/azure/cost-anomaly-explainer.md) | Azure | Claude (untested draft) |
| [Incident Ticket Summarizer](prompts/msp-ops/incident-ticket-summarizer.md) | MSP Ops | Claude |
| [Windows Event Log Triage](prompts/msp-ops/windows-event-log-triage.md) | MSP Ops | Claude (untested draft) |
| [PowerShell Runbook Generator](prompts/msp-ops/powershell-runbook-generator.md) | MSP Ops | Claude (untested draft) |
| [Phishing Email Analysis](prompts/security/phishing-email-analysis.md) | Security | Claude |
| [Email Security Domain Check](prompts/security/email-security-domain-check.md) | Security | Any |
| [PowerShell Script Security Review](prompts/security/powershell-script-security-review.md) | Security | Claude (untested draft) |
| [PowerShell Module Test Scaffold (Pester 5)](prompts/claude-code/pester-test-scaffold.md) | Claude Code | Claude (untested draft) |

### Agentic Patterns

Reusable structures for agent workflows, pulled from patterns I run in production.

| Pattern | What it solves |
|---------|----------------|
| [Adversarial Review Loop](prompts/claude-code/adversarial-review-loop.md) | A model cannot catch its own blind spots; a second independent reviewer can |
| [Independent Evaluator Loop](prompts/claude-code/independent-evaluator-loop.md) | Quality gates the author cannot talk its way past |
| [Multi-Agent Router](prompts/claude-code/multi-agent-router.md) | Routing requests to specialist subagents and synthesizing one honest answer |
| [Incremental Build with Verification](prompts/claude-code/incremental-build-with-verification.md) | Long builds in fragile platforms failing silently mid-run |
| [Agent Memory Priority Layer](prompts/claude-code/agent-memory-priority-layer.md) | Memory cleanup eating standing rules; stale notes accumulating forever |

## Using a Prompt

1. Open the prompt file.
2. Replace the `{{variables}}` with your values.
3. Paste the system prompt and user prompt into your tool of choice.
4. Check the Notes section for known limitations before trusting output.

## Contributing a Prompt

Copy [`TEMPLATE.md`](TEMPLATE.md), fill it in, and open a PR. Prompts must be scrubbed of client names, tenant IDs, and any organization-specific data before submission.

## A Note on Trust

LLM output is a draft, not a decision. Every prompt in this library assumes a human reviews the result before acting on it. That applies double for anything touching security or production infrastructure.

## License

MIT. Use them, modify them, ship them.
