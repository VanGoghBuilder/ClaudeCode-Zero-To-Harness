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

