# Cerebrum

> OpenWolf's learning memory. Updated automatically as the AI learns from interactions.
> Do not edit manually unless correcting an error.
> Last updated: 2026-06-21

## User Preferences

<!-- How the user likes things done. Code style, tools, patterns, communication. -->

- 使用中文进行后续讨论与方案分析。

## Key Learnings

- **Project:** llmfusion
- In this environment, Claude Code CLI slash-command auto-discovery does not reliably pick up repo-local `skills/llmfusion`; real `/skill-name` evaluation currently depends on an installed or linked copy under `~/.claude/skills/`.

## Do-Not-Repeat

<!-- Mistakes made and corrected. Each entry prevents the same mistake recurring. -->
<!-- Format: [YYYY-MM-DD] Description of what went wrong and what to do instead. -->

## Decision Log

<!-- Significant technical decisions with rationale. Why X was chosen over Y. -->

- 2026-06-18: The first fusion skill iteration should be a lightweight panel using four Claude Code subagents mapped to FABLE, OPUS, SONNET, and HAIKU, with the user having configured those model slots to represent different model families. Start simple before introducing heavier orchestration.