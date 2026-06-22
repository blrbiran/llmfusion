---
name: llmfusion
description: Use this skill when the user explicitly asks for fusion, a panel, multiple models, 多模型, 多视角, second opinion, or a lightweight synthesized judgment for architecture choices, tricky bugs, or technical research. In Claude Code, dispatch four subagents in parallel on FABLE, OPUS, SONNET, and HAIKU, then run a judge pass using the highest-priority available model in this order: FABLE > OPUS > SONNET > HAIKU. Prefer explicit/manual triggering. Do not use this skill for routine edits, simple factual questions, or tasks that do not benefit from independent perspectives.
compatibility:
  tools: Agent, Read, Bash
---

# llmfusion

Use this skill to produce a lightweight multi-model synthesis inside Claude Code.

This skill is for questions where one answer is not enough and the user explicitly wants multiple independent perspectives. The value comes from running several model-specific passes in parallel, then fusing them into a single answer that makes agreement, disagreement, and uncertainty visible.

## Best-fit tasks

Prefer this skill for:

- architecture or design trade-off decisions
- tricky bug analysis where multiple hypotheses are useful
- technical research or codebase understanding where second opinions help
- situations where the user explicitly asks for fusion, panel review, 多模型分析, 多角度判断, or second opinion

Avoid this skill for:

- routine implementation work
- simple code edits
- direct fact lookup
- tasks where one careful pass is enough

## Operating rule

Default to explicit trigger only.

If the user did not ask for a panel, fusion, multiple models, or multiple perspectives, do not invoke this skill unless the task clearly depends on independent judgments.

## Workflow

### 1. Frame the question tightly

Before dispatching subagents, rewrite the task as a single clear evaluation question.

Include:

- the exact decision, bug, or research question
- the relevant files or context already read
- what kind of answer is wanted: recommendation, diagnosis, comparison, or plan
- any constraints from the user such as speed, simplicity, or scope limits

If the question is still ambiguous, ask a short clarification before running the panel.

### 2. Dispatch the four panel members in parallel

Launch four subagents in the same turn using these fixed model assignments:

- FABLE
- OPUS
- SONNET
- HAIKU

Use the same task framing for all four, but give each model a distinct lens so they do not collapse into near-duplicates.

Recommended lenses:

- **FABLE** — long-horizon synthesis, strategic trade-offs, hidden assumptions
- **OPUS** — correctness check, adversarial review, failure modes
- **SONNET** — practical implementation path, repo fit, minimum viable approach
- **HAIKU** — concise summary, edge cases, simplifications, fast alternative framing

Each panel prompt should ask for this structure:

1. Short answer
2. Main reasoning
3. Risks or failure modes
4. Unknowns / what would need verification
5. Recommended next step

Keep each panel member independent. Do not let later panel prompts reference earlier outputs.

### 3. Run a judge pass

After all four panel responses are back, run a separate judge pass.

Judge model priority:

1. FABLE
2. OPUS
3. SONNET
4. HAIKU

Use the highest-priority available model from that list. If all four panel models were used, still prefer a separate FABLE judge pass. Reusing the same family for synthesis is acceptable; a synthesis pass can still add value even when it judges outputs from a matching model family.

The judge should read all panel outputs and produce:

- consensus points
- disagreements and why they differ
- unique insights worth preserving
- blind spots or missing evidence
- a final recommendation with confidence level

The judge should not average conflicting answers into mush. Pick a direction when possible. If uncertainty remains material, say exactly what must be verified next.

### 4. Deliver the fused answer

Return one final answer to the user using this structure:

## Fusion verdict
- One-paragraph conclusion

## Consensus
- Bullet list

## Disagreements
- Bullet list with why the answers diverged

## Blind spots
- Bullet list of missing evidence or uncertainty

## Recommended next step
- The single best next action

If the task is repo-specific, cite concrete files using `path:line` where possible.

## Prompt templates

### Shared task frame

Use this as the base for every panel member:

```text
You are one member of a four-model fusion panel inside Claude Code.
Task: <tight restatement of the user's question>
Context already gathered: <files, code, constraints, observations>
Goal: produce an independent judgment, not a compromise.

Return exactly:
1. Short answer
2. Main reasoning
3. Risks or failure modes
4. Unknowns / what needs verification
5. Recommended next step
```

### Judge prompt

```text
You are the judge in a lightweight fusion workflow.
Your job is to read four independent model outputs and synthesize them.
Do not simply vote. Identify:
- consensus points
- real disagreements
- unique insights
- blind spots
- the best final recommendation

If one answer is stronger, say so directly and explain why.
If uncertainty remains, say what should be verified next.
Use a clear and decision-oriented tone.
```

## Practical notes

- Read only the minimum necessary repo context before launching the panel.
- Keep the subagent prompts aligned on the same question so the comparison is meaningful.
- Distinct lenses are helpful, but keep them close enough that outputs remain comparable.
- If the user wants action after the fusion result, use the fused answer to guide the next implementation or debugging step.
- If time or cost becomes a concern, tell the user that this skill intentionally trades latency for better judgment.
