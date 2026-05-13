# HeavySkill Framework (Reference)

## Source

HeavySkill: Encoding Heavy Thinking as a Readable Skill (arXiv:2605.02396)

Core finding: HP@K regularly exceeds P@K — deliberation produces correct answers absent from every individual trace.
Hierarchy: `Heavy-Pass@K ≥ Heavy-Mean@K ≥ Vote@K ≥ Mean@K`

---

## Mode Selection

### Verification Mode
Question has a correct or clearly better answer. Use reasoning approach types.

### Deliberation Mode
Question has no single correct answer — legitimately multiple valid views. Use perspective lenses instead.
Best for: tech stack decisions, social topics, product strategy, architectural trade-offs.

---

## Step 0: Clarification + Naming + Folder Setup

### Clarification

```
模式：[Verification / Deliberation]
核心问题：[一句话]
成功标准：[什么样的答案才算"好答案"？]
约束条件：[不能违反的边界]
K值：3 (standard) / 4 (complex) / 5 (high stakes or known blind spots)
```

### Report Naming

Generate a slug from the question:
- Extract 3-5 key words (remove particles, connectives)
- Lowercase, hyphenated, ASCII-safe
- Append date: `YYYY-MM-DD`

Examples:
- "AI会让大批量人失业？" → `ai-mass-unemployment-2026-05-12`
- "React vs Vue 技术选型" → `react-vs-vue-tech-selection-2026-05-12`
- "微服务还是单体架构？" → `microservice-vs-monolith-2026-05-12`

### Folder Structure

Create this directory tree before writing any files:

```
~/Downloads/heavyskill-reports/
  └── {slug}/
      ├── traces/
      │   ├── trace-a-{approach-slug}.md
      │   ├── trace-b-{approach-slug}.md
      │   └── trace-c-{approach-slug}.md   (and so on for K)
      ├── deliberation.md                  (Codex raw output)
      ├── {slug}.md                        (final Markdown report)
      ├── {slug}.html                      (final HTML report)
      └── {slug}.pdf                       (combined PDF: traces + deliberation + verdict)
```

Create the folder with:
```bash
mkdir -p ~/Downloads/heavyskill-reports/{slug}/traces
```

Write each subagent's raw output to `traces/trace-{letter}-{approach-slug}.md` as it arrives, before deliberation. This preserves the full reasoning process.

---

## Step 1: Parallel Subagents

Launch K Agent calls **simultaneously** in the same response. Each subagent gets an isolated context window.

Subagents are `general-purpose` type and have access to `WebSearch` and `WebFetch`. Encourage them to search for grounding evidence, but only cite from reliable sources.

### Reliable Sources (cite only from these categories)

- **学术 / 研究**: arXiv, Nature, Science, ACM, IEEE, major university research blogs
- **权威媒体**: Reuters, AP, Bloomberg, Financial Times, The Economist, MIT Technology Review, Wired
- **官方来源**: 政府公告、官方文档、公司官方发布（博客/公告/财报）
- **行业数据**: Statista, McKinsey, Gartner, IDC（需注明数据年份）
- **中文可信源**: 财新、第一财经、36氪深度报道、人民网政策原文

不要引用：个人博客、Medium 随机文章、未注明来源的统计数字、Twitter/微博传言。

### Subagent Prompt Template

