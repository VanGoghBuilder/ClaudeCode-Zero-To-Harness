# 扩展、自动化与高级能力

> 阅读提示：本章更适合在你已经理解了项目上下文和日常协作节奏之后再进入；如果你想先建立从基础使用到 harness 的整体地图，先读 [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)，再结合 [Claude Code 的 Harness Engineering 总纲](./06-harness-engineering-guide.md) 理解这些能力在系统中的位置。

## 包含章节

- Claude Code MCP
- Claude Code 子代理（Subagent）
- Claude Code 插件
- Claude Code 插件参考手册
- Claude Code 钩子
- Agent Skills（智能体技能）
- skill-creator 使用
- Claude Code 控制 Chrome 浏览器

---

## Claude Code MCP

如果说 Claude Code 是一个优秀的打字员（代码生成）和测试员（代码校验），那么加上 MCP（Model Context Protocol，模型上下文协议） 则是让 Claude 真正拥有了外部感官和手脚——它不再局限于当前项目的代码文件，而是能主动连接外部世界的资源与工具，成为你的全链路开发协作伙伴。

![image](https://www.runoob.com/wp-content/uploads/2026/01/claude-code-mcp-runoob.svg)

#### 什么是 MCP (Model Context Protocol)？

MCP 是 Anthropic 推出的一套开放的标准化协议，专为解决AI 与外部工具协作的核心痛点而生。

MCP 的核心逻辑颠覆了传统思路：不要让 AI 去逐一学习所有工具的使用规则，而是让所有工具都提供一个统一的接口给 AI。

这个逻辑就像现实中的通用插座——不用让电器适配不同的插座，只要插座统一标准，所有电器都能直接使用。

连接 MCP 服务器后，你可以让 Claude Code：
- 从 JIRA/工单系统获取需求并开发功能、创建 GitHub PR
- 分析 Sentry 监控数据、定位生产环境错误
- 查询 PostgreSQL/MySQL 等数据库数据
- 基于 Figma 设计更新代码、自动化生成邮件草稿等

![image](https://www.runoob.com/wp-content/uploads/2026/01/0_ydhvFVVoKyOfEHMi.png)

#### 为什么 Claude Code 需要 MCP？

在没有 MCP 之前，Claude Code 的能力被严格限制在你当前打开的项目文件夹内，就像一个坐井观天的助手，只能看到眼前的代码，无法感知项目之外的任何信息。而 MCP 的出现，彻底打破了这个信息孤岛，让 Claude 能深度参与全流程开发：

举几个直观的场景对比，你就能快速理解 MCP 的价值：

| 场景 | 无 MCP 时 | 有 MCP 时 |
| --- | --- | --- |
| 跨仓库分析 | 只能分析当前文件夹内的代码，无法关联其它仓库 | 跨仓库协作："帮我分析仓库 A 的接口文档，然后在仓库 B 中编写对应的调用方法" |
| 需求/讨论同步 | 需要你手动把需求文档、团队讨论记录复制粘贴到对话框 | 实时同步协作信息："查一下 Slack 里 #feature-x 频道的讨论记录，看看大家对该功能的修改建议" |
| 数据库验证 | 无法直接验证代码与数据库的适配性，只能手动查表结构 | 直接联动数据库："去查一下 MySQL 中 users 表的结构和前 5 条数据，判断我写的用户查询接口是否匹配" |
| 自动化流程 | 只能生成代码片段，供你手动复制使用 | 自动化执行："帮我写浏览器自动化脚本，并通过 MCP 调用 Puppeteer 执行，验证登录页面的交互逻辑" |

简单来说，MCP 让 Claude Code 从被动接收信息的助手，变成了主动获取信息、执行操作的协作伙伴。

---

### Claude Code 中 MCP 的基本使用方式

Claude Code 内置了完整的 MCP 管理能力，核心命令只有一个：

~~~
claude mcp
~~~

常用子命令:

| 命令 | 作用 |
| --- | --- |
| `claude mcp add` | 添加一个 MCP 服务器 |
| `claude mcp list` | 查看所有已配置服务器 |
| `claude mcp get <name>` | 查看某个服务器详情 |
| `claude mcp remove <name>` | 删除服务器 |
| `/mcp` | 在 Claude Code 中查看状态 / 认证 |

---

### 安装 MCP 服务器

MCP 服务器支持 HTTP/SSE/stdio 三种接入方式，推荐优先使用 HTTP（SSE已弃用）。

#### 1. 远程 HTTP 服务器（推荐）

适用于云服务类工具，是最通用的方式：

~~~

# 基础语法

claude mcp add --transport http <服务器名称> <服务器URL>

# 示例1：连接Notion

claude mcp add --transport http notion https://mcp.notion.com/mcp

# 示例2：带身份验证的HTTP服务器

claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer 你的令牌"
~~~

#### 2. 远程 SSE 服务器（已弃用）

仅兼容旧版工具，优先用 HTTP 替代：

~~~

# 基础语法（仅兼容用）

claude mcp add --transport sse <服务器名称> <服务器URL>

# 示例：连接Asana

claude mcp add --transport sse asana https://mcp.asana.com/sse
~~~

#### 3. 本地 stdio 服务器

适用于需要本地系统访问的工具（如本地数据库、自定义脚本）：

~~~

# 基础语法（注意：--前是Claude参数，--后是服务器命令）

claude mcp add --transport stdio [--env 环境变量] <服务器名称> -- <启动命令>

# 示例：连接Airtable（需替换自己的API密钥）

claude mcp add --transport stdio --env AIRTABLE_API_KEY=你的密钥 airtable \
  -- npx -y airtable-mcp-server
~~~

> 关键注意：`--transport`/`--env` 等参数必须放在服务器名称 **前面**，`--` 用于分隔Claude参数和服务器命令，避免参数冲突。

### 管理 MCP 服务器

配置完成后，你可以通过以下命令管理服务器：

~~~

# 列出所有已配置的服务器

claude mcp list

# 查看指定服务器详情（如github）

claude mcp get github

# 删除指定服务器

claude mcp remove github

# 在Claude Code中检查服务器状态

/mcp
~~~

---

### 配置范围（控制服务器可见性）

你可以指定 MCP 服务器的生效范围，适配个人/团队使用场景：

| 范围 | 用途 | 配置命令示例 |
| --- | --- | --- |
| local（默认） | 仅当前项目可用，私密配置（如敏感密钥） | `claude mcp add --scope local ...` |
| project | 团队共享（存储在.mcp.json，可提交版本库） | `claude mcp add --scope project ...` |
| user | 所有项目可用（个人全局配置） | `claude mcp add --scope user ...` |

> 优先级：local > project > user（同名服务器，本地配置覆盖共享配置）

### 实用示例

#### 示例 1：GitHub 代码审查

~~~
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
~~~

在 Claude Code 中：

~~~
> Review PR #456 and suggest improvements
> Show me all open PRs assigned to me
~~~

#### 示例 2：Sentry 生产环境排错

~~~
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
~~~

~~~
> /mcp   # 完成 OAuth 登录
> What are the most common errors in the last 24 hours?
~~~

#### 示例 3：直接查 PostgreSQL

~~~
claude mcp add --transport stdio db \
  -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:pass@prod.db.com:5432/analytics"
~~~

~~~
> Show me the schema for the orders table
> Which users haven't purchased in 90 days?
~~~

---

### 在对话中使用 MCP 的高级方式

#### 1、使用 @ 引用 MCP 资源

~~~
> Analyze @github:issue://123 and suggest a fix
~~~

支持多个资源对比：

~~~
> Compare @postgres:schema://users with @docs:file://user-model
~~~

#### 2、MCP Prompt 作为斜杠命令

MCP 可以暴露命令：

~~~
/mcp__github__list_prs
/mcp__jira__create_issue "Login bug" high
~~~

Claude 会像执行内置命令一样执行它们。

---

### 关键注意事项

1. **身份验证**：远程MCP服务器（如GitHub/Sentry）需在Claude Code中执行 `/mcp` 完成OAuth 2.0授权；
2. **Windows兼容**：本地stdio服务器若用npx，需加`cmd /c`包装（如`-- cmd /c npx -y 包名`），否则会报"Connection closed"错误；
3. **第三方风险**：使用非官方MCP服务器时，需确认来源可信，避免提示注入/安全风险；
4. **参数顺序**：stdio服务器配置时，`--` 前后的参数不可颠倒，否则会执行失败。

---

## Claude Code 子代理（Subagent）

在 Claude Code 中，你可以创建 **专门的 AI 子代理（Subagent）**，用于处理特定类型的任务，从而获得更好的上下文隔离、更强的约束控制和更高的执行效率。

子代理运行在 **独立的上下文窗口**中，每个子代理都可以拥有独立的系统提示、指定的模型、明确的工具访问权限、独立的权限模式，以及跨会话的持久记忆。当 Claude 判断你的请求符合某个子代理的描述时，会自动将任务委托给它，由子代理独立完成后返回结果。

> 子代理只接收自身的系统提示和基础环境信息（如工作目录）， **不会继承完整的 Claude Code 系统提示**，这保证了行为的纯净和可控。

---

### 为什么要使用子代理

子代理的核心价值在于 **隔离 + 专业化**，主要体现在以下几个方面：

- **保留主对话上下文**：把探索、日志分析等“重任务”放到子代理中，主对话只接收结论摘要，不被大量中间输出淹没。
- **强制执行约束**：通过工具白名单或黑名单限制子代理的能力，例如只读分析、禁止执行危险命令。
- **行为专业化**：为特定领域（代码审查、调试、数据分析）设计专用 AI，在系统提示中明确指出代理的能力边界，避免不必要的调用。
- **控制成本**：将简单任务交给 Haiku，将复杂分析交给 Sonnet，并通过环境变量 `CLAUDE_CODE_SUBAGENT_MODEL` 统一设置所有子代理使用的模型。
- **跨项目复用**：用户级子代理一次配置，所有项目可用。

研究资料中的一个示例是：并行运行三个子代理分析 5 万行项目约需 45 秒，而串行执行需要约 3 分钟。

---

### 子代理 vs 多代理

这两个概念容易混淆，区别在于任务粒度和运行范围：

| 对比项 | 子代理（Subagent） | 多代理（Multi-agent） |
| --- | --- | --- |
| 运行范围 | 在单个 Claude Code 会话内启动，处理子任务后返回结果 | 多个 Claude Code 会话并行或串行运行，通常由编排器管理 |
| 上下文 | 独立上下文窗口，与主对话隔离 | 各会话上下文完全独立 |
| 嵌套 | 子代理不能再创建子代理（需嵌套时使用 Skills） | 可由编排器协调多层级任务 |
| 适用场景 | 聚焦子任务、大量输出隔离、专业分析 | 全功能开发流水线（设计 → 实现 → 测试 → 发布） |

---

### 内置子代理

Claude Code 已内置多种子代理，通常会自动使用，无需手动配置。

#### 1、Explore（探索代理）

用于只读搜索与分析代码库。模型默认使用 Haiku（速度快、延迟低），只开放只读工具（不能 Edit / Write）。当 Claude 需要查看代码但不修改代码时，会自动使用 Explore 代理。支持不同探索深度：`quick`、`medium`、`very thorough`。

#### 2、Plan（规划代理）

在计划模式下收集代码库信息，帮助 Claude 理解项目结构，为后续方案制定积累上下文。只开放只读工具，在不产生嵌套代理的前提下安全地收集规划所需信息。

#### 3、General-purpose（通用代理）

用于复杂、多步骤任务，开放全部工具，继承主对话的模型。适合"看 + 改 + 推理"的综合场景，以及需要多步骤代码修改的任务。

#### 4、其他内部代理

| 代理名称 | 说明 |
| --- | --- |
| Bash | 在独立上下文中运行 Shell 命令 |
| statusline-setup | 配置终端状态栏显示 |
| Claude Code Guide | 解答 Claude Code 使用相关问题 |

---

### 创建你的第一个子代理

#### 1、打开子代理管理界面

在 Claude Code 中执行以下命令，打开完整的子代理管理界面：

~~~
/agents
~~~

该界面提供所有子代理管理能力：查看全部可用代理（内置 / 用户级 / 项目级 / 插件）、创建新代理、编辑已有代理，以及在同名冲突时查看哪个版本实际生效。

#### 2、选择创建用户级子代理

在界面中选择 **Create new agent**:

![image](https://www.runoob.com/wp-content/uploads/2026/01/0bdb8194-a50c-43fd-8830-8f832753fd8f.png)

然后选择 **Project (.claude/agents/)**，代理文件会保存到当前目录的 .claude/agents/ 目录下，如果选择 `~/.claude/agents/` 目录，则对所有项目生效：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ad0b260c-8e5d-440f-bfc0-9a17a63c36b9.png)

使用 Claude 推荐的：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ca02be99-1f36-4beb-a422-910ed018fff6.png)

#### 3、描述代理的职责

我们可以直接用自然语言告诉 Claude 这个代理要做什么，Claude 会自动生成系统提示和初始配置。例如：

~~~
一个代码改进代理，扫描项目文件，
针对可读性、性能和最佳实践提出建议，
并给出改进示例。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/833a1a6c-1671-4280-ad27-8103fb373724.png)

