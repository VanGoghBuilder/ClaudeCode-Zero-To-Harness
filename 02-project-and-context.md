# 项目初始化与上下文

> 阅读提示：如果你刚建立完 Claude Code 的基本认知，建议先读 [概览与安装](./01-overview-and-setup.md)；如果你想先理解这整条主线最终会如何汇总成 harness 方法论，可以对照阅读 [Claude Code 的 Harness Engineering 总纲](./06-harness-engineering-guide.md)。

## 包含章节

- Claude Code 项目初始化
- Claude Code 项目目录结构
- CLAUDE.md 使用指南
- Claude Code 上下文管理
- Claude Code 记忆系统（Memory）
- Claude Code 权限配置

---

## Claude Code 项目初始化

Claude Code 项目初始化可以在 Claude Code 的交互界面中输入：

~~~
/init
~~~

我们可以在一个已有的项目目录初始化，也可以新建一个。

我们可以先创建一个目录：

~~~
mkdir claude-runoob-test
~~~

进入该目录：

~~~
cd claude-runoob-test
~~~

创建一个测试文件 test.py，代码如下：

~~~
print("Hello, Runoob!")
~~~

进入 Claude Code 的交互界面：

~~~
claude
~~~

使用 /init 命令来初始化项目：

~~~
/init
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/d7228f75-559e-492a-b098-36458160d8c2.png)

如果使用 VS Code，也可以在 VS Code 的 Claude Code 界面输入 /init:

![image](https://www.runoob.com/wp-content/uploads/2026/03/5f3fbc35-6c93-442f-a0b8-d97dce9fce0e.png)

Claude 会自动扫描你的代码库——读取 package.json、现有文档、配置文件以及代码结构，然后生成一份专属于你项目的 CLAUDE.md 文件。

Claude
整个过程无需手动操作，Claude 会自行分析并输出初始配置。

![image](https://www.runoob.com/wp-content/uploads/2026/03/0c727439-dc58-4d3d-b001-e4a6639a100c.png)

#### CLAUDE.md 文件

CLAUDE.md 是一个放在项目根目录的 Markdown 文件，Claude Code 在每次会话开始时都会自动读取。

CLAUDE.md 会成为 Claude 系统提示的一部分，使每次对话都能预先加载项目上下文，不再需要重复解释基本信息。

一份好的 CLAUDE.md 应该覆盖三个维度：
- WHAT（是什么）：技术栈、项目结构，为 Claude 提供代码库的全局地图
- WHY（为什么）：项目的目的，各模块的功能与定位
- HOW（怎么做）：开发方式，例如使用 bun 而非 node，以及 Claude 如何验证改动是否正确 Humanlayer

以下是一份典型的 CLAUDE.md 结构示例：

~~~

# 项目名称

## 项目概述

简述这个项目的目的和功能。

## 技术栈

- Frontend: React + TypeScript
- Backend: Node.js + Express
- Database: PostgreSQL

## 目录结构

- `src/components/` - React 组件
- `src/api/`        - API 层
- `tests/`          - 测试文件

## 常用命令

- 启动开发服务器：`pnpm dev`
- 运行测试：`pnpm test`
- 代码检查：`pnpm lint`

## 开发规范

- 使用 TypeScript strict 模式
- 优先使用 interface 而非 type
- 禁止使用 any，使用 unknown 替代

```

### 文件位置与层级

项目的核心文件结构如下：
```

your-project/
├── CLAUDE.md                  # 项目主记忆文件（团队共享）
├── .claude/
│   ├── settings.json          # Hooks、权限、环境配置
│   ├── settings.local.json    # 个人配置（建议加入 .gitignore）
│   └── commands/              # 自定义斜杠命令
│       └── my-command.md
└── .mcp.json                  # MCP 服务配置

```
~~~

---

### 初始化后的最佳实践

> **迭代优化，而非一次写死。**

`/init` 命令适合快速入门，但真正的价值来自于随时间不断迭代，可以补充 Claude 无法自动推断的内容，例如分支命名规范、部署流程、Code Review 要求；同时删去不适用的通用建议。
- **提交到版本控制**：将 `CLAUDE.md` 提交到 Git，这样整个团队都能从中受益，新成员可以通过让 Claude 解释代码库来快速上手。
- **保持精简**：内容要简洁且普遍适用。采用"渐进式披露"原则——不要把所有你想让 Claude 知道的内容都塞进去，而是告诉它**如何查找重要信息**，让它按需获取，避免撑爆上下文窗口。
- **不要让 CLAUDE.md 替代 linter**：在文件中写代码风格规范是最常见的误区之一。永远不要用 LLM 来做 linter 的工作——linter 更快、更便宜，而且是确定性的。代码风格约束只会让上下文窗口膨胀，降低 Claude 的指令遵从质量。

---

### 使用 `#` 快捷键持续更新

在对话中，随时用 `#` 前缀给 Claude 发送记忆指令：

~~~
# 我们始终使用 pnpm，不用 npm
# 所有组件必须包含单元测试
~~~

这些补充会逐渐积累，形成一份真正反映团队实际工作方式的 `CLAUDE.md`。

---

### 验证初始化效果

初始化完成后，可以通过以下对话确认 Claude 是否正确理解了项目：
- 这个项目是做什么的？
- 解释一下目录结构。
- 项目用了哪些技术？
- 运行测试的命令是什么？

---

## Claude Code 项目目录结构

一个典型的 Claude Code 项目目录结构如下：

~~~
your-project/
├── CLAUDE.md                    ← 团队共享指令，提交到 git
├── CLAUDE.local.md              ← 个人覆盖，被 git 忽略
└── .claude/
    ├── settings.json            ← 权限 + 配置，提交到 git
    ├── settings.local.json      ← 个人权限，被 git 忽略
    ├── commands/                ← 自定义斜杠命令
    │   ├── review.md            →  /project:review
    │   ├── fix-issue.md         →  /project:fix-issue
    │   └── deploy.md            →  /project:deploy
    ├── rules/                   ← 模块化指令文件（全局生效）
    │   ├── code-style.md
    │   ├── testing.md
    │   └── api-conventions.md
    ├── skills/                  ← 自动调用的工作流
    │   ├── security-review/
    │   │   └── SKILL.md
    │   └── deploy/
    │       └── SKILL.md
    └── agents/                  ← 子代理角色定义
        ├── code-reviewer.md
        └── security-auditor.md
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-1.svg)

---

### CLAUDE.md -- 项目核心指令

#### CLAUDE.md

这是 Claude 进入项目时 **第一个读取**的文件，相当于项目欢迎手册。

CLAUDE.md 放置在项目根目录，所有团队成员共享，它告诉 Claude：这个项目是什么、如何运行、有什么约定。

**典型内容：**

~~~
# My Project

一句话描述项目用途。

## Tech Stack

- Backend: Python / FastAPI
- Frontend: React + TypeScript
- Database: PostgreSQL

## Common Commands

`npm run dev`    # 启动开发服务器
`pytest tests/`  # 运行测试
`npm run build`  # 构建生产版本

## Code Conventions

- 使用 snake_case 命名变量
- 所有 API 需要写单元测试
- PR 合并前必须通过 CI

## Architecture Overview

src/
├── api/        # FastAPI 路由层
├── services/   # 业务逻辑层
└── models/     # 数据模型层
~~~

> **💡 提示：** Claude 会自动递归读取父目录中的 CLAUDE.md。在 monorepo 中，子包内可再放一个 CLAUDE.md，Claude 会将两层指令合并理解。

#### CLAUDE.local.md

个人专属的覆盖层，叠加在 CLAUDE.md 之上。

CLAUDE.local.md 存放只与你本人相关的偏好或临时指令，不应共享给团队。

典型内容：

~~~

# 我的本地覆盖

本地数据库地址：localhost:5433（非默认端口）

调试时请优先输出详细日志。

## 临时规则（本次任务用）

目前专注于重构 auth/ 模块，其他模块暂时不要改动。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-2.svg)