```
Agent(
  subagent_type="general",
  prompt="""
你是这次并行推理的 [A/B/C/D/E] 号分析者。

问题：[ORIGINAL QUESTION]

你的视角：[NAME]
说明：[one-line description]

要求：
- 只用这个视角独立推理，不知道其他分析者的存在
- 把完整的思考过程写出来，不要过早总结
- 有立场，不要为了"平衡"而含糊
- 可以用 WebSearch 和 WebFetch 搜索真实证据
- 只引用可靠来源：arXiv、Nature、Reuters、Bloomberg、FT、MIT Tech Review、
  政府/公司官方公告、Statista/McKinsey/Gartner 报告、财新/36氪深度报道。
  不要引用个人博客、来源不明的统计数字、社交媒体传言。
- 引用时在正文中标注来源和大致时间，如"（Reuters，2025年3月）"

---

写作风格要求（这是最重要的）：

用中文写作。风格参考乔木的公众号写作：
- 短段落，多留白，每段只表达一个焦点
- 重要观点加粗，且独立成段（前后留空行）
- 口语化、有对话感，像在和读者聊天，不是在写学术报告
- 关键结论直接说出来，不要绕
- 禁止用"不是A，而是B"句式（偶尔一次可以）
- 禁止"让我们来分析"、"综上所述"、"总而言之"等教学腔
- 禁止列表项每条都加粗开头

输出格式如下（照这个结构写，但用自然散文，不要填表格的感觉）：

---

## [视角名称]

**切入角度**：[一句话说明从哪个维度进入这个问题]

[2-4段推理。自然段落，不用编号。每段有自己的焦点。重要洞察加粗，加粗句独立成段，前后空行。可以用设问句或"你会发现"制造对话感。]

**我的结论**：[清晰直接，有立场，一到两句]

**最脆弱的前提**：[如果这一点是错的，结论会怎样改变，一句话]

**最有力的依据**：[最能支撑结论的那一条核心证据，一到两句]

**参考来源**：[列出搜索或引用的来源，没有就写"无"]

---
"""
)
```

### Verification Mode — Reasoning Approach Types

| Approach | Entry point | Best for |
|----------|-------------|----------|
| Direct Reasoning | Most natural method | Baseline |
| First Principles | Decompose to fundamentals, rebuild | Conceptual |
| Adversarial | Assume the obvious answer is wrong | Bug finding, verification |
| Edge Case Focus | Reason from boundary conditions inward | Systems, code |
| Constraint Propagation | Start from what CANNOT be true | Logic, math |
| Reverse Engineering | Start from desired outcome, work backward | Design |
| Historical/Empirical | What does comparable evidence show? | Strategy |
| Analogy | Map to a well-understood domain | Unfamiliar problems |

### Deliberation Mode — Perspective Lenses

| Perspective | Cares most about | Fears most |
|-------------|-----------------|------------|
| The Builder | Shipping speed, pragmatism | Over-engineering |
| The Architect | Long-term maintainability, scalability | Technical debt |
| The Skeptic | Risks, failure modes, hidden costs | False confidence |
| The User | Experience, clarity, real-world fit | Theoretical solutions |
| The Economist | ROI, trade-offs, opportunity cost | Sunk-cost traps |
| The Historian | What happened before in similar cases | Repeating mistakes |
| The Contrarian | What everyone is missing | Groupthink |
| The Ethicist | Second-order effects, who gets harmed | Narrow optimization |

---

## Step 1.5: Collection and Shuffle

After all subagents complete:

```
Collect all K outputs.

Convergence cluster: [which agents agree on conclusion / direction?]
Outliers: [which agents diverge, and what is their position?]

Shuffle order before Codex handoff. (Prevents Codex position bias — earlier traces
receive more attention weight. Random order neutralizes this.)
```

Max-Answer-Num rule: focus deliberation on the largest convergence cluster, but never discard strong outliers — they may be the correct minority.

---

## Step 2: Codex Deliberation Prompt

Invoke via `/codex:rescue [prompt]`:

```xml
<task>
You are the deliberation host for a HeavySkill reasoning session.
Mode: [Verification / Deliberation]
Original question: [QUESTION]

[K] independent agents reasoned in complete isolation. Your job:
run the four-step deliberation protocol and produce a final synthesized answer.
Do not defer to majority. Evaluate reasoning quality, not just conclusions.
</task>

<agents>
[SHUFFLED TRACE A]
Approach/Perspective: [name]
Conclusion: [conclusion]
Confidence: [level]
Weakest assumption: [assumption]
Strongest evidence: [evidence]
Key reasoning: [2-3 sentence summary]

[SHUFFLED TRACE B]
...

[SHUFFLED TRACE C]
...
</agents>

<deliberation_protocol>
Step 1 — Classify: query type and required depth of analysis.
Step 2 — Evaluate each agent:
  Sound: reasoning holds, conclusion follows from premises
  Has gaps: conclusion may be right but reasoning has holes
  Flawed: reasoning breaks down; conclusion unreliable
  Do not vote by majority. A minority agent can be more correct.
Step 3 — Re-derive (if ALL agents are Flawed):
  Do NOT synthesize from bad inputs. Re-derive from scratch.
  State explicitly: "All agents had fatal flaws. Re-deriving."
Step 4 — Synthesize across Sound agents:
  Find cross-agent insights invisible to any single agent.
  For Deliberation mode: produce a balanced analysis with a clear recommendation.
</deliberation_protocol>

<output_rules>
Write for a curious, intelligent non-expert reader. No academic jargon. No terms like
"confidence", "Pass@K", "weakest assumption", "convergence", "Sound/Flawed".

Instead write like a thoughtful analyst explaining their thinking to a friend — clear,
direct, a little opinionated where warranted. Follow 乔木写作风格：

- 短段落，多留白，每段只表达一个焦点
- 重要观点加粗，加粗句独立成段（前后留空行）
- 口语化，像聊天不像报告
- 禁止"不是A而是B"句式（偶尔一次可以，不要重复）
- 禁止"让我们来分析"、"综上所述"、"总而言之"等教学腔
- 禁止每个列表项都加粗开头
- 逐段自问：这段提供了新信息吗？没有就删

Output structure (in Chinese unless question was asked in another language):

---

## 讨论之后，我们发现了什么

[2-4 sentences: what emerged from comparing the different angles. What did multiple perspectives
agree on? Where did they sharply disagree, and why does that disagreement matter?
Write this as a narrative, not a list. No bullet points here.]

## 各个角度说了什么

For each agent, write a short paragraph (3-6 sentences) that reads like a mini-essay:
- Name the perspective naturally ("从实用主义的角度来看..." / "历史给了我们一个清醒的提醒...")
- State the core argument in plain language
- Explain the key evidence or reasoning behind it
- Note if this perspective has a significant blind spot — but phrase it as "这个角度容易忽略的是..."
  not as "weakest assumption" or any scoring system

## 我们的判断

[The final answer. This should be the most confident, clear section of the entire output.
Write 2-5 sentences that commit to a position. If the answer is nuanced, be nuanced about
the nuance — but do not hide behind it. End with one punchy sentence that someone can
remember and share.]

## 还值得想想

[1-3 things that genuinely matter for this question that weren't fully resolved.
Write as questions or considerations, not as "key uncertainties". Keep it brief.
If there's a minority view that has real merit, include it here with "另一种值得听的声音：..."]

## 如果你要行动

[Only include if there's a natural next step. 1-3 concrete sentences. Skip this section
entirely if the question is purely analytical with no obvious action implied.]

---
</output_rules>

<grounding_rules>
- Every claim must be traceable to the agent reasoning or your own independent synthesis
- If re-deriving because all agents had fatal reasoning errors, say so plainly in plain language
- Do not present tentative conclusions as firm ones — but do commit where the evidence supports it
- Match the language of the original question
</grounding_rules>
```

---

## Step 3: Report Generation

After receiving Codex output, Claude generates two files.

### Markdown Report

全程白话，没有术语，写给聪明的普通读者，不是学术同行。遵循乔木写作风格：

- 短段落，多留白，视觉舒适
- 重要观点加粗，且加粗句独立成段（前后空行）
- 口语化，有对话感，用"你"和设问句制造互动
- 禁止"不是A而是B"句式（偶尔一次可以）
- 禁止教学腔（"让我们来"、"综上所述"、"总而言之"）
- 禁止每个列表项都粗体开头
- 全文破折号（——）不超过3个