生成完成后，按 `e` 键可以手动编辑所有配置内容。

#### 4、配置工具权限与模型

- 只做代码审查 → 仅勾选只读工具（Read / Grep / Glob）
- 需要修改代码 → 保留 Edit / Write 工具
- 模型推荐选择 **Sonnet**，分析能力与执行速度较为均衡

直接选 Contiune 然后回车：

![image](https://www.runoob.com/wp-content/uploads/2026/01/46730b89-c6df-4ea0-a4ab-094bf85fab25.png)

接下来的选默认回车就好。

#### 5、选择记忆范围（可选）

- 选择 **User**：在 `~/.claude/agent-memory/` 建立持久记忆，跨所有项目积累经验
- 选择 **None**：不保留学习成果，每次任务从零开始

![image](https://www.runoob.com/wp-content/uploads/2026/01/6c13d3ad-2649-4dc6-991f-93a9977231eb.png)

#### 6、使用刚创建的代理

~~~
使用 code-improver 子代理为此项目提出改进建议
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/9de6e4af-24c7-4c24-bb3e-0fd4e09f104d.png)

代理会在独立上下文中运行，完成后将结果摘要返回给主对话。

---

### 子代理的作用范围

子代理本质上是带 YAML frontmatter 的 Markdown 文件，存放位置决定了它的作用范围和优先级：

| 存放位置 | 作用范围 | 优先级 |
| --- | --- | --- |
| CLI `--agents` 标志 | 仅当前会话 | 最高 |
| `.claude/agents/` | 当前项目 | 高 |
| `~/.claude/agents/` | 所有项目（全局） | 中 |
| 插件 agents | 插件作用域 | 最低 |

当同名子代理在不同位置都存在时，优先级高的会覆盖低的。可通过 `/agents` 命令查看当前哪个版本实际生效。

选择存放位置的建议：项目代理（`.claude/agents/`）可以跟随代码一起提交，让团队共享；用户代理（`~/.claude/agents/`）存放个人习惯与通用工具，跨项目生效；CLI 代理（`--agents`）用于临时测试或自动化脚本，不保留到磁盘。

---

### 配置文件结构

每个子代理配置文件由两部分组成：YAML frontmatter（元数据与配置）和 Markdown 正文（系统提示）。

### 实例

```yaml
---
name: code-reviewer
# 必填：唯一标识，小写字母 + 连字符
description: |
  Reviews code for quality, best practices, and security issues.
  Invoke when the user asks to review, audit, or check code quality.
# 必填：决定 Claude 何时自动调用此代理，建议写成“何时调用 + 能做什么”
tools: Read, Grep, Glob
# 工具白名单（只能使用这些工具）
model: sonnet
# 指定模型：haiku / sonnet / opus / inherit
permissionMode: default
# 权限模式（见下方权限模式章节）
memory: project
# 持久记忆范围（见下方记忆章节）
---
```

```text
You are a senior code reviewer.
Analyze code and provide actionable feedback organized by severity:
Critical / Major / Minor.

Update your agent memory with recurring patterns, conventions,
and known issues you discover.
```

#### 完整字段说明
| 字段 | 是否必填 | 说明 |
| --- | --- | --- |
| `name` | 必填 | 唯一标识，也是显式调用时的名称。格式：小写字母 + 连字符，如 `code-reviewer` |
| `description` | 必填 | **最重要的字段**，Claude 是否以及何时自动调用该代理完全依赖于此，务必写清楚使用场景 |
| `tools` | 可选 | 工具白名单；设置后只能使用列出的工具，MCP 工具也会被排除 |
| `disallowedTools` | 可选 | 工具黑名单；继承主对话全部工具，但排除列出的工具（MCP 工具保留） |
| `model` | 可选 | 指定模型，可填 `haiku`、`sonnet`、`opus`、完整模型 ID，或 `inherit`（默认，继承主对话） |
| `permissionMode` | 可选 | 权限行为控制，见下方权限模式章节 |
| `memory` | 可选 | 持久记忆范围：`user` / `project` / `local`，见下方记忆章节 |
| `background` | 可选 | 设为 `true` 时，该代理始终以后台方式运行（不阻塞主对话） |
| `isolation` | 可选 | 设为 `worktree` 时，在临时 git worktree 中运行，与主仓库完全隔离 |
| `skills` | 可选 | 在此代理启动时自动加载的 Skills 列表 |
| `hooks` | 可选 | 生命周期钩子：`SubagentStart` / `SubagentStop` / `PreToolUse` / `PostToolUse` |

#### tools 与 disallowedTools 的区别
| 配置方式 | 行为 | 典型场景 |
| --- | --- | --- |
| 两者均不设置 | 继承主对话全部工具，含 MCP 工具 | 通用代理，不需要限制 |
| 仅设置 `tools` | 只能使用白名单内的工具，MCP 工具被排除 | 只读分析代理、严格约束场景 |
| 仅设置 `disallowedTools` | 继承全部工具，排除黑名单工具，MCP 工具保留 | 保留 MCP 能力但禁止写操作 |
| 两者同时设置 | 先应用 `disallowedTools`，再从剩余工具中按 `tools` 筛选 | 精细控制工具访问 |

### 实例

**示例一：只允许只读操作（使用 tools 白名单）**

```yaml
---
name: safe-researcher
description: Research agent with read-only access. Use when analyzing code without making changes.
tools: Read, Grep, Glob, Bash
---
```

**示例二：继承所有工具但禁止写文件（使用 disallowedTools 黑名单）**

```yaml
---
name: no-writes
description: Analysis agent that inherits all tools except file writes.
disallowedTools: Write, Edit
---
```

---

### 权限模式

通过 `permissionMode` 字段控制子代理执行操作时的权限行为：

| 模式 | 行为 | 适用场景 |
| --- | --- | --- |
| `default` | 正常权限提示，每次操作前询问用户 | 通用场景，推荐默认值 |
| `acceptEdits` | 自动接受文件编辑，无需每次确认 | 频繁改动文件的代理，减少交互中断 |
| `dontAsk` | 自动拒绝未授权操作，不中断执行流程 | 严格只读场景，操作失败时静默跳过 |
| `bypassPermissions` | 跳过所有权限检查，直接执行 | 仅限完全可信、受控的自动化环境 |
| `plan` | 只读规划模式，不执行任何写操作 | 方案制定、架构分析 |

> `bypassPermissions` 只适合完全可信的子代理。另外，子代理会 **继承父会话的权限模式**——如果主会话开启了 bypass，所有子代理也会跟着 bypass，使用时请格外谨慎。

---

### 持久记忆（Memory）

通过 `memory` 字段，子代理可以在会话之间积累知识，例如代码库规律、调试经验、架构决策等，无需每次重新探索。

| 范围值 | 存储位置 | 适用场景 |
| --- | --- | --- |
| `user` | `~/.claude/agent-memory/<name>/` | 代理的知识适用于所有项目，如通用代码审查规范 |
| `project` | `.claude/agent-memory/<name>/` | 知识与项目绑定，可通过 git 在团队间共享（推荐默认值） |
| `local` | `.claude/agent-memory-local/<name>/` | 知识与项目绑定但不提交 git，仅存储个人本地经验 |

### 实例

```yaml
---
name: code-reviewer
description: Reviews code for quality and best practices. Invoke when reviewing code changes.
memory: user
---
```

```text
You are a code reviewer.
As you review code, update your agent memory with patterns, conventions,
and recurring issues you discover in this codebase.
```

在对话中控制记忆的使用方式：
- 任务开始前：`请先查阅你的记忆，再开始审查`（让代理利用已有经验）
- 任务结束后：`任务完成后，把你发现的规律保存到记忆中`（持续积累）
- 也可直接在系统提示中写入“主动维护记忆”的指令，让代理自动执行，无需每次手动提醒

---

### worktree 隔离模式

设置 `isolation: worktree` 后，子代理会在临时 git worktree 中运行，与主仓库完全隔离。适合以下场景：
- 需要大量文件修改但不确定结果的探索性任务
- 并行跑多个方案对比，互不干扰
- 自动化测试、CI 验证等需要干净环境的操作

### 实例

```yaml
---
name: experimental-refactor
description: |
  Tries refactoring approaches in an isolated worktree.
  Use when exploring risky refactoring that shouldn't affect the main branch.
isolation: worktree
tools: Read, Write, Edit, Bash
---
```

```text
You are a refactoring agent working in an isolated environment.
Feel free to make changes—they won't affect the main branch.
Summarize what you changed and whether the approach was successful.
```

---

### 后台运行（Background）

子代理支持前台和后台两种运行方式，行为不同：

| 运行方式 | 行为 | 限制 |
| --- | --- | --- |
| **前台（Foreground）** | 阻塞主对话直到完成，权限提示和澄清问题会实时传给用户 | 无特殊限制 |
| **后台（Background）** | 并行执行，不打断主对话；启动前会预先确认所需权限 | 无法使用 MCP，无法进行交互式澄清；权限不足时任务失败而非暂停等待 |

Claude 会根据任务特性自动判断使用前台还是后台。你也可以手动控制：
- `Ctrl + B`：将当前运行的子代理切换到后台
- `Ctrl + F`（按两次确认）：终止所有后台代理
- 在 frontmatter 中设置 `background: true`：该代理始终以后台方式运行
- 消息开头加 `&`：将该任务作为后台任务发送给 claude.ai 网页端
- 通过 `/tasks` 命令随时查看后台任务进度

如需彻底禁用后台任务功能，设置以下环境变量：

~~~
export CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=1
~~~

如需统一设置所有子代理使用的模型（例如主对话用 Opus 做复杂推理，子代理用 Sonnet 节省成本）：

~~~
export CLAUDE_CODE_SUBAGENT_MODEL="claude-sonnet-4-6"
~~~

---

### 生命周期钩子（Hooks）

子代理支持以下钩子事件，可用于日志记录、操作验证、结果通知等自动化场景：

| 钩子事件 | 触发时机 | 典型用途 |
| --- | --- | --- |
| `SubagentStart` | 子代理启动时 | 记录启动日志、初始化环境 |
| `SubagentStop` | 子代理任务完成时 | 记录结果、触发下游任务、发送通知。包含 `agent_id` 和 `agent_transcript_path` 字段 |
| `PreToolUse` | 工具调用前 | 校验操作合法性，脚本退出码为 2 时可阻止该工具调用 |
| `PostToolUse` | 工具调用后 | 格式化输出、生成变更记录 |

高级用法：通过 `PreToolUse` 钩子动态控制工具行为。例如，让数据库代理只允许执行只读 SQL 查询，任何写操作都被脚本拦截：

### 实例

```yaml
---
name: db-analyst
description: Read-only database analysis agent. Use for querying and reporting, never for writes.
tools: Bash
---
```

在 `.claude/settings.json` 中再配合钩子限制 Bash 只能执行只读查询：

```text
PreToolUse on Bash -> validate-readonly-query.sh
```

如果脚本检测到非 `SELECT` 操作，则以退出码 2 返回并阻止该命令执行。

```text
You are a database analyst. Only run SELECT queries.
Never run INSERT, UPDATE, DELETE, DROP, or any DDL statements.
```

---

### 禁用特定子代理

如果你不希望 Claude 自动调用某个内置子代理，可以在 `.claude/settings.json` 中将其加入禁用列表：

### 实例

```json
{
  "subagents": {
    "deny": ["explore", "plan"]
  }
}
```

禁用后 Claude 不会自动调用它们，但你仍可手动显式调用。

### 如何调用子代理

#### 1、自动委托

Claude 会根据 `description` 字段自动判断任务是否适合某个子代理，无需在提示中手动指定：

~~~
帮我检查最近的代码改动质量
~~~

#### 2、显式调用

在提示中明确指定要使用哪个代理：

~~~
让 code-reviewer 子代理检查最近的改动
~~~

---

### 典型使用模式

#### 1、隔离高输出任务

将产生大量中间输出的任务（如运行测试、扫描日志）放到子代理中，主对话只接收精简的结论：

~~~
使用子代理运行所有测试，只返回失败的测试和根因分析
~~~

#### 2、并行研究

同时启动多个子代理处理不同模块，大幅缩短分析时间：

~~~
并行使用子代理分别分析认证模块、数据库模块和 API 模块，汇总后给出整体架构建议
~~~

#### 3、串联子代理流水线

将复杂工作流拆分为多个专职代理，按顺序传递结果：

~~~
先用 code-reviewer 找出问题，再用 optimizer 子代理修复这些问题
~~~

串联工作流的设计原则：每个代理只做一件事，并通过清晰的"输入 → 处理 → 输出 → 交接信号"定义接口。以下是一个生产实践中的三段式流水线示例：
- **pm-spec**：读取需求，生成工作规格，确认后标记 `READY_FOR_ARCH`
- **architect-review**：验证设计约束，产出架构决策记录（ADR），标记 `READY_FOR_BUILD`
- **implementer-tester**：实现代码和测试，更新文档，标记 `DONE`

通过 `SubagentStop` 钩子监听状态文件，自动触发下一个代理，无需手动干预。

#### 4、并行代码审查

~~~
同时启动 style-checker、security-scanner、test-coverage 三个子代理并行审查，
将审查时间从数分钟压缩到数十秒
~~~

---

### 什么时候该用子代理

**适合用子代理的场景：**
- 任务自包含，可以给出明确的输入和期望输出
- 输出量很大，会显著占用主对话上下文
- 需要强约束（只读、隔离 worktree 等）
- 同类任务会重复出现，值得固化为代理
- 涉及多个独立子域，可以并行处理

**适合用主对话的场景：**
- 需要频繁来回调整，交互性强
- 多阶段任务有强依赖关系，上下文需要连续
- 快速、小改动，启动代理的开销不值得
- 实际经验：超过 3～4 个子代理后，管理成本可能反而降低整体效率

> 子代理不能再创建子代理（防止无限嵌套）。如需嵌套逻辑，请使用 **Skills**。

---

### 最佳实践

**description 怎么写**
- 用动作式描述：`当用户要求审查 / 分析 / 检查代码质量时调用`
- 说明前置条件：`在规格文档确认后使用，产出架构决策记录`
- 写清楚代理的边界和不擅长的场景，防止被错误调用

**工具权限设计（最小权限原则）**
- 只读代理（审查、审计）：`Read, Grep, Glob`
- 研究代理（信息收集）：`Read, Grep, Glob, WebFetch, WebSearch`
- 实现代理（写代码）：`Read, Write, Edit, Bash, Glob, Grep`

**单一职责**
- 每个代理只做一件事，给出清晰的输入 / 输出 / 交接规则
- 不要试图用一个代理包办所有事情

**系统提示建议**
- 在系统提示中明确代理的性格：`请保持批判性，不要只说好话`
- 指出代理的弱点和局限，避免过度自信
- 如果启用了记忆，在系统提示里写入"主动维护记忆"的指令，让代理自动执行

**并行 vs 串行的选择**
- 各子域之间相互独立 → 优先并行，节省时间
- 下一步依赖上一步的结果 → 必须串行，保证质量
- 避免为了并行而并行：10 个并行代理处理简单任务反而浪费 Token 和协调成本

---

## Claude Code 插件

插件（Plugin）是 Claude Code 中 **最高级别的扩展机制**，用于将命令、代理、Skills、钩子、MCP、LSP 等能力 **打包、版本化、共享和分发**。

**插件 = 一组可复用的 Claude Code 扩展能力集合**

一个插件可以包含：
- 斜杠命令（Slash Commands）
- 子代理（Agents）
- Skills（能力说明）
- Hooks（事件钩子）
- MCP 服务器（外部工具/服务）
- LSP 服务器（代码智能）

> 插件的核心目标只有一个：
> **让 Claude Code 的能力像工具箱"一样被复用，而不是每个项目重复配置**

---

### 插件 vs 独立配置（如何选择）

Claude Code 支持两种扩展方式：

| 方式 | 命令形式 | 适合场景 |
| --- | --- | --- |
| **独立配置**（`.claude/`） | `/hello` | 个人使用、单项目、快速实验 |
| **插件**（`.claude-plugin/`） | `/plugin-name:hello` | 团队共享、跨项目、版本化 |

#### 什么时候用独立配置？

- 只在当前项目使用
- 个人工作流
- 尚未稳定的实验性配置
- 想要简短命令名（如 `/review`）

#### 什么时候用插件？

- 要在 **多个项目复用**
- 要 **分享给团队或社区**
- 需要 **版本控制、升级、回滚**
- 计划通过市场分发
- 可以接受命名空间命令（避免冲突）

> **最佳实践：**
> 先在 `.claude/` 中迭代 → 稳定后打包为插件

---

### 插件的最小结构（必须记住）

~~~
my-plugin/
├── .claude-plugin/
│   └── plugin.json     # 插件清单（必需）
├── commands/           # 斜杠命令
├── agents/             # 子代理
├── skills/             # Skills
├── hooks/              # 钩子
├── .mcp.json           # MCP 配置
└── .lsp.json           # LSP 配置
~~~

**重要规则**
- `.claude-plugin/` 目录中 **只能放 `plugin.json`**
- 其他目录必须在插件根目录

---

### 插件清单（plugin.json）

插件的"身份证"，决定：
- 插件名称
- 命令命名空间
- 版本
- 作者信息

示例：

~~~
{
  "name": "my-first-plugin",
  "description": "A greeting plugin to learn the basics",
  "version": "1.0.0",
  "author": { "name": "Your Name" }
}
~~~

关键字段说明：

| 字段 | 作用 |
| --- | --- |
| name | 唯一标识 + 命令命名空间 |
| description | 插件市场中展示 |
| version | 语义化版本控制 |
| author | 可选，归属说明 |

---

### 斜杠命令（最常用插件能力）

#### 1、命令定义方式

- 位于 `commands/` 目录
- 每个命令 = 一个 Markdown 文件
- 文件名 = 命令名

示例：

~~~
commands/hello.md
~~~

对应命令：

~~~
/my-first-plugin:hello
~~~

#### 2、命令内容示例

~~~

---

description: Greet the user with a friendly message

---

Greet the user warmly and ask how you can help them today.
~~~

#### 3、命令参数

使用 `$ARGUMENTS` 捕获用户输入：

~~~
Greet the user named "$ARGUMENTS" warmly.
~~~

调用：

~~~
/my-first-plugin:hello Alex
~~~

---

### 本地测试插件（开发必会）

使用 `--plugin-dir` 直接加载插件目录：

~~~
claude --plugin-dir ./my-plugin
~~~

特点：
- 不需要安装
- 修改后需重启 Claude Code
- 支持同时加载多个插件

~~~
claude --plugin-dir ./plugin-a --plugin-dir ./plugin-b
~~~

---

### 插件还能做什么

| 能力 | 用途 |
| --- | --- |
| Commands | 自定义斜杠命令 |
| Agents | 专用子代理 |
| Skills | 教会 Claude 何时用某种能力 |
| Hooks | 自动化（写完文件后执行命令等） |
| MCP | 连接外部服务（GitHub、DB、API） |
| LSP | 代码智能（跳转、类型检查） |

---

### 插件市场（Plugin Marketplace）

插件通过 **市场**分发，本质是一个插件目录仓库。

#### 官方市场

- 默认已添加
- 运行 `/plugin` → **Discover**

![image](https://www.runoob.com/wp-content/uploads/2026/01/9019efba-efb3-4311-8286-a784ad0e6356.png)

安装插件：

~~~
/plugin install plugin-name@claude-plugins-official
~~~

---

### 插件安装范围

| 范围 | 说明 |
| --- | --- |
| 用户范围 | 仅你自己，所有项目 |
| 项目范围 | 当前仓库，团队共享 |
| 本地范围 | 当前仓库，仅你 |

推荐：
- 团队工具 → **项目范围**
- 个人效率工具 → **用户范围**

---

### 典型插件分类

#### 1、代码智能（LSP）

- TypeScript、Python、Go、Rust 等
- 提供跳转定义、引用、类型错误

需要本地安装对应语言服务器

#### 2、外部集成（MCP）

- GitHub / GitLab
- Jira / Notion
- Slack / Figma
- Vercel / Supabase

> 本质： **插件 = MCP 服务器 + 配置**

#### 3、开发工作流

- Git 提交、PR
- 代码审查代理
- 插件开发工具

---

### 插件管理常用命令

~~~
/plugin                # 打开插件管理器
/plugin install         # 安装插件
/plugin uninstall       # 卸载
/plugin enable/disable  # 启用 / 禁用
/plugin marketplace add # 添加市场
/plugin marketplace rm  # 移除市场
~~~

---

### 从 `.claude/` 迁移到插件（核心思路）

| 原来 | 迁移后 |
| --- | --- |
| `.claude/commands` | `plugin/commands` |
| `.claude/agents` | `plugin/agents` |
| `settings.json hooks` | `plugin/hooks/hooks.json` |

迁移后：
- 插件版本优先生效
- 可删除旧 `.claude/` 配置避免重复

---

### 什么时候你一定要用插件？

- 你已经有 **稳定的 Claude 工作流**
- 你在 **反复复制 `.claude/`**
- 团队成员开始问你："这个怎么配置？"
- 你希望 Claude 像 IDE 插件一样可控

> **插件，是 Claude Code 从"个人 AI 助手"走向"工程化工具"的分水岭**

---

## Claude Code 插件参考手册

插件是 Claude Code 的 **功能扩展核心**，能帮你添加自定义斜杠命令、子代理、自动化钩子等能力。本教程从 **核心组件**、 **配置规范**、 **CLI 管理**三个维度，带你快速掌握插件的使用与开发要点。

---

### 插件核心组件：5类扩展能力

插件通过 5 类组件实现功能扩展，每个组件都有固定的存储位置和格式要求。

| 组件类型 | 存储位置 | 文件格式 | 核心作用 |
| --- | --- | --- | --- |
| **命令** | 插件根目录 `commands/` | 带前置元数据的 Markdown 文件 | 新增自定义斜杠命令，比如 `/deploy` `/code-review` |
| **代理** | 插件根目录 `agents/` | Markdown 文件 | 提供专属子代理，比如代码审查代理、性能测试代理 |
| **技能** | 插件根目录 `skills/` | 包含 `SKILL.md` 的目录 | 让 Claude 自动识别场景并调用，比如 PDF 解析、数据可视化 |
| **钩子** | 插件根目录 `hooks/hooks.json` 或 `plugin.json` 内联 | JSON 配置文件 | 监听 Claude 事件并自动响应，比如文件编辑后自动格式化代码 |
| **MCP 服务器** | 插件根目录 `.mcp.json` 或 `plugin.json` 内联 | JSON 配置文件 | 连接外部工具（如 GitHub、Jira），将其功能转为 Claude 可用工具 |
| **LSP 服务器** | 插件根目录 `.lsp.json` 或 `plugin.json` 内联 | JSON 配置文件 | 提供代码智能能力，比如语法检查、跳转定义、悬停提示 |

#### 关键组件示例

**1、钩子配置示例**：文件编辑后自动格式化

~~~
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PLUGIN_ROOT}/scripts/format-code.sh"
          }
        ]
      }
    ]
  }
