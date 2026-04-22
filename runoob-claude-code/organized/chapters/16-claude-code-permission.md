## Claude Code 权限配置

Claude Code 使用分层权限系统来平衡功能和安全性，支持细粒度权限规则、权限模式和沙箱策略来控制 Claude 可以访问和执行的操作。合理配置权限，既能让 AI 高效完成任务，又能防止误操作破坏代码或泄露敏感文件。

> 从 Claude Code v1.1.1 开始，推荐使用新的权限配置方式。旧的 `tools` 布尔配置仍被支持，但建议迁移到新的权限规则语法。

---

### 权限系统概述

Claude Code 的权限系统将操作分为三类，不同类型的操作有不同的默认权限策略：

| 工具类型 | 示例 | 是否需要批准 | 永久允许行为 |
| --- | --- | --- | --- |
| 只读操作 | 文件读取、Grep 搜索 | 否 | 不适用 |
| Bash 命令 | Shell 命令执行 | 是 | 每个项目目录和命令永久有效 |
| 文件修改 | Edit/Write 文件 | 是 | 直到会话结束 |

---

### 三种权限动作

每条权限规则最终会解析为以下三种动作之一：

| 动作 | 效果 | 适用场景 |
| --- | --- | --- |
| `"allow"` | 无需审批，直接自动运行 | 低风险、高频操作，如 git status、npm run build |
| `"ask"` | 弹出审批提示，由你决定是否允许 | 有一定风险的操作，如文件写入、危险命令执行 |
| `"deny"` | 直接阻止，不会执行也不会提示 | 明确不允许的危险操作，如 git push、rm -rf |

> **规则优先级：deny → ask → allow。** 第一个匹配的规则获胜，因此 deny 规则始终优先于 allow 和 ask。

---

### 权限模式

权限模式控制 Claude 是否在执行操作前询问用户。不同的任务需要不同级别的自主权。

#### 1、六种可用模式

| 模式 | 描述 | 最适用场景 |
| --- | --- | --- |
| `default` | 标准行为：首次使用每个工具时提示权限 | 入门学习、敏感工作需要完全监督 |
| `acceptEdits` | 自动接受会话的文件编辑权限，受保护目录除外 | 迭代正在审查的代码 |
| `plan` | Plan Mode：Claude 可以分析但不能修改文件或执行命令 | 探索代码库、规划重构 |
| `auto` | 自动批准工具调用，并进行后台安全检查（研究预览） | 长时间运行的任务、减少提示疲劳 |
| `dontAsk` | 自动拒绝工具调用，除非通过权限规则预先批准 | 锁定环境、CI 管道 |
| `bypassPermissions` | 跳过权限提示，但对受保护目录的写入仍会提示 | 仅隔离容器和 VM |

> **受保护目录说明： **无论模式如何，对 `.git`、`.vscode`、`.idea`、`.husky` 和 `.claude` 的写入永远不会自动批准，除了 `.claude/commands`、`.claude/agents` 和 `.claude/skills`。

#### 2、切换权限模式

**会话期间切换： **按 `Shift+Tab` 循环切换 `default` → `acceptEdits` → `plan` → `auto` **启动时指定模式： **

~~~
claude --permission-mode plan
claude --permission-mode bypassPermissions
~~~

**设置为默认模式（settings.json）： **

### 实例

{
"permissions": {
"defaultMode": "acceptEdits"
}
}

---

### 权限规则语法

#### 1、基本格式

权限规则遵循格式 `Tool` 或 `Tool(specifier)`：

### 实例

{
"permissions": {
"allow": ["Bash", "WebFetch", "Read"],
"deny": ["Edit"]
}
}

#### 2、匹配所有工具使用

不带说明符的规则匹配该工具的所有使用：

| 规则 | 效果 |
| --- | --- |
| `Bash` | 匹配所有 Bash 命令 |
| `WebFetch` | 匹配所有网络获取请求 |
| `Read` | 匹配所有文件读取 |
| `Edit` | 匹配所有文件编辑 |