```markdown
# [QUESTION]

> [One sentence that captures why this question matters]

*[DATE] · [K] 个独立视角 · 主持：Codex*

---

## 各个角度怎么看

### [视角名称，用中文，比如"实用主义者的视角"或"悲观者的角度"]

[3-5 sentences written as a mini-essay. State the position, give the core reasoning,
note what this perspective uniquely contributes. End with what it might miss.]

### [下一个视角]

[Same format]

### [再下一个]

[Same format]

---

## 讨论之后，我们发现了什么

[Codex synthesis output, verbatim from the "讨论之后，我们发现了什么" section]

---

## 各个角度说了什么

[Codex per-perspective paragraphs, verbatim]

---

## 我们的判断

[Codex final answer, verbatim — this is the most prominent section]

---

## 还值得想想

[Codex "还值得想想" section, verbatim]

---

## 如果你要行动

[Codex action section, verbatim — omit this section entirely if Codex omitted it]

---

*由 qiaomu-heavyskill 生成 · 基于 HeavySkill 论文方法 (arXiv:2605.02396)*
```

### HTML Report Template

Medium-style: off-white background, near-black text, generous whitespace, large readable type, humanist sans-serif. Professional editorial feel. Self-contained, no external dependencies.

**Design rules**:
- Background `#fafaf9`, body text `#1a1a1a`, muted `#6b7280`, border `#e5e7eb`
- Large body font: 18px, line-height 1.85 — prioritise readability over density
- Headings: heavy weight, tight letter-spacing, generous margin-top
- Trace cards: white background, subtle border + left accent stripe, generous padding, border-radius 8px
- Section separators: single horizontal rule, generous vertical padding
- Pull-quote block for the final verdict: large font, left border accent in dark ink
- Monospace only for tiny labels/meta — body text uses sans-serif throughout
- No dark backgrounds, no shadows — use border and whitespace to create depth
- Max content width: 740px, centered

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>[SHORT QUESTION] — HeavySkill</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,700;0,800;0,900;1,700&family=Source+Serif+4:ital,opsz,wght@0,8..60,400;0,8..60,600;1,8..60,400&family=IBM+Plex+Mono:wght@400;500;600&family=Noto+Serif+SC:wght@400;500;600;700;900&family=Noto+Sans+SC:wght@300;400;500;700&display=swap" rel="stylesheet">
<style>
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  /* ── Monocle 配色（guizang 墨水经典主题） ── */
  --bg:         #f1efea;
  --surface:    #faf9f6;
  --surface-2:  #e8e5de;
  --border:     #dedad4;
  --border-2:   #c4bfb7;
  --text:       #0a0a0b;
  --text-2:     #2c2c2d;
  --text-muted: #6b6760;
  --text-faint: #aaa79f;
  --accent:     #0a0a0b;

  /* ── 字体栈（guizang 三层体系）── */
  --display:    'Playfair Display', 'Source Serif 4', Georgia, serif;
  --serif:      'Noto Serif SC', 'Source Serif 4', source-han-serif-sc, serif;
  --sans:       'Noto Sans SC', source-han-sans-sc, system-ui, sans-serif;
  --mono:       'IBM Plex Mono', 'SFMono-Regular', 'Fira Code', monospace;
  --ui:         var(--sans);
}

html { scroll-behavior: smooth; }
body {
  font-family: var(--ui);
  background: var(--bg);
  color: var(--text);
  font-size: 17px;
  line-height: 1.8;
  -webkit-font-smoothing: antialiased;
}

/* ── Layout ── */
.wrap { max-width: 760px; margin: 0 auto; padding: 0 32px; }

/* ── Nav ── */
.nav {
  position: sticky; top: 0; z-index: 30;
  background: rgba(248,247,244,0.95);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--border);
  height: 54px; padding: 0 32px;
  display: flex; align-items: center; justify-content: space-between;
}
.nav-brand { font-family: var(--serif); font-size: 13px; font-weight: 700; color: var(--text); letter-spacing: -0.01em; }
.nav-meta  { font-family: var(--mono); font-size: 11px; color: var(--text-muted); }

/* ── Hero ── */
.hero { padding: 80px 0 64px; border-bottom: 1px solid var(--border); }
.hero-eyebrow {
  font-family: var(--mono); font-size: 11px; font-weight: 500; letter-spacing: 0.14em;
  text-transform: uppercase; color: var(--text-muted); margin-bottom: 18px;
}
.hero-h1 {
  font-family: var(--serif); font-size: clamp(2rem, 5vw, 3rem);
  font-weight: 900; letter-spacing: -0.03em; line-height: 1.1;
  color: var(--text); margin-bottom: 22px;
}
.hero-sub {
  font-family: var(--sans); font-size: 1.05rem; color: var(--text-muted);
  line-height: 1.75; max-width: 560px; margin-bottom: 32px;
}
.hero-chips { display: flex; gap: 8px; flex-wrap: wrap; }
.chip {
  font-size: 12px; font-weight: 500; color: var(--text-muted);
  background: var(--surface); border: 1px solid var(--border-2);
  border-radius: 100px; padding: 4px 14px;
}