~~~

}：支持 Go 语言智能提示

~~~
{
  "go": {
    "command": "gopls",
    "args": ["serve"],
    "extensionToLanguage": {
      ".go": "go"
    }
  }
}
~~~

---

### 插件基础规范：安装范围与清单

#### 1. 安装范围：决定插件的可用范围

安装插件时需选择范围，不同范围对应不同的配置文件和使用场景：

| 范围 | 配置文件路径 | 适用场景 |
| --- | --- | --- |
| `user` | `~/.claude/settings.json` | 个人所有项目通用（默认） |
| `project` | `.claude/settings.json` | 团队共享，随代码仓同步 |
| `local` | `.claude/settings.local.json` | 项目专属，被 `.gitignore` 忽略 |
| `managed` | `managed-settings.json` | 托管插件，只读且自动更新 |

#### 2. 插件清单：`plugin.json` 必知要点

`plugin.json` 是插件的 **核心配置文件**，存放于 `.claude-plugin/` 目录下，用于定义插件元数据和组件路径。

##### a、必需字段

| 字段 | 类型 | 要求 | 示例 |
| --- | --- | --- | --- |
| `name` | string | 唯一标识符，kebab-case 格式 | `"go-code-helper"` |

##### b、核心元数据字段

~~~
{
  "version": "1.0.0", // 语义化版本
  "description": "提供 Go 语言代码智能和调试能力",
  "author": {
    "name": "Dev Team",
    "email": "dev@example.com"
  },
  "license": "MIT"
}
~~~

