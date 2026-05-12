---
name: qiaomu-heavyskill
description: |
  Deep multi-perspective reasoning via parallel isolated subagents + Codex deliberation host,
  with final Markdown + HTML report output. Use for complex questions, tech decisions, social
  topics, architectural choices, hard analysis, or any question worth thinking harder about.
  Triggers include 深度分析, 多角度推理, 验证答案, heavyskill, think harder, 让我们深入思考,
  推敲一下, 讨论一下, 分析利弊. Do not use for simple factual lookups or routine tasks.
---

# HeavySkill

Multi-agent reasoning pipeline. K isolated subagents reason independently → Codex hosts deliberation → Claude renders a readable Markdown + HTML report.

Based on arXiv:2605.02396. Key finding: deliberation is generative — the synthesizer produces correct answers absent from every individual trace.

## Two Modes

Choose based on whether the question has a correct answer:

| Mode | When | Subagent type | Example |
|------|------|---------------|---------|
| **Verification** | Has a correct/better answer | Reasoning approaches | Code bug, math, logic, factual analysis |
| **Deliberation** | No single correct answer; multiple valid views | Stakeholder perspectives | Tech stack choice, social topic, product decision, strategy |

## Roles

| Role | Implementation | Responsibility |
|------|---------------|----------------|
| Parallel Thinkers | K independent subagents (Agent tool, parallel) | One trace each, isolated context |
| Deliberation Host | Codex via `/codex:rescue` | Critical evaluation → synthesized conclusion |
| Report Author | Claude (main context) | Render Markdown + HTML from Codex output |

## Why Subagents

Sequential traces in the same context window are architecturally wrong — the model's attention sees prior traces even when instructed to ignore them. Each subagent gets a fresh isolated context: true independence, not instructed independence.

## Workflow

1. **Clarify** — identify mode (Verification / Deliberation), question, success criteria, K (3 = standard / 4 = complex / 5 = high stakes).
2. **Launch K subagents in parallel** — Agent tool, K simultaneous calls, each with isolated context and one assigned approach type or perspective lens.
3. **Collect** — gather all outputs, identify convergence cluster, flag outliers, shuffle order (prevents Codex position bias).
4. **Codex deliberation** — format traces into structured prompt, invoke `/codex:rescue`. Codex runs: classify → evaluate per-trace → re-derive if all flawed → synthesize.
5. **Render report** — Claude generates:
   - `heavyskill-report.md` — structured Markdown report
   - `heavyskill-report.html` — single-page readable HTML report
6. **Iterate** (optional) — if Codex confidence is Low/Medium, one more deliberation round. Max 2 total.

See [Framework](references/framework.md) for subagent prompts, approach types, perspective lenses, Codex prompt template, and HTML template.

## Output Contract

- Create folder `~/Downloads/heavyskill-reports/{question-slug}-{date}/` before writing any files.
- Write each subagent trace to `traces/trace-{letter}-{approach}.md` as it arrives.
- Write Codex raw output to `deliberation.md`.
- Write final Markdown report to `{slug}.md`.
- Write final HTML report to `{slug}.html` — Medium-style (off-white bg, near-black text, large readable type, generous whitespace, editorial feel), self-contained, no external dependencies.
- Present Codex output verbatim in the conversation. No paraphrasing.
- Report name is generated from the question — never a generic filename.
