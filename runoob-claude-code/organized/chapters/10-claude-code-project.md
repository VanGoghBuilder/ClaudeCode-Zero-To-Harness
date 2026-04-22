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

**典型内容： **

~~~

# My Project

一句话描述项目用途。

## Tech Stack

- Backend: Python / FastAPI
- Frontend: React + TypeScript
- Database: PostgreSQL

## Common Commands

\`npm run dev\`    # 启动开发服务器
\`pytest tests/\`  # 运行测试
\`npm run build\`  # 构建生产版本

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

> **💡 提示： ** Claude 会自动递归读取父目录中的 CLAUDE.md。在 monorepo 中，子包内可再放一个 CLAUDE.md，Claude 会将两层指令合并理解。

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

> **💡 参数传递： ** 命令文件中可使用 `$ARGUMENTS` 占位符接收调用时传入的参数。

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

> **⚡ Skills vs Commands 的区别： **
> - **Commands** 需要用户主动输入斜杠命令触发，是"工具箱"
> - **Skills** 由 Claude 根据上下文自动判断是否调用，是"智能本能"

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