##### c、组件路径字段

用于指定自定义组件的位置，路径需 **相对插件根目录**且以 `./` 开头：

~~~
{
  "commands": ["./custom-commands/deploy.md"],
  "agents": "./custom-agents/",
  "hooks": "./hooks.json"
}
~~~

##### d、环境变量

`${CLAUDE_PLUGIN_ROOT}`：插件根目录的绝对路径，用于脚本和配置中引用插件内文件，避免路径错误。

#### 3. 标准插件目录结构

~~~
my-plugin/
├── .claude-plugin/           # 元数据目录
│   └── plugin.json          # 插件清单（必需）
├── commands/                 # 自定义斜杠命令
├── agents/                   # 子代理定义
├── skills/                   # 自动技能
├── hooks/                    # 事件钩子配置
├── .mcp.json                # MCP 服务器配置
├── .lsp.json                # LSP 服务器配置
└── scripts/                 # 钩子执行脚本
~~~

> 注意：`commands/` `agents/` 等组件目录必须在插件 **根目录**，不能放在 `.claude-plugin/` 内。

---

### 插件管理：CLI 命令速查

通过 Claude Code CLI 可快速完成插件的安装、卸载、启用/禁用等操作，适合脚本和自动化场景。

| 命令 | 用途 | 示例 |
| --- | --- | --- |
| `claude plugin install <插件名> -s <范围>` | 安装插件 | `claude plugin install go-lsp --scope project` |
| `claude plugin uninstall <插件名>` | 卸载插件 | `claude plugin uninstall go-lsp` |
| `claude plugin enable <插件名>` | 启用已禁用插件 | `claude plugin enable go-lsp` |
| `claude plugin disable <插件名>` | 禁用插件（不卸载） | `claude plugin disable go-lsp` |
| `claude plugin update <插件名>` | 更新插件到最新版本 | `claude plugin update go-lsp` |