---

### `.claude/settings.json` -- 权限与配置中心

#### settings.json

团队共享的配置文件，控制 Claude **允许或禁止**执行哪些操作，作为团队安全基线。

~~~
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(pytest:*)",
      "Bash(git diff:*)",
      "Bash(git log:*)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(curl * | bash)"
    ]
  }
}
~~~

#### settings.local.json

个人本地权限覆盖，临时放开或收紧某些权限，不影响团队其他成员。

~~~
{
  "permissions": {
    "allow": [
      "Bash(rm ./tmp/*)"
    ]
  }
}
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-3.svg)

---

### `.claude/commands/` -- 自定义斜杠命令

目录下每个 `.md` 文件自动映射为一条 `/project:文件名` 命令。

`.claude/commands/` 是团队将重复性任务 **标准化**的核心机制。

| 文件名 | 对应命令 |
| --- | --- |
| `review.md` | `/project:review` |
| `fix-issue.md` | `/project:fix-issue` |
| `deploy.md` | `/project:deploy` |

#### 示例：`commands/review.md`

~~~

# Code Review

请对当前修改执行完整的代码审查：

1. 检查是否有安全漏洞（SQL 注入、XSS 等）
2. 验证错误处理是否完整
3. 确认测试覆盖率是否达标
4. 检查是否符合代码风格规范
5. 评估性能影响

用中文输出结构化审查报告，按严重程度排列问题。
~~~

#### 示例：`commands/fix-issue.md`（带参数）

~~~

# Fix GitHub Issue

给定 Issue 编号 $ARGUMENTS，请：

1. 读取并理解 Issue 描述
2. 定位相关代码文件
3. 实现最小化修复方案
4. 编写对应的单元测试
5. 更新 CHANGELOG.md

调用方式：/project:fix-issue 123
~~~

> **💡 参数传递：** 命令文件中可使用 `$ARGUMENTS` 占位符接收调用时传入的参数。
![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-4.svg)

---

### `.claude/rules/` -- 模块化行为规则

将 CLAUDE.md 中的规则 **拆分模块化**存放，Claude 在整个会话中始终遵守。适合存放长期稳定执行的行为约定，避免 CLAUDE.md 过于臃肿。

#### 示例：`rules/code-style.md`

~~~

# Code Style Rules

- TypeScript 严格模式，禁用 any 类型
- 函数长度不超过 40 行，超出则拆分
- 优先使用 const，避免使用 let
- 导入顺序：标准库 → 三方包 → 本地模块
- 所有 export 的函数/类型需要 JSDoc 注释
- 禁止使用 console.log，使用项目 logger
~~~

#### 示例：`rules/testing.md`

~~~

# Code Style Rules

- TypeScript 严格模式，禁用 any 类型
- 函数长度不超过 40 行，超出则拆分
- 优先使用 const，避免使用 let
- 导入顺序：标准库 → 三方包 → 本地模块
- 所有 export 的函数/类型需要 JSDoc 注释
- 禁止使用 console.log，使用项目 logger
~~~

#### 示例：`rules/api-conventions.md`

~~~

# API Conventions

- RESTful 风格，资源名使用复数形式
- 统一响应格式：{ data, error, meta }
- 错误码遵循 HTTP 标准语义
- 所有接口需要在 OpenAPI 文档中声明
- 分页参数统一使用 page / page_size
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-5.svg)

---

### `.claude/skills/` -- 自动调用的工作流

Skills 是更高级的 **复合工作流**。当 Claude 判断某个任务适合某个 skill 时，会自动读取并执行对应的 SKILL.md， **无需手动调用**。

每个 skill 是一个子目录，目录内包含 `SKILL.md`。

#### 示例：`skills/security-review/SKILL.md`

~~~

# Security Review Skill

## 触发条件

当用户请求代码审查、代码涉及认证/授权/加密/用户输入处理时自动触发。

## 执行步骤

1. 扫描 SQL 注入风险（检查所有数据库查询）
2. 检查 XSS 防护（验证输出转义）
3. 审计权限边界（确认最小权限原则）
4. 检查敏感数据处理（日志、错误信息中是否泄露）
5. 输出 OWASP Top 10 对照检查表

## 输出格式

按 CVSS 评分排列，高危问题优先展示。
~~~

> **⚡ Skills vs Commands 的区别：**
> - **Commands** 需要用户主动输入斜杠命令触发，是“工具箱”
> - **Skills** 由 Claude 根据上下文自动判断是否调用，是“智能本能”

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-6.svg)

---

### `.claude/agents/` -- 子代理角色

定义可被主 Claude 实例 **派遣的专业子代理**。在复杂任务中，主代理将子任务委派给对应专家角色，实现 **多代理协作**。子代理在隔离上下文中运行，拥有独立的权限范围。

#### 示例：`agents/code-reviewer.md`

~~~

---

name: code-reviewer
description: 资深代码审查员，专注代码质量与可维护性

---

# 代码审查员

## 角色定位

你是一名拥有 10 年经验的资深工程师，专注于代码可读性、性能优化和最佳实践。

## 审查重点

- 命名是否清晰表达意图
- 函数/类的单一职责原则
- 边界条件和错误处理
- 性能瓶颈（N+1 查询、不必要的循环等）

## 权限

只读访问，不直接修改文件。

## 输出格式

使用 Markdown 表格输出，包含：问题位置、严重程度、建议方案。
~~~

#### 示例：`agents/security-auditor.md`

~~~

---

name: security-auditor
description: 安全审计专家，专注漏洞扫描与合规审计

---

# 安全审计员

## 角色定位

你是一名安全工程师，熟悉 OWASP、CVE 数据库和常见攻击向量。

## 审计范围

- 认证与授权逻辑
- 输入验证与输出转义
- 依赖包已知漏洞（结合 npm audit / pip audit）
- 敏感信息泄露风险

## 权限

只读访问 + 可运行安全扫描工具。

## 输出格式

按 CVSS 3.1 评分排列，包含：漏洞描述、影响范围、修复建议、参考链接。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/claude-code-project-runoob-7.svg)

---

### 速查表

