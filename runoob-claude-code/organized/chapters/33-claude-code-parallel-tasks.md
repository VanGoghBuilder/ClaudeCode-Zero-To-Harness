## Claude Code 并行任务

Claude Code 提供了多种并行执行任务的机制，让你可以同时处理多个工作项，显著提升开发效率。本章将介绍三种主要的并行化方案：Subagents（子代理）、Agent Teams（代理团队）和 Git Worktree（Git 工作树），帮助你根据不同的使用场景选择最合适的方式。

---

### 并行任务概述

当面对复杂任务时，单个 Claude 实例可能会力不从心——上下文过长、响应变慢、任务交织。Claude Code 提供了三层并行化机制，满足不同复杂度和协作需求：

| 机制 | 适用场景 | 协作方式 | 复杂度 |
| --- | --- | --- | --- |
| **Subagents** | 专注型任务，只需关注结果 | 单向汇报（结果返回主代理） | 低 |
| **Agent Teams** | 需要讨论与协作的复杂工作 | 多向通信（队友直接互发消息） | 中 |
| **Git Worktree** | 多个任务需要隔离的代码环境 | 完全独立（各自的工作目录） | 中 |

> **选择建议： **
> - 只需并行处理、相互独立的子任务 → 使用 **Subagents**
> - 需要多个 Agent 相互讨论、协调工作 → 使用 **Agent Teams**
> - 多个任务需要操作同一仓库的不同分支 → 使用 **Git Worktree**

---

### Subagents（子代理）

#### 什么是 Subagents

Subagent 是一个独立运行的 Claude 实例，它有自己的上下文和任务焦点。主 Claude 可以创建多个 Subagent，每个 Subagent 负责一个特定的子任务：

~~~
┌─────────────────────────────────────────────────────────┐
│                    主 Claude                            │
│                    (协调者)                              │
└─────────────────────────────────────────────────────────┘
                        │
                        ▼
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
┌──────────┐    ┌──────────┐    ┌──────────┐
│ Agent A  │    │ Agent B  │    │ Agent C  │
│ 代码审查  │    │ 测试生成  │    │ 文档编写  │
└──────────┘    └──────────┘    └──────────┘
        │               │               │
        └───────────────┴───────────────┘
                        │
                        ▼
                 返回结果给主代理
~~~

Subagent 最多可并行运行 49 个，完全满足大多数并行处理需求。

#### 内置 Subagents

Claude Code 预置了以下内置 Subagent：

| Agent | 模型 | 工具 | 用途 |
| --- | --- | --- | --- |
| `Explore` | Haiku（快速、低延迟） | 只读工具 | 文件发现、代码搜索、代码库探索 |
| `Plan` | 继承主对话 | 只读工具 | 规划模式下的代码库研究 |
| `General-purpose` | 继承主对话 | 所有工具 | 复杂研究、多步骤操作、代码修改 |
| `statusline-setup` | Sonnet | — | 运行 `/statusline` 配置状态行 |
| `Claude Code Guide` | Haiku | — | 回答 Claude Code 功能问题 |

#### 创建 Subagent

##### 方式一：使用 /agents 命令

运行 `/agents` 命令，按提示创建新的 Subagent：

~~~
/agents
~~~

选择 **Create new agent**，然后选择保存位置，描述功能后让 Claude 生成配置。

##### 方式二：手动创建 Subagent 文件

Subagent 文件使用 YAML frontmatter 进行配置，然后是 Markdown 中的系统提示：

### 实例

---name:code-reviewerdescription:Reviews code for quality and best practicestools:Read, Glob, Grepmodel:sonnet

---

You are a code reviewer. When invoked, analyze the code and provide
specific, actionable feedback on quality, security, and best practices.

#### Subagent 配置字段

