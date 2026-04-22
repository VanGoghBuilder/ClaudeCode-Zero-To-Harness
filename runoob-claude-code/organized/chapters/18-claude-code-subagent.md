## Claude Code 子代理（Subagent）

在 Claude Code 中，你可以创建 **专门的 AI 子代理（Subagent）**，用于处理特定类型的任务，从而获得更好的上下文隔离、更强的约束控制和更高的执行效率。

子代理运行在 **独立的上下文窗口**中，每个子代理都可以拥有独立的系统提示、指定的模型、明确的工具访问权限、独立的权限模式，以及跨会话的持久记忆。当 Claude 判断你的请求符合某个子代理的描述时，会自动将任务委托给它，由子代理独立完成后返回结果。

> 子代理只接收自身的系统提示和基础环境信息（如工作目录）， **不会继承完整的 Claude Code 系统提示**，这保证了行为的纯净和可控。

---

### 为什么要使用子代理

子代理的核心价值在于 **隔离 + 专业化**，主要体现在以下几个方面： **保留主对话上下文 **：把探索、日志分析等"重任务"放到子代理中，主对话只接收结论摘要，不被大量中间输出淹没。研究表明，并行运行三个子代理分析 5 万行项目约需 45 秒，而串行执行需要 3 分钟。**强制执行约束 **：通过工具白名单或黑名单限制子代理的能力，例如只读分析、禁止执行危险命令。**行为专业化 **：为特定领域（代码审查、调试、数据分析）设计专用 AI，在系统提示中明确指出代理的能力边界，避免不必要的调用。**控制成本 **：将简单任务交给 Haiku，将复杂分析交给 Sonnet，通过环境变量 `CLAUDE_CODE_SUBAGENT_MODEL` 统一设置所有子代理使用的模型。**跨项目复用**：用户级子代理一次配置，所有项目可用。

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

---name:code-reviewer # 必填：唯一标识，小写字母 + 连字符description:Reviews code for quality, best practices, and security issues.
Invoke when the user asks to review, audit, or check code quality.

# 必填：决定 Claude 何时自动调用此代理，建议写成"何时调用 + 能做什么"tools:Read, Grep, Glob # 工具白名单（只能使用这些工具）model:sonnet # 指定模型：haiku / sonnet / opus / inheritpermissionMode:default # 权限模式（见下方权限模式章节）memory:project # 持久记忆范围（见下方记忆章节）

---

You are a senior code reviewer.Analyze code and provide actionable feedback organized by severity:Critical / Major / Minor.

Update your agent memory with recurring patterns, conventions, and known issues you discover.

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

# 示例一：只允许只读操作（使用 tools 白名单）

---name:safe-researcherdescription:Research agent with read-only access. Use when analyzing code without making changes.tools:Read, Grep, Glob, Bash # 只开放这四个工具，Write 和 Edit 不在白名单内无法使用

---

### 实例

# 示例二：继承所有工具但禁止写文件（使用 disallowedTools 黑名单）

---name:no-writesdescription:Analysis agent that inherits all tools except file writes.disallowedTools:Write, Edit # 保留 MCP 工具，仅排除 Write 和 Edit

---

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

---name:code-reviewerdescription:Reviews code for quality and best practices. Invoke when reviewing code changes.memory:user # 用户级记忆：跨项目积累审查经验

---

You are a code reviewer.
As you review code, update your agent memory with patterns, conventions,
and recurring issues you discover in this codebase.

在对话中控制记忆的使用方式：
- 任务开始前：`请先查阅你的记忆，再开始审查`（让代理利用已有经验）
- 任务结束后：`任务完成后，把你发现的规律保存到记忆中`（持续积累）
- 也可直接在系统提示中写入"主动维护记忆"的指令，让代理自动执行，无需每次手动提醒

---

### worktree 隔离模式

设置 `isolation: worktree` 后，子代理会在临时 git worktree 中运行，与主仓库完全隔离。适合以下场景：
- 需要大量文件修改但不确定结果的探索性任务
- 并行跑多个方案对比，互不干扰
- 自动化测试、CI 验证等需要干净环境的操作

### 实例

---name:experimental-refactordescription:Tries refactoring approaches in an isolated worktree.
Use when exploring risky refactoring that shouldn't affect main branch.isolation:worktree # 在临时 worktree 中运行，修改不影响主仓库tools:Read, Write, Edit, Bash

---

You are a refactoring agent working in an isolated environment.
Feel free to make changes—they won't affect the main branch.
Summarize what you changed and whether the approach was successful.

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

---name:db-analystdescription:Read-only database analysis agent. Use for querying and reporting, never for writes.tools:Bash

# 在 .claude/settings.json 中配置钩子：

# PreToolUse on Bash -> validate-readonly-query.sh

# 脚本检测到非 SELECT 操作时以退出码 2 返回，阻止该命令执行

---

You are a database analyst. Only run SELECT queries.
Never run INSERT, UPDATE, DELETE, DROP, or any DDL statements.

---

### 禁用特定子代理

如果你不希望 Claude 自动调用某个内置子代理，可以在 `.claude/settings.json` 中将其加入禁用列表：

### 实例

{
"subagents": {
"deny": ["explore", "plan"] // 禁用 explore 和 plan 内置代理
// 禁用后 Claude 不会自动调用它们，但你仍可手动显式调用
}
}

---

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

**适合用子代理的场景： **
- 任务自包含，可以给出明确的输入和期望输出
- 输出量很大，会显著占用主对话上下文
- 需要强约束（只读、隔离 worktree 等）
- 同类任务会重复出现，值得固化为代理
- 涉及多个独立子域，可以并行处理

**适合用主对话的场景： **
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