| 文件 / 目录 | 提交 Git | 触发方式 | 核心用途 |
| --- | --- | --- | --- |
| `CLAUDE.md` | ✅ 是 | 自动（启动时） | 项目基础指令与约定 |
| `CLAUDE.local.md` | ❌ 否 | 自动（启动时） | 个人覆盖、临时指令 |
| `.claude/settings.json` | ✅ 是 | 自动（启动时） | 团队权限基线 |
| `.claude/settings.local.json` | ❌ 否 | 自动（启动时） | 个人权限覆盖 |
| `.claude/commands/*.md` | ✅ 是 | 手动（`/project:xxx`） | 可复用的标准化流程 |
| `.claude/rules/*.md` | ✅ 是 | 自动（全局生效） | 模块化行为规范 |
| `.claude/skills/*/SKILL.md` | ✅ 是 | 自动（Claude 判断） | 智能复合工作流 |
| `.claude/agents/*.md` | ✅ 是 | 自动（主代理派遣） | 专业子代理角色定义 |

---

### 最佳实践建议

**从最小可行配置开始： **

~~~
your-project/
├── CLAUDE.md        ✅ 先从这里开始
└── .claude/
    └── settings.json    ✅ 配置基本权限
~~~

随着项目发展，再逐步引入：

1. `commands/` — 当你发现自己重复输入相同的指令时
2. `rules/` — 当 CLAUDE.md 超过 100 行时，拆分模块
3. `skills/` — 当有复杂的多步骤工作流需要标准化时
4. `agents/` — 当任务复杂到需要多个专业视角并行时

> **原则： ** "够用"胜过"完美"。不要一开始就构建复杂的多代理系统，在真正需要时再引入。

---

## CLAUDE.md 使用指南

`CLAUDE.md` 是 Claude Code 中最重要的配置文件，用于向 Claude 传递项目级别的持久指令。每次启动 Claude Code 会话时，它都会自动读取并加载这个文件中的内容，作为系统级上下文融入每一次对话中。

通俗地说，`CLAUDE.md` 就是你在项目中给 Claude 写的一份工作手册——告诉它这个项目是什么、遵循什么规范、有哪些注意事项，让它每次都能以符合项目要求的方式工作，而不是每次对话都重新解释。

---

### CLAUDE.md 的作用

没有 `CLAUDE.md` 时，Claude 每次都从零开始理解你的项目，你需要反复告诉它：用哪个包管理器、代码风格是什么、测试怎么跑、哪些文件不要动……有了 `CLAUDE.md`，这些信息只需写一次，Claude 每次都会遵守。

具体来说，`CLAUDE.md` 可以帮你做到以下几件事：
- **统一团队行为**：将文件提交到 git，所有团队成员使用 Claude Code 时都遵循相同的规范
- **减少重复沟通**：项目约定、架构规则、禁止事项只写一次，永久生效
- **降低出错概率**：明确告知 Claude 哪些操作有风险，避免它做出错误的决策
- **加速 AI 理解**：帮助 Claude 快速定位关键文件和理解项目结构，减少不必要的文件探索

---

### 文件放置位置

Claude Code 会从多个位置加载 `CLAUDE.md`，不同位置的文件作用范围不同：

| 位置 | 路径 | 作用范围 | 是否提交 git |
| --- | --- | --- | --- |
| **项目根目录** | `{项目根目录}/CLAUDE.md` | 当前项目所有会话 | ✅ 推荐提交，团队共享 |
| **项目本地** | `{项目根目录}/.claude/CLAUDE.md` | 当前项目所有会话 | ❌ 加入 .gitignore，仅个人使用 |
| **子目录** | `{任意子目录}/CLAUDE.md` | Claude 打开该目录下的文件时自动加载 | ✅ 适合多模块仓库 |
| **全局用户级** | `~/.claude/CLAUDE.md` | 当前用户的所有项目 | ❌ 个人配置，不提交 |

当多个位置都存在 `CLAUDE.md` 时，Claude Code 会将它们 **全部加载并合并**，优先级从高到低依次为：

~~~
项目本地 → 项目根目录 → 子目录 → 全局用户级
~~~

> 项目根目录的 `CLAUDE.md` 建议提交到 git，让整个团队共享同一套 AI 工作规范。个人偏好（如不喜欢加分号）放在 `.claude/CLAUDE.md` 中并加入 `.gitignore`，不影响他人。

---

### 快速创建 CLAUDE.md

最简单的方式是让 Claude Code 自动生成初始版本。在项目目录中启动 Claude Code 后，执行：

~~~
/init
~~~

Claude Code 会分析你的项目结构、代码风格、已有配置文件（如 `package.json`、`pyproject.toml`、`.eslintrc` 等），自动生成一份符合项目实际情况的 `CLAUDE.md`，然后你可以在此基础上补充和调整。

也可以在项目目录中手动创建：

~~~
touch CLAUDE.md
~~~

---

### 文件内容结构

`CLAUDE.md` 是一个普通的 Markdown 文件，没有强制的格式要求，但良好的结构能帮助 Claude 更快找到关键信息。以下是推荐的内容结构：

~~~

# 项目名称

一句话说明这个项目是什么，方便 Claude 快速定位项目性质。

## 技术栈

- 语言：Python 3.11
- 框架：FastAPI 0.110
- 数据库：PostgreSQL 15 + SQLAlchemy ORM
- 测试：pytest

## 常用命令

### 开发

```
bash
uv run uvicorn main:app --reload   # 启动开发服务器
uv run pytest                       # 运行所有测试
uv run pytest -k "test_auth"        # 运行指定测试
```

### 代码检查

```
bash
uv run ruff check .                 # 代码检查
uv run ruff format .                # 代码格式化
```

## 项目结构

- `src/api/` — API 路由和请求处理
- `src/models/` — 数据库模型定义
- `src/services/` — 业务逻辑层
- `tests/` — 测试文件，与 src/ 目录结构镜像对应

## 编码规范

- 使用 `uv` 管理依赖，不使用 pip 直接安装
- 所有函数必须有类型注解
- 字符串一律使用双引号
- 新增 API 路由必须同步添加测试

## 注意事项

- 不要修改 `migrations/` 目录下的已有文件，只能新增迁移文件
- `config/secrets.py` 包含敏感配置，禁止输出其内容到日志或终端
- 数据库操作必须通过 Service 层，不要在路由层直接操作 ORM
~~~

---

### 核心内容模块详解

#### 1、常用命令

这是 `CLAUDE.md` 中 **最高频被参考**的部分。Claude 在执行测试、构建、代码检查等任务时，会优先查找这里定义的命令，避免猜测或使用错误的命令：

~~~

## 常用命令

### 安装依赖

```
bash
npm ci                    # 安装依赖（CI 环境使用，严格按 lock 文件安装）
```

### 开发

```
bash
npm run dev               # 启动开发服务器（端口 3000）
npm run build             # 构建生产版本
npm run preview           # 预览生产构建
```

### 测试

```
bash
npm test                  # 运行所有测试
npm test -- --watch       # 监听模式
npm test -- --coverage    # 生成覆盖率报告
```

### 代码质量

```
bash
npm run lint              # ESLint 检查
npm run lint:fix          # 自动修复可修复的问题
npm run typecheck         # TypeScript 类型检查
```

~~~

#### 2、项目结构说明

帮助 Claude 快速定位文件，减少不必要的目录扫描，尤其在大型项目中效果明显：

~~~

## 项目结构

