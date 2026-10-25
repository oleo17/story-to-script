# story-to-script｜剧本写手

> 剧本写手 skill：把你的故事写成剧本。

一个中文剧本创作 Skill：从灵感、项目档案、大纲，到完整剧本、台词精修、资产库、AI 视频分镜，再到多模型视频提示词与后期声音设计。

核心设计原则是 **体量先于创作**——进入任何模块之前，先用「事件颗粒数」核定剧本规格，防止 AI 自行注水把 60 秒写成 3 分钟。

> **English?** There's a native English edition built on English speech rates and industry conventions, not a translation: [story-to-script-en](https://github.com/oleo17/story-to-script-en)

**适用于 Claude Code、OpenAI Codex、Cursor、Gemini CLI、GitHub Copilot、WorkBuddy** 等所有支持 Agent Skills 标准的 agent —— 纯 Markdown，无脚本依赖，安装即用。见 [安装](#安装)。

---

## 能力范围

| 阶段 | 模块 | 产出 |
|---|---|---|
| 规格 | `00-scale-control` | 【剧本规格卡】：档位 / 时长 / 场次 / 镜数 / 字数 / 台词量 / 决策往返上限 |
| 创作 | `01` `01b` `02` `03` | 项目档案、大纲、完整剧本、剧本会诊、台词精修 |
| 资产 | `05` | 角色 / 场景 / 道具资产库、参考图整理、连续性方案 |
| 分镜 | `04` `04b` `04f` | AI 视频分镜表、打戏导演、战斗与动作场面拆解 |
| 提示词 | `06` | Seedance 2.0 / Seedance 2.5 / MiniMax H3 三模型并行输出 |
| 后期 | `07` | BGM、音乐搜索词、AI 音乐提示词、音效、静默点、声音方案 |

---

## 安装

本 skill 遵循开放的 [Agent Skills](https://agentskills.io) 标准：`SKILL.md` 只有 `name` + `description` 两个 frontmatter 字段，正文为纯 Markdown，不含脚本、无运行时依赖。

因此 **同一份文件可以直接放进任何支持该标准的 agent，不需要改一行内容**：

| 支持的工具 | |
|---|---|
| Claude Code | OpenAI Codex |
| Cursor | Gemini CLI |
| GitHub Copilot / VS Code | WorkBuddy |

不同工具读取的目录不同，复制到对应位置即可：

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

装完用 `/story-to-script` 显式调用，或直接描述任务让它自动匹配（例如"帮我把这个灵感写成 60 秒竖屏短剧"）。

### 一次装到多个 agent

`~/.agents/skills/` 是被多数工具共同读取的位置，装一份即可覆盖 Codex、Cursor、Gemini CLI、Copilot：

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.agents/skills/story-to-script
```

注意：**Claude Code 只读 `.claude/skills/`，不读 `.agents/skills/`**；Codex 反过来只读 `.agents/skills/`。要两者都能用，各放一份即可。

### WorkBuddy

```bash
git clone https://github.com/oleo17/story-to-script.git ~/.workbuddy/skills/story-to-script
```

### 软链接方案（推荐，避免多份副本不同步）

```bash
git clone https://github.com/oleo17/story-to-script.git ~/skills/story-to-script
ln -s ~/skills/story-to-script ~/.claude/skills/story-to-script
ln -s ~/skills/story-to-script ~/.agents/skills/story-to-script
```

安装后重启会话，在对话里提到「写剧本 / 短剧脚本 / 分镜 / Seedance 提示词 / 漫剧打戏」等关键词即可触发，也可以直接点名 `story-to-script`。

> 模型能力会影响效果。本 skill 的核心是「先数事件颗粒、再定时长」这类硬约束，在 Claude Code（Opus / Sonnet）、Cursor 等强模型上遵守度较好；小模型容易跳过 Step 0 直接开始写。

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

`SKILL.md` 只做路由和全局原则，按「最早缺失环节」引导用户走到正确模块，不会一次性把所有 reference 塞进上下文。

---

## 四档速查

| 档位 | 时长 | 事件颗粒 G | 场次 | 镜数 | 台词量 |
|---|---|---|---|---|---|
| S 微 | 15–120s | 1–6 | 1–3 | 5–15 | ≤6 句 / ≤60 字 |
| M 小 | 2–8min | 7–25 | 3–8 | 25–50 | ≤40 句 / ≤600 字 |
| L 中 | 10–30min | 26–90 | 8–20 | 60–150 | ≤150 句 |
| XL 大 | ≥40min 或 ≥6 集 | ≥90 | 分集 | 分集 | 逐集预算 |

事件颗粒计数公式：`T_max = G × 20` 秒，`T_min = G × 8` 秒。档位由事件颗粒决定，不由用户口头报的时长决定——这是防止篇幅失控的关键。

---

## 使用示例

```text
用 story-to-script 把这个灵感做成 60 秒竖屏短剧：
一个外卖骑手在暴雨夜送最后一单，发现收件人是三年前不告而别的女友。
```

Skill 会先数事件颗粒、落到 S 档、出规格卡，确认后再走剧本 → 分镜 → Seedance 三模型提示词，全程不超预算。

---

## License

MIT