`Bash(*)` 等同于 `Bash`，两者效果相同。

#### 3、使用说明符进行细粒度控制

### 实例

{
"permissions": {
"allow": [
"Bash(npm run build)", // 匹配确切的命令
"Read(./.env)", // 匹配读取当前目录中的 .env 文件
"WebFetch(domain:example.com)" // 匹配对 example.com 的获取请求
]
}
}

#### 4、通配符模式

Bash 规则支持带有 `*` 的 glob 模式，通配符可以出现在命令中的任何位置：

### 实例

{
"permissions": {
"allow": [
"Bash(npm run *)", // 匹配 npm run build、npm run test 等
"Bash(git commit *)", // 匹配 git commit -m "message" 等
"Bash(git * main)", // 匹配 git checkout main、git merge main 等
"Bash(* --version)", // 匹配任何带 --version 参数的命令
"Bash(* --help *)" // 匹配任何带 --help 参数的命令
],
"deny": [
"Bash(git push *)" // 阻止所有 git push 操作
]
}
}

> **注意通配符前的空格： **`Bash(ls *)` 匹配 `ls -la` 但不匹配 `lsof`；`Bash(ls*)` 匹配两者。

---

### 工具特定的权限规则

#### 1、Bash 命令

| 规则 | 匹配示例 |
| --- | --- |
| `Bash(npm run build)` | 仅 `npm run build` |
| `Bash(npm run test *)` | `npm run test`、`npm run test --coverage` |
| `Bash(npm *)` | 任何以 npm 开头的命令 |
| `Bash(* install)` | 任何以 install 结尾的命令 |
| `Bash(git * main)` | `git checkout main`、`git merge main` |

> **重要限制： **尝试约束命令参数的 Bash 权限模式可能很脆弱。URL 前的选项、不同的协议、重定向、变量、额外空格都可能导致不匹配。建议使用 WebFetch 工具配合 `domain:` 权限进行 URL 过滤。

#### 2、Read 和 Edit（文件操作）

文件路径支持多种路径前缀模式：

| 模式前缀 | 含义 | 示例 |
| --- | --- | --- |
| `//path` | 绝对路径（从文件系统根目录） | `Read(//Users/alice/secrets/ **)` 匹配 `/Users/alice/secrets/**` |
| `~/path` | 主目录路径 | `Read(~/Documents/*.pdf)` 匹配 `/Users/alice/Documents/*.pdf` |
| `/path` | 相对于项目根目录 | `Edit(/src/**/*.ts)` 匹配 `<project root>/src/**/*.ts` |
| `path` 或 `./path` | 相对于当前目录 | `Read(*.env)` 匹配 `<cwd>/*.env` |

### 实例

{
"permissions": {
"allow": [
"Edit(/docs/**)", // 允许编辑项目 docs 目录下的文件
"Read(~/.zshrc)", // 允许读取主目录的 .zshrc
"Edit(//tmp/scratch.txt)", // 允许编辑绝对路径的临时文件
"Read(src/**)" // 允许读取当前目录 src 子目录的文件
],
"deny": [
"Read(*.env)", // 阻止读取 .env 文件（防止泄露密钥）
"Edit(//etc/**)" // 阻止编辑系统目录文件
]
}
}

> **注意： **Read 和 Edit deny 规则不适用于 Bash 子进程中的 `cat .env`。要获得 OS 级别强制执行，需启用沙箱。

#### 3、WebFetch（网络请求）

### 实例

{
"permissions": {
"allow": [
"WebFetch(domain:github.com)", // 允许访问 GitHub
"WebFetch(domain:api.example.com)" // 允许访问内部 API
],
"deny": [
"WebFetch(domain:untrusted.com)" // 阻止访问不可信域名
]
}
}

#### 4、MCP（模型上下文协议）

### 实例