```
src/
├── app/                  # Next.js App Router 页面
│   ├── (auth)/           # 需要登录才能访问的页面组
│   └── api/              # API 路由
├── components/           # 可复用 UI 组件
│   ├── ui/               # 基础 UI 组件（Button、Input 等）
│   └── features/         # 业务组件（按功能模块组织）
├── lib/                  # 工具函数和配置
│   ├── db/               # 数据库客户端和查询
│   └── auth/             # 认证相关逻辑
└── types/                # TypeScript 类型定义
```

关键文件：
- `src/lib/db/client.ts` — 数据库连接配置
- `src/middleware.ts` — 认证中间件，处理路由保护
- `env.example` — 所有必要的环境变量示例
~~~

#### 3、编码规范

告知 Claude 项目的代码风格和约定，确保生成的代码与现有代码库风格一致：

~~~

## 编码规范

### 通用

- 文件名使用 kebab-case（如 `user-profile.ts`），类名使用 PascalCase
- 优先使用具名导出（named export），避免默认导出（default export）
- 异步函数一律使用 async/await，禁止使用 .then() 链式调用

### 组件规范

- 组件文件与其测试文件放在同一目录（如 `Button.tsx` 和 `Button.test.tsx`）
- Props 类型使用 interface 定义，命名格式为 `${组件名}Props`
- 不要将业务逻辑写在组件中，提取为自定义 Hook 或 Service

### 错误处理

- API 路由使用统一的错误响应格式：`{ error: string, code: string }`
- 客户端错误通过 Error Boundary 捕获，不要在每个组件里单独 try/catch
~~~

#### 4、架构约束与禁止事项

这是防止 Claude 犯"聪明但错误"决策的关键部分。对于你了解但 Claude 不知道的特殊情况，必须明确写出来：

~~~

## 架构约束

- 所有数据库查询必须通过 `src/lib/db/queries/` 中的函数执行，不要在路由或组件中直接写 SQL
- 状态管理使用 Zustand，不要引入 Redux 或其他状态管理库
- 样式使用 Tailwind CSS utility class，不要新增 CSS 文件或使用 CSS Modules

## 注意事项（重要）

- `legacy/` 目录下的代码是遗留代码， **禁止修改**，只能读取
- `.env.local` 和 `.env.production` 包含真实密钥， **禁止输出文件内容**
- `prisma/migrations/` 中已有的迁移文件 **禁止修改**，数据库变更只能新增迁移
- 修改 `src/middleware.ts` 前必须先告知我，该文件影响所有路由的认证逻辑
~~~

#### 5、开发环境说明

帮助 Claude 理解项目的运行环境，避免因环境差异导致命令执行失败：

~~~

## 开发环境

- Node.js：需要 v20 或以上版本（通过 `.nvmrc` 指定）
- 包管理器：pnpm（禁止使用 npm 或 yarn 安装依赖）
- 本地数据库：Docker Compose 启动（`docker compose up -d`）
- 端口：前端 3000，API 3001，数据库 5432

### 环境变量

参考 `.env.example` 文件配置本地环境变量，复制为 `.env.local` 后填入实际值。
必填项：`DATABASE_URL`、`NEXTAUTH_SECRET`、`NEXTAUTH_URL`
~~~

---

### 多模块仓库（Monorepo）的配置方式

在 Monorepo 中，可以在仓库根目录放一个全局 `CLAUDE.md`，每个子包目录下再放各自的 `CLAUDE.md`。Claude 打开某个子包的文件时，会同时加载根目录和该子包目录下的两个文件：

~~~
my-monorepo/
├── CLAUDE.md                  ← 全局规范：共用命令、整体架构、通用约定
├── packages/
│   ├── web/
│   │   └── CLAUDE.md          ← 前端专属：React 规范、样式约定、构建流程
│   ├── api/
│   │   └── CLAUDE.md          ← 后端专属：API 设计规范、数据库约定
│   └── shared/
│       └── CLAUDE.md          ← 共享包：导出规则、版本管理约定
└── tools/
    └── CLAUDE.md              ← 工具脚本：特殊说明和使用限制
~~~

~~~

# My Monorepo

使用 pnpm workspace 管理的前后端一体化项目。

## 全局命令

```
bash
pnpm install          # 安装所有包的依赖
pnpm -r build         # 构建所有包
pnpm -r test          # 运行所有包的测试
pnpm --filter web dev # 只启动 web 包的开发服务器
```

## 包之间的依赖关系

- `web` 和 `api` 都依赖 `shared`
- 禁止 `shared` 依赖 `web` 或 `api`（防止循环依赖）
- 跨包引用使用包名（如 `@my-app/shared`），不要使用相对路径
~~~

---

### 用 @ 语法引用外部文件

当项目已经有了规范文档（如 API 设计规范、数据库设计文档等），不需要将内容复制到 `CLAUDE.md` 中，直接用 `@文件路径` 引用即可。~~~
Claude 读取 <code>CLAUDE.md</code> 时会自动加载引用的文件内容：</p>
<pre>

## 规范文档

详细的 API 设计规范请参考：
@docs/api-design-guide.md

数据库设计约定：
@docs/database-conventions.md

组件库使用说明：
@docs/component-guidelines.md
~~~

> 引用的文件路径是相对于 `CLAUDE.md` 所在目录的相对路径。引用的文件内容会占用上下文窗口，避免引用过大的文件（建议单个引用文件不超过 500 行）。

---

### 全局 CLAUDE.md 的使用建议

用户级别的 `~/.claude/CLAUDE.md` 适合存放跨项目通用的个人偏好和习惯，这些内容对所有项目生效：
~~~

# 个人全局配置

## 回答偏好

- 回复使用中文
- 代码修改前先简要说明修改思路，不要直接给出代码
- 遇到有多种实现方案时，列出选项让我选择，而不是直接选一种

## 通用约定

- 提交信息使用英文，格式：`type(scope): description`
- 新文件开头不加版权注释
- 优先使用原生 API，避免引入不必要的依赖

## 安全习惯

- 修改认证相关代码前主动提示我注意安全影响
- 不要在代码注释或日志中输出任何密钥或 token
~~~

---

### CLAUDE.md 的维护建议

#### 1、保持精简

`CLAUDE.md` 的内容会在每次会话中占用上下文窗口。内容过多会压缩 Claude 实际可用的上下文空间，反而降低效率。建议遵循以下原则：
- 每条规则只写一次，不要重复表达相同意思
- 与代码无关的背景信息（如公司介绍、产品规划）不要写进来
- 能通过代码本身传达的信息（如 eslint 配置已经定义了代码风格），不需要在 `CLAUDE.md` 中重复声明
- 建议总字数控制在 500 字以内，超过 1000 字时需要考虑精简

#### 2、持续更新

随着项目的演进，`CLAUDE.md` 也需要同步更新。以下时机应该触发更新：
- 更换了包管理器或构建工具
- 添加或移除了重要的依赖库
- 制定了新的编码约定
- 发现 Claude 反复犯同一类错误（说明需要在 `CLAUDE.md` 中补充说明）
- 某个文件或模块变得不能随意修改（增加到注意事项中）

#### 3、用命令式语言

指令越明确，Claude 遵守的概率越高。避免模糊的描述，使用直接的命令：

