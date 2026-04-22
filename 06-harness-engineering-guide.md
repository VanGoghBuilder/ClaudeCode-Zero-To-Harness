# Claude Code 的 Harness Engineering 总纲

> 目标：把前面各卷分散讲解的能力，重新组织成一套能在真实项目中落地的 Claude Code Harness Engineering 框架。

如果你还没有建立从日常使用过渡到 harness 的整体直觉，建议先读 [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)。

Claude Code 的难点，从来不只是“会不会生成代码”，而是：

- 它是否知道项目上下文
- 它是否遵守边界和权限
- 它是否能用稳定的方式调用工具
- 它是否在完成前经过验证

前面的 `guide/00-05` 已经把这些能力分别拆开讲过了。这一卷做的事，不是重复介绍功能，而是把前面主线里分散的能力重新拼成一套控制系统。

换句话说，这一卷专注回答三个问题：

1. 什么是 Claude Code 的 Harness Engineering
2. 前面各卷里的能力如何映射到 harness
3. 落地时应该按什么顺序搭建最小可行控制系统

---

## 一句话定义

Claude Code 的 Harness Engineering，就是用版本化的上下文、权限、工具、验证回路和角色分工，稳定地驾驭 Claude Code 的行为。

重点不是从零造一套新的 agent framework，而是在 Claude Code 已有能力之上，补齐你项目自己的控制系统。

---

## Claude Code 自带的 harness 原语

Claude Code 已经给出了足够多的原语：

- `CLAUDE.md`
- `.claude/settings.json`
- hooks
- MCP
- subagents
- slash commands
- scripts / checks

真正需要工程化的地方，不是“再发明一套 AI 平台”，而是决定：

- 哪些规则写进项目上下文
- 哪些边界用权限机制守住
- 哪些动作沉淀成脚本或命令
- 哪些任务拆给不同角色
- 什么才算真正完成

---

## 五层结构

最适合把 `guide/` 内容重新串起来的方式，是用五层结构理解 harness：

| 层 | 目标 | Claude Code 对应物 |
| --- | --- | --- |
| Memory | 让它知道“我们这里怎么做事” | `CLAUDE.md` |
| Permissions | 让它知道“什么能做，什么不能做” | `.claude/settings.json` |
| Tools | 让它能安全地接触项目内外的工具 | scripts、commands、MCP |
| Delegation | 让复杂任务分角色、分上下文处理 | subagents |
| Back-pressure | 让“完成”必须经过验证 | hooks + lint/test/typecheck/review |

这五层不是并列堆料，而是一条从“能工作”到“稳定工作”的升级路径。

---

## `guide/` 主线如何映射到这五层

这一部分是整条 `guide/` 主线和 harness 方法论真正接上的核心。

### 1. `00`：从 Zero 到 Harness，先建立过桥直觉

- 对应读物：[Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)
- 作用：先让读者理解为什么 Claude Code 不是聊天框，为什么真实项目一定会走向 harness

这一步不讲细节配置，而是建立“先会用，再固化；同一个问题出现两次，就写进系统”的直觉。

### 2. `01`：概览与安装，先建立能力边界

- 对应读物：[概览与安装](./01-overview-and-setup.md)
- 作用：建立 Claude Code 的能力边界、适用人群和首次使用直觉

这一卷解决的是“它是什么、能做什么、跟网页版 Claude 有什么不同”，属于 harness 前的认知准备。

### 3. `02`：项目初始化与上下文，进入 Memory 基础层

- 对应读物：[项目初始化与上下文](./02-project-and-context.md)
- 重点对应：Memory / 部分 Permissions

这一卷最接近 harness 的起点，因为它直接进入：

- `/init`
- `CLAUDE.md`
- 项目结构
- 上下文管理
- 权限配置

如果只从工程化角度看，`02` 是整个 `guide/` 里最像“第一层 harness 搭建手册”的一卷。

### 4. `03`：日常使用与交互，形成稳定工作流

- 对应读物：[日常使用与交互](./03-daily-usage.md)
- 重点对应：操作节奏与协作边界

Ask / Plan / Edit 本身不是 harness 配置项，但它们决定了你如何与 Claude Code 协作。

如果团队连“什么时候先 Ask、什么时候先 Plan、什么时候再 Edit”都没有稳定习惯，那么后面的 hooks、subagents、review 流程往往也会飘。

所以 `03` 更像 harness 的工作流底座。

### 5. `04`：扩展与自动化，进入 Tools / Delegation / Back-pressure

- 对应读物：[扩展、自动化与高级能力](./04-extension-and-automation.md)
- 重点对应：Tools / Delegation / Back-pressure

这一卷包含：

- MCP
- subagents
- hooks
- skills
- 插件与自动化能力

如果说 `02` 是“先把 Claude 放进项目里”，那 `04` 就是“让 Claude 在项目里形成可扩展的控制系统”。

但这并不意味着这些能力应该一次全上。它们更适合作为第二阶段、第三阶段的扩展，而不是新手第一天的起点。

### 6. `05`：参考与工程集成，把 harness 放进真实环境

- 对应读物：[参考、环境与工程集成](./05-reference-and-ops.md)
- 重点对应：Tools / 环境 / 集成

