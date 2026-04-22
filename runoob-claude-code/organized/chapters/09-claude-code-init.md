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
- **保持精简 **：内容要简洁且普遍适用。采用"渐进式披露"原则——不要把所有你想让 Claude 知道的内容都塞进去，而是告诉它**如何查找重要信息**，让它按需获取，避免撑爆上下文窗口。
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

