# 参考、环境与工程集成

> 阅读提示：本章更适合在你已经理解了项目上下文、日常交互和扩展能力之后再查阅；如果你想把前面几卷重新汇总成一套 Harness Engineering 视角，继续读 [Claude Code 的 Harness Engineering 总纲](./06-harness-engineering-guide.md)。

## 包含章节

- Claude Code CLI 参考手册
- Claude Code 环境变量
- Claude Code Git 工作流
- Claude Code GitHub Actions

---

## Claude Code CLI 参考手册

---

### 一、CLI 命令

| 命令 | 描述 | 示例 |
| --- | --- | --- |
| `claude` | 启动交互式 REPL | `claude` |
| `claude "query"` | 带初始提示启动 REPL | `claude "explain this project"` |
| `claude -p "query"` | SDK 查询后退出 | `claude -p "explain this function"` |
| cat file \| claude -p "query" | 处理管道输入内容 | cat logs.txt \| claude -p "explain" |
| `claude -c` | 继续当前目录最近对话 | `claude -c` |
| `claude -c -p "query"` | 通过 SDK 继续对话 | `claude -c -p "Check for type errors"` |
| `claude -r "<session>" "query"` | 按 ID/名称恢复会话 | `claude -r "auth-refactor" "Finish this PR"` |
| `claude update` | 更新至最新版本 | `claude update` |
| `claude mcp` | 配置 MCP 服务器 | 详见 [Claude Code MCP 文档](https://www.runoob.com/claude-code/claude-code-mcp.html) |

---

### 二、CLI 标志

通过以下标志可自定义 Claude Code 运行行为：

| 标志 | 描述 | 示例 |
| --- | --- | --- |
| `--add-dir` | 添加工作目录（自动验证路径有效性） | `claude --add-dir ../apps ../lib` |
| `--agent` | 指定会话代理（覆盖默认 `agent` 设置） | `claude --agent my-custom-agent` |
| `--agents` | 以 JSON 定义自定义子代理 | `claude --agents '{"reviewer":{"description":"Reviews code","prompt":"You are a code reviewer"}}'` |
| `--allowedTools` | 免权限提示的可用工具（限制工具用 `--tools`） | `"Bash(git log:*)" "Bash(git diff:*)" "Read"` |
| `--append-system-prompt` | 追加内容到默认系统提示（交互/打印模式均生效） | `claude --append-system-prompt "Always use TypeScript"` |
| `--betas` | API 请求附加 Beta 标头（仅限 API 密钥用户） | `claude --betas interleaved-thinking` |
| `--chrome` | 启用 Chrome 浏览器集成（网络自动化/测试） | `claude --chrome` |
| `--continue`, `-c` | 加载当前目录最近对话 | `claude --continue` |
| `--dangerously-skip-permissions` | 跳过权限提示（谨慎操作） | `claude --dangerously-skip-permissions` |
| `--debug` | 启用调试模式，支持类别过滤（如 `"api,hooks"`） | `claude --debug "api,mcp"` |
| `--disallowedTools` | 禁用指定工具（从上下文移除） | `"Bash(git log:*)" "Bash(git diff:*)" "Edit"` |
| `--fallback-model` | 默认模型过载时自动切换（仅打印模式） | `claude -p --fallback-model sonnet "query"` |
| `--fork-session` | 恢复会话时生成新 ID（搭配 `--resume`/`--continue`） | `claude --resume abc123 --fork-session` |
| `--ide` | 自动连接可用 IDE | `claude --ide` |
| `--include-partial-messages` | 输出包含部分流事件（需搭配 `--print` 和 `--output-format=stream-json`） | `claude -p --output-format stream-json --include-partial-messages "query"` |
| `--input-format` | 打印模式输入格式（可选 `text`/`stream-json`） | `claude -p --output-format json --input-format stream-json` |
| `--json-schema` | 输出符合 JSON Schema 的验证结果（仅打印模式） | `claude -p --json-schema '{"type":"object","properties":{...}}' "query"` |
| `--max-turns` | 限制代理轮次（仅打印模式，超限报错退出） | `claude -p --max-turns 3 "query"` |
| `--mcp-config` | 从 JSON 文件/字符串加载 MCP 配置 | `claude --mcp-config ./mcp.json` |
| `--model` | 指定会话模型（支持别名 `sonnet`/`opus` 或完整名称） | `claude --model claude-sonnet-4-5-20250929` |
| `--no-chrome` | 禁用 Chrome 集成 | `claude --no-chrome` |
| `--output-format` | 打印模式输出格式（可选 `text`/`json`/`stream-json`） | `claude -p "query" --output-format json` |
| `--permission-mode` | 按指定权限模式启动 | `claude --permission-mode plan` |
| `--permission-prompt-tool` | 非交互模式指定权限提示处理工具 | `claude -p --permission-prompt-tool mcp_auth_tool "query"` |
| `--plugin-dir` | 加载指定目录插件（可重复使用） | `claude --plugin-dir ./my-plugins` |
| `--print`, `-p` | 打印响应后退出（非交互模式） | `claude -p "query"` |
| `--resume`, `-r` | 按 ID/名称恢复会话，或唤起交互式选择器 | `claude --resume auth-refactor` |
| `--session-id` | 指定会话 ID（需为有效 UUID） | `claude --session-id "550e8400-e29b-41d4-a716-446655440000"` |
| `--setting-sources` | 指定加载的设置源（逗号分隔 `user`/`project`/`local`） | `claude --setting-sources user,project` |
| `--settings` | 加载自定义 JSON 配置文件/字符串 | `claude --settings ./settings.json` |
| `--strict-mcp-config` | 仅使用 `--mcp-config` 配置，忽略其他 MCP 设置 | `claude --strict-mcp-config --mcp-config ./mcp.json` |
| `--system-prompt` | 替换默认系统提示（交互/打印模式均生效） | `claude --system-prompt "You are a Python expert"` |
| `--system-prompt-file` | 从文件加载系统提示（替换默认，仅打印模式） | `claude -p --system-prompt-file ./custom-prompt.txt "query"` |
| `--tools` | 限制可用内置工具（`""` 禁用全部，`"default"` 启用全部） | `claude --tools "Bash,Edit,Read"` |
| `--verbose` | 启用详细日志，展示完整逐轮输出 | `claude --verbose` |
| `--version`, `-v` | 输出版本号 | `claude -v` |

> **提示**：`--output-format json` 标志非常适合脚本和自动化场景，可直接编程解析 Claude 响应结果。

---

### 三、扩展说明

#### 3.1 代理标志格式

`--agents` 标志接收 JSON 对象，用于定义一个或多个自定义子代理。每个子代理需配置唯一名称作为键，值为包含以下字段的对象：

| 字段 | 必填 | 描述 |
| --- | --- | --- |
| `description` | 是 | 描述子代理的适用场景 |
| `prompt` | 是 | 定义子代理行为的系统提示 |
| `tools` | 否 | 子代理专属工具列表（如 `["Read", "Edit"]`，省略则继承全部工具） |
| `model` | 否 | 子代理使用模型（支持 `sonnet`/`opus`/`haiku`，省略则用默认模型） |

**示例**

~~~
claude --agents '{
  "code-reviewer": {
    "description": "Expert code reviewer. Use proactively after code changes.",
    "prompt": "You are a senior code reviewer. Focus on code quality, security, and best practices.",
    "tools": ["Read", "Grep", "Glob", "Bash"],
    "model": "sonnet"
  },
  "debugger": {
    "description": "Debugging specialist for errors and test failures.",
    "prompt": "You are an expert debugger. Analyze errors, identify root causes, and provide fixes."
  }
}'
~~~