| ❌ 模糊描述 | ✅ 明确指令 |
| --- | --- |
| 代码应该比较整洁 | 函数不超过 50 行，超过时必须拆分 |
| 尽量写测试 | 每个新增函数都必须有对应的单元测试 |
| 注意安全 | 用户输入必须通过 `sanitize()` 函数处理后才能传入数据库查询 |
| legacy 目录不太重要 | 禁止修改 `legacy/` 目录下的任何文件 |
| 用 pnpm 比较好 | 依赖管理只使用 pnpm，禁止使用 npm 或 yarn |

---

### 完整示例

以下是一个 Node.js + TypeScript 全栈项目的 `CLAUDE.md` 完整示例，可以作为你项目的参考模板：

### 实例

# MyApp — 电商管理后台

基于 Next.js 14 App Router + Prisma + PostgreSQL 的电商管理系统。

## 技术栈

- 前端：Next.js 14（App Router）、TypeScript、Tailwind CSS、shadcn/ui
- 后端：Next.js API Routes、Prisma ORM
- 数据库：PostgreSQL 15
- 认证：NextAuth.js v5
- 包管理：pnpm

## 常用命令

```
bash
pnpm dev # 启动开发服务器（端口 3000）
pnpm build && pnpm start # 构建并启动生产服务器
pnpm test # 运行所有测试
pnpm test:e2e # 运行端到端测试（需先启动 dev server）
pnpm db:migrate # 执行数据库迁移
pnpm db:studio # 打开 Prisma Studio（数据库可视化工具）
pnpm lint && pnpm typecheck # 代码检查和类型检查
```

## 项目结构

- `src/app/` — App Router 页面和 API 路由
- `src/app/(dashboard)/` — 需要登录的后台页面
- `src/app/api/` — API 路由（RESTful 风格）
- `src/components/` — 可复用组件
- `src/lib/` — 工具函数、数据库客户端、认证配置
- `prisma/schema.prisma` — 数据库 Schema 定义

## 编码规范

- 只使用具名导出（named export），禁止 default export（除 Next.js 页面文件外）
- 服务端组件默认 async，客户端组件在文件顶部加 `"use client"`
- API 路由统一返回格式：成功 `{ data: T }`，失败 `{ error: string, code: string }`
- 数据库查询封装在 `src/lib/db/` 目录下，不在其他地方直接使用 `prisma` 客户端

## 注意事项

- `prisma/migrations/` 中已有文件 **禁止修改**，数据库变更只能执行 `pnpm db:migrate` 新增迁移
- `.env.local` 包含真实密钥， **禁止读取或输出文件内容**
- `src/lib/auth.ts` 是认证核心文件， **修改前必须告知我**
- 修改 `prisma/schema.prisma` 后必须执行 `pnpm db:migrate` 并提交迁移文件

---

### 常见问题

**Q：CLAUDE.md 里的规则 Claude 不遵守怎么办？**
首先检查规则的表述是否足够明确（见上方"用命令式语言"的建议）。如果表述已经很明确但仍不遵守，可以在具体的对话中再次强调，或者将该规则放到文件靠前的位置——Claude 对文件前半部分的内容注意力更高。

**Q：CLAUDE.md 越长越好吗？**
不是。内容过多有两个副作用：一是占用上下文窗口，压缩 Claude 处理实际任务的空间；二是重要规则淹没在大量文字中，反而不容易被遵守。建议定期审视，删除已经不再适用或低价值的条目。

**Q：子目录下的 CLAUDE.md 什么时候会被加载？**
当 Claude 打开或编辑该子目录下的文件时，该子目录的 `CLAUDE.md` 会自动被加载。你不需要手动告诉 Claude 去读取它，整个过程是自动的。

**Q：可以在 CLAUDE.md 中引用另一个 CLAUDE.md 吗？**
不能直接引用，但你可以通过 `@文件路径` 语法引用任意 Markdown 文件的内容。如果有跨模块共享的规范，建议提取到独立的文档文件中，再分别在各 `CLAUDE.md` 中用 `@` 引用。

---

## Claude Code 上下文管理

上下文管理是高效使用 Claude Code 的核心技能。

官方文档明确指出： **上下文窗口是 Claude Code 最重要的资源**，随着上下文填满，模型性能会逐渐下降（称为上下文衰退，context rot）——这是使用 Claude Code 时需要主动对抗的核心问题。

本章深入讲解如何通过 CLAUDE.md 指令文件、自动记忆系统、上下文窗口优化命令等手段，让 Claude 在长会话和跨会话中保持高效状态。

---

### 上下文窗口基础

#### 1、上下文窗口包含什么

Claude Code 的上下文窗口大小为 **1,000,000 tokens（100 万 token）**。它容纳了 Claude 在当前会话中"看到"的一切内容：

| 内容来源 | 加载时机 | Token 占用 |
| --- | --- | --- |
| 系统提示词（Claude Code 行为规范） | 每次会话启动 | 固定占用 |
| CLAUDE.md 文件 | 启动时完整加载 | 取决于文件大小 |
| 自动记忆（Auto memory） | 启动时加载（前 200 行或 25KB） | 有上限 |
| 对话历史 | 累积增长 | 随会话持续增加 |
| 工具结果（文件读取、命令输出等） | 每次工具调用后追加 | 日志/大文件消耗极快 |
| MCP 工具名称、Skills 描述等 | 启动时加载 | 中等 |

#### 2、上下文衰退（Context rot）

官方明确指出：随着上下文填满， **LLM 性能会下降**，因为注意力被分散到更多 token 上，旧的、不相关的内容开始干扰当前任务。具体症状包括：
- Claude 开始前后矛盾，忘记之前达成的决策
- 响应变得模糊、笼统，细节减少
- 对同一问题反复询问已经回答过的内容
- 在同一个会话里对同一个问题纠正了两次以上

出现以上症状说明上下文已经"污染"，此时应主动使用 `/compact` 或 `/clear` 处理，而不是继续在同一会话中纠正。

使用 `/context` 命令可以随时查看当前上下文的详细用量分析：

~~~
/context
~~~

它会按分类（系统提示、CLAUDE.md、记忆文件、会话历史等）列出 token 占用，并给出优化建议。建议在开始重要任务前先检查一次。

---

### CLAUDE.md 指令文件

CLAUDE.md 是 Claude Code 中记录静态指令和项目规范的核心文件。每次会话启动时，Claude 会自动将其加载到上下文窗口中。

#### 1、存放位置与作用范围

CLAUDE.md 支持多个位置， **更具体的位置优先级更高**：

| 范围 | 文件路径 | 适用场景 | 是否提交 git |
| --- | --- | --- | --- |
| **组织级** | macOS: `/Library/Application Support/ClaudeCode/CLAUDE.md`<br>Linux/WSL: `/etc/claude-code/CLAUDE.md` | 公司编码标准、合规要求 | 由 IT 统一管理 |
| **项目级** | `./CLAUDE.md` 或 `./.claude/CLAUDE.md` | 项目架构、编码规范、工作流 | 是，与团队共享 |
| **用户级** | `~/.claude/CLAUDE.md` | 个人编码偏好，适用所有项目 | 否，个人私有 |
| **本地私有** | `./CLAUDE.local.md`（加入 .gitignore） | 个人的项目特定配置，不提交 git | 否，加入 .gitignore |

