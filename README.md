# story-to-script｜剧本写手

**一个不许 60 秒的剧本写成 3 分钟的创作 skill。**

[![License: MIT](https://img.shields.io/badge/license-MIT-2ea44f?style=flat-square)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/format-agent%20skills-6e4aff?style=flat-square)](https://agentskills.io)
[![Works with](https://img.shields.io/badge/works%20with-Claude%20Code%20·%20Codex%20·%20Cursor%20·%20Gemini%20CLI%20·%20Copilot-1f2328?style=flat-square)](#安装)
[![Discussions](https://img.shields.io/badge/discussions-open-0969da?style=flat-square)](https://github.com/oleo17/story-to-script/discussions)

从灵感、项目档案、大纲，到完整剧本、台词精修、资产库、AI 视频分镜，再到多模型视频提示词与后期声音设计。

支持 Claude Code、OpenAI Codex、Cursor、Gemini CLI、GitHub Copilot、WorkBuddy。纯 Markdown，无脚本、无依赖、不需要 API key。

> **English?** There's a native English edition, built on English speech rates and industry conventions rather than translated: [story-to-script-en](https://github.com/oleo17/story-to-script-en)

---

## 它解决什么

你让 AI 写一个 60 秒竖屏短剧，它给你三分钟的量。场次翻倍、钩子掉到第 8 秒，等到剪辑才发现，只能重来。

这个 skill 干的第一件事不是写，是**先出一张【剧本规格卡】**——时长、场次、镜数、字数、台词量的硬预算，写在第一个字之前。后面所有环节都在这张卡里花预算。「写得更完整」不构成超预算的理由。

时长也不是你报多少就多少，而是由「事件颗粒」反推：

```text
事件颗粒 G = 一次可见的状态改变。
计数：有人做了付代价的决定 / 信息被揭示 / 两人关系位移 / 物件或环境不可逆改变。
不计：氛围、内心感受、重复动作、同一件事换个机位。

T_max = G × 20 秒      T_min = G × 8 秒
```

四颗粒 = 80 秒上限。这个数字在动笔前定，不是在剪辑台上发现。

---

## 快速开始

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.claude/skills/story-to-script
```

重启会话，然后直接说任务（比如「帮我把这个灵感写成 60 秒竖屏短剧」），或者用 `/story-to-script` 点名调用。

其他工具与多 agent 装法见 [安装](#安装)。

---

## 能力范围

| 阶段 | 模块 | 产出 |
|---|---|---|
| 体量 | `00` | 【剧本规格卡】：档位 / 时长 / 场次 / 镜数 / 字数 / 台词量 / 决策往返上限 |
| 创作 | `01` `01b` `02` `03` | 项目档案、大纲、完整剧本、剧本会诊、台词精修 |
| 资产 | `05` | 角色 / 场景 / 道具资产库、参考图整理、连续性方案 |
| 分镜 | `04` `04b` `04f` | AI 视频分镜表、动作设计、打戏导演 |
| 提示词 | `06` | Seedance 2.0 / 2.5 / MiniMax H3 多模型并行输出 |
| 后期 | `07` | BGM、音乐搜索词、AI 音乐提示词、音效、静默点、声音方案 |

`SKILL.md` 只做路由。60 秒的片子不会把 41 篇规则全部拖进上下文——只读当前请求真正需要的那两三个模块。

---

## 安装

本 skill 遵循开放的 [Agent Skills](https://agentskills.io) 标准：`SKILL.md` 只有 `name` + `description` 两个 frontmatter 字段，正文为纯 Markdown，不含脚本、无运行时依赖。

**同一份文件可以直接放进任何支持该标准的 agent，不需要改一行内容。**

| 工具 | 用户级目录 | 项目级目录 |
|---|---|---|
| **通用**（Codex / Cursor / Gemini CLI / Copilot 都会读） | `~/.agents/skills/` | `.agents/skills/` |
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| Cursor | `~/.cursor/skills/` | `.cursor/skills/` |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` |
| GitHub Copilot / VS Code | `~/.copilot/skills/` | `.github/skills/` |
| WorkBuddy | `~/.workbuddy/skills/` | `.workbuddy/skills/` |

### Claude Code

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.claude/skills/story-to-script
```

装完用 `/story-to-script` 显式调用，或直接描述任务让它自动匹配。

### 一次装到多个 agent

`~/.agents/skills/` 是被多数工具共同读取的位置，装一份即可覆盖 Codex、Cursor、Gemini CLI、Copilot：

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.agents/skills/story-to-script
```

注意：**Claude Code 只读 `.claude/skills/`，Codex 只读 `.agents/skills/`**。要两者都能用，各放一份。

### WorkBuddy

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.workbuddy/skills/story-to-script
```

### 软链接（推荐，避免多份副本不同步）

```bash
git clone https://github.com/oleo17/story-to-script.git ~/skills/story-to-script
ln -s ~/skills/story-to-script ~/.claude/skills/story-to-script
ln -s ~/skills/story-to-script ~/.agents/skills/story-to-script
```

**模型能力会影响效果。** 这里的关键约束都是硬规则——先数事件颗粒、不许注水填充时长。Claude Code（Opus / Sonnet）、Cursor 等强模型遵守度较好；小模型或上下文窗口窄的模型容易跳过 Step 0 直接开写。如果遇到，值得报个 issue。

---

## 目录结构

```text
story-to-script/
├── SKILL.md              # 总控入口：路由、全局红线、Step 0 体量监控
├── references/           # 各专科规则（按需读取，不必全读）
│   ├── 00-scale-control.md          # 体量监控与剧本规格卡
│   ├── 01-*.md  01b-*.md            # 剧本创作、模式、节奏矩阵、边界示例
│   ├── 02-*.md                      # 剧本会诊、启动流程、规则粒度
│   ├── 03-dialogue-expert.md        # 台词专项
│   ├── 04-*.md  04b-*.md  04f-*.md  # 分镜、动作与打戏导演
│   ├── 05-*.md                      # 风格、角色、场景、道具资产库
│   ├── 06-*.md                      # 多模型视频提示词与各模型 adapter
│   └── 07-post-music-sound-design.md # 后期音乐与声音设计
└── agents/openai.yaml    # 展示名、简介与默认调用语
```

`SKILL.md` 按「最早缺失环节」引导用户走到正确模块，不会一次性把所有 reference 塞进上下文。

---

## 四档速查

| 档位 | 时长 | 事件颗粒 G | 场次 | 镜数 | 台词量 |
|---|---|---|---|---|---|
| S 微 | 15–120s | 1–6 | 1–3 | 5–15 | ≤6 句 / ≤60 字 |
| M 小 | 2–8min | 7–25 | 3–8 | 25–50 | ≤40 句 / ≤600 字 |
| L 中 | 10–30min | 26–90 | 8–20 | 60–150 | ≤150 句 |
| XL 大 | ≥40min 或 ≥6 集 | ≥90 | 分集 | 分集 | 逐集预算 |

档位由事件颗粒决定，不由用户口头报的时长决定——这是防止篇幅失控的关键。

---

## 使用示例

```text
用 story-to-script 把这个灵感做成 60 秒竖屏短剧：
一个外卖骑手在暴雨夜送最后一单，发现收件人是三年前不告而别的女友。
```

Skill 会先数事件颗粒、落到 S 档、出规格卡，确认后再走剧本 → 分镜 → Seedance 多模型提示词，全程不超预算。

也可以从中间插入。直接说「给我出分镜」，它会先提醒一句缺资产库，然后照做——不会拦着你，也不会假装没看见。

---

## 几个刻意的取舍

三条看起来别扭、但能省掉一次重写的设计。

**拒绝你的时长，是功能不是毛病。** 要一个 45 秒的复仇片，你会被告知：复仇这个承诺需要 6 个颗粒，45 秒已经是下限，等于每颗粒 7.5 秒——这是压缩作业，不是常规稿。它宁可说清楚，也不悄悄写一个 90 秒的东西交给你。

**只谈代价，不谈主题。** 「这个故事讲的是宽恕」不可执行。「她得交出唯一的证人才赢」可执行。每个模块都要求把代价落成观众看得见的单位。

**不替你做加档决定。** 预算爆了，它停下来亮红灯，给两个选项：砍掉某个具体的东西，或者加档到下一级并重算预算。它推荐一个，然后等你——因为加档改变的是你要交付的东西。

---

## 讨论与反馈

- **提问、聊创作、挑规则的错** → [讨论区](https://github.com/oleo17/story-to-script/discussions)。哪个套路写不下去、哪条规则你觉得不对、写出来的剧本超预算了想让人看看，都放这里。
- **Bug** → [Issues](https://github.com/oleo17/story-to-script/issues)。跳过 Step 0、字数超上限、第 12 镜凭空多出一个人——这些都算 bug。请附上工具、模型和原始 prompt。
- **模型版本会过期。** `06` 里的提示词适配按 2026 年底校对。如果某家改了结构，带厂商文档链接开个 issue。

没有服务承诺。这是一个人的工作工具箱，觉得有用就放出来了。

---

## 相关

**[story-to-script-en](https://github.com/oleo17/story-to-script-en)** — 英文原生版，不是这一版的翻译。语速、格式、套路池都不一样：它按英语语速重新标定字数，用美式剧本格式，套路库是英语言情出版业那一套。两边共享原则，不共享文本。

---

## License

MIT —— 随便用、随便改、拿它做出来的东西随便卖。
