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

**✅ 要这样写： **
- 使用祈使句和简短列表，而非叙述性段落
- 包含具体的版本号和命令
- 加入代码示例（5 行示例胜过 50 字说明）
- 控制在 **200 行以内 **（超过部分不会在会话开始时加载）**❌ 避免这样写： **
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

**方式一：通过 `/memory` 命令切换 **（见下节）**方式二：在项目设置中配置**

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

blockquote>
**注意： ** Auto Memory 是 **本地机器级别**的，同一 Git 仓库的所有 worktree 和子目录共享一个记忆目录，但 **不会跨机器或云环境同步**。

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

