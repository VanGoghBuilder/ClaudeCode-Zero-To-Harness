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

