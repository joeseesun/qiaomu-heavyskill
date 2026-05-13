# qiaomu-heavyskill

> 让 Claude Code 真正「深想」一个问题——多个 AI 独立推理，Codex 主持讨论，给你一份比任何单一答案都更扎实的报告。
>
> Multi-perspective deep reasoning for Claude Code — parallel subagents think independently, Codex deliberates, you get a polished HTML report.

**[中文](#中文) | [English](#english)**

---

<a name="中文"></a>
## 中文

### 你有没有这种感受

问 AI 一个真正难的问题，它给你一个答案，一个角度。

听起来有道理，但你总觉得哪里不对。换个角度问，答案又变了。你不知道该信哪个，也不知道自己是不是漏掉了什么关键的东西。

**HeavySkill 解决的就是这个问题。**

它让 3–5 个完全独立的 AI 并行思考同一个问题（真正的隔离上下文，不是"被要求忽略前面"的假独立），再由 Codex 主持一场批判性讨论——找出每个视角的盲点，综合出一个比任何单一推理都更深的结论。

论文数据（[arXiv:2605.02396](https://arxiv.org/abs/2605.02396)）证明：讨论是**生成性**的。综合者能产出所有单一视角都没有想到的正确答案。

### 安装

```bash
npx skills add joeseesun/qiaomu-heavyskill
```

### 直接和 Claude 说

不需要记命令，用自然语言触发：

```
"DeepSeek V4 发布对中国的影响，多角度分析一下"
"讨论一下 React vs Vue 的技术选型"
"分析利弊：把产品做成 SaaS 还是本地部署"
"heavyskill: 最好的所见即所得 Markdown 开源编辑库"
"think harder: 微服务还是单体架构"
```

### 你会得到什么

每次运行在 `~/Downloads/heavyskill-reports/` 生成一个文件夹：

```
├── traces/
│   ├── trace-a-{视角}.md    ← 每个独立 AI 的完整推理过程
│   ├── trace-b-{视角}.md
│   └── ...
├── deliberation.md          ← Codex 讨论原始输出（逐字保留）
├── {slug}.md                ← 最终 Markdown 报告
├── {slug}.html              ← 精致单页 HTML 报告
└── {slug}.pdf               ← 合并 PDF（推理过程 + 讨论 + 最终结论，一份完整文档）
```

**PDF 是最终的可分享产物**——把推理过程、Codex 讨论发现、最终判断合并成一份文档，可以直接发给别人。HTML 报告采用杂志编辑风格，打印 CSS 已专门优化，也可以从浏览器 File → Print → Save as PDF 导出。

### 两种模式

| 模式 | 什么时候用 | 举例 |
|------|-----------|------|
| **验证模式** | 问题有更好的答案 | Bug 分析、数学推导、逻辑验证 |
| **讨论模式** | 没有唯一正确答案 | 技术选型、产品策略、架构权衡 |

### 前置条件

- [ ] **Claude Code** 已安装（`claude --version`）
- [ ] **OpenAI Codex 插件** 已安装——Codex 是讨论主持人，没有它讨论步骤无法运行
  ```bash
  /plugin marketplace add openai/codex-plugin-cc
  /codex:setup
  ```

### 限制说明

- 必须安装 OpenAI Codex 插件
- 每次启动 3–5 个并行 Subagent，消耗相应 Claude 用量
- 适合真正需要多视角的问题；简单问题用这个是浪费
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

<a name="english"></a>
## English

### The problem

You ask AI a hard question. It gives you one answer, from one angle.

Sounds reasonable. But you're not sure you're getting the full picture. Ask it from a different angle, you get a different answer. You don't know which to trust, or what you're missing.

**HeavySkill fixes this.**

It runs 3–5 fully independent AI agents in parallel — each in a truly isolated context, not just "instructed to ignore prior reasoning" — then uses Codex to host a critical deliberation: identify each trace's blind spots, synthesize a conclusion that goes deeper than any single view.

Key finding from [arXiv:2605.02396](https://arxiv.org/abs/2605.02396): deliberation is *generative*, not just aggregative. The synthesizer regularly produces correct answers absent from every individual trace.

### Installation

```bash
npx skills add joeseesun/qiaomu-heavyskill
```

### Usage — just talk to Claude

```
"DeepSeek V4 发布对中国的影响，多角度分析一下"
"think harder: should we use microservices or a monolith?"
"heavyskill: best WYSIWYG Markdown open source editor"
"分析利弊：SaaS vs on-premise deployment"
```

### Output

Each run creates a folder in `~/Downloads/heavyskill-reports/{slug}-{date}/`:

```
├── traces/
│   ├── trace-a-{perspective}.md   ← each agent's full reasoning
│   ├── trace-b-{perspective}.md
│   └── ...
├── deliberation.md                ← Codex raw output (verbatim)
├── {slug}.md                      ← final Markdown report
├── {slug}.html                    ← polished single-page HTML report
└── {slug}.pdf                     ← combined PDF (traces + deliberation + verdict)
```

**The PDF is the primary shareable artifact** — everything in one document. The HTML uses Medium editorial style with print-optimised CSS, so browser Print → Save as PDF works too if Chrome headless isn't available.

### Two modes

| Mode | When | Example |
|------|------|---------|
| **Verification** | Has a correct/better answer | Bug analysis, math, logic |
| **Deliberation** | No single right answer | Tech stack, strategy, architecture |

### Prerequisites

- [ ] **Claude Code** installed (`claude --version`)
- [ ] **OpenAI Codex plugin** — required for the deliberation host
  ```bash
  /plugin marketplace add openai/codex-plugin-cc
  /codex:setup
  ```

### Troubleshooting

| Problem | Solution |
|---------|----------|
| "codex:codex-rescue not found" | Run `/codex:setup` and authenticate with your OpenAI account |
| Subagents return empty output | Check Claude Code permissions — subagents need Agent tool access |
| HTML report not found | Reports are saved to `~/Downloads/heavyskill-reports/` |
| Codex output looks wrong | Run `/codex:setup` to verify Codex CLI is installed and authenticated |

### Acknowledgements

- **HeavySkill paper**: [arXiv:2605.02396](https://arxiv.org/abs/2605.02396)
- **OpenAI Codex plugin**: [codex-plugin-cc](https://github.com/openai/codex-plugin-cc)
- Writing style: 乔木 ([@vista8](https://x.com/vista8))

---

*Made with [Claude Code](https://claude.ai/code)*
