# Agent Memory Priority Layer

## Metadata

- **Category:** Claude Code / Agentic Patterns
- **Model:** Any agent with file-based persistent memory
- **Version:** 1.0
- **Last Tested:** 2026-07-11
- **Author:** Andrew Minga

## Description

A priority scheme for agent memory files that prevents the two standard failure modes: critical standing rules getting pruned during cleanup, and stale project notes accumulating forever. Every memory file gets a priority tier and a last-accessed date in frontmatter; cleanup rules key off both.

## Frontmatter Schema

```yaml
---
name: short-kebab-slug
description: one-line summary used for recall relevance
priority: pinned | normal | decaying
last_accessed: YYYY-MM-DD
---
```

## Tier Rules

| Tier | Meaning | Cleanup rule |
|------|---------|--------------|
| `pinned` | Standing rules, identity, hard constraints | Never delete. Never auto-demote. |
| `normal` | Active projects, working preferences | Keep unless proven obsolete or superseded |
| `decaying` | Time-bound context, one-off project state | Flag for deletion when `last_accessed` is more than 90 days old |

## Maintenance Instructions (add to the agent's memory guidance)

```
1. On every recall of a memory file, update its last_accessed date.
2. New memories default to priority: normal. Use decaying for anything
   tied to a project with an end date. Pinned requires the fact to be a
   standing rule or identity-level constraint.
3. During memory cleanup: never touch pinned files. Delete decaying
   files past their staleness window. For normal files, verify the fact
   is still true before keeping; delete when superseded.
4. Keep a pinned index at the top of the memory index file so the most
   important rules survive any summarization of the index itself.
5. Demotion is allowed (pinned to normal, normal to decaying) when the
   user confirms a rule no longer applies. Log the demotion.
```

## Example

```yaml
---
name: evidence-standard
description: Every factual claim requires an observed source
priority: pinned
last_accessed: 2026-07-11
---
```

```yaml
---
name: q3-migration-cutover-notes
description: Cutover sequence for the Q3 tenant migration
priority: decaying
last_accessed: 2026-04-02
---
```
The second file is 90+ days stale and gets flagged for removal; the first is untouchable.

## Notes

- The pinned tier exists because generic "clean up old memories" instructions will eventually eat your standing rules. Priority must be explicit metadata, not inferred from content.
- `last_accessed` only works if recall actually updates it. Put the update instruction in the same place the agent reads its recall rules, or the dates rot.
- 90 days is a default. Tune per tier to how fast your project context churns.