---

### 调试与排错：常见问题解决

#### 1. 调试命令

运行以下命令查看插件加载详情，定位配置和加载问题：

~~~
claude --debug
~~~

可查看：插件加载状态、清单语法错误、组件注册情况、MCP/LSP 服务器初始化日志。

#### 2. 高频问题与解决方案

| 问题 | 原因 | 解决办法 |
| --- | --- | --- |
| 插件未加载 | `plugin.json` 语法错误或缺少必需字段 | 用 `claude plugin validate` 验证 JSON 语法，补充 `name` 字段 |
| 自定义命令不显示 | 命令文件放在 `.claude-plugin/` 内 | 将 `commands/` 目录移到插件根目录 |
| 钩子脚本不执行 | 脚本没有可执行权限 | 运行 `chmod +x scripts/your-script.sh` 赋予权限 |
| LSP 提示 `Executable not found` | 未安装对应语言服务器 | 安装二进制文件（如 Go 需安装 `gopls`） |
| MCP 服务器启动失败 | 路径使用绝对路径，未用 `${CLAUDE_PLUGIN_ROOT}` | 替换为环境变量引用，如 `${CLAUDE_PLUGIN_ROOT}/server` |

---

### 版本管理与分发

- **版本规范**：遵循语义化版本 `MAJOR.MINOR.PATCH`，比如 `1.2.3`
- **分发渠道**：通过插件市场分发，或直接分享插件目录（需包含完整结构）
- **更新日志**：建议在插件根目录添加 `CHANGELOG.md`，记录版本更新内容

---

## Claude Code 钩子

Claude Code 钩子是 **用户自定义的 Shell 命令**，会在 Claude Code 生命周期的特定节点自动执行。

借助钩子，你可以对 Claude Code 的行为实现精准控制，确保某些操作（如代码格式化、日志记录） **必定触发**，而非依赖大模型自主选择是否执行。

#### 钩子的典型应用场景

钩子能帮你实现很多实用功能，常见场景包括：
- **消息通知**：当 Claude Code 等待输入或需要权限时，自动发送桌面/邮件提醒
- **自动格式化**：编辑 `.ts` 文件后自动运行 `prettier`，修改 `.go` 文件后执行 `gofmt`
- **操作日志**：记录 Claude 执行的所有命令，用于合规审计或调试排障
- **代码规范校验**：若 Claude 生成的代码不符合项目规范（如命名规则），自动给出反馈
- **文件权限管控**：阻止 Claude 修改生产环境配置文件或敏感目录（如 `.env`、`.git`）

相比于通过提示词约束 Claude 的行为，钩子是 **应用级的硬规则**，只要触发对应事件就会强制执行，稳定性和可靠性更高。

#### 重要安全提醒

钩子运行时会 **直接使用当前系统环境的凭证**（如环境变量、用户权限），存在一定安全风险：
- 恶意钩子代码可能泄露你的敏感数据（如 API 密钥、项目源码）
- 错误的钩子命令可能导致文件误删、系统异常

**必做安全操作**：

1. 注册钩子前，务必逐行审查命令的逻辑和权限
2. 避免在钩子中执行来源不明的脚本
3. 详细安全最佳实践，可参考官方文档的 [安全注意事项](https://code.claude.com/docs/zh-CN/hooks#security-considerations)

---

### 钩子事件类型说明

Claude Code 内置了多个生命周期事件，你可以为不同事件绑定钩子命令。每个事件会传递不同的上下文数据，且对 Claude 行为的影响方式不同。

| 事件名称 | 触发时机 | 核心作用 |
| --- | --- | --- |
| `PreToolUse` | 工具调用 **之前** | 可拦截工具执行（如阻止修改敏感文件），并向 Claude 反馈调整建议 |
| `PermissionRequest` | 弹出权限请求对话框时 | 自动批准或拒绝权限申请 |
| `PostToolUse` | 工具调用 **完成后** | 执行后置操作（如格式化代码、记录日志） |
| `UserPromptSubmit` | 用户提交提示词后、Claude 处理前 | 预处理用户输入（如补充上下文信息） |
| `Notification` | Claude 发送通知时 | 自定义通知方式（如桌面弹窗、短信提醒） |
| `Stop` | Claude 完成响应时 | 执行收尾工作（如清理临时文件） |
| `SubagentStop` | 子代理任务完成时 | 处理子代理的执行结果 |
| `PreCompact` | 即将执行上下文压缩操作时 | 自定义压缩规则 |
| `SessionStart` | 启动新会话或恢复旧会话时 | 初始化会话环境（如加载项目配置） |
| `SessionEnd` | 会话结束时 | 保存会话数据、清理环境 |

---

### 快速入门：实现命令日志记录

下面以 **记录 Claude 执行的所有 Bash 命令** 为例，带你一步步完成钩子的配置和使用。

#### 前置准备

安装 `jq` 工具（用于命令行解析 JSON 数据）：
- **macOS**：`brew install jq`
- **Linux**：`sudo apt install jq` / `sudo yum install jq`
- **Windows**：下载 [jq 官方安装包](https://stedolan.github.io/jq/download/)，或通过 WSL 安装

#### 步骤 1：打开钩子配置界面

在 Claude Code 的交互界面中，输入斜杠命令 `/hooks`，回车后选择要绑定的事件 —— 这里我们选 `PreToolUse`（工具调用前触发，适合记录命令）。

#### 步骤 2：添加事件匹配器

匹配器的作用是 **限定钩子的触发条件**，只在指定工具被调用时执行钩子命令。

1. 选择 `+ Add new matcher…`
2. 输入匹配关键词 `Bash`，表示仅当 Claude 调用 Bash 工具时触发钩子
   > 小技巧：输入 `*` 可以匹配 **所有工具**，实现全局钩子

#### 步骤 3：添加钩子命令

选择 `+ Add new hook…`，输入以下命令（功能：提取命令内容和描述，写入日志文件）：

~~~
jq -r '"\(.tool_input.command) - \(.tool_input.description // "无描述信息")"' >> ~/.claude/bash-command-log.txt
~~~

命令说明：
- `jq -r ...`：提取 JSON 中的命令（`command`）和描述（`description`），无描述时显示"无描述信息"
- `>> ~/.claude/...`：将内容追加写入用户主目录下的日志文件

#### 步骤 4：选择配置存储位置

配置保存位置决定钩子的生效范围：
- **User settings **：保存到用户级配置（`~/.claude/settings.json`），**所有项目生效**
- **Project settings **：保存到当前项目配置（`.claude/settings.local.json`），**仅当前项目生效**

这里我们选 `User settings`，实现全局命令日志记录。选择后按 `Esc` 键退出配置界面，钩子即注册完成。

#### 步骤 5：验证钩子配置

1. 再次输入 `/hooks` 命令，可查看已配置的钩子列表
2. 或直接打开配置文件 `~/.claude/settings.json`，会看到如下配置内容：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '\"\\(.tool_input.command) - \\(.tool_input.description // \"无描述信息\")\"' >> ~/.claude/bash-command-log.txt"
          }
        ]
      }
    ]
  }
}
```

#### 步骤 6：测试钩子效果

1. 在 Claude Code 中输入指令：`帮我执行 ls 命令`
2. 执行完成后，在终端中查看日志文件：

```bash
cat ~/.claude/bash-command-log.txt
```

3. 若日志中出现如下内容，说明钩子配置成功：

```text
ls - Lists files and directories
```

---

### 实用钩子示例

下面提供几个常用的钩子配置，你可以直接复制使用或按需修改。

> 📌 完整示例代码可参考官方仓库：[bash 命令验证器示例](https://github.com/anthropics/claude-code/blob/main/examples/hooks/bash_command_validator_example.py)

#### 示例 1：自动格式化 TypeScript 文件

功能：编辑/写入 `.ts` 文件后，自动用 `prettier` 格式化代码

~~~
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write", // 匹配“编辑”和“写入”工具
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | { read file_path; if echo \"$file_path\" | grep -q '\\.ts$'; then npx prettier --write \"$file_path\"; fi; }"
          }
        ]
      }
    ]
  }
}
~~~

#### 示例 2：自动修复 Markdown 文件格式

功能：为无语言标签的代码块自动补全标签、清理多余空行

##### 第一步：添加钩子配置

~~~
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/markdown_formatter.py"
          }
        ]
      }
    ]
  }
}
~~~

##### 第二步：创建格式化脚本

在项目目录下新建文件 `.claude/hooks/markdown_formatter.py`，粘贴以下代码：

### 实例

#!/usr/bin/env python3
"""

Markdown 格式化工具：自动补全代码块语言标签、清理多余空行

"""
import json
import sys
import re
import os

def detect_language(code):
"""根据代码内容自动检测编程语言"""
code = code.strip()

# 检测 JSON

if re.search(r'^\s*[{\[]', code):
try:
json.loads(code)
return 'json'
except:
pass

# 检测 Python

if re.search(r'^\s*def\s+\w+\s*\(', code, re.M) or re.search(r'^\s*(import|from)\s+\w+', code, re.M):
return 'python'

# 检测 JavaScript/TypeScript

if re.search(r'\b(function\s+\w+\s*\(|const\s+\w+\s*=)', code) or re.search(r'=>|console\.(log|error)', code):
return 'javascript'

# 检测 Bash

if re.search(r'^#!.*\b(bash|sh)\b', code, re.M) or re.search(r'\b(if|then|fi|for|in|do|done)\b', code):
return 'bash'

# 默认文本格式

return 'text'

def format_markdown(content):
"""格式化 Markdown 内容"""

# 为无标签代码块补全语言

fence_pattern = r'(?ms)^([ \t]{0,3})```([^\n]*)\n(.*?)(\n\1```)\s*$'
def add_lang(match):
indent, info, body, closing = match.groups()
if not info.strip():
lang = detect_language(body)
return f"{indent}```{lang}\n{body}{closing}\n"
return match.group(0)
content = re.sub(fence_pattern, add_lang, content)

# 清理多余空行（仅清理代码块外的内容）

content = re.sub(r'\n{3,}', '\n\n', content)
return content.rstrip() + '\n'

if __name__ == "__main__":
try:

# 读取 Claude 传递的 JSON 数据

input_data = json.load(sys.stdin)
file_path = input_data.get('tool_input', {}).get('file_path', '')

# 仅处理 .md/.mdx 文件

if not file_path.endswith(('.md', '.mdx')):
sys.exit(0)

# 读取并格式化文件

if os.path.exists(file_path):
with open(file_path, 'r', encoding='utf-8') as f:
content = f.read()
formatted_content = format_markdown(content)

# 仅在内容变化时写入

if formatted_content != content:
with open(file_path, 'w', encoding='utf-8') as f:
f.write(formatted_content)
print(f"已格式化 Markdown 文件：{file_path}")
except Exception as e:
print(f"格式化失败：{e}", file=sys.stderr)
sys.exit(1)

##### 第三步：赋予脚本执行权限

~~~
chmod +x .claude/hooks/markdown_formatter.py
~~~

#### 示例 3：Claude 等待输入时发送桌面通知

功能：当 Claude 需要用户输入时，自动弹出桌面提醒（适用于 Linux/macOS）

~~~
{
  "hooks": {
    "Notification": [
      {
        "matcher": "", // 匹配所有通知事件
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude Code 提示' '请你输入指令或确认权限'"
          }
        ]
      }
    ]
  }
}
~~~