#### 系统提示标志

Claude Code 提供 3 种系统提示自定义方式，满足不同使用需求：

| 标志 | 行为 | 适用模式 | 典型用例 |
| --- | --- | --- | --- |
| `--system-prompt` | 替换默认系统提示 | 交互+打印 | 完全自定义 Claude 行为指令 |
| `--system-prompt-file` | 从文件加载提示并替换 | 仅打印 | 团队共享提示模板、版本控制 |
| `--append-system-prompt` | 追加内容到默认提示 | 交互+打印 | 保留默认功能，添加个性化指令 |

##### 使用场景与示例

1. **`--system-prompt`**：完全接管系统提示，清空默认指令
  ~~~
  claude --system-prompt "You are a Python expert who only writes type-annotated code"
  ~~~
2. **`--system-prompt-file`**：从文件读取提示，适合标准化场景
  ~~~
  claude -p --system-prompt-file ./prompts/code-review.txt "Review this PR"
  ~~~
3. **`--append-system-prompt`**：保留默认功能，追加定制要求（推荐大多数场景使用）
  ~~~
  claude --append-system-prompt "Always use TypeScript and include JSDoc comments"
  ~~~

> **注意**：`--system-prompt` 和 `--system-prompt-file` 互斥，不可同时使用。
> **提示**：优先使用 `--append-system-prompt`，既能保留 Claude Code 内置能力，又能满足定制需求；仅需完全自定义时，再使用另外两个标志。