| 字段 | 必需 | 描述 |
| --- | --- | --- |
| `name` | 是 | 唯一标识符，使用小写字母和连字符 |
| `description` | 是 | 描述 Claude 何时应该委托给此 Subagent |
| `tools` | 否 | Subagent 可以使用的工具列表 |
| `disallowedTools` | 否 | 要拒绝的工具 |
| `model` | 否 | 使用的模型：sonnet、opus、haiku 或 inherit |
| `permissionMode` | 否 | 权限模式：default、acceptEdits、auto、dontAsk、bypassPermissions、plan |
| `maxTurns` | 否 | Subagent 停止前的最大代理轮数 |
| `skills` | 否 | 启动时加载的 skills |
| `mcpServers` | 否 | 对此 Subagent 可用的 MCP 服务器 |
| `memory` | 否 | 持久内存范围：user、project 或 local |
| `background` | 否 | 是否始终作为后台任务运行 |
| `isolation` | 否 | 设置为 worktree 在临时 git worktree 中运行 |

#### 控制 Subagent 能力

##### 工具限制

### 实例

# 只允许特定工具

---name:safe-researcherdescription:Research agent with restricted capabilitiestools:Read, Grep, Glob, Bash

---

# 排除特定工具

---name:no-writesdescription:Inherits every tool except file writesdisallowedTools:Write, Edit

---

##### 模型选择

### 实例

# 使用 Haiku（快速、便宜）

---name:quick-searcherdescription:Quick file searchmodel:haiku

---

# 使用 Opus（强大、昂贵）

---name:deep-analystdescription:Deep code analysismodel:opus

---

# 继承主对话模型

---name:general-purposemodel:inherit

---

#### 调用 Subagent

##### 方式一：自然语言

~~~
Use the test-runner subagent to fix failing tests
~~~

##### 方式二：@-mention

~~~
@"code-reviewer (agent)" look at the auth changes
~~~

##### 方式三：命令行启动

~~~
claude --agent code-reviewer
~~~

#### 前台与后台运行

- **前台 Subagent**：阻塞主对话直到完成
- **后台 Subagent**：并发运行，可按 `Ctrl+B` 切换

---

### Agent Teams（代理团队）

#### 什么是 Agent Teams

Agent Teams 可以让你协调多个 Claude Code 实例一起工作。想象一下，你同时开启 4 个 Claude 会话（ABCD），其中一个充当团队负责人（Leader），负责协调工作、分配任务并整合结果。其他三个成员各自独立工作，拥有各自的上下文窗口，同时还能直接相互沟通。

#### Subagents vs Agent Teams

| 特性 | Subagents | Agent Teams |
| --- | --- | --- |
| **上下文** | 自己的上下文窗口；结果返回给调用者 | 自己的上下文窗口；完全独立 |
| **通信** | 仅将结果汇报给主代理 | 队友之间直接互发消息 |
| **协调** | 主代理负责管理所有工作 | 共享任务列表，支持自我协调 |
| **最适合** | 只需关注结果的专注型任务 | 需要讨论与协作的复杂工作 |
| **Token 成本** | 较低：结果汇总回主上下文 | 较高：每位队友都是独立的 Claude 实例 |

简单来说： **Subagent 是打工人向老板汇报，Agent Teams 是平等协作的项目组**。

#### 启用 Agent Teams

Agent Teams 默认禁用。需要在 `settings.json` 中添加：

### 实例

{
"env": {
"CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
}
}

或设置环境变量：

~~~
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
~~~

> **要求**：Claude Code v2.1.32 或更高版本

#### 启动 Agent Team

启用后，用自然语言描述任务和团队结构：

### 实例

I'm designing a CLI tool that helps developers track TODO comments across
their codebase. Create an agent team to explore this from different angles: one
teammate on UX, one on technical architecture, one playing devil's advocate.

#### 控制 Agent Team

##### 显示模式

- **In-process**：所有队友在主终端内运行，使用 `Shift+Down` 循环浏览队友
- **Split panes**：每个队友获得自己的 tmux/iTerm2 窗格

### 实例

{
"teammateMode": "in-process"
}

##### 指定队友数量和模型

~~~
Create a team with 4 teammates to refactor these modules in parallel.
Use Sonnet for each teammate.
~~~