#### 示例 4：禁止修改敏感文件

功能：阻止 Claude 编辑 `.env`、`package-lock.json` 等敏感文件

~~~
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json, sys; data=json.load(sys.stdin); path=data.get('tool_input',{}).get('file_path',''); sys.exit(2 if any(p in path for p in ['.env', 'package-lock.json', '.git/']) else 0)\""
          }
        ]
      }
    ]
  }
}
~~~

说明：脚本返回状态码 `2` 时，Claude Code 会拦截此次工具调用，从而阻止文件修改。

---

### Claude Code 钩子参考手册

#### 配置文件路径

| 配置级别 | 文件路径 | 生效范围 |
| --- | --- | --- |
| 用户级 | `~/.claude/settings.json` | 所有项目 |
| 项目级 | `.claude/settings.json` | 当前项目 |
| 本地项目级（不提交） | `.claude/settings.local.json` | 当前项目，不纳入版本控制 |
| 托管策略级 | 管理员指定路径 | 企业/团队统一管控 |

#### 核心配置结构

Hooks 按 **事件+匹配器**组织，支持 `command`（执行 Shell 命令）和 `prompt`（调用 LLM 决策）两种类型。

~~~
{
  "hooks": {
    "【钩子事件名】": [
      {
        "matcher": "【工具匹配规则】", // 部分事件可省略
        "hooks": [
          {
            "type": "command/prompt",
            "command": "【Shell 命令】", // type=command 时必填
            "prompt": "【LLM 提示词】",  // type=prompt 时必填
            "timeout": 30 // 可选，超时时间（秒）
          }
        ]
      }
    ]
  }
}
~~~

#### 匹配器规则（仅适用于工具类事件）

| 匹配规则 | 示例 | 说明 |
| --- | --- | --- |
| 精确匹配 | `Write` | 仅匹配 `Write` 工具 |
| 多工具匹配 | Edit \| Write | 匹配 `Edit` 或 `Write` 工具 |
| 前缀匹配 | `Notebook.*` | 匹配所有以 `Notebook` 开头的工具 |
| 全匹配 | `*` / 空字符串 | 匹配所有工具 |

#### 特殊配置技巧

| 场景 | 配置方法 | 示例 |
| --- | --- | --- |
| 引用项目内脚本 | 使用环境变量 `$CLAUDE_PROJECT_DIR` | `"command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/check-style.sh"` |
| 插件 Hooks | 插件内配置 `hooks/hooks.json`，用 `${CLAUDE_PLUGIN_ROOT}` 引用插件文件 | `"command": "${CLAUDE_PLUGIN_ROOT}/scripts/format.sh"` |
| 组件级 Hooks（Skill/Agent） | 在组件 frontmatter 中定义，作用域仅限组件生命周期 | 见下方 **扩展配置**表格 |

#### 扩展配置（Skill/Agent/斜杠命令）

扩展配置允许直接在 Skill、Agent 或自定义斜杠命令的定义中内嵌 Hooks 配置，这类 Hooks 仅在对应组件被激活并运行时生效，组件执行完成后会自动清理，不会影响全局会话。

##### 支持的钩子事件

仅支持 `PreToolUse`、`PostToolUse`、`Stop` 三类事件，与全局 Hooks 功能一致，但作用域仅限当前 Skill/斜杠命令的生命周期。

##### 特有配置项

- `once: true`（可选）：设置为 `true` 时，该 Hooks 在整个会话中仅运行一次，首次成功执行后会自动移除，避免重复触发。

##### 完整配置示例

~~~

---

# Skill/斜杠命令的基础信息

name: secure-operations
description: 执行Shell命令前先做安全校验的工具

# Hooks 配置段

hooks:
  PreToolUse:

# 匹配器：仅拦截Bash工具调用

- matcher: "Bash"
      hooks:
- type: "command"

# 要执行的安全校验脚本

          command: "./scripts/security-check.sh"

# 会话内仅执行一次

          once: true

# 超时时间（秒），避免脚本卡死

          timeout: 15

---

~~~

#### Agent 中的 Hooks 配置

##### 支持的钩子事件

同样仅支持 `PreToolUse`、`PostToolUse`、`Stop` 三类事件，作用域仅限该子 Agent 的任务执行周期。

##### 特有配置项

无额外专属配置项，不支持 `once: true`（Agent 每次执行任务时，Hooks 都会触发）。

##### 完整配置示例

~~~

---

# Agent 的基础信息

name: code-reviewer
description: 自动审查代码修改并运行代码检查的子代理

# Hooks 配置段

hooks:
  PostToolUse:

# 匹配器：拦截Edit（编辑）或Write（写入）工具

```yaml
hooks:
  PreToolUse:
    - matcher: "Edit|Write"
      hooks:
        - type: "command"
          command: "./scripts/run-linter.sh"
          timeout: 30
```

**注意事项：**

1. 组件内 Hooks 的匹配器规则与全局 Hooks 完全一致：支持精确匹配（如 `"Write"`）、多工具匹配（如 `"Edit|Write"`）、通配匹配（`"*"`），且区分大小写；
2. 组件内 Hooks 与全局 Hooks 会并行执行：若全局和组件内同时配置了针对同一事件的 Hooks，触发时两类 Hooks 会一起运行，互不冲突；
3. 配置格式要求：组件内 Hooks 需写在 frontmatter（`---` 包裹的区域）中，遵循 YAML 语法，缩进错误会导致配置失效；
4. 脚本路径建议：优先使用相对路径（如 `./scripts/xxx.sh`），或借助 `$CLAUDE_PROJECT_DIR` 环境变量指定绝对路径，确保组件在任意目录下都能找到脚本。

#### 钩子事件--工具类事件（支持匹配器）

