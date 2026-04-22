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

**Beta 版本： **

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

**GA 版本 (v1.0)： **

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