> `CLAUDE.local.md` 是个人私有配置的官方推荐方式：沙箱 URL、本地测试账号、个人调试习惯等不应共享的内容，应放在这里而不是 `CLAUDE.md` 中。运行 `/init` 时选择"个人"选项，它会自动将该文件加入 `.gitignore`。

#### 2、创建 CLAUDE.md

执行以下命令，Claude 会自动分析项目并生成初始 CLAUDE.md：

~~~
/init
~~~

如果已有 CLAUDE.md，`/init` 会建议改进而不是直接覆盖。生成后，手动补充 Claude 无法自动发现的内容（如禁止修改的文件、特殊约束等）。

也可以在会话中用 `#` 快捷键快速向 CLAUDE.md 追加一条记忆：

~~~

# 所有 API 错误响应必须使用 { code, message } 格式

~~~

按下 `#` 后输入内容回车，Claude 会将其写入对应层级的 CLAUDE.md，作为持久指令保存。

#### 3、CLAUDE.md 内容要求

官方要求每个 CLAUDE.md 文件 **控制在 200 行以内**。文件越大，消耗的启动 token 越多，Claude 的遵守率反而越低。

**应该写入：**
- 构建、测试、部署的常用命令
- 代码规范：命名约定、缩进、禁止使用的模式
- 关键约束："始终使用 pnpm"、"禁止修改 migrations/ 目录"
- 项目架构说明：主要目录的用途

**不应该写入：**
- 多步骤操作流程（应放到 Skills 文件中）
- 只与代码库某一部分相关的规则（应使用 `.claude/rules/` 按路径限定）
- Claude 通过读取代码就能自己发现的信息

### 实例

# CLAUDE.md 示例（精简具体，每个文件控制在 200 行以内）

## 常用命令

- 包管理：始终使用 pnpm，不要使用 npm 或 yarn
- 运行测试：pnpm test:watch
- 构建：pnpm build

## 代码规范

- TypeScript strict 模式，不允许 any
- 组件文件使用 PascalCase 命名（如 UserProfile.tsx）
- 数据库时间戳统一使用 UTC 格式

## 约束事项

- 禁止直接修改 prisma/migrations/ 目录下的已有文件
- .env.local 包含真实密钥，禁止读取或输出文件内容
- API 接口统一在 src/api/ 目录下管理，不在其他地方直接发请求

#### 4、按路径限定规则（.claude/rules/）

对于较大的项目，可以用 `.claude/rules/` 目录将指令拆分为按文件路径生效的细粒度规则，避免所有规则都堆在根 CLAUDE.md 中消耗启动 token：

~~~
.claude/
└── rules/
    ├── api.md         # 只对 src/api/**/*.ts 文件生效
    ├── frontend.md    # 只对 src/components/**/*.tsx 文件生效
    └── testing.md     # 只对 *.test.ts 文件生效
~~~

路径限定规则只在 Claude 读取对应目录下的文件时才会加载，不在启动时全量占用上下文。

---

### 自动记忆（Auto memory）

自动记忆是 Claude Code v2.1.59 及以上版本内置的跨会话记忆系统， **默认开启**，无需任何配置。它让 Claude 在工作过程中自主记录发现的构建命令、调试经验、代码风格偏好等信息。

#### 1、工作机制

- Claude 不会每次会话都写记忆，它会判断信息是否值得保留（在未来对话中是否有用）
- 记忆按 git 仓库路径隔离，存储于 `~/.claude/projects/<项目路径>/memory/`
- 同一仓库的所有工作树共享一份自动记忆
- 写入记忆时，终端会显示 "Writing memory"；下次检索时显示 "Recalled memory"

#### 2、查看与编辑记忆（/memory）

使用 `/memory` 命令查看和编辑所有记忆内容，包括各级 CLAUDE.md 和自动记忆：

~~~
/memory
~~~

在编辑器中可以删除不准确的条目，或使用 Auto memory 开关禁用自动记忆功能。也可以通过环境变量临时禁用：

~~~
CLAUDE_CODE_DISABLE_AUTO_MEMORY=1 claude
~~~

#### 3、主动让 Claude 记住内容

对话中可以直接让 Claude 写入记忆：

~~~
记住：我们的 API 测试需要本地运行一个 Redis 实例
~~~

~~~
以后请记住：处理错误时，统一返回 { code, message } 结构
~~~

如果想升级为强制规范而非只是偏好记忆，可以说："把这条规则写进 CLAUDE.md"，Claude 会将其写入对应的 CLAUDE.md 文件。

---

### 上下文窗口优化命令

#### 1、自动压缩（Auto-compaction）

Claude Code 在 **上下文接近限制时会自动触发压缩**（约在 75% 用量左右），将对话历史总结为摘要后继续工作。但官方建议不要等待自动压缩，应 **主动在 60~70% 时手动触发**，避免在关键任务中间被打断，且主动压缩可以通过指令控制保留哪些内容。

#### 2、压缩上下文（/compact）

手动压缩对话历史，将长会话总结为精简摘要继续工作：

~~~
/compact
~~~

`/compact` 支持传入自定义指令，控制压缩时重点保留哪些内容：

~~~
/compact Focus on the API changes
~~~

~~~
/compact 保留认证流程的架构决策，丢弃调试过程中的无效尝试
~~~

> 压缩是有损操作。压缩时 Claude 处于上下文最满、性能最弱的状态，如果不给指令，它可能丢掉你认为重要的内容。 **在重大决策后立即主动压缩，并用指令说明保留重点**，比等待自动压缩效果好得多。

#### 3、清除上下文（/clear）

切换到全新任务时清除所有对话历史：

~~~
/clear
~~~

清除后 CLAUDE.md 和自动记忆不受影响，仍会在新会话中加载。官方建议： **如果在同一会话里对同一问题纠正了两次以上，不如直接 /clear，带着学到的约束重新开始一个更精准的提示词**——干净的会话加上更好的提示，几乎总是好过在污染的上下文中继续纠正。

#### 4、回退到检查点（/rewind）

Claude Code 在每次修改文件前会自动创建检查点。双击 `Esc` 或执行 `/rewind` 可打开回退菜单，选择要恢复的检查点：

~~~
/rewind
~~~

~~~
（或双击 Esc 键）
~~~

在回退菜单中，你可以选择以下操作：

| 操作 | 效果 | 适用场景 |
| --- | --- | --- |
| **恢复对话 + 代码** | 回退对话历史和文件改动，完全还原 | Claude 走偏了，改动有问题，全部撤销 |
| **仅恢复对话** | 回退对话历史，保留文件改动 | 想重试不同思路，但代码改动可以保留 |
| **仅恢复代码** | 还原文件到检查点状态，保留对话 | 代码改坏了，但对话中的分析有参考价值 |
| **从此处摘要（Summarize from here）** | 将该检查点之后的对话压缩为摘要，保留之前的完整历史 | 只想清理后半段冗余对话，保留前期的完整上下文 |

> 检查点跨会话持久保存——关闭终端后仍然可以回退到之前的检查点。这不是 git 的替代品，但在探索性实验中比手动 git stash 更方便。

#### 5、快速提问不污染上下文（/btw）

如果需要快速查询一个小细节，但不想让这次问答进入对话历史（消耗上下文），使用 `/btw`：

~~~
/btw 这个项目的 TypeScript 版本是多少？
~~~