{
"permissions": {
"allow": [
"mcp__puppeteer", // 允许 puppeteer 服务器提供的任何工具
"mcp__puppeteer__*" // 允许来自 puppeteer 服务器的所有工具
],
"deny": [
"mcp__puppeteer__puppeteer_navigate" // 阻止特定工具
]
}
}

#### 5、Agent（子代理）

### 实例

{
"permissions": {
"allow": [
"Agent(Explore)", // 允许使用 Explore 子代理
"Agent(Plan)" // 允许使用 Plan 子代理
],
"deny": [
"Agent(my-custom-agent)" // 阻止自定义子代理
]
}
}

---

### 工作目录配置

默认情况下，Claude Code 只允许访问启动时的工作目录及其子目录。如果需要访问项目目录之外的路径，必须显式配置。

#### 1、扩展访问方式

- **启动期间： **使用 `--add-dir <path>` CLI 参数
- **会话期间： **使用 `/add-dir` 命令
- **持久配置： **添加到 settings.json 中的 `additionalDirectories`

### 实例

{
"additionalDirectories": [
"~/projects/personal/**", // 允许访问个人项目目录
"~/projects/work/**", // 允许访问工作项目目录
"~/dotfiles/**" // 允许访问配置文件目录
]
}

#### 2、例外情况

以下内容在设置额外目录后仍可访问，无需额外配置：
- `.claude/skills/` 中的 Skills（带有实时重新加载）
- `.claude/settings.json` 中的插件设置（仅 `enabledPlugins` 和 `extraKnownMarketplaces`）
- CLAUDE.md 文件和 `.claude/rules/`（仅当设置 `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` 时）

---

### 自动模式（Auto Mode）

自动模式是 Claude Code 引入的一种新机制，通过模型驱动的分类器替代人工审批，在保证效率的同时尽量控制风险。

#### 1、可用条件

- 仅限 Team、Enterprise 和 API 计划
- 需要 Claude Sonnet 4.6 或 Claude Opus 4.6
- 在 Haiku、claude-3 模型或第三方提供商上不可用
- 管理员必须在 Claude Code 管理员设置中启用

#### 2、工作原理

在每个操作运行前，一个单独的分类器模型审查对话并决定该操作是否与用户要求的相匹配。

**防御层次： **

1. 服务器端探针扫描传入的工具结果
2. 分类器永远不会看到工具结果，防止注入指令影响决策

**操作评估顺序： **

1. 与允许或拒绝规则匹配的操作立即解决
2. 只读操作和工作目录中的文件编辑自动批准（受保护目录除外）
3. 其他内容发送到分类器
4. 如果分类器阻止，Claude 收到原因并尝试替代方法

#### 3、分类器默认行为

| 操作类型 | 行为 |
| --- | --- |
| **默认阻止** |   |
| 下载和执行代码 | `curl \| bash` 等 |
| 向外部端点发送敏感数据 | 数据泄露风险 |
| 生产部署和迁移 | 生产环境修改 |
| 云存储上的大规模删除 | 数据丢失风险 |
| 授予 IAM 或仓库权限 | 权限提升风险 |
| 修改共享基础设施 | 影响其他用户 |
| 不可逆地销毁文件 | 会话开始前存在的文件 |
| 破坏性源代码控制操作 | 强制推送、直接推送到 main |
| **默认允许** |   |
| 工作目录中的本地文件操作 | 安全范围内 |
| 安装声明的依赖项 | package.json 中已有的依赖 |
| 读取 .env 并发送凭证 | 向匹配的 API 发送 |
| 只读 HTTP 请求 | GET 请求 |
| 推送到启动的分支 | 安全分支操作 |

#### 4、自动模式配置

### 实例