| 事件名 | 触发时机 | 常见匹配器 | 核心作用 |
| --- | --- | --- | --- |
| `PreToolUse` | 工具调用 **前** | `Bash`/`Edit`/`Write`/`Read` | 拦截工具执行、修改入参、自动批准/拒绝权限 |
| `PermissionRequest` | 弹出权限请求对话框时 | 同 `PreToolUse` | 自动处理权限申请，无需用户手动确认 |
| `PostToolUse` | 工具调用 **成功后** | 同 `PreToolUse` | 执行后置操作（如代码格式化、日志记录） |
| `Notification` | Claude 发送通知时 | `permission_prompt`/`idle_prompt`/`auth_success` | 自定义通知方式（如桌面弹窗、邮件提醒） |
| `PreCompact` | 执行上下文压缩操作前 | `manual`（手动触发）/`auto`（自动触发） | 自定义压缩规则、备份重要上下文 |

#### 钩子事件--会话/任务类事件（无匹配器）

| 事件名 | 触发时机 | 核心作用 |
| --- | --- | --- |
| `UserPromptSubmit` | 用户提交提示后、Claude 处理前 | 验证提示合法性、补充上下文信息 |
| `Stop` | 主 Agent 完成响应时（用户中断不触发） | 智能判断是否需要继续执行任务 |
| `SubagentStop` | 子 Agent 任务完成时 | 评估子任务结果，决定是否终止 |
| `SessionStart` | 启动/恢复会话时 | 初始化环境、加载项目配置、设置持久化环境变量 |
| `SessionEnd` | 会话结束时 | 清理临时文件、记录会话日志、保存工作状态 |

### 钩子类型对比

Claude Code 支持两种钩子类型，满足不同场景需求。

| 特性 | `command` 类型（命令钩子） | `prompt` 类型（提示词钩子） |
| --- | --- | --- |
| 执行方式 | 运行 Shell 命令/脚本 | 调用 LLM（默认 Haiku 模型）做智能决策 |
| 决策逻辑 | 基于代码逻辑的 **确定性判断** | 基于上下文的 **灵活语义判断** |
| 配置难度 | 需要编写脚本，门槛较高 | 只需写提示词，简单易上手 |
| 响应速度 | 快（本地执行） | 较慢（需 API 调用） |
| 适用场景 | 代码格式化、日志记录、权限拦截等固定规则 | 任务完成度评估、复杂意图判断等灵活场景 |
| 核心配置 | `command` + `timeout` | `prompt` + `timeout`（可引用 `$ARGUMENTS` 占位符） |

#### 输入与输出 -- 钩子输入（stdin 传入 JSON）

所有钩子都会收到通用字段，部分事件附带特定字段。

| 字段类型 | 通用字段 | 说明 |
| --- | --- | --- |
| 基础信息 | `session_id` | 会话唯一标识 |
|   | `transcript_path` | 对话记录文件路径 |
|   | `cwd` | 钩子执行时的当前工作目录 |
|   | `permission_mode` | 当前权限模式（default/plan/acceptEdits 等） |
|   | `hook_event_name` | 当前触发的钩子事件名 |

**各事件特有字段示例**

| 事件名 | 特有字段 | 示例 |
| --- | --- | --- |
| `PreToolUse` | `tool_name`/`tool_input` | `{"tool_name":"Write","tool_input":{"file_path":"/test.txt"}}` |
| `UserPromptSubmit` | `prompt` | `{"prompt":"帮我写一个排序函数"}` |
| `SessionEnd` | `reason` | `{"reason":"clear/logout/other"}` |

#### 钩子输出（两种方式）

##### 方式1：退出代码（简单场景）

通过退出代码传递执行状态，`stdout`/`stderr` 用于反馈信息。

| 退出代码 | 含义 | 行为说明 |
| --- | --- | --- |
| `0` | 执行成功 | `stdout` 可返回 JSON 做高级控制；部分事件（如 `UserPromptSubmit`）会将 `stdout` 加入上下文 |
| `2` | 阻止操作 | 仅使用 `stderr` 作为错误消息反馈给 Claude， **阻止当前事件继续执行** |
| 其他非零值 | 非阻塞错误 | `stderr` 仅在详细模式（`ctrl+o`）显示，不影响事件执行 |

**退出代码 2 在各事件中的行为**

| 事件名 | 触发行为 |
| --- | --- |
| `PreToolUse` | 阻止工具调用，向 Claude 展示 `stderr` |
| `UserPromptSubmit` | 阻止提示处理，擦除用户输入 |
| `Stop` | 阻止 Claude 停止，强制继续工作 |
| `PostToolUse`/`Notification` | 仅展示 `stderr`，不影响已完成操作 |

##### 方式2：JSON 输出（高级场景）

退出代码为 `0` 时，可通过 `stdout` 返回 JSON 实现精细化控制，核心字段如下：

| 通用 JSON 字段 | 作用 |
| --- | --- |
| `continue: true/false` | 是否允许事件继续执行（`false` 优先于其他规则） |
| `stopReason` | `continue=false` 时，展示给用户的原因 |
| `systemMessage` | 向用户显示的警告信息 |

**事件特有 JSON 字段示例**

| 事件名 | 特有字段 | 示例 |
| --- | --- | --- |
| `PreToolUse` | `permissionDecision`（allow/deny/ask） | `{"hookSpecificOutput":{"permissionDecision":"allow","updatedInput":{"file_path":"/new.txt"}}}` |
| `UserPromptSubmit` | `decision: block/undefined` | `{"decision":"block","reason":"提示包含敏感信息"}` |
| `PostToolUse` | `additionalContext` | `{"hookSpecificOutput":{"additionalContext":"文件已格式化完成"}}` |

---

### MCP 工具的 Hooks 配置

MCP 工具可与 Hooks 无缝集成，通过特定命名规则匹配。

#### MCP 工具命名规则

~~~
mcp__<服务器名>__<工具名>
~~~

示例：`mcp__github__search_repositories`、`mcp__filesystem__read_file`

#### 匹配示例

~~~
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__memory__.*", // 匹配 memory 服务器的所有工具
        "hooks": [{"type": "command", "command": "echo '内存操作日志' >> log.txt"}]
      },
      {
        "matcher": "mcp__.*__write.*", // 匹配所有服务器的写操作工具
        "hooks": [{"type": "command", "command": "./validate-write.py"}]
      }
    ]
  }
}
~~~

---

### 安全与调试

#### 安全最佳实践

| 安全要点 | 具体操作 |
| --- | --- |
| 输入校验 | 严格校验 `tool_input` 中的文件路径、命令参数，防止路径遍历（如 `../`） |
| 变量引用 | Shell 命令中使用 `"$VAR"` 而非 `$VAR`，避免参数注入 |
| 权限最小化 | 钩子脚本仅赋予必要权限，避免使用 `sudo` 等高危命令 |
| 敏感文件排除 | 拦截对 `.env`、`.git`、密钥文件的操作 |
| 命令审查 | 注册钩子前，手动执行命令验证逻辑，确认无恶意行为 |

#### 调试技巧

| 问题类型 | 排查步骤 |
| --- | --- |
| 钩子不生效 | 1. 执行 `/hooks` 命令检查配置是否注册<br>2. 验证 JSON 语法是否正确<br>3. 检查匹配器规则是否与工具名一致（区分大小写） |
| 命令执行失败 | 1. 手动运行钩子命令，确认是否可正常执行<br>2. 检查脚本是否有可执行权限（`chmod +x script.sh`）<br>3. 使用绝对路径调用脚本，避免环境变量问题 |
| 查看详细日志 | 启动 Claude Code 时添加 `--debug` 参数，查看钩子执行全过程 |