答案会以浮层方式展示，问题和答案都不会进入对话历史，适合查阅配置、版本号等不需要保留的一次性信息。

---

### 会话管理命令

| 命令 | 功能 | 使用场景 |
| --- | --- | --- |
| `/context` | 查看上下文各部分的详细 token 用量分析 | 排查哪部分消耗了过多 token |
| `/compact [指令]` | 压缩对话历史为摘要，可指定保留重点 | 上下文 60~70% 时主动压缩 |
| `/clear` | 清除所有对话历史，保留 CLAUDE.md 和记忆 | 切换到新任务，或上下文已严重污染 |
| `/rewind`（或双击 Esc） | 打开检查点菜单，可选择恢复对话/代码/两者，或从某处摘要 | Claude 走偏了，需要回退后重试 |
| `/memory` | 查看和编辑 CLAUDE.md 及自动记忆内容 | 检查记忆是否准确，删除过时条目 |
| `/btw` | 快速提问，答案不进入对话历史 | 查阅小细节，不想污染上下文 |
| `/init` | 分析项目并生成或改进 CLAUDE.md | 首次在新项目使用，或项目有重大变更 |
| `#`（快捷键） | 快速向 CLAUDE.md 添加一条持久指令 | 随时记录规范或约定 |
| `claude --continue` | 继续最近一次会话 | 重新打开终端后接着工作 |
| `claude --resume` | 从历史列表中选择一次会话继续 | 恢复几天前的某个特定任务 |

---

### 常见问题与解决方案

**问题一：Claude 没有遵守 CLAUDE.md 中的规则**
- 检查 CLAUDE.md 是否过大（超过 200 行时遵守率下降）
- 将模糊表述改为具体指令：不要"写整洁的代码"，要"使用 2 个空格缩进"
- 用 `/memory` 检查是否有冲突的自动记忆条目
- 考虑用 `.claude/rules/` 将规则按文件路径拆分，减少每次加载量

**问题二：Claude 开始前后矛盾，"忘记"之前的决策**
- 这是上下文衰退的典型症状，不要继续在同一会话纠正
- 运行 `/compact 保留关键决策`，或直接 `/clear` 后带着总结重新开始
- 重要结论用 `#` 快捷键写入 CLAUDE.md，确保下次会话仍然有效

**问题三：/compact 后关键信息丢失**
- 压缩时传入具体指令：`/compact 保留认证流程的架构决策和已确认的 API 格式`
- 在压缩前将最重要的结论写入 CLAUDE.md
- 调试失败的尝试不值得保留，可以更激进地压缩或 /clear

**问题四：关闭终端后如何继续上次工作**
- 使用 `claude --continue` 继续最近一次会话
- 使用 `claude --resume` 从历史列表选择特定会话
- 用 `/rename` 给重要会话起一个描述性名称（如 "oauth-migration"），方便以后找到

**问题五：某个大任务会产生大量工具输出，怎么避免上下文爆满**
- 使用子代理（subagent）委托执行：主对话只收到摘要，中间输出留在子代理独立上下文中
- 例如：`使用子代理分析所有日志文件，找出性能瓶颈，只把结论告诉我`

---

## Claude Code 记忆系统（Memory）

> 每次 Claude Code 会话结束，上下文就会清空。
> 有时候我们每次都要重新告诉 Claude "用 pnpm 而不是 npm"、"我们的缩进是 2 个空格"？ **记忆系统（Memory）** 正是为此而生。

---

### 什么是 Claude Code 的记忆系统？

Claude Code 没有跨会话的自动记忆——每个新会话都从一个全新的上下文窗口开始。

记忆系统通过两种互补机制，让知识能够 **跨会话持久保存**，并在每次对话开始时自动加载：

| 机制 | 谁来写 | 适合什么 |
| --- | --- | --- |
| **CLAUDE.md 文件** | 你（开发者）手动编写 | 项目规范、团队约定、构建命令等 |
| **Auto Memory（自动记忆）** | Claude 自动写入 | 从你的纠正和偏好中积累的经验 |

两种机制在每次会话开始时都会被加载到上下文中。Claude 将它们作为 **参考上下文**，而非强制配置——指令越具体、越简洁，Claude 遵循得越一致。

---

### 记忆文件的层级结构

Claude Code 采用 **四层记忆层级**，优先级从高到低：

~~~
1. 企业级配置（Enterprise policy）    ← 最高优先级，只读
2. 用户级 CLAUDE.md                   ← ~/.claude/CLAUDE.md，对所有项目生效
3. 项目级 CLAUDE.md                   ← 项目根目录，随 Git 提交共享给团队
4. 子目录级 CLAUDE.md                 ← src/、api/、tests/ 等子目录，按上下文加载
~~~

**最具体的规则优先**：子目录的 CLAUDE.md 会覆盖上层的同类规则。

---

### CLAUDE.md 文件详解

#### 什么是 CLAUDE.md？

`CLAUDE.md` 是一个放在项目根目录（或子目录）的 Markdown 文件。Claude Code 在每次新会话启动时，会 **自动将其注入系统提示词**。它是你可以配置的长期记忆。

#### 创建 CLAUDE.md

**方式一：使用 `/init` 命令自动生成**

~~~

# 在 Claude Code 会话中执行

/init
~~~

Claude 会分析你的目录结构，自动生成一份针对你的技术栈的 CLAUDE.md 骨架。例如，在一个 Node.js 项目中运行 `/init`，Claude 会自动检测框架、测试工具、构建命令等，30 秒内生成一份 80% 完整度的初始文件。

**方式二：手动创建**

~~~
touch CLAUDE.md
~~~

#### 推荐的 CLAUDE.md 结构

~~~

# 项目约定

## 技术栈

- 前端：Next.js 15、TypeScript 5.7、Tailwind CSS 4
- 后端：Node.js 22、Prisma 6
- 测试：Vitest 3.2

## 代码规范

- 始终使用函数式 React 组件
- 文件名使用 kebab-case
- 测试文件与源码放在同一目录

## 常用命令

- 构建：`pnpm build`
- 测试：`pnpm test`
- 启动开发服务器：`pnpm dev`

## API 约定

- 所有 API 路由以 `/api/v1/` 开头
- 错误响应格式：`{ error: string, code: number }`
~~~

#### 写好 CLAUDE.md 的黄金法则

**✅ 要这样写：**
- 使用祈使句和简短列表，而非叙述性段落
- 包含具体的版本号和命令
- 加入代码示例（5 行示例胜过 50 字说明）
- 控制在 **200 行以内**（超过部分不会在会话开始时加载）

**❌ 避免这样写：**
- 模糊指令如"遵循最佳实践"或"写干净的代码"
- 过多通用规则（只放这个项目独有的约定）
- 过时的信息（建议每月审查一次）

#### 子目录 CLAUDE.md

~~~
my-project/
├── CLAUDE.md              # 全局项目规范
├── src/
│   └── CLAUDE.md          # 仅在处理 src/ 文件时加载
├── api/
│   └── CLAUDE.md          # API 特定约定
└── tests/
    └── CLAUDE.md          # 测试特定规则
~~~

Claude Code 只在处理对应目录的文件时加载子目录的 CLAUDE.md，节省 token 的同时提供更精准的上下文。

---

### Auto Memory（自动记忆）详解