##### 任务分配

- 负责人可显式分配任务
- 队友可自我认领未分配、未阻止的任务
- 任务有三种状态：待处理、进行中、已完成

##### 与队友交互

- **In-process**：`Shift+Down` 循环浏览 → 输入消息 → `Enter` 查看 → `Escape` 中断
- **Split-pane**：点击队友窗格直接交互

##### 关闭队友

~~~
Ask the researcher teammate to shut down
~~~

#### Agent Teams 架构

| 组件 | 角色 |
| --- | --- |
| **Team Lead** | 创建团队、生成队友并协调工作的主 Claude Code 会话 |
| **Teammates** | 各自处理分配任务的独立 Claude Code 实例 |
| **Task List** | 队友认领和完成的共享工作项列表 |
| **Mailbox ** | 代理之间通信的消息系统 |**存储位置： **

- Team config: `~/.claude/teams/{team-name}/config.json`
- Task list: `~/.claude/tasks/{team-name}/`

---

### Git Worktree 支持

#### 问题背景

多个 Agent 同时干活时，可能会"打架"。想象一下这个场景：你让 Agent A 去重构数据库模块，让 Agent B 去修复登录页面的 bug。两个任务看起来互不相关，但它们都在同一个代码仓库、同一分支上工作。Agent A 正在修改 `utils.py`，Agent B 也在修改 `utils.py`。一个保存了一个版本，另一个覆盖了不同的版本，最终导致冲突、报错，甚至数据丢失。

这不是 AI 的问题，这是底层 Git 仓库结构的限制。

#### 什么是 Git Worktree

Git Worktree 是 Git 的一个功能，允许你在同一个仓库上挂载多个独立的工作目录。每个工作目录有自己的分支、自己的 HEAD、自己的暂存区，但共用同一个 `.git` 数据库（历史记录、对象存储）。

### 类比

日常生活比喻："同一家公司（主仓库）有多个办公室（worktree），
每个办公室有不同的团队（Agent），各自处理不同的项目（分支），
但都共享公司的数据库（.git）和历史记录。"

#### 使用 Git Worktree

##### 命令行启动

~~~

# 创建 feature-auth 分支的工作树

claude -w feature-auth

# 同时创建 tmux 会话

claude -w feature-auth --tmux

# 使用传统 tmux

claude -w feature-auth --tmux=classic
~~~

##### CLI 参数

| 参数 | 描述 | 示例 |
| --- | --- | --- |
| `--worktree`, `-w` | 在隔离的 git worktree 中启动 Claude | `claude -w feature-auth` |
| `--tmux` | 为 worktree 创建 tmux 会话 | `claude -w feature-auth --tmux` |

Worktree 创建位置：`<repo>/.claude/worktrees/<name>`

##### 桌面端支持

在 Claude 桌面端应用中，进入 Code 选项卡，直接勾选 worktree mode 即可开启工作区模式。

#### Subagent + Worktree 隔离

可以让自定义 Subagent 始终在自己的 worktree 中运行：

### 实例

---name:background-refactorerdescription:Background refactoring agentisolation:worktree

---

---

### 使用示例

#### 示例一：并行代码审查

### 使用 Subagent

# 让多个 Subagent 同时审查代码的不同方面

Use the security-reviewer subagent to check for security issues
Use the performance-reviewer subagent to analyze performance impact
Use the test-coverage subagent to validate test coverage

### 使用 Agent Teams

Create an agent team to review PR #142. Spawn three reviewers:
- One focused on security implications
- One checking performance impact
- One validating test coverage
Have them each review and report findings.

#### 示例二：并行研究

~~~
Research the authentication, database, and API modules in parallel using separate subagents
~~~

#### 示例三：使用竞争假设进行调查

### 实例

Users report the app exits after one message instead of staying connected.
Spawn 5 agent teammates to investigate different hypotheses. Have them talk to
each other to try to disprove each other's theories, like a scientific
debate. Update the findings doc with whatever consensus emerges.

