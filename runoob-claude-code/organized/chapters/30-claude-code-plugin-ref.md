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