/* ── Section ── */
.sec { padding: 64px 0; border-bottom: 1px solid var(--border); }
.sec:last-of-type { border-bottom: none; }
.sec-num {
  font-family: var(--mono); font-size: 10px; font-weight: 500;
  letter-spacing: 0.14em; text-transform: uppercase;
  color: var(--text-faint); margin-bottom: 6px;
}
.sec-title {
  font-family: var(--serif); font-size: 1.4rem; font-weight: 700; letter-spacing: -0.02em;
  color: var(--text); margin-bottom: 36px;
}

/* ── Trace grid ── */
.trace-grid {
  display: grid; grid-template-columns: 1fr 1fr; gap: 1px;
  background: var(--border); border: 1px solid var(--border); border-radius: 12px;
  overflow: hidden;
}
@media (max-width: 580px) { .trace-grid { grid-template-columns: 1fr; } }

.trace {
  background: var(--surface); padding: 30px 28px;
  transition: background 0.12s;
  /* No border-left accent — colored left stripes look cheap */
}
.trace:hover { background: #fdfcfa; }

.trace-label {
  font-family: var(--mono); font-size: 10px; font-weight: 500;
  letter-spacing: 0.12em; text-transform: uppercase;
  color: var(--tc, var(--text-faint)); opacity: 0.7; margin-bottom: 14px;
}
.trace-pick {
  /* Plain colored text, no border box */
  font-family: var(--mono); font-size: 11px; font-weight: 600;
  color: var(--tc, var(--text-muted));
  display: block; margin-bottom: 14px; letter-spacing: 0.03em;
}
.trace-name {
  font-family: var(--serif); font-size: 16px; font-weight: 700; letter-spacing: -0.01em;
  color: var(--text); margin-bottom: 14px;
}
.trace-body { font-family: var(--sans); font-size: 14px; line-height: 1.78; color: var(--text-2); margin-bottom: 18px; }
.trace-blind {
  font-family: var(--sans); font-size: 12px; color: var(--text-muted); line-height: 1.65;
  border-top: 1px solid var(--border); padding-top: 12px;
}
.trace-blind::before { content: '盲点 → '; color: var(--text-faint); font-family: var(--mono); font-size: 10px; font-weight: 600; }

/* ── Discovery ── */
.discovery {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: 12px; padding: 36px;
}
.discovery p { font-family: var(--sans); font-size: 17px; line-height: 1.9; color: var(--text-2); }
.discovery p + p { margin-top: 1.3em; }
.discovery strong { font-family: var(--serif); color: var(--text); font-weight: 700; }

/* ── Perspective prose ── */
.persp + .persp { margin-top: 2.5em; padding-top: 2.5em; border-top: 1px solid var(--border); }
.persp-tag {
  font-family: var(--mono); font-size: 10px; font-weight: 500; letter-spacing: 0.14em;
  text-transform: uppercase; color: var(--text-muted); margin-bottom: 12px;
}
.persp p { font-family: var(--sans); font-size: 17px; line-height: 1.9; color: var(--text-2); }
.persp p + p { margin-top: 1em; }
.persp strong { font-family: var(--serif); color: var(--text); font-weight: 700; }

/* ── Verdict ── */
.verdict {
  background: var(--text); color: var(--bg);
  border-radius: 12px; padding: 48px; margin-bottom: 24px;
}
.verdict-tag {
  font-family: var(--mono); font-size: 10px; font-weight: 500;
  letter-spacing: 0.14em; text-transform: uppercase;
  color: rgba(255,255,255,0.4); margin-bottom: 18px;
}
.verdict-text {
  font-family: var(--serif); font-size: clamp(1.15rem, 2.5vw, 1.6rem);
  font-weight: 700; line-height: 1.5; letter-spacing: -0.02em;
  color: #ffffff;
}

/* ── Supp grid ── */
.supp-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
@media (max-width: 540px) { .supp-grid { grid-template-columns: 1fr; } }

.supp {
  background: var(--surface); border: 1px solid var(--border);
  border-radius: 10px; padding: 22px 24px;
}
.supp-tag {
  font-family: var(--mono); font-size: 10px; font-weight: 500; letter-spacing: 0.12em;
  text-transform: uppercase; color: var(--text-muted); margin-bottom: 10px;
}
.supp-body { font-family: var(--sans); font-size: 14px; line-height: 1.75; color: var(--text-2); }
.supp-body p + p { margin-top: 0.7em; }
.supp-body code {
  font-family: var(--mono); font-size: 12px;
  background: var(--surface-2); border: 1px solid var(--border);
  border-radius: 3px; padding: 1px 5px;
}

/* ── Aside / voice callout ── */
.callout-voice {
  background: #fffbeb; border: 1px solid #fde68a;
  border-radius: 10px; padding: 20px 24px;
  grid-column: 1 / -1;
}
.callout-voice .supp-tag { color: #92400e; }
.callout-voice .supp-body { color: #78350f; }

/* ── Decision table ── */
.dtable {
  width: 100%; border-collapse: collapse;
  font-size: 14px; margin-bottom: 32px;
}
.dtable thead tr { border-bottom: 2px solid var(--border-2); }
.dtable th {
  text-align: left; padding: 10px 14px;
  font-size: 11px; font-weight: 700; letter-spacing: 0.04em;
  color: var(--text-muted); background: var(--surface-2);
}
.dtable td {
  padding: 13px 14px; border-bottom: 1px solid var(--border);
  color: var(--text-2); vertical-align: top; line-height: 1.5;
}
.dtable tr:last-child td { border-bottom: none; }
.dtable tr:hover td { background: rgba(0,0,0,0.015); }
.dlib { font-family: var(--mono); font-size: 13px; font-weight: 700; color: var(--text); }

/* ── Footer ── */
.footer {
  padding: 40px 0; border-top: 1px solid var(--border);
  display: flex; justify-content: space-between; align-items: center;
  flex-wrap: wrap; gap: 10px;
}
.footer span { font-size: 12px; color: var(--text-muted); }

/* ── Print / PDF ── */
@media print {
  @page {
    size: A4;
    margin: 20mm 22mm;
  }
  /* 打印时切换为衬线正文，更有书感 */
  body {
    font-family: 'Noto Serif SC', 'Source Serif 4', Georgia, serif;
    font-size: 13.5px; background: #fff; color: #0a0a0b;
    -webkit-print-color-adjust: exact; print-color-adjust: exact;
  }
  .nav { display: none; }
  .wrap { max-width: 100%; padding: 0; }
  .hero { padding: 32px 0 20px; border-bottom: 1.5px solid #0a0a0b; }
  .hero-eyebrow { font-family: 'IBM Plex Mono', monospace; }
  .hero-h1 { font-size: 2rem; line-height: 1.1; }
  .hero-sub { font-family: 'Noto Sans SC', sans-serif; font-size: 0.95rem; }
  .hero-chips { display: none; }
  .sec { padding: 24px 0 20px; break-inside: avoid; }
  .sec-num { font-family: 'IBM Plex Mono', monospace; }
  .trace-grid { display: block; background: none; border: none; border-radius: 0; }
  .trace {
    border: 0.5px solid #c4bfb7;
    border-radius: 0; margin-bottom: 8px; break-inside: avoid;
    padding: 16px 18px;
  }
  .trace-name { font-family: 'Noto Serif SC', serif; }
  .trace-body { font-family: 'Noto Sans SC', sans-serif; font-size: 12.5px; }
  .discovery { border: 0.5px solid #c4bfb7; border-radius: 0; break-inside: avoid; }
  .discovery p { font-size: 14px; }
  .persp p { font-size: 14px; }
  .supp-grid { display: block; }
  .supp { margin-bottom: 8px; break-inside: avoid; border-radius: 0; }
  .supp-tag { font-family: 'IBM Plex Mono', monospace; }
  .callout-voice { border-radius: 0; break-inside: avoid; }
  .verdict {
    background: #0a0a0b !important; color: #f1efea !important;
    border-radius: 0; break-inside: avoid; padding: 32px;
  }
  .verdict-text { font-size: 1.15rem; line-height: 1.55; }
  a { color: inherit; text-decoration: none; }
  .footer { padding: 16px 0; }
}
</style>
</head>
<body>

<nav class="nav">
  <span class="nav-brand">HeavySkill 深度分析</span>
  <span class="nav-meta">[DATE]</span>
</nav>

<div class="wrap">

  <!-- Hero -->
  <div class="hero">
    <div class="hero-eyebrow">多角度推理报告</div>
    <h1 class="hero-h1">[QUESTION]</h1>
    <p class="hero-sub">[一句话说明这个问题为什么值得深度分析，或者核心洞察是什么]</p>
    <div class="hero-chips">
      <span class="chip">[K] 个独立视角</span>
      <span class="chip">Codex 主持讨论</span>
      <span class="chip">[Verification / Deliberation] 模式</span>
    </div>
  </div>

  <!-- 各个角度怎么看 -->
  <div class="sec">
    <div class="sec-num">01</div>
    <div class="sec-title">各个角度怎么看</div>
    <!-- Trace accent colors: A=#0f0f0f B=#16a34a C=#7c3aed D=#d97706 E=#0284c7 -->
    <div class="trace-grid">

      <div class="trace" style="--tc:#0f0f0f">
        <div class="trace-label">视角 A</div>
        <div class="trace-name">[视角名称，用中文]</div>
        <span class="trace-pick">[结论关键词，2-4字]</span>
        <div class="trace-body">[这个视角的核心观点，2-3句，普通人能读懂]</div>
        <div class="trace-blind">[这个视角忽视了什么]</div>
      </div>

      <div class="trace" style="--tc:#16a34a">
        <div class="trace-label">视角 B</div>
        <div class="trace-name">[视角名称]</div>
        <span class="trace-pick">[结论关键词]</span>
        <div class="trace-body">[核心观点]</div>
        <div class="trace-blind">[盲点]</div>
      </div>

      <div class="trace" style="--tc:#7c3aed">
        <div class="trace-label">视角 C</div>
        <div class="trace-name">[视角名称]</div>
        <span class="trace-pick">[结论关键词]</span>
        <div class="trace-body">[核心观点]</div>
        <div class="trace-blind">[盲点]</div>
      </div>

      <!-- 如有 D (#d97706) E (#0284c7) 按需添加 -->

    </div>
  </div>

  <!-- 讨论之后，我们发现了什么 -->
  <div class="sec">
    <div class="sec-num">02</div>
    <div class="sec-title">讨论之后，我们发现了什么</div>
    <div class="discovery">
      <p>[Codex "讨论之后，我们发现了什么" 原文，叙事风格，2-4段]</p>
    </div>
  </div>

  <!-- 各个角度说了什么 -->
  <div class="sec">
    <div class="sec-num">03</div>
    <div class="sec-title">每个视角说了什么</div>

    <div class="persp">
      <div class="persp-tag">[视角名称]</div>
      <p>[Codex 对这个视角的叙述性段落，3-6句，有评价有温度]</p>
    </div>
    <div class="persp">
      <div class="persp-tag">[视角名称]</div>
      <p>[段落]</p>
    </div>
    <div class="persp">
      <div class="persp-tag">[视角名称]</div>
      <p>[段落]</p>
    </div>
    <!-- 按视角数量添加 -->

  </div>

  <!-- 我们的判断 -->
  <div class="sec">
    <div class="sec-num">04</div>
    <div class="sec-title">我们的判断</div>

    <div class="verdict">
      <div class="verdict-tag">Codex · 综合结论</div>
      <div class="verdict-text">[最终结论 — 清晰、直接、有立场。有一句让人能记住的话。]</div>
    </div>

    <!-- 如有决策场景速查表，在此插入 .dtable（可选） -->
    <!--
    <table class="dtable">
      <thead><tr><th>场景</th><th>推荐</th><th>理由</th></tr></thead>
      <tbody>
        <tr><td>[场景描述]</td><td><span class="dlib">[库/方案名]</span></td><td>[一句话理由]</td></tr>
      </tbody>
    </table>
    -->

    <div class="supp-grid">
      <div class="supp">
        <div class="supp-tag">还值得想想</div>
        <div class="supp-body"><p>[Codex "还值得想想" 内容]</p></div>
      </div>
      <div class="supp">
        <div class="supp-tag">如果你要行动</div>
        <div class="supp-body"><p>[Codex 行动建议，如无则省略此卡片]</p></div>
      </div>
      <!-- 只在有少数值得听的声音时加 -->
      <!--
      <div class="callout-voice">
        <div class="supp-tag">另一种值得听的声音</div>
        <div class="supp-body"><p>[少数但有价值的观点]</p></div>
      </div>
      -->
    </div>
  </div>

  <div class="footer wrap">
    <span>qiaomu-heavyskill · arXiv:2605.02396</span>
    <span>Deliberation by Codex</span>
  </div>

</div>
</body>
</html>
```

**Trace card accent colors**:
- A: `#0f0f0f` (near-black)
- B: `#16a34a` (forest green)
- C: `#7c3aed` (violet)
- D: `#d97706` (amber)
- E: `#0284c7` (sky blue)

---

## Step 3.5: PDF Export

After writing `{slug}.html`, export a PDF using Chrome headless. Run this bash command:

```bash
SLUG="{slug}"
REPORT_DIR="$HOME/Downloads/heavyskill-reports/${SLUG}"
HTML_FILE="${REPORT_DIR}/${SLUG}.html"
PDF_FILE="${REPORT_DIR}/${SLUG}.pdf"

# Try Chrome locations in order
CHROME=""
for path in \
  "/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  "/Applications/Chromium.app/Contents/MacOS/Chromium" \
  "google-chrome" \
  "chromium"; do
  if [ -x "$path" ] || command -v "$path" &>/dev/null 2>&1; then
    CHROME="$path"; break
  fi
done

if [ -n "$CHROME" ]; then
  "$CHROME" \
    --headless --disable-gpu \
    --run-all-compositor-stages-before-draw \
    --print-to-pdf="$PDF_FILE" \
    --no-margins \
    "file://${HTML_FILE}" 2>/dev/null \
  && echo "PDF saved: $PDF_FILE" \
  || echo "Chrome PDF export failed"
else
  echo "Chrome not found — open ${HTML_FILE} in browser and use File > Print > Save as PDF"
fi
```

If Chrome is not available, tell the user to open the HTML in a browser and use **File → Print → Save as PDF** — the `@media print` styles in the HTML are already optimised for this.

The PDF is the primary shareable artifact. It contains the complete report: traces overview, Codex deliberation findings, and the final verdict block — all in one document.

---

## Step 4: Optional Iteration (Max 2 Rounds)

Paper Figure 4: HM@K improves per round but HP@K degrades after round 2.

If Codex confidence is Low or Medium:
- Feed original traces (reshuffled) + Codex Round 1 synthesis as a new trace
- Run same Codex prompt with K+1 traces
- Stop after round 2 — do not iterate further

---

## Common Mistakes

| Mistake | Correct approach |
|---------|-----------------|
| Sequential traces in same context | Launch K subagents in parallel — isolated contexts |
| Verification approach types for open questions | Use Deliberation perspective lenses instead |
| Claude does the deliberation | Always handoff to Codex via `/codex:rescue` |
| Paraphrase Codex output | Verbatim in conversation and in report |
| Skip the HTML report | Markdown, HTML, and PDF are all mandatory outputs |
| Skip PDF export | Run the Chrome headless command in Step 3.5 — PDF is the shareable artifact |
| Synthesize when all traces are flawed | Codex must re-derive from scratch — prompt makes this explicit |
| More than 2 deliberation rounds | HP@K degrades — stop at round 2 |
