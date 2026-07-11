# Prompt Library

Battle-tested LLM prompts for IT operations. Written and maintained by [Andrew Minga](https://mingasolutions.com), Manager of Customer Experience Engineering at C Spire Business.

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

| Prompt | Category | Model Tested |
|--------|----------|--------------|
| [Conditional Access Policy Review](prompts/m365/conditional-access-policy-review.md) | M365 | Claude |
| [Incident Ticket Summarizer](prompts/msp-ops/incident-ticket-summarizer.md) | MSP Ops | Claude |
| [Phishing Email Analysis](prompts/security/phishing-email-analysis.md) | Security | Claude |

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
