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
