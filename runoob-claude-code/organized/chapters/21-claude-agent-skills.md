## Agent Skills（智能体技能）

Agent 是智能体，Skills 是技能的意思，Agent Skills（智能体技能）是将专业知识、工作流规范固化为可复用资产的核心工具。

Agent Skills 本质上是一个模块化的 Markdown 文件，能教会 AI 工具 （如 Claude、GitHub Copilot、Cursor 等） 执行特定任务，且支持自动触发、团队共享与工程化管理，彻底告别重复的提示词输入。

Agent Skills 的本质不是工具，而是：

> **行为规范 + 专业知识 + 使用时机的组合**

Skills 基础内容参考：[Skills 教程](https://www.runoob.com/ai-agent/skills-agent.html)

**核心形式： **
- 一个 Skill 就是一个文件夹，里面必须有一个 SKILL.md 文件（包含说明和元数据），可选其他资源文件（如脚本、示例、参考文档）。
- Skill 是一个 Markdown 文件（SKILL.md），用于教 Claude 在特定场景下按你的方式做事。
- 本质是其实就是相当于给 AI 代理发放一本专业手册，AI 不会每次都从零学习，而是根据任务自动调用手册中的知识。
- 简单来说，过去我们用提示词（prompt）教 AI 做事，现在用 Agent Skills 可以把提示词 + 资源打包成可复用、可共享的技能包，更高效、更可靠。

![image](https://www.runoob.com/wp-content/uploads/2026/01/b3eb7f1a-4905-49a6-bda7-976b1415e213.png)

#### Agent Skills 的工作原理

Agent Skills 的关键是渐进式披露，分三层加载：
- **层级 1：技能发现** -- AI 先读取所有技能的元数据（name 和 description），判断任务是否相关，这些元数据始终在系统提示中。
- **层级 2：加载核心指令** -- 如果相关，AI 自动读取 SKILL.md 的正文内容，获取详细指导。
- **层级 3：加载资源文件** -- 只在需要时读取额外文件（如脚本、示例），或通过工具执行脚本。

#### 支持的工具和环境

目前主要支持：
- Claude（Anthropic）：Claude.ai、Claude Code、Agent SDK。
- VS Code + GitHub Copilot：项目级（.github/skills/）或个人级技能。
- Cursor：项目级（.cursor/skills/）或全局技能，支持从 GitHub 安装。
- 其他：正在扩展中，标准开源在 [https://github.com/agentskills/agentskills](https://github.com/agentskills/agentskills)。

#### 为什么需要 Skills？它解决了什么问题？

普通 AI 代理（如 Claude 或 Copilot）很聪明，但缺少特定上下文时容易出错。例如：
- 团队有自己的代码规范，但 AI 每次都要手动提醒。
- 需要处理 PDF 表单、调试 GitHub Actions 等复杂流程，AI 可能不知道最佳实践。

Agent Skills 解决这些问题：
- **自动触发**：AI 根据任务自动加载相关技能，无需手动输入长提示。
- **可复用 & 可共享**：一次创建，全团队或社区使用，支持 Git 版本控制。
- **高效利用上下文**：采用渐进式披露（progressive disclosure），只加载需要的部分，避免上下文窗口溢出。
- **跨平台**：同一个 Skill 可以在 Claude、VS Code Copilot、Cursor 等工具中使用。

#### 核心概念快速理解

| 概念 | 比喻 | 作用 |
| --- | --- | --- |
| **Skill（技能）** | 一个独立的瑞士军刀工具或烹饪食谱 | 完成一项特定任务（如写邮件、分析数据）的完整套件。 |
| **Instruction（指令）** | 工具的使用说明书或食谱的步骤 | 告诉Claude具体要做什么、如何思考、输出什么格式。 |
| **Knowledge（知识）** | 工具的零件清单或食谱的食材背景资料 | 上传文件（如产品手册、API 文档）作为 Skill 的专属知识库。 |
| **Tool（工具）** | 工具上的特殊配件（如开瓶器） | 定义 Skill 可以调用的外部 API 或函数，用于获取数据或执行操作。 |

#### Skill 执行流程

- 从用户指令开始，先进行 Skill 意图识别，决定是否进入受控执行路径。
- 命中 Skill 后，系统加载 SKILL.md，建立工具权限与行为边界，再结合上下文进行推理。
- 只有在确实需要时才调用被允许的外部工具，否则在规则内完成逻辑。
- 最终结果经过约束整合后输出，用户的下一次输入触发新一轮完整流程。

![image](https://www.runoob.com/wp-content/uploads/2026/01/claude-agent-skills-runoob.png)

---

### Skill 的最小结构

~~~
my-skill/
└── SKILL.md   （唯一必需）
~~~

SKILL.md 基本模板:

~~~

---

name: your-skill-name
description: What it does and when Claude should use it

---

# Skill Title

## Instructions

Clear, concrete, actionable rules.

## Examples

- Example usage 1
- Example usage 2

## Guidelines

- Guideline 1
- Guideline 2
~~~

元数据字段:

| 字段 | 必填 | 说明 |
| --- | --- | --- |
| name | 否 | Skill 显示名称，默认使用目录名，仅支持小写字母、数字和短横线（最长 64 字符） |
| description | 推荐 | 技能用途及使用场景，Claude 根据它判断是否自动应用 |
| argument-hint | 否 | 自动补全时显示的参数提示，如 `[issue-number]`、`[filename] [format]` |
| disable-model-invocation | 否 | 设为 `true` 禁止 Claude 自动触发，仅能手动 `/name` 调用（默认 false） |
| user-invocable | 否 | 设为 `false` 从 `/` 菜单隐藏，作为后台增强能力使用（默认 true） |
| allowed-tools | 否 | Skill 激活时 Claude 可无授权使用的工具 |
| model | 否 | Skill 激活时使用的模型 |
| context | 否 | 设为 `fork` 时在子代理上下文中运行 |
| agent | 否 | 子代理类型（配合 context: fork 使用） |
| hooks | 否 | 技能生命周期钩子配置 |

Skills 支持在内容中插入动态变量：

| 变量 | 说明 |
| --- | --- |
| `$ARGUMENTS` | 调用 Skill 时传入的所有参数 |
| `$ARGUMENTS[N]` | 按索引访问参数，如 `$ARGUMENTS[0]` |
| `$N` | 简写方式，如 `$0` 表示第一个参数 |
| `${CLAUDE_SESSION_ID}` | 当前会话 ID，用于日志、临时文件、关联输出 |

例如：

~~~

---

name: session-logger
description: 记录当前会话活动

---

请将以下内容写入日志文件：

logs/${CLAUDE_SESSION_ID}.log

$ARGUMENTS
~~~

调用：

~~~
/session-logger 用户登录成功
~~~

实际会生成会话专属日志记录。

#### SKILL.md 文件的核心构成

以 Claude 的 PDF 文档编辑技能为例，Claude 原生可解析 PDF，但无法直接操作（如填写表单），该技能补足了这一短板。
- **核心形态： **一个包含 SKILL.md 的目录
- **必填元数据： **SKILL.md 开头的 YAML 块，需包含 name（名称）和 description（描述），启动时预加载至系统提示词

![image](https://www.runoob.com/wp-content/uploads/2026/01/6f22d8913dbc6228e7f11a41e0b3c124d817b6d2-1650x929-1.webp)

#### 多文件 Skill（渐进式披露）

**渐进式披露机制**
- 第一层：元数据 → 让 Claude 判断技能适用场景，不加载全部内容
- 第二层：SKILL.md正文 → 判定相关后，载入完整上下文
- 第三层 +：附属文件（如forms.md）→ 按需引用，精简核心文件体积

![image](https://www.runoob.com/wp-content/uploads/2026/01/6f22d8913dbc6228e7f11a41e0b3c124d817b6d2-1650x929-1.webp)

下图显示了当用户消息触发技能时，上下文窗口如何变化:

![image](https://www.runoob.com/wp-content/uploads/2026/01/441b9f6cc0d2337913c1f41b05357f16f51f702e-1650x929-1.webp)

- 初始状态：上下文窗口含系统提示词、技能元数据、用户指令
- 调用 Bash 工具读取目标 SKILL.md，触发对应技能
- 按需加载附属文件（如forms.md）
- 加载完成后执行用户任务

#### 推荐目录结构

为避免上下文膨胀：
- 核心规则 → `SKILL.md`
- 详细资料 → 单独文件
- 实用逻辑 → 脚本执行（不加载）

推荐结构:

~~~
my-skill/
├── SKILL.md
├── reference.md
├── examples.md    # 存放示例文件
└── scripts/
    └── helper.py
~~~

---

### 第一个 Skill

让我们暂时忘掉复杂的创建过程，先从 **使用一个现成的 Skill** 开始，感受它带来的便利。

#### 创建 Skill 目录

Skills 存放在 ~/.claude/skills/（个人全局）或项目目录下的 .claude/skills/（项目专用）。

本章节再项目目录下测试，先创建个目录 claude-test:

~~~
mkdir claude-test
~~~

进入该目录，创建 skills 的目录与文件：

~~~
mkdir -p .claude/skills/python-naming-standard
~~~

#### 编写配置文件 SKILL.md

在目录下创建 SKILL.md，这是 Skill 的大脑 ，告诉 Claude 什么时候用它。

~~~

---

name: Python 内部命名规范技能
description: 当用户要求重构、审查或编写 Python 代码时，请参考此规范。

---

## 指令

1. 所有的内部辅助函数必须以 `_internal_` 前缀命名。
2. 如果发现不符合此规则的代码，请自动提出修改建议。
3. 在执行 `claude commit` 前，必须检查此规范。

## 参考示例

- 正确：`def _internal_calculate_risk():`
- 错误：`def _calculate_risk():`
~~~

字段要求：
- **name**：必须仅使用小写字母、数字和连字符（最多 64 个字符）
- **description**：Skill 的简要描述及其使用时机（最多 1024 个字符）

创建完后文件结构如下：

![image](https://www.runoob.com/wp-content/uploads/2026/01/7d0592b4-61f8-4170-a639-6e83f6740cb6.png)

你的项目现在看起来应该是这样的：

~~~
my-project/
├─ src/
│  └─ test.py              # 项目源码
├─ .claude/
│  ├─ skills/
│  │  └─ hello-world/
│  │     ├─ skill.md       # Skill 定义（YAML + Instructions，机器可执行）
│  │     └─ README.md      # Skill 说明（人类阅读，可选）
│  └─ config.yml           # Claude 项目级配置（可选）
├─ .gitignore
└─ README.md               # 项目整体说明
~~~

接下来我们再终端执行以下命令启动 Claude Code：

~~~
claude
~~~

输入任务：

~~~
帮我写一个计算用户折扣的函数
~~~

Claude 就会会扫描已安装的 Skills，发现你的请求涉及 "Python 代码编写"，匹配了 python-naming-standard。

![image](https://www.runoob.com/wp-content/uploads/2026/01/1527ed0b-d1a9-420a-8f25-c71231e23c05.png)

它会根据 SKILL.md 中的要求，生成如下代码：

~~~
def _internal_get_discount(user_score):

# 计算逻辑...

    return discount
~~~

#### 添加资源文件（可选）

另外我们可以在 .claude/skills/ 下添加以下目录：

在同一文件夹添加：
- `examples/`：存放示例文件。
- `references/`：存放参考文档。
- `scripts/`：存放可执行脚本（例如 Python 处理 PDF）。

然后在 SKILL.md 中引用：

~~~
查看示例 commit：./examples/good-commit.txt
运行脚本：使用工具执行 ./scripts/process.py
~~~

---

### 官方市场

除了自己编写，你还可以利用 2025 年末发布的 Agent Skills 开放标准：
- 官方市场：访问 [https://github.com/anthropics/skills](https://github.com/anthropics/skills) 仓库下载预设的技能（如：React 优化器、SQL 调优工具）。
- Skill Creator：你可以对 Claude 说："帮我把我刚才教你的关于 Docker 的配置逻辑总结成一个 Skill"，它会自动在相应目录为你生成文件。

我们可以将本仓库注册为 Claude Code 的插件市场，只需在 Claude Code 中执行以下命令：

~~~
/plugin marketplace add anthropics/skills
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/780e9cce-ff89-4960-ab36-be41428a3899.png)

然后就可以使用 /plugin 查看：

![image](https://www.runoob.com/wp-content/uploads/2026/01/00814d49-4942-45b4-87e7-c14d682a7af5.png)

**安装指定技能集的步骤： **
- 浏览并安装插件（Browse and install plugins）
- 选择 anthropic-agent-skills 插件源
- 选择 document-skills（文档技能） 或 example-skills（示例技能）![image](https://www.runoob.com/wp-content/uploads/2026/01/8c5a3a09-2943-49e0-b073-aa5385826510.png)
- 点击立即安装（Install now）![image](https://www.runoob.com/wp-content/uploads/2026/01/45288835-7200-48a8-90f0-b9ce408a059d.png)

我们也可直接通过命令安装上述两类插件：

~~~
/plugin install document-skills@anthropic-agent-skills
/plugin install example-skills@anthropic-agent-skills
~~~

**注意： **使用插件安装的 skills 目录在 ～/claude/plugins/marketplaces/ 下。

插件安装完成后，需要重启一下 Claude Code。

使用的时候只需在指令中提及技能名称即可调用，例如安装 document-skills 插件后，可向 Claude Code 下达指令：

~~~
使用 PDF 技能提取 path/to/some-file.pdf 文件中的表单字段
~~~

或者创建一个 PPT：

~~~
创建一个 Agent Skill 的演示文稿
~~~

可以看到，调用了 **/document-skills:pptx**：

![image](https://www.runoob.com/wp-content/uploads/2026/01/74f0eea5-9416-4a27-a827-2378896805c5.png)

开始生成：

![image](https://www.runoob.com/wp-content/uploads/2026/01/f5a9c860-9291-4d3f-b72e-7af1ad80a528.png)

之后就会告诉你生成的文件位置：

![image](https://www.runoob.com/wp-content/uploads/2026/01/dc42fb40-b5ad-476d-aa64-bdec040f752e.png)

---

### Agent Skills 相关资源整理

| 资源说明 | 链接 |
| --- | --- |
| Skill 聚合入口 | [https://skills.sh/](https://skills.sh/) |
| Skills 市场（中文界面） | [https://skillsmp.com/zh](https://skillsmp.com/zh) |
| Agent Skills 官方标准站点 | [https://agentskills.io](https://agentskills.io) |
| Anthropic 官方工程文章（Agent Skills 实战理念） | [https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) |
| VS Code Copilot Agent Skills 文档 | [https://code.visualstudio.com/docs/copilot/customization/agent-skills](https://code.visualstudio.com/docs/copilot/customization/agent-skills) |
| Anthropic 官方 Skills GitHub 仓库 | [https://github.com/anthropics/skills](https://github.com/anthropics/skills) |
| Claude 技能精选列表（Awesome 系列） | [https://github.com/ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) |
| 软件开发自动化工作流 Skills 集合 | [https://github.com/obra/superpowers](https://github.com/obra/superpowers) |
| 自动生成 Skill 的 Skill（官方示例） | [https://github.com/anthropics/skills/tree/main/skills/skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator) |

---

### Skills 使用实例

在上一章节 [Agent Skills（智能体技能）](https://www.runoob.com/claude-code/claude-agent-skills.html) 中我们已经了解了 Skills 的基本概念，本章节我们将结合一个具体的 Skills 来开发一个项目。

> 现在市面上已经有很多现成的 skills，我么可以直接拿来使用，我们可以在 [https://skills.sh/](https://skills.sh/) 查找更多的 skill。
> 安装方式：
> ~~~
> npx skills add <owner/repo>
> ~~~npx 安装 ui-ux-pro-max 可参考：[OpenCode skills 使用](https://www.runoob.com/ai-agent/opencode-skills-intro.html)。
> **注： **如果对 npx 不了解，可以参阅：[npx 入门教程](https://www.runoob.com/nodejs/npx-intro.html)

本章节我们将介绍一个支持多平台、多框架的专业级 UI/UX SKILL 插件 - UI UX Pro Max。

UI UX Pro Max 旨在为跨平台和多框架开发提供专业的 UI/UX 设计智能支持。

UI UX Pro Max 本质上是一个可检索的设计数据库，包含样式、色板、字体、组件建议及 UX 准则，专门喂给 Cursor、Claude Code、Windsurf等这些 AI 编码助手。

**核心亮点： **
- 67 种 UI 风格（从 Minimalism 到 Brutalism、Glassmorphism、Bento Grid、AI-Native 等）
- 96 个行业调色盘（SaaS、Fintech、Healthcare、Beauty、EdTech…）
- 57 组字体搭配（Google Fonts 精选组合）
- 25 种常用图表类型（适合 dashboard）
- 99 条 UX 指导原则（包含可访问性、性能、交互规范）
- 100 条行业专项推理规则（包含反面模式 Anti-patterns）
- 支持 13 个技术栈：React / Next.js / Vue / Tailwind / Flutter / SwiftUI / HTML+CSS / Electron 等

**工作原理： **
- 提出需求：构建、设计、实现、优化或评审 UI / UX
- 技能自动触发：AI 会在内部搜索 UI 风格、配色、字体和 UX 规范数据库
- 智能设计推荐：根据产品类型与使用场景匹配合适的设计体系
- 直接生成代码：按最佳实践输出包含正确配色、字体、间距和结构的 UI 实现
开源地址：[https://github.com/nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
官方文档：[https://ui-ux-pro-max-skill.nextlevelbuilder.io/](https://ui-ux-pro-max-skill.nextlevelbuilder.io/)

#### 安装方式1、通过 Claude Marketplace（Claude Code）安装

在 Claude Code 中执行以下两条命令即可直接安装。
注册插件市场源：
~~~
/plugin marketplace add nextlevelbuilder/ui-ux-pro-max-skill
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/5cd82658-5a31-4653-ad1d-e8b0d7335744.png)

安装插件：
~~~
/plugin install ui-ux-pro-max@ui-ux-pro-max-skill
~~~安装成功后重启 Claude Code 就可以使用了:

![image](https://www.runoob.com/wp-content/uploads/2026/01/a818e0e6-b4f9-437f-8031-8fc4fb2c25c9.png)

2、使用 CLI（推荐）
全局安装 CLI：

~~~
npm install -g uipro-cli

# 进入你的项目目录

cd /path/to/your/project

# 为对应 AI 助手安装

uipro init --ai claude # Claude Code
uipro init --ai cursor # Cursor
uipro init --ai windsurf # Windsurf
uipro init --ai antigravity # Antigravity (.agent + .shared)
uipro init --ai copilot # GitHub Copilot
uipro init --ai kiro # Kiro
uipro init --ai codex # Codex CLI
uipro init --ai qoder # Qoder
uipro init --ai roocode # Roo Code
uipro init --ai gemini # Gemini CLI
uipro init --ai trae # Trae
uipro init --ai all # 所有助手
~~~
3、其他 CLI 命令：~~~
uipro versions # 查看可用版本
uipro update # 更新到最新版本
uipro init --version v1.0.0 # 安装指定版本
~~~

2、使用 npx
~~~
npx skills add https://github.com/nextlevelbuilder/ui-ux-pro-max-skill --skill ui-ux-pro-max
~~~
5、安装指定版本安装完后，假设在 Claude 中使用，执行以下命令：~~~
uipro init --ai claude
~~~

> 搜索脚本需要使用 Python 3.x：
> ~~~
> # 查看 Python 是否已经安装
> python3 --version
>
> # macOS
> brew install python3
>
> # Ubuntu/Debian
> sudo apt update && sudo apt install python3
>
> # Windows
> winget install Python.Python.3.12
> ~~~

安装完成后输出如下：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ui-ux-pro-runoob.webp)

然后启用输入命令进入 Claude：~~~
claude
~~~我们可以使用 /plugin 命令查看已经安装的创建：

![image](https://www.runoob.com/wp-content/uploads/2026/01/15b4de08-8b55-40eb-81f3-4b6eec604738.png)

按 Esc 按键退出。
注意：通过插件安装的 Skill，目录放在 ～/.claude/plugins/marketplaces。

---

### 如何使用

接下来我们就可以直接输入需求：
~~~
为宠物美容服务搭建一个着陆页，风格活泼亲和，并设置预约类行动召唤按钮。
~~~
会提示是否使用我们安装的 skill 来设计，一路回车就好了：

![image](https://www.runoob.com/wp-content/uploads/2026/01/6832cf20-057a-40d9-8693-06f205d1777e.png)

一路回车就好了，会提示完成的功能：

![image](https://www.runoob.com/wp-content/uploads/2026/01/89a8159c-41bb-454a-b63e-0c743ee5d23d.png)

然后查看最终效果，好看很多：

![image](https://www.runoob.com/wp-content/uploads/2026/01/a9d18e1c-ae82-4dfa-bb9f-f7fe30a01cb7-scaled.png)