这一卷更偏参考与工程集成，包括：

- CLI 用法
- 环境变量
- Git 工作流
- GitHub Actions

它不负责建立 harness 概念，但负责把 harness 放进真实仓库、真实终端、真实 CI 环境里运转。

---

## 最小可行目录

一个与 `guide/` 主线兼容的最小可行目录，大致如下：

```text
your-project/
├── CLAUDE.md
├── .claude/
│   ├── settings.json
│   ├── agents/
│   └── commands/
├── docs/
├── scripts/ai/
└── ...
```

这不是为了追求目录完整，而是为了保证五层结构至少有最基本的落点。

---

## 最小起步方案

如果你已经读过 `00` 和 `01-03`，真正开始落地时，建议先做这 5 件事：

1. 写一个 40 行以内的 `CLAUDE.md`
2. 写一个统一的 `scripts/ai/run-checks.sh`
3. 用 `.claude/settings.json` 明确敏感区和高风险操作
4. 用 `Stop` hook 把“完成”改成“通过检查后完成”
5. 建一个只负责 review 的 `code-reviewer` subagent

这 5 件事分别对应：

- Memory
- Tools
- Permissions
- Back-pressure
- Delegation

也就是说，当这 5 件事齐了，你就已经有了一个最小可行 harness，而不是零散技巧的集合。

---

## 一个关键原则

同一个问题出现两次，就不要再靠人提醒，应该把它写进 harness。

比如：

- Claude 总忘记先跑检查
- Claude 经常碰敏感目录
- Claude 在复杂任务里容易漏 review
- Claude 总是误解某个模块边界

这些问题第一次可以口头修正，第二次就应该考虑写进：

- `CLAUDE.md`
- settings permissions
- checks script
- hook
- subagent
- command

这是把“会用 Claude Code”升级为“能稳定驾驭 Claude Code”的分水岭。

---

## 推荐实施顺序

为了和 `guide/` 主线保持一致，推荐用下面这个顺序来落地：

### Phase 0：先建立使用直觉

先读：

- [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)
- [概览与安装](./01-overview-and-setup.md)
- [日常使用与交互](./03-daily-usage.md)

目标不是配置系统，而是先建立正确使用节奏。

### Phase 1：先把 Memory 与基础命令放稳

重点看：

- [项目初始化与上下文](./02-project-and-context.md)

先完成：

- 建 `CLAUDE.md`
- 补 `docs/architecture.md` 或类似项目文档
- 明确 build / test / lint / typecheck 命令

### Phase 2：建立 Back-pressure 与基础 Permissions

重点回看：

- [扩展、自动化与高级能力](./04-extension-and-automation.md)

先完成：

- 加 `run-checks.sh`
- 用 `Stop` hook 卡最终验证
- 把敏感区放进 permissions deny / ask

### Phase 3：增加角色分工与高频流程

继续参考：

- [扩展、自动化与高级能力](./04-extension-and-automation.md)
- [参考、环境与工程集成](./05-reference-and-ops.md)

再完成：

- 增加 `code-reviewer`、`test-runner` 等子代理
- 把高频流程做成 slash commands 或固定脚本

### Phase 4：按需接入外部系统

最后再考虑：

- MCP
- issue / review / CI 链路
- GitHub Actions 或其他自动化集成

只有当你的项目真的需要 Claude 连接仓库外部世界时，这一层才值得引入。

---

## 常见误区

### 把 `CLAUDE.md` 当作万能药

`CLAUDE.md` 很重要，但它只负责项目级记忆，不负责替代测试、权限和自动化验证。

### 把 harness 等同于“写很多提示词”

harness 的核心不是提示词堆料，而是把规则写成版本化制品，把完成写成可验证状态。

### 工具越多越强

MCP、hooks、skills、subagents 都有价值，但它们不应该在第一天全部出现。

### subagents 越多越高级

真正重要的不是代理数量，而是职责是否清晰、结果是否稳定。

### 只做生成，不做垃圾回收

如果没有 checks、review、hooks 和收尾动作，再会生成也只是更快地产生不稳定结果。

---

## 如何把这篇文章和前面几卷一起使用

如果你把这篇文章当作 `guide/` 的收束总纲，推荐这样使用：

- **想建立整体地图**：先读 [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)，再读当前这篇
- **想开始真正落地**：重点读 [项目初始化与上下文](./02-project-and-context.md) 和 [扩展、自动化与高级能力](./04-extension-and-automation.md)
- **想补齐日常协作习惯**：回到 [日常使用与交互](./03-daily-usage.md)
- **想做 CLI / 环境 / CI 集成**：去看 [参考、环境与工程集成](./05-reference-and-ops.md)

换句话说：

- `00` 帮你过桥
- `01-05` 帮你学会每个部件
- `06` 帮你把这些部件重新装配成一套 harness

---

## 最后一句话

Claude Code 的 Harness Engineering，不是把更多能力堆到 Claude 身上，而是把你项目里的规则、边界、工具和验证方式，组织成一套它能够稳定遵守的系统。

而 `guide/` 的价值，正是在于把这套系统拆开讲清楚，再在这里重新组装起来。