#### 它是如何工作的？

Auto Memory 让 Claude 能够跨会话 **自我积累知识**，无需你手动编写任何内容。Claude 会在工作过程中自动保存笔记，包括：
- 构建命令和调试技巧
- 架构决策笔记
- 代码风格偏好
- 工作流习惯

Claude 并不会每次都保存内容，它会判断哪些信息在 **未来会话中有用**才写入。

#### 自动记忆的文件结构

~~~
~/.claude/projects/<project>/memory/
├── MEMORY.md          # 简洁的索引文件，每次会话开始时加载（前 200 行）
├── debugging.md       # 调试模式的详细笔记
├── api-conventions.md # API 设计决策
└── ...                # Claude 创建的其他主题文件
~~~

`MEMORY.md` 是整个记忆目录的索引，Claude 通过它来追踪各文件中存储的内容。

#### 触发自动记忆

当你告诉 Claude 某些事情时，它会自动保存到记忆中：

~~~
你：始终使用 pnpm，不要用 npm
你：记住 API 测试需要本地运行 Redis 实例
你：我们的日期格式统一用 ISO 8601
~~~

**想保存到 CLAUDE.md 而不是 Auto Memory？** 明确说明：

~~~
你：把这条加到 CLAUDE.md
~~~

#### 开启 / 关闭 Auto Memory

**方式一：通过 `/memory` 命令切换**（见下节）

**方式二：在项目设置中配置**

~~~
// .claude/settings.json
{
  "autoMemoryEnabled": false
}
~~~

**方式三：环境变量**

~~~
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=1
~~~

> **注意：** Auto Memory 是 **本地机器级别**的，同一 Git 仓库的所有 worktree 和子目录共享一个记忆目录，但 **不会跨机器或云环境同步**。

---

### `/memory` 命令使用指南

`/memory` 是管理记忆系统的核心命令。

#### `/memory` 命令的功能

在 Claude Code 会话中输入 `/memory`，可以：
- **查看**当前会话加载的所有 CLAUDE.md 和规则文件列表
- **切换** Auto Memory 的开启/关闭状态
- **打开** Auto Memory 文件夹链接
- **选择任意文件**在编辑器中打开编辑

#### `#` 快捷键——快速添加记忆

这是一个隐藏的效率神器：

~~~

# 始终在函数参数中使用具名参数（named parameters）

~~~

按下 `#` 键，输入你想记住的内容，按回车——Claude Code 会自动将其写入对应的 CLAUDE.md 文件。非常适合：
- 记录项目约定
- 保存常用 Bash 命令
- 记下代码风格细节

---

### 常见问题与调试

#### Claude 忽略了 CLAUDE.md 的指令？

1. 运行 `/memory` 确认 CLAUDE.md 文件已被加载
2. 确保 Claude Code 在 CLAUDE.md 所在目录（或其子目录）中运行
3. 检查指令是否足够具体——"遵循最佳实践"太模糊，"使用具名导入（tree-shaking 兼容性）"则更有效
4. 检查文件是否超过 200 行（超出部分不加载）

#### CLAUDE.md 是上下文，不是强制执行

Claude **读取**并尽力遵循 CLAUDE.md，但没有严格合规的保证，尤其是指令模糊或相互冲突时。将其理解为"给 Claude 的工作指南"而非"不可违反的规则"。

---

### 实际工作流示例

#### 场景一：初始化新项目

~~~

# 1. 在项目根目录启动 Claude Code

cd my-project
claude

# 2. 生成记忆文件骨架

/init

# 3. 审查并完善生成的 CLAUDE.md

/memory  # 打开文件编辑

# 4. 开始工作，Claude 会自动积累记忆

~~~

#### 场景二：记录调试发现

~~~
你：记住，运行集成测试前必须先启动 Docker Compose
Claude：好的，我已将这条记录到 Auto Memory 中。
~~~

下次会话，Claude 会自动知道这个依赖关系。

#### 场景三：团队协作

将项目根目录的 `CLAUDE.md` 提交到 Git 仓库——团队中每个人的 Claude 助手都会读取相同的规范，实现一致的 AI 辅助体验。

~~~
git add CLAUDE.md
git commit -m "feat: add Claude Code memory configuration"
~~~

---

### 最佳实践总结

| 场景 | 推荐做法 |
| --- | --- |
| 团队共享规范 | 项目根目录的 CLAUDE.md，提交到 Git |
| 个人偏好 | `~/.claude/CLAUDE.md`（用户级） |
| 模块特定规则 | 子目录 CLAUDE.md |
| 让 Claude 自学 | 开启 Auto Memory，口头告知偏好 |
| 临时告知上下文 | `[@docs](https://github.com/docs)/filename.md` 按需引用，不要塞进 CLAUDE.md |
| 任务跟踪 | 在 Markdown 文件中使用 `[ ]` 复选框 |

---

### 参考资源

- [官方文档：Memory System](https://code.claude.com/docs/en/memory)
- [Claude Code 概览](https://docs.claude.com/en/docs/claude-code/overview)
- [Prompt Engineering 指南](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)

---

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

**会话期间切换：**
按 `Shift+Tab` 循环切换 `default` → `acceptEdits` → `plan` → `auto`

**启动时指定模式：**

```bash
claude --permission-mode plan
claude --permission-mode bypassPermissions
```

**设置为默认模式（settings.json）：**

```json
{
  "permissions": {
    "defaultMode": "acceptEdits"
  }
}
```

---

### 权限规则语法

#### 1、基本格式

权限规则遵循格式 `Tool` 或 `Tool(specifier)`：

### 实例

```json
{
  "permissions": {
    "allow": ["Bash", "WebFetch", "Read"],
    "deny": ["Edit"]
  }
}
```

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

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run build)",
      "Read(./.env)",
      "WebFetch(domain:example.com)"
    ]
  }
}
```

#### 4、通配符模式

Bash 规则支持带有 `*` 的 glob 模式，通配符可以出现在命令中的任何位置：

### 实例

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git commit *)",
      "Bash(git * main)",
      "Bash(* --version)",
      "Bash(* --help *)"
    ],
    "deny": [
      "Bash(git push *)"
    ]
  }
}
```

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

```json
{
  "permissions": {
    "allow": [
      "Edit(/docs/**)",
      "Read(~/.zshrc)",
      "Edit(//tmp/scratch.txt)",
      "Read(src/**)"
    ],
    "deny": [
      "Read(*.env)",
      "Edit(//etc/**)"
    ]
  }
}
```

> **注意： **Read 和 Edit deny 规则不适用于 Bash 子进程中的 `cat .env`。要获得 OS 级别强制执行，需启用沙箱。

#### 3、WebFetch（网络请求）

### 实例

```json
{
  "permissions": {
    "allow": [
      "WebFetch(domain:github.com)",
      "WebFetch(domain:api.example.com)"
    ],
    "deny": [
      "WebFetch(domain:untrusted.com)"
    ]
  }
}
```

#### 4、MCP（模型上下文协议）

### 实例

```json
{
  "permissions": {
    "allow": [
      "mcp__puppeteer",
      "mcp__puppeteer__*"
    ],
    "deny": [
      "mcp__puppeteer__puppeteer_navigate"
    ]
  }
}
```

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

---