打印模式（`-p`）的详细用法（输出格式、流式传输、程序化集成等），参考 [SDK 文档](https://docs.claude.com/en/docs/agent-sdk)。

---

## Claude Code 环境变量

环境变量是控制 Claude Code 行为的重要方式。

和直接改配置文件相比，环境变量更适合这些场景：

- 临时切换模型或 API 端点
- 在不同终端会话里做实验
- 给 CI / 自动化任务注入配置
- 不想把敏感值直接写进项目文件

本章重点不是罗列一切变量，而是把最常用的变量、配置方式和示例整理清楚，并把所有命令与配置统一放进代码块中。

---

### 一、Claude Code 会从哪里读取环境变量

常见来源有四种：

1. 当前 shell 会话里的 `export`
2. 用户级 `~/.claude/settings.json`
3. 项目级 `.claude/settings.json`
4. IDE / 插件注入的环境变量

通常可以理解为：

- 临时调试，用 shell
- 稳定项目配置，用项目级 `settings.json`
- 跨项目复用，用用户级 `settings.json`

---

### 二、认证相关变量

| 变量名 | 说明 | 常见值 |
| --- | --- | --- |
| `ANTHROPIC_API_KEY` | Claude API Key | `sk-ant-...` |
| `ANTHROPIC_BASE_URL` | API 端点地址 | `https://api.anthropic.com` 或兼容代理地址 |
| `ANTHROPIC_AUTH_TOKEN` | 认证令牌 | 第三方兼容服务提供的 token |

#### Shell 示例

```bash
export ANTHROPIC_API_KEY="sk-ant-xxxxx"
export ANTHROPIC_BASE_URL="https://api.anthropic.com"
```

#### settings.json 示例

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-xxxxx",
    "ANTHROPIC_BASE_URL": "https://custom-proxy.com/v1"
  }
}
```

---

### 三、模型相关变量

| 变量名 | 说明 |
| --- | --- |
| `ANTHROPIC_MODEL` | 主对话默认模型 |
| `ANTHROPIC_SMALL_FAST_MODEL` | 更快、更轻量的模型 |
| `CLAUDE_CODE_SUBAGENT_MODEL` | 子代理统一使用的模型 |
| `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` | 禁用实验性 beta 能力 |

#### 常见使用方式

```bash
export ANTHROPIC_MODEL="claude-opus-4-5"
export CLAUDE_CODE_SUBAGENT_MODEL="claude-sonnet-4-5"
```

这类配置适合：

- 主对话做复杂推理
- 子代理做较轻量、成本更低的任务

---

### 四、工具与行为控制变量

| 变量名 | 说明 |
| --- | --- |
| `CLAUDE_CODE_DISABLE_SLASH_COMMANDS` | 禁用斜杠命令 |
| `CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS` | 禁用内置 Git 提示 |
| `CLAUDE_CODE_USE_POWERSHELL_TOOL` | Windows 上启用 PowerShell 工具 |
| `CLAUDE_CODE_IDE_SKIP_AUTO_INSTALL` | 跳过自动安装 IDE 扩展 |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | 禁用后台任务 |

---

### 五、权限与安全相关变量

| 变量名 | 说明 |
| --- | --- |
| `CLAUDE_CODE_PERMISSION_MODE` | 默认权限模式 |
| `CLAUDE_CODE_ALLOWED_TOOLS` | 工具白名单 |
| `CLAUDE_CODE_DISALLOWED_TOOLS` | 工具黑名单 |

#### 常见权限模式

- `default`：标准权限确认
- `acceptEdits`：自动接受文件编辑
- `dontAsk`：自动拒绝未授权操作
- `bypassPermissions`：跳过权限检查
- `plan`：只做规划，不执行写操作

#### 示例：CI 中使用只读模式

```bash
export CLAUDE_CODE_PERMISSION_MODE="plan"
export CLAUDE_CODE_ALLOWED_TOOLS="Read,Grep,Glob,Bash(gh *)"
```

---

### 六、日志、会话与调试变量

| 变量名 | 说明 |
| --- | --- |
| `CLAUDE_CODE_DEBUG` | 开启调试输出 |
| `CLAUDE_CODE_ENABLE_TELEMETRY` | 启用遥测 |
| `OTEL_METRICS_EXPORTER` | OpenTelemetry 指标导出器 |
| `CLAUDE_CODE_DISABLE_HISTORY` | 禁用对话历史保存 |
| `CLAUDE_CODE_SESSION_TIMEOUT` | 会话超时 |
| `CLAUDE_CODE_MAX_SESSIONS` | 最大保存会话数 |

#### 示例：临时开启调试

```bash
export CLAUDE_CODE_DEBUG="1"
claude
```

#### 示例：CI 中关闭历史

```bash
export CLAUDE_CODE_DISABLE_HISTORY="1"
export CLAUDE_CODE_PERMISSION_MODE="plan"
```

---

### 七、MCP 相关变量

当 Claude Code 执行 MCP 工具时，会自动携带一些上下文变量：

| 变量名 | 说明 |
| --- | --- |
| `CLAUDE_CODE_MCP_SERVER_NAME` | MCP 服务器名称 |
| `CLAUDE_CODE_MCP_TOOL_NAME` | 当前调用的 MCP 工具名 |
| `CLAUDE_CODE_MCP_TOOL_ARGS` | 传给工具的参数（JSON 字符串） |

这类变量通常不是你手动设置，而是给钩子、脚本或自动化逻辑读取用的。

---

### 八、工作流相关变量

| 变量名 | 说明 |
| --- | --- |
| `CLAUDE_CODE_WORKTREE_CLEANUP_PERIOD_DAYS` | worktree 自动清理周期 |
| `CLAUDE_CODE_DISABLE_WORKTREE_AUTO_CLEANUP` | 禁用自动清理 |

---

### 九、GitHub Actions / CI 常见变量

| 变量名 | 说明 |
| --- | --- |
| `ANTHROPIC_VERTEX_PROJECT_ID` | Vertex AI 项目 ID |
| `CLOUD_ML_REGION` | Vertex AI 区域 |

这类变量通常出现在 GitHub Actions、CI 平台或云端运行环境中，而不是本地开发终端。

---

### 十、统一写进 settings.json 的方式

如果你希望配置更稳定、更可重复，推荐把非敏感环境变量写进 `settings.json`：

```json
{
  "env": {
    "ANTHROPIC_MODEL": "claude-sonnet-4-5",
    "CLAUDE_CODE_SUBAGENT_MODEL": "claude-haiku-3-5",
    "CLAUDE_CODE_PERMISSION_MODE": "plan",
    "CLAUDE_CODE_ALLOWED_TOOLS": "Read,Grep,Glob,Bash",
    "CLAUDE_CODE_ENABLE_TELEMETRY": "1",
    "OTEL_METRICS_EXPORTER": "otlp"
  }
}
```

---

### 十一、VS Code 插件里的配置方式

如果你在 VS Code 里使用 Claude Code 插件，可以通过 `environmentVariables` 注入：

```json
{
  "claudeCode.environmentVariables": [
    {
      "name": "ANTHROPIC_BASE_URL",
      "value": "https://custom-proxy.com/api"
    },
    {
      "name": "ANTHROPIC_AUTH_TOKEN",
      "value": "your-token-here"
    },
    {
      "name": "ANTHROPIC_MODEL",
      "value": "claude-sonnet-4-5"
    }
  ]
}
```

---

### 十二、几个最常见的配置场景

#### 场景 1：使用代理或自定义 API 端点

```bash
export ANTHROPIC_BASE_URL="https://proxy.company.com/anthropic/v1"
export ANTHROPIC_API_KEY="your-api-key"
```

#### 场景 2：优化成本

```bash
export ANTHROPIC_MODEL="claude-sonnet-4-5"
export CLAUDE_CODE_SUBAGENT_MODEL="claude-haiku-3-5"
```

#### 场景 3：CI / 自动化只读运行

```bash
export CLAUDE_CODE_PERMISSION_MODE="plan"
export CLAUDE_CODE_DISABLE_HISTORY="1"
export CLAUDE_CODE_ALLOWED_TOOLS="Read,Grep,Glob,Bash(gh *)"
```

#### 场景 4：Windows PowerShell

```powershell
$env:CLAUDE_CODE_USE_POWERSHELL_TOOL = "1"
```

如果走 Windows，还需要在配置里配合 `defaultShell: "powershell"`。

---

### 十三、查看当前配置

你可以直接在 Claude Code 中执行：

```text
/config
```

这个命令会帮助你查看当前生效的配置。

---

### 十四、最佳实践

#### 1、敏感信息不要乱放

不要把敏感 Token 直接提交进项目仓库。

推荐做法：

- 本地 shell 环境变量
- CI Secret
- 用户级私有配置

#### 2、区分通用配置和项目配置

- 通用配置放 `~/.claude/settings.json`
- 项目特有配置放 `.claude/settings.json`
- 敏感值尽量别进项目文件

#### 3、先做最小配置

最稳的方式不是一次性把所有变量都配满，而是只先配这几项：

```bash
export ANTHROPIC_AUTH_TOKEN="YOUR_API_KEY"
export ANTHROPIC_BASE_URL="https://example.com/apps/anthropic"
export ANTHROPIC_MODEL="claude-sonnet-4-5"
```

等确认能跑通，再慢慢加：

- 子代理模型
- 权限模式
- 调试 / 遥测
- 背景任务或工作流相关变量

---

### 十五、常见问题

**Q：环境变量和 settings.json 哪个优先？**

通常 `settings.json` 中显式配置的值优先级更高。

**Q：如何切换到不同 API 端点？**

设置 `ANTHROPIC_BASE_URL` 指向目标端点。

**Q：如何统一控制子代理模型？**

使用 `CLAUDE_CODE_SUBAGENT_MODEL`。

**Q：如何禁用斜杠命令？**

设置：

```bash
export CLAUDE_CODE_DISABLE_SLASH_COMMANDS="1"
```

**Q：如何确认当前配置真的生效了？**

最直接的做法是先执行 `/config`，然后再做一次最小验证任务。
---

## Claude Code Git 工作流

Claude Code 深度集成了 Git 功能，可以用自然语言完成几乎所有 Git 操作，包括创建提交、管理分支、处理合并冲突，以及利用 Git Worktree 实现并行工作流。本章详细介绍如何在 Claude Code 中高效使用 Git。

---

### 基础 Git 操作

#### 1、查看变更

用自然语言询问即可获取 Git 状态：

~~~
我改了哪些文件？
~~~

~~~
这次的改动有哪些内容？
~~~

~~~
最近 10 次提交都改了什么？
~~~

~~~
检查一下当前的 git 状态
~~~

Claude 会自动执行相应的 Git 命令并展示结果。

#### 2、提交代码

简单提交：

~~~
提交这次的改动
~~~

~~~
把这次的修改提交
~~~

带描述的提交：

~~~
把这次的修改提交，commit 信息说明修复了登录验证的 bug
~~~

Claude 会根据实际改动内容自动生成符合规范的 commit 信息。

#### 3、分支管理

~~~
新建一个 feature/user-profile 分支
~~~

~~~
切换到 develop 分支
~~~

~~~
把 main 分支的最新改动合并进来
~~~

~~~
查看所有分支
~~~

~~~
删除已合并的分支
~~~

---

### 处理合并冲突

#### 1、自动解决简单冲突

~~~
帮我解决合并冲突
~~~

~~~
这个文件有冲突，帮我看看哪个版本是正确的
~~~

Claude 会分析冲突内容，根据项目上下文选择合适的版本或提出解决方案。

#### 2、人工介入解决复杂冲突

当冲突过于复杂时，Claude 会向你说明各种选择的利弊：

~~~
这个冲突涉及架构设计，你来决定保留哪个方案：
A. 保留我们的实现...
B. 保留引入的优化...
~~~

> Claude Code 在执行任何文件修改前都会展示修改内容并请求你的确认。对于合并冲突这类高风险操作，更要仔细审查每个修改。

---

### Git Worktree 并行工作流

Git Worktree 允许你在不同目录中同时处理不同的分支，而无需切换分支或克隆仓库。这是 Claude Code 中最强大的并行工作方式之一。

#### 1、使用 Claude 创建 Worktree

启动 Claude 时指定 worktree：

~~~
claude --worktree feature-auth
~~~

这会自动：
- 在 `.claude/worktrees/feature-auth/` 创建新目录
- 基于 `origin/HEAD`（远程默认分支）创建新分支
- 在新目录中启动独立 Claude 会话

同时开多个 worktree：

~~~
claude --worktree bugfix-123
~~~

~~~
claude --worktree
~~~

不指定名称时，Claude 会自动生成有趣的名字（如 `bright-running-fox`）。

#### 2、Worktree 属性

| 属性 | 值 |
| --- | --- |
| 位置 | `<repo>/.claude/worktrees/<name>` |
| 分支命名 | `worktree-<name>` |
| 基础分支 | 远程 `origin/HEAD` 指向的分支 |

#### 3、Worktree 生命周期

| 场景 | 行为 |
| --- | --- |
| 没有做出任何修改 | Worktree 和分支自动删除 |
| 存在变更或提交 | Claude 提示你选择保留或删除 |
| Claude 崩溃导致孤立 worktree | 超过 `cleanupPeriodDays` 设置的天数后自动删除 |

#### 4、复制 .gitignore 文件到 Worktree

在项目根目录创建 `.worktreeinclude` 文件，指定需要复制到 worktree 的 gitignore 文件：

~~~
.env
.env.local
config/secrets.json
~~~

只有匹配某个模式 **且** 被 gitignore 的文件才会被复制。

#### 5、手动管理 Worktree

如果需要更多控制，可以手动管理：

~~~

# 创建 worktree 并指定分支

git worktree add ../project-feature-a -b feature-a

# 使用已有分支创建 worktree

git worktree add ../project-bugfix bugfix-123

# 在 worktree 中启动 Claude

cd ../project-feature-a && claude

# 完成后清理

git worktree list
git worktree remove ../project-feature-a
~~~

> 建议将 `.claude/worktrees/` 加入 `.gitignore`，避免 worktree 内容在主仓库中显示为未跟踪文件。

---

### 子代理 Worktree 隔离

子代理可以使用 worktree 隔离功能，实现完全独立的并行工作：

方式一：让 Claude 自动处理

~~~
让子代理使用 worktree 来并行处理这些任务
~~~

方式二：在子代理配置中指定

### 实例

---name:experimental-refactordescription:在隔离的 worktree 中尝试重构方案isolation:worktree # 在临时 worktree 中运行tools:Read, Write, Edit, Bash

---

你可以在隔离环境中自由修改，不会影响主分支。
完成后总结改动和方案是否成功。

#### 子代理 Worktree 特点

- 每个子代理自动获得独立的 worktree
- 任务完成后自动清理 worktree
- 修改不会影响主仓库
- 适合探索性任务和方案对比

---

### Pull Request 工作流

#### 1、创建 PR 的步骤

**第一步：让 Claude 总结变更**

~~~
总结一下我对认证模块做的改动
~~~

**第二步：生成 PR**

~~~
创建一个 PR
~~~

**第三步：完善 PR 描述**

~~~
在 PR 描述中补充更多关于安全改进的内容
~~~

#### 2、PR 会话关联

使用 `gh pr create` 创建 PR 时，Claude 会话会自动关联到该 PR：
- 会话链接到 PR 后，可以从 PR 恢复对话
- 使用 `claude --from-pr <number>` 恢复关联的会话

#### 3、使用 GitHub CLI

Claude 了解如何使用 `gh` CLI 工具。如果没有安装 `gh`，Claude 可以读写 GitHub API，但功能有限。

~~~

# Claude 可以执行的操作

gh pr create --title "Fix login bug"
gh pr view --comments
gh pr diff
gh issue create --title "Bug report"
~~~

---

### 会话管理与 Git 集成

#### 1、恢复之前的会话

| 命令 | 功能 |
| --- | --- |
| `claude --continue` | 继续当前目录最近的对话 |
| `claude --resume` | 打开会话选择器或按名称恢复 |
| `claude --from-pr 123` | 恢复与特定 PR 关联的会话 |

#### 2、会话选择器 Git 功能

| 快捷键 | 功能 |
| --- | --- |
| `B` | 筛选当前 git 分支的会话 |

#### 3、会话元数据显示

会话选择器显示以下信息：
- 会话名称或初始提示
- 上次活动距今时间
- 消息数量
- Git 分支（如果有）

#### 4、分支会话

使用 `/branch`、`/rewind` 或 `--fork-session` 创建的会话会分组在根会话下，方便管理。

---

### 计划模式与安全分析

计划模式（Plan Mode）使用只读操作安全地分析代码库，不会执行任何写操作。

#### 启动计划模式

~~~
claude --permission-mode plan
~~~

或者在会话中运行一次性查询：

~~~
claude --permission-mode plan -p "分析认证系统并提出改进建议"
~~~

#### 在 settings.json 中配置默认模式

### 实例

{
"permissions": {
"defaultMode": "plan"
}
}

---

### 典型工作流示例

#### 工作流一：多任务并行开发

~~~

# 场景：同时开发三个功能，但不想切换分支

> 启动三个 worktree，分别处理登录重构、支付集成和性能优化
> 在每个 worktree 中独立工作，完成后合并到主分支
~~~

#### 工作流二：Bug 修复流程

~~~

# 第一步：描述问题

> 用户反馈：用户登出后刷新页面仍然显示已登录

# 第二步：创建修复分支

> 创建一个 bugfix/session-cookie 分支来修复这个问题

# 第三步：分析与修复

> 先分析可能的原因，在 bugfix 分支中修复

# 第四步：提交并创建 PR

> 提交修复并创建一个 PR
~~~

#### 工作流三：代码审查

~~~

# 审查特定文件

> 帮我审查 src/payment/processor.ts，重点关注错误处理

# 审查 git 改动

> 审查我这次的所有改动，看看有没有明显的问题

# 在独立分支中审查

> 创建一个 worktree 来审查这个重构方案
~~~

#### 工作流四：功能开发与验证

~~~

# 主会话：实现新功能

> 实现用户资料编辑功能

# 子代理：并行运行测试

> 在子代理中运行所有测试，只返回失败的测试和根因

# 子代理：检查代码规范

> 使用子代理审查代码是否符合项目规范

# 完成后：创建 PR

> 创建一个 PR 并添加详细的描述
~~~

---

### 非 Git 版本控制系统

对于 SVN、Perforce 或 Mercurial 用户，Claude Code 支持通过钩子扩展：

#### 配置自定义钩子

在 `.claude/settings.json` 中配置：

### 实例

{
"hooks": {
"WorktreeCreate": "./scripts/create-worktree.sh",
"WorktreeRemove": "./scripts/remove-worktree.sh"
}
}

这些钩子替换默认的 git 行为。使用钩子脚本时，在脚本内部复制本地配置文件，而不是使用 `.worktreeinclude`。

---

### Anthropic 内部团队最佳实践

- **多开 worktree**：同时跑 3-5 个 git worktree，每个开一个独立会话，这是团队公认的提效最佳实践
- **复杂任务先规划**：使用 `plan` 模式让一个 Claude 写计划，另一个当幕僚审查
- **错误后更新 CLAUDE.md**：每次纠错后加一句"更新你的 CLAUDE.md，别再犯同样的错"
- **验证环节专门进计划模式**：确保验证过程安全可控

---

### 常见问题

**Q：Worktree 和分支有什么区别？**

Worktree 是独立的目录，可以在不同目录同时工作；分支是同一目录中的不同提交历史。Worktree 更适合需要同时处理多个复杂任务的场景。

**Q：Claude 创建的 Worktree 会不会影响主仓库？**

不会。Worktree 有独立的目录，内容不会影响主仓库的未提交更改。

**Q：如何让 Claude 在特定分支上工作？**

先切换分支再启动 Claude，或者使用 `claude --worktree <name>` 自动创建分支。

**Q：Worktree 太多会不会占用太多空间？**

Git Worktree 共享仓库历史，新目录只包含分支差异，所以占用空间很小。

**Q：子代理的 Worktree 什么时候清理？**

正常完成时，Claude 会提示你选择保留或删除。因崩溃孤立的 worktree 会在超过 `cleanupPeriodDays` 设置的天数后自动删除。

---

## Claude Code GitHub Actions

Claude Code GitHub Actions 将 AI 驱动的自动化带到你的 GitHub 工作流中。

通过在 PR 或 Issue 中简单地提及 `@claude`，Claude 就可以分析代码、创建 Pull Request、实现功能、修复 Bug，同时遵循你项目的标准规范。

---

### 为什么使用 Claude Code GitHub Actions

- **即时创建 PR**：描述你的需求，Claude 即可创建完整的 Pull Request
- **自动化代码实现**：将 Issue 转化为可工作的代码，只需一条命令
- **遵循项目标准**：尊重你的 `CLAUDE.md` 指南和现有代码模式
- **简单快速**：几分钟内即可开始使用
- **安全可靠**：代码保留在 GitHub 的 runner 上运行

---

### 快速开始

#### 方式一：自动安装（推荐）

在 Claude Code 终端中运行：

~~~
/install-github-app
~~~

这会引导你完成：
- 设置 GitHub App
- 添加必要的 Secrets

> 要求：你必须是仓库管理员。GitHub App 需要 Contents、Issues 和 Pull requests 的读写权限。仅适用于直接使用 Claude API 的用户（不支持 AWS Bedrock 或 Google Vertex AI）。

#### 方式二：手动安装

##### 第一步：安装 Claude GitHub App

访问：[https://github.com/apps/claude](https://github.com/apps/claude)

需要的权限：
- **Contents**：读写权限
- **Issues**：读写权限
- **Pull requests**：读写权限

##### 第二步：添加 API 密钥

在仓库 Secrets 中添加 `ANTHROPIC_API_KEY`

##### 第三步：创建工作流文件

从示例文件复制：`examples/claude.yml` 到 `.github/workflows/`

---

### 工作流配置

#### 基础工作流（响应 @claude 提及）

~~~
name: Claude Code
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
jobs:
  claude:
    runs-on: ubuntu-latest
    steps:
- uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
~~~

#### 自动代码审查

~~~
name: Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
- uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Review this pull request for code quality, correctness, and security."
          claude_args: "--max-turns 5"
~~~

#### 定时任务

~~~
name: Daily Report
on:
  schedule:
- cron: "0 9 * * *"
jobs:
  report:
    runs-on: ubuntu-latest
    steps:
- uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: "Generate a summary of yesterday's commits and open issues"
          claude_args: "--model opus"
~~~

---

### @claude 常用命令

在 PR 或 Issue 评论中使用：

~~~
@claude implement this feature based on the issue description
~~~

~~~
@claude how should I implement user authentication for this endpoint?
~~~

~~~
@claude fix the TypeError in the user dashboard component
~~~

~~~
@claude review this PR for security issues
~~~

~~~
@claude add tests for this new function
~~~

---

### Action 参数详解

| 参数 | 说明 | 是否必填 |
| --- | --- | --- |
| `prompt` | 给 Claude 的指令（纯文本或技能名称） | 否* |
| `claude_args` | 传递给 Claude Code 的 CLI 参数 | 否 |
| `anthropic_api_key` | Claude API 密钥 | 是** |
| `github_token` | GitHub Token（用于 API 访问） | 否 |
| `trigger_phrase` | 自定义触发词（默认：`@claude`） | 否 |
| `use_bedrock` | 使用 AWS Bedrock 而非 Claude API | 否 |
| `use_vertex` | 使用 Google Vertex AI 而非 Claude API | 否 |

*当在 issue/PR 评论中使用时省略 prompt，Claude 会响应触发词

**直接使用 Claude API 时必填，Bedrock/Vertex 时不需要

#### 常用 CLI 参数

| 参数 | 说明 |
| --- | --- |
| `--max-turns` | 最大对话轮次（默认 10） |
| `--model` | 使用的模型（如 `claude-sonnet-4-6`） |
| `--mcp-config` | MCP 配置文件路径 |
| `--allowedTools` | 允许使用的工具（逗号分隔） |
| `--append-system-prompt` | 追加系统提示 |
| `--debug` | 启用调试输出 |

#### claude_args 示例

~~~
claude_args: |
  --max-turns 5
  --model claude-sonnet-4-6
  --mcp-config /path/to/config.json
  --append-system-prompt "Follow our coding standards"
~~~

---

### AWS Bedrock 集成

#### 前置条件

- AWS Bedrock 访问已启用，包含 Claude 模型权限
- GitHub 在 AWS 中配置为 OIDC 身份提供者
- IAM 角色具有 Bedrock 权限

#### 必需的 Secrets

| Secret 名称 | 说明 |
| --- | --- |
| `AWS_ROLE_TO_ASSUME` | 用于 Bedrock 访问的 IAM 角色 ARN |
| `APP_ID` | GitHub App ID |
| `APP_PRIVATE_KEY` | GitHub App 私钥 |

#### Bedrock 工作流示例

~~~
name: Claude PR Action
permissions:
  contents: write
  pull-requests: write
  issues: write
  id-token: write

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  claude-pr:
    if: |
      (github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')) ||
      (github.event_name == 'pull_request_review_comment' && contains(github.event.comment.body, '@claude'))
    runs-on: ubuntu-latest
    env:
      AWS_REGION: us-west-2
    steps:
- name: Checkout repository
        uses: actions/checkout@v4
- name: Generate GitHub App token
        id: app-token
        uses: actions/create-github-app-token@v2
        with:
          app-id: ${{ secrets.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}
- name: Configure AWS Credentials (OIDC)
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
          aws-region: us-west-2
- uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ steps.app-token.outputs.token }}
          use_bedrock: "true"
          claude_args: '--model us.anthropic.claude-sonnet-4-6 --max-turns 10'
~~~

---

### Google Vertex AI 集成

#### 前置条件

- GCP 项目中启用 Vertex AI API
- 为 GitHub 配置 Workload Identity Federation
- 具有 Vertex AI 权限的服务账号

#### 必需的 Secrets

| Secret 名称 | 说明 |
| --- | --- |
| `GCP_WORKLOAD_IDENTITY_PROVIDER` | Workload Identity Provider 资源名称 |
| `GCP_SERVICE_ACCOUNT` | 具有 Vertex AI 访问权限的服务账号邮箱 |
| `APP_ID` | GitHub App ID |
| `APP_PRIVATE_KEY` | GitHub App 私钥 |

#### Vertex AI 工作流示例

~~~
name: Claude PR Action
permissions:
  contents: write
  pull-requests: write
  issues: write
  id-token: write

on:
  issue_comment:
    types: [created]

jobs:
  claude-pr:
    if: github.event_name == 'issue_comment' && contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    steps:
- name: Checkout repository
        uses: actions/checkout@v4
- name: Generate GitHub App token
        id: app-token
        uses: actions/create-github-app-token@v2
        with:
          app-id: ${{ secrets.APP_ID }}
          private-key: ${{ secrets.APP_PRIVATE_KEY }}
- name: Authenticate to Google Cloud
        id: auth
        uses: google-github-actions/auth@v2
        with:
          workload_identity_provider: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
          service_account: ${{ secrets.GCP_SERVICE_ACCOUNT }}
- uses: anthropics/claude-code-action@v1
        with:
          github_token: ${{ steps.app-token.outputs.token }}
          trigger_phrase: "@claude"
          use_vertex: "true"
          claude_args: '--model claude-sonnet-4-5@20250929 --max-turns 10'
        env:
          ANTHROPIC_VERTEX_PROJECT_ID: ${{ steps.auth.outputs.project_id }}
          CLOUD_ML_REGION: us-east5
~~~

---

### 从 Beta 版本升级（v1.0 重大变更）

| Beta 输入 | 新的 v1.0 输入 |
| --- | --- |
| `mode` | （已移除 - 自动检测） |
| `direct_prompt` | `prompt` |
| `override_prompt` | 带 GitHub 变量的 `prompt` |
| `custom_instructions` | `claude_args: --append-system-prompt` |
| `max_turns` | `claude_args: --max-turns` |
| `model` | `claude_args: --model` |
| `allowed_tools` | `claude_args: --allowedTools` |
| `disallowed_tools` | `claude_args: --disallowedTools` |

#### 升级前后对比

**Beta 版本：**

~~~
- uses: anthropics/claude-code-action@beta
  with:
    mode: "tag"
    direct_prompt: "Review this PR for security issues"
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    custom_instructions: "Follow our coding standards"
    max_turns: "10"
    model: "claude-sonnet-4-6"
~~~

**GA 版本 (v1.0)：**

~~~
- uses: anthropics/claude-code-action@v1
  with:
    prompt: "Review this PR for security issues"
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    claude_args: |
      --append-system-prompt "Follow our coding standards"
      --max-turns 10
      --model claude-sonnet-4-6
~~~

---

### 故障排除

#### Claude 不响应 @claude 命令

- 确认 GitHub App 已正确安装
- 检查工作流是否已启用
- 确保 API 密钥已在仓库 Secrets 中设置
- 确认评论包含 `@claude`（不是 `/claude`）

#### CI 不运行在 Claude 的提交上

- 使用 GitHub App 或自定义 app（不要用 Actions 用户）
- 检查工作流触发器是否包含必要的事件
- 确认 app 权限包含 CI 触发器

#### 认证错误

- 确认 API 密钥有效且有足够权限
- 对于 Bedrock/Vertex：检查凭证配置
- 确保 Secrets 名称在工作流中正确

---

### 安全注意事项

- **绝不直接提交 API 密钥**到仓库
- 使用 GitHub Secrets 存储 API 密钥：`${{ secrets.ANTHROPIC_API_KEY }}`
- 限制 action 权限，只授权必要的权限
- 合并前审查 Claude 的建议
- 对于 AWS/GCP：使用 OIDC 身份提供者而非静态凭证
- 为每个仓库创建专用服务账号

---

### 成本考虑

#### 费用构成

- **GitHub Actions 费用**：在 GitHub 托管的 runner 上运行，消耗 GitHub Actions 分钟数
- **API 费用**：每次 Claude 交互根据提示/响应长度消耗 API Token

#### 成本优化建议

- 使用具体的 `@claude` 命令，减少不必要的 API 调用
- 配置合适的 `--max-turns` 限制对话轮次
- 设置工作流超时，避免任务失控
- 使用 GitHub 并发控制限制并行运行

---

### 最佳实践

#### 1、创建 CLAUDE.md

在仓库根目录创建 `CLAUDE.md` 文件，定义代码风格指南和项目特有规则：

~~~

# 项目开发规范

## 代码风格

- 使用 TypeScript 4.x
- 遵循 ESLint 配置
- 函数必须有 JSDoc 注释

## PR 要求

- 必须通过所有 CI 检查
- 至少一个代码审查
- 更新相关文档

## 禁止事项

- 不要修改 `migrations/` 目录
- 不要提交 `.env` 文件
~~~

#### 2、使用具体的 @claude 命令

~~~
@claude review this PR for SQL injection vulnerabilities
~~~

~~~
@claude add unit tests for the new validateEmail function
~~~

#### 3、配置适当的限制

~~~
jobs:
  claude:
    runs-on: ubuntu-latest
    timeout-minutes: 15  # 设置超时
    steps:
- uses: anthropics/claude-code-action@v1
        with:
          claude_args: "--max-turns 5 --allowedTools Read,Grep,Glob,Bash,Write,Edit"
~~~

#### 4、CI/CD 自动化场景

- **PR 自动审查**：PR 打开或更新时自动触发审查
- **Issue 自动分类**：新 Issue 创建时自动添加标签和分配
- **定时报告**：每日生成代码统计或安全报告
- **自动化修复**：CI 失败时自动分析并尝试修复

---