{
"autoMode": {
"environment": [
"Source control: github.example.com/acme-corp and all repos under it",
"Trusted cloud buckets: s3://acme-build-artifacts, gs://acme-ml-datasets",
"Trusted internal domains: *.corp.example.com, api.internal.example.com",
"Key internal services: Jenkins at ci.example.com, Artifactory at artifacts.example.com"
],
"allow": [
"Deploying to the staging namespace is allowed",
"Writing to s3://acme-scratch/ is allowed"
],
"soft_deny": [
"Never run database migrations outside the migrations CLI",
"Never modify files under infra/terraform/prod/: production infrastructure changes go through the review workflow"
]
}
}

#### 5、回退机制

如果分类器在一行中阻止操作 3 次，或在一个会话中总共阻止 20 次，自动模式会暂停，Claude Code 恢复为每个操作提示。

~~~
/permissions  # 在 "最近拒绝" 选项卡查看被拒绝的操作
~~~

---

### 沙箱与权限的关系

沙箱和权限是互补的安全层，协同工作：

| 方面 | 权限 | 沙箱 |
| --- | --- | --- |
| **控制对象** | 控制 Claude Code 可以使用哪些工具 | 限制 Bash 命令可访问的文件系统和网络内容 |
| **评估时机** | 在任何工具运行之前进行评估 | 仅适用于 Bash 命令及其子进程 |
| **适用范围** | 所有工具：Bash、Read、Edit、WebFetch、MCP 等 | 仅 Bash 命令 |

#### 1、启用沙箱

~~~
/sandbox
~~~

运行 `/sandbox` 命令启用沙箱，会打开一个菜单选择沙箱模式。

#### 2、配置沙箱

### 实例

{
"sandbox": {
"enabled": true,
"filesystem": {
"allowWrite": ["~/.kube", "/tmp/build"], // 允许写入这些路径
"denyWrite": ["~/important/**"], // 阻止写入重要目录
"denyRead": ["~/"] // 阻止读取主目录
},
"network": {
"httpProxyPort": 8080, // HTTP 代理端口
"socksProxyPort": 8081 // SOCKS 代理端口
}
}
}

> **深度防御： **权限和沙箱应同时启用。权限 deny 规则阻止 Claude 尝试访问受限资源，沙箱限制防止 Bash 命令到达定义边界之外的资源。

---

### 使用 Hooks 扩展权限

PreToolUse hooks 在权限提示之前运行，可以：
- 拒绝工具调用
- 强制提示
- 跳过提示让调用继续

### 实例

#!/bin/bash

# PreToolUse hook 示例：拦截危险命令

COMMAND=$(cat | jq -r '.tool_input.command // empty')

if echo "$COMMAND" | grep -qE 'rm\s+(-[rf]+\s+)*(\/|~|\.\.\/)'; then
echo "BLOCKED: rm on sensitive path"
exit 2 # 退出代码 2 阻止工具调用
fi
exit 0 # 允许继续

> **重要： **跳过提示不会绕过权限规则。Deny 和 ask 规则在 hook 返回 "allow" 后仍会被评估。阻止 hook 以退出代码 2 退出时，在权限规则被评估之前停止工具调用。

---

### 设置优先级

Claude Code 的设置按以下优先级生效（从高到低）：

1. **托管设置** - 无法被任何其他级别覆盖（管理员控制）
2. **命令行参数** - 临时会话覆盖
3. **本地项目设置** (`.claude/settings.local.json`)
4. **共享项目设置** (`.claude/settings.json`)
5. **用户设置** (`~/.claude/settings.json`)

> **关键规则： **如果工具在任何级别被拒绝，没有其他级别可以允许它。

---

### 实用配置示例

#### 示例一：保守模式（所有操作都需审批）

适合初次接触 Claude Code 或在重要项目上工作时使用：

### 实例

{
"permissions": {
"defaultMode": "default"
}
}

#### 示例二：开发常用配置（读操作放开，命令执行审批）

适合日常开发：允许 Claude 自由读取和搜索代码，但修改文件和执行命令时需要确认：

### 实例

