# qiaomu-heavyskill

> Multi-perspective deep reasoning for Claude Code — parallel subagents think independently, Codex deliberates, you get a polished Markdown + HTML report.
>
> 多视角深度推理 Skill——让 Claude Code 真正「深想」一个问题，而不只是给你一个答案。

**[English](#english) | [中文](#中文)**

---

<a name="english"></a>
## English

### What problem does this solve?

When you ask an AI a hard question, it gives you *one* answer from *one* angle. That's fine for simple lookups. For decisions that matter — tech stack choices, strategic trade-offs, complex analysis — you want multiple independent perspectives, evaluated critically, synthesized into a conclusion that's better than any single view.

HeavySkill does exactly that:

1. **K independent subagents** reason in parallel, each in a fully isolated context (not the same window — true independence)
2. **Codex hosts deliberation** — critically evaluates each trace, identifies blind spots, synthesizes a conclusion that can be absent from every individual trace
3. **Claude renders** a readable Markdown report + a polished single-page HTML report

Based on [arXiv:2605.02396](https://arxiv.org/abs/2605.02396) — key finding: deliberation is *generative*, not just aggregative. The synthesizer regularly produces correct answers that no individual trace reached.

### Prerequisites

- [ ] **Claude Code** installed and running (`claude --version`)
- [ ] **OpenAI Codex plugin** installed in Claude Code — required for the deliberation host
  ```bash
  # Install via Claude Code plugin marketplace
  /plugin marketplace add openai/codex-plugin-cc
  # Then authenticate
  /codex:setup
  ```
- [ ] `skills` CLI for installation (optional but recommended)
  ```bash
  npx skills --version
  ```

### Installation

```bash
npx skills add joeseesun/qiaomu-heavyskill
```

Or clone manually:

```bash
git clone https://github.com/joeseesun/qiaomu-heavyskill ~/.agents/skills/qiaomu-heavyskill
```

### Usage — just talk to Claude

```
"DeepSeek V4 发布对中国的影响，多角度分析一下"

"think harder: should we use microservices or a monolith for this project?"

"讨论一下 React vs Vue 的技术选型"

"分析利弊：把产品做成 SaaS 还是本地部署"

"heavyskill: 最好的所见即所得 Markdown 开源编辑库是哪个"
```

### Two modes

| Mode | When to use | Example |
|------|-------------|---------|
| **Verification** | Question has a correct or clearly better answer | Bug analysis, math, logic, factual claims |
| **Deliberation** | No single right answer; multiple valid views | Tech stack, product strategy, social topics |

### Output

Each run produces a folder in `~/Downloads/heavyskill-reports/{slug}-{date}/`:

```
├── traces/
│   ├── trace-a-{perspective}.md   ← each subagent's full reasoning
│   ├── trace-b-{perspective}.md
│   └── ...
├── deliberation.md                ← Codex raw output (verbatim)
├── {slug}.md                      ← final Markdown report
└── {slug}.html                    ← polished single-page HTML report
```

The HTML report uses an editorial Medium-style design: off-white background, large readable type, generous whitespace, dark verdict block.

### Limitations

- Requires the OpenAI Codex plugin — without it, the deliberation step cannot run
- Each run launches K parallel subagents (3–5), which counts against your Claude usage
- Best for questions that genuinely benefit from multiple perspectives; overkill for simple factual questions
- Maximum 2 deliberation rounds (per paper findings: quality degrades after round 2)

### Troubleshooting

| Problem | Solution |
|---------|----------|
| "codex:codex-rescue not found" | Run `/codex:setup` and authenticate with your OpenAI account |
| Subagents return empty output | Check Claude Code permissions — subagents need Agent tool access |
| HTML report not opening | Reports are saved to `~/Downloads/heavyskill-reports/` — check there |
| Codex output looks wrong | Run `/codex:setup` to verify Codex CLI is installed and authenticated |

### Acknowledgements

- **HeavySkill paper**: [arXiv:2605.02396](https://arxiv.org/abs/2605.02396) — the research this skill is based on
- **OpenAI Codex plugin**: [codex-plugin-cc](https://github.com/openai/codex-plugin-cc) — the deliberation host
- Writing style: 乔木 ([@vista8](https://x.com/vista8))

---

<a name="中文"></a>
## 中文

### 这个 Skill 解决什么问题？

问 AI 一个难问题，它给你一个角度的一个答案。对简单问题够用。对真正重要的决策——技术选型、战略权衡、复杂分析——你需要多个独立视角，批判性地评估，再综合出一个比任何单一视角都更扎实的结论。

HeavySkill 做的就是这件事：

1. **K 个独立 Subagent** 并行推理，每个在完全隔离的上下文里（不是同一个窗口——是真正的独立，不是"被要求忽略前面"的假独立）
2. **Codex 主持讨论**——批判性评估每条推理链，找出盲点，综合出一个比任何单一视角都更好的结论
3. **Claude 生成报告**——可读的 Markdown 报告 + 精致的单页 HTML 报告

基于 [arXiv:2605.02396](https://arxiv.org/abs/2605.02396) ——核心发现：讨论是**生成性的**，不只是聚合。综合者能产出所有单一视角都没有得出的正确答案。

### 前置条件

- [ ] **Claude Code** 已安装（`claude --version`）
- [ ] **OpenAI Codex 插件** 已安装——这是讨论主持人，没有它无法运行
  ```bash
  /plugin marketplace add openai/codex-plugin-cc
  /codex:setup
  ```
- [ ] `skills` CLI（可选）
  ```bash
  npx skills --version
  ```

### 安装

```bash
npx skills add joeseesun/qiaomu-heavyskill
```

或手动克隆：

```bash
git clone https://github.com/joeseesun/qiaomu-heavyskill ~/.agents/skills/qiaomu-heavyskill
```

### 触发方式

直接和 Claude 说就行，不需要记命令：

```
"DeepSeek V4 发布对中国的影响，多角度分析一下"
"讨论一下 React vs Vue 的技术选型"
"分析利弊：把产品做成 SaaS 还是本地部署"
"heavyskill: 最好的所见即所得 Markdown 开源编辑库"
"think harder: 微服务还是单体架构"
```

### 两种模式

| 模式 | 什么时候用 | 举例 |
|------|-----------|------|
| **验证模式** | 问题有正确答案或更好的答案 | Bug 分析、数学、逻辑、事实核查 |
| **讨论模式** | 没有唯一正确答案，多个立场都成立 | 技术选型、产品策略、社会话题 |

### 输出文件

每次运行在 `~/Downloads/heavyskill-reports/{slug}-{date}/` 生成：

```
├── traces/
│   ├── trace-a-{视角}.md    ← 每个 subagent 的完整推理过程
│   ├── trace-b-{视角}.md
│   └── ...
├── deliberation.md          ← Codex 原始输出（逐字保留）
├── {slug}.md                ← 最终 Markdown 报告
└── {slug}.html              ← 精致单页 HTML 报告
```

HTML 报告采用杂志编辑风格：暖白背景、大字号、充足留白、深色结论区块。

### 限制说明

- 必须安装 OpenAI Codex 插件，否则讨论步骤无法运行
- 每次启动 3–5 个并行 Subagent，会消耗相应的 Claude 用量
- 适合真正需要多角度的问题；简单问题用这个是浪费
- 最多 2 轮讨论（论文数据：第 2 轮后质量下降）

### 常见问题

| 问题 | 解决方法 |
|------|---------|
| 提示"codex:codex-rescue not found" | 运行 `/codex:setup` 并用 OpenAI 账号认证 |
| Subagent 返回空输出 | 检查 Claude Code 权限，Subagent 需要 Agent 工具访问权限 |
| 找不到生成的报告 | 报告保存在 `~/Downloads/heavyskill-reports/` |
| Codex 输出异常 | 运行 `/codex:setup` 验证 Codex CLI 安装和认证状态 |

### 致谢

- **HeavySkill 论文**：[arXiv:2605.02396](https://arxiv.org/abs/2605.02396)
- **OpenAI Codex 插件**：[codex-plugin-cc](https://github.com/openai/codex-plugin-cc)
- 乔木（[@vista8](https://x.com/vista8)）

---

*Made with [Claude Code](https://claude.ai/code)*