#### 示例四：多分支开发

~~~

# 终端 1：重构用户模块

claude -w feature/user-refactor

# 终端 2：修复登录 bug

claude -w bugfix/login-issue

# 终端 3：开发新功能

claude -w feature/new-dashboard
~~~

#### 示例五：链接 Subagent

~~~
Use the code-reviewer subagent to find performance issues, then use the optimizer subagent to fix them
~~~

---

### 最佳实践

#### Subagent 最佳实践

1. **保持专注**：每个 Subagent 应有明确、单一的任务
2. **限制工具**：根据任务需要限制 Subagent 的工具访问权限
3. **选择合适模型**：简单任务使用 Haiku，复杂任务使用 Sonnet 或 Opus
4. **使用后台运行**：长时间任务使用后台 Subagent，避免阻塞主对话
5. **恢复 Subagent**：需要继续工作时，要求 Claude 恢复 Subagent

#### Agent Teams 最佳实践

1. **给队友足够的上下文**：在生成提示中包含特定于任务的详细信息
2. **选择适当的团队规模**：大多数工作流从 3-5 个队友开始
3. **适当调整任务大小**：每个队友 5-6 个任务，保持生产力
4. **等待队友完成**：告诉负责人等待队友完成任务后再进行
5. **从研究和审查开始**：从不需要编写代码的任务开始
6. **避免文件冲突**：分解工作使每个队友拥有不同的文件集
7. **监控和指导**：检查进度，重定向不起作用的方法

#### Git Worktree 最佳实践

1. **每个任务一个 Worktree**：避免在同一个 worktree 中处理多个不相关的任务
2. **使用 tmux 管理**：同时运行多个 Claude 会话时，使用 tmux 便于切换
3. **清理不需要的 Worktree**：任务完成后及时删除 worktree

---

### 故障排除

#### Agent Teams 常见问题

| 问题 | 解决方案 |
| --- | --- |
| 队友未出现 | 检查任务是否足够复杂、tmux 是否安装 |
| 过多权限提示 | 在权限设置中预批准常见操作 |
| 队友在错误后停止 | 检查输出并给予额外指示或生成替代队友 |
| 负责人提前关闭 | 告诉负责人继续或等待队友完成 |
| 孤立的 tmux 会话 | 使用 `tmux ls` 和 `tmux kill-session -t <session-name>` 清理 |

---

### 实用 Subagent 示例

#### 代码审查者

### 实例

---name:code-reviewerdescription:Expert code review specialist. Proactively reviews code for quality, security, and maintainability.tools:Read, Grep, Glob, Bashmodel:inherit

---

You are a senior code reviewer ensuring high standards of code quality and security.
When invoked:1. Run git diff to see recent changes
2. Focus on modified files
3. Begin review immediately
Review checklist:- Code is clear and readable
- Functions and variables are well-named
- No duplicated code
- Proper error handling
- No exposed secrets or API keys
- Input validation implemented
- Good test coverage
- Performance considerations addressed
Provide feedback organized by priority:- Critical issues (must fix)
- Warnings (should fix)
- Suggestions (consider improving)

#### 调试器

### 实例

---name:debuggerdescription:Debugging specialist for errors, test failures, and unexpected behavior.tools:Read, Edit, Bash, Grep, Glob

---

You are an expert debugger specializing in root cause analysis.
When invoked:1. Capture error message and stack trace
2. Identify reproduction steps
3. Isolate the failure location
4. Implement minimal fix
5. Verify solution works
Debugging process:- Analyze error messages and logs
- Check recent code changes
- Form and test hypotheses
- Add strategic debug logging
- Inspect variable states

#### 只读数据库查询器

### 实例

---name:db-readerdescription:Execute read-only database queries. Use when analyzing data or generating reports.tools:Bashhooks:PreToolUse:- matcher:"Bash"hooks:- type:commandcommand:"./scripts/validate-readonly-query.sh"

---

You are a database analyst with read-only access. Execute SELECT queries to answer questions about the data.

