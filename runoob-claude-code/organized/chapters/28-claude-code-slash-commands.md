.new-badge {
display: inline-block;
background: #4CAF50;
color: #fff;
font-size: .72em;
border-radius: 3px;
padding: 1px 6px;
vertical-align: middle;
margin-left: 5px;
font-weight: normal;
}
.legacy-badge {
display: inline-block;
background: #f0ad4e;
color: #fff;
font-size: .72em;
border-radius: 3px;
padding: 1px 6px;
vertical-align: middle;
margin-left: 5px;
font-weight: normal;
}
.tip-box {
background: #e8f4fd;
border-left: 4px solid #3498db;
padding: 10px 16px;
margin: 12px 0;
border-radius: 0 4px 4px 0;
font-size: .95em;
}
.warn-box {
background: #fff8e1;
border-left: 4px solid #f0ad4e;
padding: 10px 16px;
margin: 12px 0;
border-radius: 0 4px 4px 0;
font-size: .95em;
}
.tip-box .fa, .warn-box .fa {
margin-right: 6px;
}

## Claude Code 斜杠 / 命令

斜杠 / 命令是 Claude Code 交互式会话的 **快捷控制入口**，通过输入 `/` 开头的指令，就能快速调用功能、管理会话、自定义工作流。

![image](https://www.runoob.com/wp-content/uploads/2026/01/23e5a097-f781-4f4b-8597-35d18a7aae29.png)

查看所有可用命令：

~~~
/help
~~~

`/help` 会列出所有内置命令、当前项目的自定义命令（Skills / Commands），以及已连接 MCP 服务器暴露的命令。

命令类型：

| 类型 | 触发方式 | 来源 | 存储位置 | 适用场景 |
| --- | --- | --- | --- | --- |
| **内置命令** | `/command` | 随 Claude Code 安装 | 内部实现 | 会话管理、配置、集成等系统操作 |
| **内置 Skills** | `/skill-name` | 随 Claude Code 附带 | 内部实现 | 代码审查、调试、批处理等预置 AI 工作流 |
| **项目自定义 Skills** 推荐 | `/skill-name` | 用户创建目录 + SKILL.md | `.claude/skills/<名称>/SKILL.md` | 团队共享的可复用工作流，纳入 git；支持 Claude 自动触发 |
| **项目自定义命令** 旧格式 | `/command` | 用户创建 `.md` 文件 | `.claude/commands/` | 团队共享的重复工作流，纳入 git |
| **个人自定义 Skills** | `/skill-name` | 用户创建目录 + SKILL.md | `~/.claude/skills/<名称>/SKILL.md` | 跨项目通用的个人工作流 |
| **个人自定义命令** | `/command` | 用户创建 `.md` 文件 | `~/.claude/commands/` | 跨项目通用的个人工作流（旧格式） |
| **MCP 命令** | `/mcp__服务器__命令` | MCP 服务器自动暴露 | 由 MCP 服务器提供 | 调用 GitHub、Slack、数据库等外部工具 |
| **`!` Shell 命令 ** | `!bash命令` | 直接执行 | — | 绕过 AI 处理，直接运行终端命令 |**命令优先级（同名时）**：企业级 > 个人级 > 项目级 > 内置

 Skills vs Commands：两者均用 / 触发，但 Skills（.claude/skills/）是新一代格式，支持多文件组织、Claude 自动触发、更丰富的 frontmatter 控制；Commands（.claude/commands/）为旧格式，仍完全兼容，新项目推荐直接使用 Skills。

---

### 一、常用内置斜杠命令

内置命令是 Claude Code 自带的核心功能，直接输入就能用。新手优先掌握这几类高频命令：

#### 基础操作命令

| 命令 | 用途 | 新手示例 |
| --- | --- | --- |
| `/help` | 查看所有可用命令及说明（含 MCP 命令） | 输入 `/help` 快速查命令清单 |
| `/exit` | 退出当前交互式会话 | 不想继续聊了，输 `/exit` 直接走 |
| `/clear` | 清除当前对话历史 | 会话太乱？`/clear` 一键清空 |

例如我们使用 /help 命令查看帮助信息，Tab 键可以切换菜单，Esc 退出：

![image](https://www.runoob.com/wp-content/uploads/2026/01/93013ac4-eb3c-4b2c-8d8a-86edb32d778e.png)

#### 会话管理命令

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/clear` | `/clear` | 清除全部对话历史，开始全新会话 | `/clear` | 不可恢复，建议先 `/export` |
| `/compact` | `/compact [侧重点]` | 智能压缩历史对话，保留语义，缩小 Token 占用 | `/compact 保留数据库设计部分` | 与 `/clear` 区别：保留语义摘要而非完全清空 |
| `/resume` | `/resume [id或名称]` | 恢复历史会话；不带参数列出所有可恢复会话 | `/resume task-hub-backend` | 配合 `/rename` 效果更佳 |
| `/rewind` | `/rewind` | 撤销最近操作，含文件修改和对话内容，恢复到上一检查点；新版新增"仅回退代码"选项，可保留对话历史 | 按 `Esc` 两次呼出回退菜单 | 适合 Agent 执行结果不满意时回退 |
| `/rename` | `/rename <名称>` | 为当前会话命名，便于后续 `/resume` 找回 | `/rename auth-module-refactor` | 建议命名规范：`功能-操作` |
| `/export` | `/export [文件名]` | 导出对话为文件；不指定文件名则复制到剪贴板 | `/export debug-session.md` | 支持 Markdown 格式 |
| `/copy` NEW | `/copy` | 将 Claude 最近一次回复复制到剪贴板；若含多个代码块，会弹出交互式选择器 | `/copy` | 比手动框选文本快得多 |
| `/btw` NEW | `/btw <问题>` | 在不影响主任务上下文的情况下，快速插问一个临时问题，回答不会污染主对话流 | `/btw Redis 的默认端口是多少？` | 适合临时查阅而不想干扰当前任务 |
| `/exit` | `/exit` | 安全退出 Claude Code REPL | `/exit` | — |

#### 上下文与记忆

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/context` | `/context` | 可视化上下文窗口使用量，显示各部分（历史/文件/系统提示/Skills）占比 | `/context` | 接近上限时应执行 `/compact` 或 `/clear` |
| `/memory` | `/memory` | 打开编辑器修改 `CLAUDE.md`（项目级与全局级），跨会话持久化项目约定 | `/memory` | 修改后对后续所有会话生效 |
| `/add-dir` | `/add-dir <路径>` | 将额外目录纳入工作范围，适合 monorepo 或跨目录操作 | `/add-dir ../packages/shared` | 可多次调用添加多个目录 |
| `/todos` | `/todos` | 列出当前会话中 Claude 记录的 TODO 事项 | `/todos` | — |
| `/diff` NEW | `/diff` | 查看本次会话中所有文件变更的 diff 摘要，便于回顾 Claude 做了哪些修改 | `/diff` | 与 `/rewind` 配合：先 `/diff` 确认变更，再决定是否回滚 |

#### 项目与配置

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/init` | `/init` | 分析项目结构，自动生成 `CLAUDE.md`，写入技术栈/构建命令/代码规范 | `/init` | 新项目必备第一步 |
| `/config` | `/config` | 以交互式界面管理 Claude Code 全局配置 | `/config` | — |
| `/status` | `/status` | 打开设置状态页，查看当前配置概览 | `/status` | — |
| `/hooks` | `/hooks` | 交互式配置生命周期 Hook（如提交前 lint、保存后格式化） | `/hooks` | 支持 PreToolUse / PostToolUse / Notification / Stop |
| `/permissions` | `/permissions` | 查看或更新工具权限（读文件/写文件/执行命令等） | `/permissions` | 限制权限可提升操作安全性 |
| `/sandbox` | `/sandbox` | 启用隔离沙盒环境执行 Bash 命令，防止影响宿主系统 | `/sandbox` | 适合测试不确定的脚本 |
| `/doctor` | `/doctor` | 诊断安装状态，检查依赖/配置/网络连接是否正常 | `/doctor` | 遇到异常时首先执行 |

#### 模型与输出

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/model` | `/model [模型名]` | 切换底层 AI 模型；不带参数显示交互式选择菜单 | `/model claude-haiku-4-5-20251001` | 见下方模型对照表 |
| `/plan` | `/plan [任务描述]` | 进入计划模式，Claude 先输出执行计划供确认再行动 | `/plan 重构认证模块并补充测试` | 适合复杂多步骤任务，避免走偏 |
| `/output-style` | `/output-style` | 设置响应格式（代码优先/说明优先/简洁模式等） | `/output-style` | — |
| `/theme` | `/theme` | 切换终端界面颜色方案 | `/theme` | — |
| `/statusline` | `/statusline` | 定制终端底部状态栏显示内容 | `/statusline` | — |
| `/vim` | `/vim` | 启用 Vim 键位绑定 | `/vim` | 适合 Vim 用户 |
| `/terminal-setup` | `/terminal-setup` | 安装 `Shift+Enter` 换行绑定，方便输入多行 Prompt | `/terminal-setup` | 首次使用建议配置 |

**可用模型对照表（2026）： **

| 模型标识 | 定位 | 适用场景 |
| --- | --- | --- |
| `claude-sonnet-4-6` | 均衡型（默认） | 日常开发任务，Pro/Max5 用户首选 |
| `claude-haiku-4-5-20251001` | 快速轻量 | 简单查询、自定义命令中节省 Token |
| `claude-opus-4-6` | 高能力旗舰 | 复杂架构规划、高难度代码推理，Max20 用户可作默认 |

#### 代码与工具

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/review` | `/review` | 对最近修改的代码进行结构化 Code Review，按优先级输出改进建议 | `/review` | — |
| `/security-review` | `/security-review` | 针对当前改动进行安全专项审查（SQL 注入/XSS/敏感信息泄露等） | `/security-review` | 上线前必执行 |
| `/pr-comments` | `/pr-comments` | 拉取当前分支关联 PR 的审查意见，在终端中直接处理 | `/pr-comments` | 需配置 GitHub 集成 |
| `/install-github-app` | `/install-github-app` | 配置 GitHub Actions 集成，Claude 自动 Review 新 PR | `/install-github-app` | 生成 `.github/workflows/claude-code-review.yml` |
| `/agents` | `/agents` | 查看、创建和管理专项子代理（DB 专家/安全专家/测试专家等） | `/agents` | — |
| `/bashes` | `/bashes` | 查看当前后台运行的 Bash 进程，可中断或查看输出 | `/bashes` | — |
| `/skills` NEW | `/skills` | 列出所有可用的 Skills（内置 + 项目级 + 个人级），含描述和触发方式 | `/skills` | 上下文预算不足时部分 Skills 会被排除，通过此命令可确认加载状态 |

#### 集成与扩展

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/mcp` | `/mcp` | 管理 MCP 服务器连接（连接/断开/查看可用工具） | `/mcp` | 见第七节 MCP 命令详解 |
| `/ide` | `/ide` | 配置与 VSCode / JetBrains 等 IDE 的联动集成 | `/ide` | — |
| `/plugin` | `/plugin [子命令]` | 管理 Claude Code 插件：安装、卸载、更新、浏览市场 | `/plugin install typescript-lsp` | 官方插件市场含 36 款精选插件 |
| `/teleport` | `/teleport` | 将 claude.ai 网页端发起的会话迁移到本地终端继续（云端 → 本地方向） | `/teleport` | 需安装 GitHub App，仅支持 GitHub 托管仓库 |

**`/plugin` 子命令详情： **

| 子命令 | 功能 |
| --- | --- |
| `/plugin install <名称>` | 安装指定插件 |
| `/plugin list` | 列出所有已安装插件 |
| `/plugin update` | 更新所有已安装插件 |
| `/plugin marketplace` | 浏览官方插件市场 |
| `/plugin uninstall <名称>` | 卸载指定插件 |

#### 统计与账户

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/cost` | `/cost` | 显示当前会话的 Token 消耗量与预估费用 | `/cost` | API 计费用户可见；订阅制用户 Token 已含在套餐内 |
| `/usage` | `/usage` | 查看当前套餐用量、剩余配额和速率限制状态 | `/usage` | — |
| `/stats` | `/stats` | 图表形式展示历史使用数据（每日 Token / 会话数量） | `/stats` | — |
| `/release-notes` | `/release-notes` | 查看 Claude Code 最新版本变更记录 | `/release-notes` | — |
| `/bug` | `/bug` | 将当前会话上下文打包，向 Anthropic 提交 Bug 报告 | `/bug` | — |
| `/login` | `/login` | 登录或切换 Anthropic 账户 | `/login` | 适合多账号开发者 |
| `/logout` | `/logout` | 注销当前账户 | `/logout` | — |
| `/privacy-settings` | `/privacy-settings` | 管理数据使用偏好（是否允许对话用于模型训练等） | `/privacy-settings` | — |
| `/remote-env` | `/remote-env` | 配置远程会话的环境变量 | `/remote-env` | — |

---

### 二、内置 Skills NEW

内置 Skills 是随 Claude Code 附带的预置 AI 工作流，与内置命令不同——它们加载详细提示词后由 Claude 推理执行，可产出更丰富的分析和操作结果，同样用 `/` 触发。

 区别：内置命令 vs 内置 Skills
内置命令（如 /clear、/compact）执行固定逻辑，不涉及 AI 推理，速度极快。
内置 Skills（如 /debug、/simplify）加载提示词后由 Claude 推理执行，结果更丰富，适合复杂分析任务。

| 命令 | 功能说明 | 适用场景 |
| --- | --- | --- |
| `/simplify` | 将指定代码或文本简化，提升可读性，消除不必要的复杂度 | 重构冗长函数、简化过度工程化的代码 |
| `/debug` | 对报错信息或异常行为进行系统化根因分析，给出修复思路 | 定位难以复现的 bug |
| `/batch` | 对多个文件或目标并行执行相同任务（如批量重命名、批量添加类型注解） | 大批量重复性代码改动 |
| `/loop` | 在循环中反复执行某个操作直到满足退出条件（如不断重试测试直至通过） | 自动化 CI 修复循环 |
| `/claude-api` | 快速生成调用 Anthropic API 的示例代码，支持指定模型和任务类型 | 搭建 Claude API 集成原型 |

---

### 三、命令前缀语法

| 前缀 | 语法 | 功能说明 | 示例 | 对比 |
| --- | --- | --- | --- | --- |
| `/` | `/command` | 触发内置命令、内置 Skills 或自定义 Skills/Commands | `/review` | — |
| `!` | `!<bash命令>` | 直接执行 Shell 命令，绕过 AI 处理，节省 Token | `!git status` | 等价于在终端直接运行，但比「帮我查看 git 状态」省 Token |
| `@` | `@<文件路径>` | 将文件内容注入当前上下文 | `@src/api/users.ts` | 比手动粘贴代码更精准，支持多文件 |

**`!` 前缀使用示例： **

![image](https://www.runoob.com/wp-content/uploads/2026/01/4d37b1ea-521b-49b5-badf-85636f7e756d.png)

~~~
> ! ls
> !git log --oneline -10
> !npm test -- --coverage
> !cat logs/error.log | tail -50
~~~

@ 前缀使用示例：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ec825797-0ed6-43ba-b694-c9bdc16d32e9.png)

~~~
> 对比 @src/auth/v1.ts 和 @src/auth/v2.ts 的实现差异
> 审查 @src/api/users.ts 中的错误处理逻辑是否完善
~~~

---

### 四、自定义 Skills（新推荐格式）

Skills 是自定义命令的新一代格式，取代旧版 `.claude/commands/`。两种格式均可正常使用，但新项目建议直接使用 Skills。

#### Skills vs Commands 对比

| 特性 | Skills（推荐） | Commands（旧格式） |
| --- | --- | --- |
| 存储路径 | `.claude/skills/<名称>/SKILL.md` | `.claude/commands/<名称>.md` |
| 触发方式 | `/名称` 手动触发，或 Claude 根据描述自动触发 | `/名称`（仅手动触发） |
| 多文件支持 | 支持附带模板、示例、辅助脚本 | 单文件 |
| Claude 自动触发 | Claude 根据 description 智能判断调用 |   |
| Agent Skills 标准 | 符合开放标准，可跨工具共享 |   |

#### 创建 Skill 步骤

##### 步骤 1：创建目录结构

自定义 Skills 分两种，存储位置不同：
- **项目 Skill**：只在当前项目生效，团队共享 → 目录：`.claude/skills/<名称>/`
- **个人 Skill**：所有项目通用，自己用 → 目录：`~/.claude/skills/<名称>/`

~~~

# 创建项目级 Skill（团队共享，纳入 git）

mkdir -p .claude/skills/optimize

# 创建个人级 Skill（跨项目通用，本地私有）

mkdir -p ~/.claude/skills/fix-bug
~~~

##### 步骤 2：编写 SKILL.md

新建 `SKILL.md` 文件，YAML frontmatter 写配置，下方 Markdown 正文写提示词。`name` 字段定义触发命令名。

**示例：创建代码性能优化 Skill**

~~~

---

name: optimize
description: 分析代码性能瓶颈，给出优化建议。当用户提到性能问题时自动触发。
allowed-tools: Read, Grep, Glob
argument-hint: [目标文件或目录]
model: claude-sonnet-4-6

---

分析以下代码的性能瓶颈，给出具体的优化建议，优先考虑时间复杂度和内存占用：
$ARGUMENTS
~~~

使用时输入：

~~~
/optimize src/utils/data-processor.ts
~~~

#### 子目录命名空间组织

可在 `.claude/skills/` 下用子目录按职能分类管理，目录名即命令名：

~~~
.claude/skills/
├── optimize/            → /optimize
│   ├── SKILL.md
│   └── examples/        # 可选：示例文件辅助 Claude 理解
├── debug/               → /debug
│   ├── SKILL.md
│   └── scripts/         # 可选：辅助脚本
└── commit/              → /commit
    └── SKILL.md
~~~

---

### 五、自定义斜杠命令（旧格式，兼容）

如果有 **重复使用的提示词**（比如固定的代码审查要求、重复的指令模板），可以把它做成自定义命令，一键调用。 **命令文件存储位置： **

| 作用域 | 存储路径 | 共享方式 | 适用场景 |
| --- | --- | --- | --- |
| 项目级 | `.claude/commands/<命令名>.md` | 纳入 git，团队共享 | 项目专属工作流（如特定框架的代码生成） |
| 个人级 | `~/.claude/commands/<命令名>.md` | 本地私有 | 通用开发习惯（如个人代码风格检查） |

**参数语法： **

| 语法 | 说明 | 命令文件示例 | 调用示例 |
| --- | --- | --- | --- |
| `$ARGUMENTS` | 捕获命令名之后的所有内容 | `分析并修复：$ARGUMENTS` | `/fix 登录接口返回 500，见 logs/error.log` |
| `$1` `$2` | 按空格分割的位置参数 | `修复 Issue #$1，优先级 $2` | `/fix-issue 42 high` |
| `!`cmd`` | 执行 Shell 命令并将输出嵌入 Prompt | `!`git diff HEAD`` | 自动展开为命令执行结果 |
| `@文件路径` | 将文件内容注入 Prompt | `审查 @src/utils/helpers.ts` | 自动读取文件内容 |

#### 核心原理

自定义命令本质是 **Markdown 文本文件**——文件名就是命令名，文件内容就是要执行的提示词，支持传参数、调用 Bash 命令。

#### 步骤创建自定义命令

##### 步骤 1：创建命令存储目录

自定义命令分两种，存储位置不同：
- **项目命令**：只在当前项目生效，团队共享 → 目录：`.claude/commands/`
- **个人命令**：所有项目通用，自己用 → 目录：`~/.claude/commands/`

以创建项目命令为例，先建目录：

~~~

# 在项目根目录执行

mkdir -p .claude/commands
~~~

##### 步骤 2：写 Markdown 命令文件

新建一个 `.md` 文件，文件名就是命令名（比如 `optimize.md` → 命令 `/optimize`），文件内容写提示词。

**示例：创建代码性能优化命令**

~~~

# 写入提示词到文件

echo "分析这段代码的性能瓶颈，给出具体的优化建议，优先考虑时间复杂度和内存占用：" > .claude/commands/optimize.md
~~~

现在，在会话里输入 `/optimize`，再粘贴代码，Claude 就会按你的要求做性能分析！

#### 高级技巧：给命令加参数

命令可以带参数，用 `$ARGUMENTS`（捕获所有参数）或 `$1` `$2`（按位置取参数）。

**示例：带参数的 Bug 修复命令**

创建命令文件 `.claude/commands/fix-issue.md`：

~~~
修复 Issue #$ARGUMENTS，要求：
1. 符合项目编码规范
2. 附上测试用例
3. 说明修复思路
~~~

使用命令：

~~~
/fix-issue 123  # $ARGUMENTS 会被替换成 "123"
~~~

---

### 六、Frontmatter 字段参考

自定义命令文件（`commands/*.md`）和 Skills（`skills/*/SKILL.md`）顶部均可添加 YAML Frontmatter 进行精细配置：

~~~

---

name: commit                          # Skills 中必填；命令文件中可选（默认取文件名）
allowed-tools: Bash(git add:*), Bash(git commit:*), Read, Edit
argument-hint: [commit-message]
description: 检查代码质量后提交
model: claude-haiku-4-5-20251001
context: fork
agent: general-purpose
disable-model-invocation: false
user-invocable: true
hooks:
  PreToolUse:
- matcher: "Bash"
      hooks:
- type: command
          command: "./scripts/validate.sh"
          once: true

---

~~~

#### 字段说明

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `name` | string | Skills 必填 | 定义触发命令名（`/name`）；命令文件默认取文件名 |
| `allowed-tools` | string | 否 | 命令可使用的工具，匹配的工具无需每次人工确认 |
| `argument-hint` | string | 否 | 在 `/help` 列表中显示的参数格式提示，如 `[message]` |
| `description` | string | 否 | 命令功能描述，展示在 `/help` 和 `/skills` 列表中；Skills 中还用于让 Claude 判断是否自动触发 |
| `model` | string | 否 | 强制指定模型；简单任务用 Haiku 可降低成本 |
| `context` | enum | 否 | `fork`：以独立子代理运行；`inline`：在当前会话内运行 |
| `agent` | string | 否 | `context: fork` 时指定代理类型 |
| `disable-model-invocation` | bool | 否 | 为 `true` 时禁止通过 Skill 工具编程调用此命令 |
| `user-invocable` NEW | bool | 否 | 为 `false` 时只允许 Claude 自动触发，用户无法手动 `/调用` |
| `hooks` | object | 否 | 命令执行过程中的生命周期 Hook |

#### `allowed-tools` 写法示例

| 写法 | 含义 |
| --- | --- |
| `Read` | 允许读取任意文件 |
| `Read(src/*)` | 只允许读取 `src/` 目录 |
| `Bash(git *:*)` | 允许所有 git 子命令 |
| `Bash(npm test:*)` | 只允许运行 `npm test` |
| `Read, Grep, Glob` | 组合多个工具 |

#### Hook 触发时机

| 时机 | 说明 | 典型用途 |
| --- | --- | --- |
| `PreToolUse` | 工具调用前 | 提交前运行 lint、写文件前备份 |
| `PostToolUse` | 工具调用后 | 修改后自动格式化、生成变更日志 |
| `Notification` | Claude 发出通知时 | 发送 Slack 消息、桌面通知 |
| `Stop` | Claude 完成响应时 | 记录日志、触发下游任务 |

---

### 七、插件与 MCP 命令

除了自己写，还能通过 **插件**和 **MCP 服务器**获取更多扩展命令。

命名格式：

~~~
/mcp__<服务器名>__<命令名> [参数]
~~~

#### 常用 MCP 命令示例

| MCP 服务器 | 命令示例 | 功能 |
| --- | --- | --- |
| GitHub | `/mcp__github__list_prs` | 列出当前仓库的 PR |
| GitHub | `/mcp__github__create_issue "Bug标题" high` | 创建 Issue |
| PostgreSQL | `/mcp__postgres__query "SELECT * FROM users LIMIT 10"` | 执行 SQL 查询 |
| Slack | `/mcp__slack__send_message "#dev" "部署完成"` | 发送 Slack 消息 |
| Jira | `/mcp__jira__create_issue "修复登录Bug" high` | 创建 Jira 工单 |
| Figma | `/mcp__figma__get_component "Button"` | 获取 Figma 组件信息 |

查看所有可用 MCP 命令：`/help`（MCP 命令在帮助列表底部单独分组展示）

#### 1. 插件命令

安装 Claude Code 插件后，会自动新增插件专属命令，格式通常是：

~~~
/plugin-name:command-name  # 避免命令名冲突
~~~

比如安装 Git 插件后，可能会有 `/git:commit` 命令，一键生成规范的 commit 信息。

#### 2. MCP 命令

MCP（模型上下文协议）服务器可以把外部工具（比如 GitHub、Jira）的功能变成斜杠命令，格式：

~~~
/mcp__<服务器名>__<功能名> [参数]
~~~

示例：

~~~
/mcp__github__list_prs  # 列出 GitHub 仓库的 PR
/mcp__jira__create_issue "登录按钮失效" high  # 在 Jira 创建高优先级问题
~~~

---

### 八、实用命令模板库

#### 模板概览

| 模板名 | 文件路径 | 推荐模型 | 功能 |
| --- | --- | --- | --- |
| 智能 Git 提交 | `.claude/commands/commit.md` | Haiku | 检查调试残留后提交 |
| PR 代码审查 | `.claude/commands/pr-review.md` | Sonnet | 全面分级审查 |
| 智能测试运行 | `.claude/commands/test.md` | Sonnet | 运行并自动修复失败用例 |
| 安全漏洞扫描 | `.claude/commands/security-scan.md` | Opus | OWASP Top 10 扫描 |
| API 文档生成 | `.claude/commands/api-docs.md` | Sonnet | 自动生成 REST API 文档 |
| 代码重构 | `.claude/commands/refactor.md` | Sonnet | 可读性 / DRY / 类型安全重构 |
| 文档链接检查 | `.claude/commands/check-links.md` | Haiku | 批量验证文档中的超链接有效性 |

智能 Git 提交：

~~~

---

allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*), Bash(git commit:*)
argument-hint: [commit-message]
description: 检查代码质量后提交
model: claude-haiku-4-5-20251001

---

## 暂存区内容

!`git diff --cached`

提交前检查以下问题，发现问题则报告并询问是否继续：
1. 遗留的 `console.log` / `print` 调试语句
2. 未处理的 `TODO` / `FIXME` 注释
3. 大段被注释掉的代码
4. 测试文件中的 `.only` / `.skip` 标记

若无问题，使用以下信息提交：$ARGUMENTS
~~~

PR 代码审查：

~~~

---

allowed-tools: Read, Grep, Glob, Bash(git diff:*), Bash(git log:*)
description: 全面的 PR 代码审查

---

## 变更文件

!`git diff --name-only HEAD~1`

## 详细差异

!`git diff HEAD~1`

## 近期提交

!`git log --oneline -5`

按 Critical / Major / Minor 三级优先级输出审查结果，涵盖：
代码逻辑与可读性、安全漏洞、性能隐患（N+1 查询）、测试覆盖率、文档完整性
~~~

智能测试运行：

~~~

---

allowed-tools: Bash, Read, Edit
argument-hint: [test-pattern]
description: 运行测试并自动修复失败用例

---

运行匹配 "$ARGUMENTS" 的测试：
1. 自动检测测试框架（Jest / pytest / Go test）
2. 运行指定模式的测试
3. 若有失败，分析根因并修复代码
4. 重新运行验证修复

不提供参数则运行全量测试并输出覆盖率报告。
~~~

安全漏洞扫描：

~~~

---

allowed-tools: Read, Grep, Glob
description: OWASP Top 10 安全漏洞扫描
model: claude-opus-4-6

---

以安全工程师视角审查代码库：

高危：SQL 注入、XSS、命令注入、硬编码密钥
中危：不安全的反序列化、过时依赖、敏感信息写入日志、CORS 配置过宽
低危：缺失安全响应头、错误信息暴露内部细节

输出格式：漏洞位置 + 危险级别 + 修复建议代码示例
~~~

API 文档生成：

~~~

---

allowed-tools: Read, Glob, Edit
argument-hint: [输出文件路径]
description: 自动生成 REST API 文档

---

扫描 @src/routes/ 和 @src/controllers/，为所有端点生成 Markdown 文档：
- 端点路径与 HTTP 方法
- 请求参数（Query / Body / Path）及类型
- 响应结构（成功与错误）
- 认证要求
- curl 调用示例

保存到：$ARGUMENTS（默认：docs/api.md）
~~~

代码重构：

~~~

---

allowed-tools: Read, Edit, Glob
argument-hint: [目标文件或目录]
description: 代码质量重构

---

对 $ARGUMENTS 进行重构，遵循原则：
- 可读性：变量/函数命名语义化，消除魔法数字
- DRY 原则：提取重复逻辑为复用函数
- 单一职责：拆分超过 50 行的函数
- 错误处理：完善异常捕获和错误边界
- TypeScript：消除 `any` 类型，补全类型注解

重构前先输出变更计划，确认后再执行。
~~~

文档链接检查：

~~~

---

allowed-tools: Read, Bash, WebFetch
argument-hint: [文档文件路径]
description: 检查文档中的链接是否全部有效
model: claude-haiku-4-5-20251001

---

读取 $ARGUMENTS 文件，提取所有超链接（http/https），逐一请求并检查响应状态码。

输出格式：
- 有效链接（200）
- 重定向链接（3xx）及目标地址
- 失效链接（4xx/5xx）及建议替代链接

最后输出汇总：共 N 个链接，M 个失效。
~~~

---

### 附：配置文件说明

 建议：将 CLAUDE.local.md 和 .claude/settings.local.json 加入 .gitignore，避免个人偏好影响团队成员。

| 文件 / 目录 | 作用 | 版本控制 |
| --- | --- | --- |
| `CLAUDE.md` | 项目级共享指令（技术栈、构建命令、编码规范等） | 纳入 git |
| `CLAUDE.local.md` NEW | 个人私有指令（个人偏好，不共享给团队） | 加入 .gitignore |
| `~/.claude/CLAUDE.md` | 全局指令，所有项目通用 | — |
| `.claude/settings.json` | 项目共享配置（工具权限、hooks 等） | 纳入 git |
| `.claude/settings.local.json` | 项目个人配置（覆盖 settings.json，不共享） | 加入 .gitignore |
| `.claude/skills/` 推荐 | 项目级自定义 Skills（新推荐格式） | 纳入 git |
| `.claude/commands/` 旧格式 | 项目级自定义命令（旧格式，仍完全兼容） | 纳入 git |
| `~/.claude/skills/` | 个人全局 Skills（跨项目通用，本地私有） | — |