{
"permissions": {
"defaultMode": "acceptEdits",
"allow": [
"Bash(git status *)", // 查看 git 状态
"Bash(git log *)", // 查看 git 日志
"Bash(git diff *)", // 查看 git 差异
"Bash(npm run *)", // 运行 npm 脚本
"Bash(npm test *)", // 运行测试
"Bash(ls *)", // 列出目录
"Bash(grep *)" // 搜索内容
],
"deny": [
"Bash(rm *)", // 删除文件：直接阻止
"Bash(git push *)", // 推送代码：直接阻止
"Bash(mkdir / *)", // 创建系统目录：直接阻止
"Edit(*.env)" // 阻止编辑 .env 文件
]
}
}

#### 示例三：锁定环境配置（仅允许预批准操作）

适合 CI 管道或需要严格控制的环境：

### 实例

{
"permissions": {
"defaultMode": "dontAsk",
"allow": [
"Read(*)", // 允许读取所有文件
"Bash(npm run build *)", // 允许构建命令
"Bash(npm test *)" // 允许运行测试
],
"deny": [
"Edit(*)", // 禁止所有文件编辑
"Bash(git *)", // 禁止所有 git 操作
"Bash(curl *)", // 禁止网络请求
"Bash(ssh *)" // 禁止 SSH 连接
]
}
}

#### 示例四：允许多项目访问

当 Claude 需要跨多个项目目录工作时：

### 实例

{
"permissions": {
"allow": [
"Edit(/src/**)", // 允许编辑 src 目录
"Edit(/docs/**)" // 允许编辑文档目录
],
"deny": [
"Edit(/docs/**/*.md)" // 禁止编辑文档文件（只读）
]
},
"additionalDirectories": [
"~/projects/personal/**", // 允许访问个人项目
"~/projects/work/**", // 允许访问工作项目
"~/dotfiles/**" // 允许访问配置文件
]
}

#### 示例五：使用沙箱限制 Bash 操作

启用沙箱提供 OS 级别的额外保护：

### 实例

{
"sandbox": {
"enabled": true,
"filesystem": {
"allowWrite": ["/tmp/build", "~/projects/myapp/**"],
"denyRead": ["~/secrets/**"]
},
"network": {
"httpProxyPort": 8080
}
},
"permissions": {
"allow": [
"Bash(*)", // 沙箱内允许所有命令
"WebFetch(domain:api.github.com)"
],
"deny": [
"WebFetch(domain:untrusted.com)"
]
}
}

---

### 托管设置（管理员配置）

管理员可以部署无法被用户或项目设置覆盖的托管设置。

| 设置 | 描述 |
| --- | --- |
| `allowManagedHooksOnly` | 防止加载用户、项目和插件 hooks |
| `allowManagedMcpServersOnly` | 仅尊重托管设置的 MCP 服务器 |
| `allowManagedPermissionRulesOnly` | 防止用户和项目设置定义权限规则 |
| `sandbox.filesystem.allowManagedReadPathsOnly` | 仅尊重托管设置的读取路径 |
| `sandbox.network.allowManagedDomainsOnly` | 仅尊重托管设置的允许域 |
| `permissions.disableBypassPermissionsMode` | 设置为 "disable" 可防止使用 bypassPermissions 模式 |
| `permissions.disableAutoMode` | 设置为 "disable" 可防止使用自动模式 |

---

### 安全最佳实践

1. **从限制性开始**：从最小权限开始，根据需要扩展
2. **使用沙箱**：沙箱提供 OS 级别的额外保护，应同时启用
3. **保护敏感文件**：使用 `deny` 规则阻止访问 `.env`、密钥文件等
4. **限制网络访问**：仅允许访问必要的域名，防止数据泄露
5. **避免 bypassPermissions**：仅在隔离环境（容器、VM）中使用
6. **使用 auto 模式的 soft_deny**：提供安全指导而不过度限制
7. **定期审查配置**：查看沙箱违规尝试和被拒绝的操作

> **安全警告： **`bypassPermissions` 模式不提供针对提示注入或意外操作的保护。对于仍维护后台安全检查的更安全替代品，应使用 auto 模式。