> 参考文档：如需查看钩子的完整功能说明，可查阅官方 [钩子参考文档](https://code.claude.com/docs/zh-CN/hooks)。

---

## Agent Skills（智能体技能）

Agent 是智能体，Skills 是技能的意思，Agent Skills（智能体技能）是将专业知识、工作流规范固化为可复用资产的核心工具。

Agent Skills 本质上是一个模块化的 Markdown 文件，能教会 AI 工具 （如 Claude、GitHub Copilot、Cursor 等） 执行特定任务，且支持自动触发、团队共享与工程化管理，彻底告别重复的提示词输入。

Agent Skills 的本质不是工具，而是：

> **行为规范 + 专业知识 + 使用时机的组合**

Skills 基础内容参考：[Skills 教程](https://www.runoob.com/ai-agent/skills-agent.html)

**核心形式：**
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
- **核心形态：** 一个包含 SKILL.md 的目录
- **必填元数据：** SKILL.md 开头的 YAML 块，需包含 name（名称）和 description（描述），启动时预加载至系统提示词

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
---

## skill-creator 使用

在 Claude Code 生态中，Skill（技能） 是扩展 Agent 能力的重要机制。

Skill 本质上是一个模块化知识包，可以给 Claude 添加：
- 专业领域知识
- 固定工作流程
- API / 工具使用方式
- 模板和脚本

简单理解：

~~~
Skill = 给 AI 写的一份操作说明书。
~~~

而 skill-creator 就是 Anthropic 官方提供的 Skill 开发助手，帮助开发者创建、优化和打包技能。

GitHub 地址：[https://github.com/anthropics/skills/tree/main/skills/skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator)

#### Skill 是什么

在 Claude Code 中，Skill 是一种 可复用的能力扩展包。

一个 Skill 通常包含：

~~~
skill-name/
├── SKILL.md        # 核心说明（必须）
├── scripts/        # 可执行脚本
├── references/     # 文档或知识
├── assets/         # 附加资源
~~~

其中最重要的是 SKILL.md。

示例：

~~~

---

name: video-tool
description: Video processing CLI for editing and transcribing videos

---

# Video Tool Skill

Use the video-tool CLI to process videos.

## Quick Start

video-tool video download -u URL
video-tool generate transcript -i video.mp4
~~~

SKILL.md 的作用就是：教 Claude 如何使用某个工具或完成某个流程。

#### 安装 skill-creator

首先安装 Anthropic 的 skills 集合。

~~~
npx skills add https://github.com/anthropics/skills --skill skill-creator
~~~

或者：

~~~
claude install anthropics/skills/skill-creator
~~~

安装完成后，你就可以在 Claude 中调用：

~~~
/skill-creator
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/49085a23-ff2a-4ca5-af60-f9f1872b8f4b.png)

skill-creator 提供完整的 Skill 开发流程工具链。

安装完成后，本地会下载该 Skill，其中包含：

~~~
skills/skill-creator/
├── SKILL.md          ← 核心说明文件
├── agents/           ← 内置的评审助手
├── eval-viewer/      ← 测试结果可视化工具
├── references/       ← 参考文档（数据格式说明等）
└── scripts/          ← 自动化脚本（打包、测评等）
~~~

Skill Creator 的工作流程可以概括为一个循环：

~~~
想清楚需求
    ↓
起草 SKILL.md
    ↓
设计测试用例
    ↓
运行测试（有 Skill vs 没有 Skill，对比效果）
    ↓
评估结果（看报告 + 打分）
    ↓
根据反馈修改 SKILL.md
    ↓
重复，直到满意
    ↓
打包成 .skill 文件
~~~

---

### 用 skill-creator 创建 Skill

#### 需求询问

Claude 会先问你几个问题，帮你把需求想清楚。你不需要一次说完所有细节，像聊天一样回答就行。

**Claude 通常会问：**

1. 这个 Skill 具体做什么？
2. 什么情况下触发（用户说什么、上传什么文件）？
3. 输出是什么格式？
4. 有没有特殊要求（固定模板、特定格式规范……）？

**示例对话：**

~~~
你：我想做一个 Skill，把会议录音的文字稿整理成结构化的会议纪要。

Claude：好的，我来问几个问题帮你确认需求：
1. 纪要里需要包含哪些内容？（比如：时间、参与人、决议、行动事项……）
2. 输出格式是 Word 文档、Markdown，还是直接在对话里回复？
3. 有没有固定的纪要模板？

你：需要包含会议主题、时间、参与人、讨论要点、决议事项、
    下一步行动（含负责人和截止日期）。
    输出 Word 文档。
    有模板，我来上传。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/328e2713-f38c-4079-a832-555b7a19e9f0.png)

可以按 Tab 键或方向按键切换菜单详情，并提交。

这个阶段你的目标： 把所有细节和边界情况说清楚，不要跳过，后面测试时踩的坑大多源于这里没说清楚。

也可以直接回车，它也能帮我们创建：

![image](https://www.runoob.com/wp-content/uploads/2026/03/f5d438c9-dc20-4465-9d30-c22edaf87f3e.png)

之后 Claude 会写出 SKILL.md 草稿，大致长这样：

![image](https://www.runoob.com/wp-content/uploads/2026/03/7d25c5a6-fba5-41ae-99f7-1421774459ee.png)

接下来还会创建参考文件：

![image](https://www.runoob.com/wp-content/uploads/2026/03/6eddc6e5-d124-43ae-9fd6-3e81b2b0aa48.png)

创建完成后，会验证技能结构是否正确，等待就好了。

验证通过！就会打包 Skill，并展示目录结构：

![image](https://www.runoob.com/wp-content/uploads/2026/03/4c93a5ac-6e28-48eb-82e9-a23b3568783f.png)

测试下，输入会议内容：

~~~
整理以下会议纪要：一、会议基本信息
  会议主题：学习平台内容优化会议
  参会人员：张三、李四、王五
  会议时间：2025-XX-XX 14:00-15:00
  记录人：张三
  二、会议内容
  讨论当前团队在编程教学中使用的案例素材，一致认为runoob教程简洁易懂，适合作为新手入门参考。
  确定后续内部培训将优先采用 runoob 上的基础语法、实战小案例进行讲解。
  安排李四整理 runoob 中 Python、Java 高频知识点，形成内部速查文档。
  下次会议检查文档完成情况。
  三、待办事项
  李四：整理 runoob 核心知识点文档，下周一前完成。
  全体：提前熟悉 runoob 对应章节，便于下次讨论。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/c1fc4b18-f5d5-4c3c-8a28-d6ffc7c02410.png)

---

## Claude Code 控制 Chrome 浏览器

Claude Code 的 Chrome 集成功能，让你可以直接从命令行（CLI）或 VS Code 扩展中控制浏览器。Claude 能够：
- 打开网页、点击按钮、填写表单
- 读取浏览器控制台日志（console errors、network requests、DOM 状态）
- 与任何你已登录的网站交互（Gmail、Notion、Google Docs 等）
- 录制浏览器操作为 GIF 文件
- 在多个标签页之间协同工作

**核心优势**：Claude 共享你浏览器的登录状态，无需额外的 API 密钥或重新认证。

> 这个功能目前处于 Beta 阶段，允许你在终端（CLI）或 VS Code 中用自然语言指令，让 Claude 直接控制你的 Chrome 浏览器，进行导航、点击、输入、调试、数据提取等操作，无需手动切换窗口。

---

### 安装与准备

#### 前置条件

使用该功能前，你需要满足以下要求：

| 条件 | 说明 |
| --- | --- |
| Claude Code 版本 | v2.0.73 或更高 |
| Chrome 扩展版本 | Claude in Chrome v1.0.36 或更高 |
| Anthropic 账户 | 需要 **直接付费计划**（Pro、Max、Teams 或 Enterprise） |
| 不支持的渠道 | 通过 Amazon Bedrock、Google Vertex AI、Microsoft Foundry 访问的用户 **不可用** |

#### 安装步骤

**1. 安装浏览器扩展**：
- 打开 Chrome/Edge，访问 [Chrome Web Store - Claude](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn)。
- 点击"添加至 Chrome"并安装。
- 安装后建议重启浏览器，确保 Native Messaging Host 正确加载。

![image](https://www.runoob.com/wp-content/uploads/2026/03/8f9ff3fd-6d17-4058-9e37-97cbc03445a3.png)

**2. 安装/更新 Claude Code**：
- 按照官方快速入门安装 Claude Code（CLI 或 VS Code 扩展）。

**3. 权限与连接**：
- 首次运行时，扩展会自动安装 Native Messaging Host。
- 常见路径（macOS 示例）：`~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`

---

### 如何启动并连接

##### 在 CLI 中启动

- **推荐方式**：直接带 Chrome 启动

  ```bash
  claude --chrome
  ```

- 或在已有会话中启用：

  ```text
  /chrome
  ```

  然后选择启用或重新连接。

- **设为默认**（方便但会增加上下文消耗）：
  在 `/chrome` 菜单中选择 "Enabled by default"。

#### 在 VS Code 中使用

- 在提示框中输入 `@browser` + 你的指令，例如：
  ~~~
  @browser 打开 localhost:3000，检查控制台是否有错误
  ~~~

#### 检查连接状态

在 Claude Code 会话中输入 `/chrome`，可以查看状态、管理权限、重新连接扩展。

---

### 基本使用方法

Claude Code 通过 **自然语言提示**控制浏览器，无需编写复杂代码。Claude 会自动解析你的指令，执行相应操作，并返回结果（包括截图、控制台日志、提取的数据等）。 **核心能力**：
- 导航：打开网址
- 交互：点击元素、输入文字、滚动页面
- 读取：查看 DOM、控制台错误、网络请求
- 自动化：表单填写、数据提取、跨标签页操作
- 其他：录制 GIF 演示、保存 CSV 等

**提示技巧**：
- 描述要清晰、逐步（例如"先打开网址，然后点击搜索框，输入 XXX 并告诉我结果"）。
- 可以结合本地文件（如读取 CSV 批量操作）。
- 长任务时，Claude 会分步执行并报告进度。

---

### 实用示例

**示例 1：简单导航与交互**

~~~
去到 https://www.baidu.com，点击搜索框，输入 "python"，告诉我出现的搜索结果。
~~~

**示例 2：本地网页测试与调试**

~~~
我刚更新了登录表单验证。请打开 localhost:3000，用无效数据提交表单，检查错误消息是否正确显示，并查看控制台日志。
~~~

**示例 3：表单自动化（结合本地文件）**

~~~
我有一个 contacts.csv 文件，里面有客户联系方式。对于每一行，打开 crm.example.com，点击“添加联系人”，填写姓名、邮箱和电话，然后提交。
~~~

**示例 4：Google Docs 等已登录网站操作**

~~~
根据最近的 Git 提交，起草一份项目更新，并添加到我的 Google Doc（网址：docs.google.com/document/d/abc123）。
~~~

**示例 5：数据提取与保存**

~~~
打开产品列表页面，提取每个商品的名称、价格和库存状态，保存为 CSV 文件。
~~~

**示例 6：录制演示 GIF**

~~~
录制一个 GIF，展示从购物车添加商品到结账确认的完整流程。
~~~

**示例 7：多站点工作流**

~~~
查看我明天的日历，对于每个有外部参会者的会议，查找他们公司的网站并添加备注。
~~~

---
