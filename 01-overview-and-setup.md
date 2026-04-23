# 概览与安装

> 阅读提示：如果你想先建立“为什么 Claude Code 会走向 harness”的整体地图，先读 [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md)；如果你已经准备开始把 Claude Code 放进真实项目，读完本章后继续看 [项目初始化与上下文](./02-project-and-context.md)。

## 包含章节

- Claude Code 教程
- Claude Code 简介
- Claude Code 安装与使用
- Claude Code 如何工作
- Claude Code API 配置
- Claude Code 第一次使用

---

## Claude Code 教程

![image](https://www.runoob.com/wp-content/uploads/2026/01/Gc2r0cfW0AAKQw2.jpg)

Claude Code 是 Anthropic 推出的面向开发者的 AI 编程协作工具。

Claude Code 定位不是聊天，而是在本地代码仓库中执行高权限、可上下文感知的工程任务。

Claude Code 与在聊天窗口里写几段代码不同，它理解你的整个项目，能直接读取你的文件、运行测试并根据反馈修改代码。

Claude Code 不是一个代码生成器，而是一个能读项目、懂上下文、遵守约束的 AI 编程搭档。

Claude Code 是 Agent（智能体工具），不是 Chat（聊天工具）。

---

### 谁适合阅读本教程？

- 已学习过至少一门编程语言（如 JavaScript / Python / Java / Go）
- 能看懂基础语法、函数、条件判断和循环
- 写过简单项目或练习代码
- 希望借助 AI 提升开发效率与工程能力
- 独立开发者或需要长期维护项目的工程师
- 技术负责人或团队核心成员

---

### 需要具备的基础能力

- 基础编程能力（能读懂并编写简单代码）
- 基本的代码阅读与理解能力
- 会使用常见开发工具（编辑器、命令行）
- 愿意理解代码，而不是完全依赖 AI

---

### Claude Code vs. 网页版 Claude

| 特性 | Claude 网页版 / App | Claude Code (CLI) |
| --- | --- | --- |
| **交互方式** | 复制粘贴代码到对话框 | 直接在终端通过指令操作 |
| **文件访问** | 手动上传文件 | **自动**读取/搜索整个工程目录 |
| **执行能力** | 仅生成文本 | **执行** Shell 命令、运行测试、创建文件 |
| **上下文感知** | 有限的对话上下文 | 深度感知项目结构和 Git 历史 |

---

### 相关资源

- 官方文档：[https://code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview)
- 中文文档：[https://code.claude.com/docs/zh-CN/overview](https://code.claude.com/docs/zh-CN/overview)
- Github 开源：[https://github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

---

---

## Claude Code 简介

Claude Code 是 Anthropic 推出的面向开发者的 AI 编程协作工具，与在聊天窗口里写几段代码不同，Claude Code 的核心目标是理解你的整个项目，并参与到真实的编码、修改和重构过程中。

Claude Code 不是一个代码生成器，而是一个能读项目、懂上下文、遵守约束的 AI 编程搭档。

简单说: **Claude Code 是 Claude 的命令行版本,专门为编程场景设计**。

它不是网页里的聊天框，而是直接在你的终端(Terminal)里运行,可以:
- **读取你整个项目的代码**
- **理解文件之间的关系**
- **直接修改代码文件**
- **执行你的指令并给出建议**

![image](https://www.runoob.com/wp-content/uploads/2026/01/Claude-Code-Screenshot.png)

从能力角度看，Claude Code 主要具备三点特征：
- **上下文感知**：不仅理解单个文件，而是理解整个项目结构
- **工程化导向**：关注可维护性、规范、测试，而不是一次性代码
- **可定制行为**：通过 Skills（技能包）让 AI 遵守你的规则

打个比方:
- **Claude(网页版)** 就像一个顾问，你把代码截图或复制给他，他给你建议，但你得自己动手改。
- **Claude Code** 就像一个坐在你旁边的搭档，他能看到你的整个项目，帮你改代码，甚至帮你写测试、重构函数。

![image](https://www.runoob.com/wp-content/uploads/2026/01/1757591151865.jpg)

---

### Claude Code 能做什么?

Claude Code 的核心能力可以归纳为这几点:

#### 1、代码理解与解释

我们可以问它:
- 这个函数是干什么的?
- 为什么这里会报错?
- 这段代码的性能瓶颈在哪?

它会结合你的项目上下文给出解释。

#### 2、多文件上下文分析

不同于简单的代码补全工具,Claude Code 能理解:
- 这个函数在哪些地方被调用了
- 这个模块依赖了哪些其他文件
- 整个项目的架构是怎样的

#### 3、工程级代码修改

我们可以说:
- 把所有用 var 的地方改成 let
- 把这个函数拆成三个小函数
- 给所有接口加上错误处理

它会帮你实际修改代码,而不只是给建议。

#### 4、通过 Skills(技能包)扩展能力

Skills 是 Claude Code 最强大的地方——我们可以 **教它你的编码习惯和团队规范**，让它按我们的方式工作。

比如:
- 我们团队要求所有函数都加注释
- API 响应必须符合特定格式
- 测试用例要覆盖边界情况

你可以把这些要求写成 Skills，Claude Code 就会自动遵守。

#### Claude Code **不能**做什么?

Claude Code 不擅长的事情：
- 不能替你做技术决策的最终判断
- 不能保证生成代码 100% 无 Bug
- 不能理解你没有明确说明的业务语义
- 不适合在你完全不理解的情况下全自动接管项目

> 一个成熟的使用心态是：让 Claude Code 提供高质量候选方案，而不是绝对正确答案。

---

### Claude Code 的核心价值

#### Claude Code 的核心理念：协作，而不是替代

Claude Code 并不是在追求把开发者踢出局，它的设计理念非常明确：
- **人负责：**目标、约束、判断、审美
- **AI 负责：**执行、分析、对比、重复劳动

#### 对编程新手: 降低学习门槛

如果你是新手，最痛苦的可能是:
- 看不懂别人的代码
- 不知道为什么报错
- 不知道好代码长什么样

Claude Code 可以:
- **用人话解释代码**:这段代码的意思是先检查用户是否登录，如果没登录就跳转到登录页
- **帮你 Debug**: 这里报错是因为变量名拼错了，应该是 `username` 不是 `usrname`
- **教你写更好的代码**: 这个函数太长了，可以拆成三个小函数，分别负责验证、处理、返回

---

### 对独立开发者: 提升开发效率

如果你是独立开发者，你可能:
- 一个人负责前后端
- 没人帮你 Code Review
- 需要快速试错和迭代

Claude Code 可以:
- **帮你快速理解第三方库的用法**
- **自动生成测试用例**
- **帮你重构混乱的代码**

---

### 对团队: 统一规范,降低协作成本

如果你在团队工作，你可能遇到:
- 每个人代码风格不一样
- 新人上手慢,老是问重复问题
- Code Review 花时间

Claude Code 可以:
- **通过 Skills 统一团队规范**(所有人用同一套规则)
- **帮新人快速理解项目**
- **在提交前自动检查代码质量**

---

### Claude Code 适合哪些人?

我建议以下几类人可以尝试 Claude Code:

#### **编程新手**

- 想学编程但总是卡在看不懂代码
- 需要一个随时解答问题的老师

#### **独立开发者 / 创业者**

- 一个人做项目,需要快速试错
- 想把时间花在核心功能上,不想在琐碎问题上卡住

#### **后端 / 前端 / 全栈工程师**

- 想提升开发效率
- 想学习更好的代码写法
- 需要快速理解复杂项目

#### **技术负责人 / 架构师**

- 想用 AI 辅助团队规范落地
- 想降低新人培养成本

#### **不适合的场景**

- 你完全不想学编程，只想输入需求自动出项目(AI 还做不到)
- 你的工作涉及高度敏感代码，不能使用外部 AI 服务

---

### Claude Code 和其他 AI 编程工具有什么区别?

你可能还听说过 ChatGPT、Cursor、GitHub Copilot，它们和 Claude Code 有什么不同?

#### Claude Code vs ChatGPT

| 维度 | ChatGPT | Claude Code |
| --- | --- | --- |
| **使用方式** | 网页聊天 | 命令行工具 |
| **代码理解** | 需要手动粘贴 | 自动读取整个项目 |
| **文件修改** | 给建议,你手动改 | 可以直接修改文件 |
| **适合场景** | 问问题、学概念 | 真实项目开发 |

#### Claude Code vs Cursor / Copilot

| 维度 | Cursor / Copilot | Claude Code |
| --- | --- | --- |
| **工作方式** | 编辑器内自动补全 | 命令行对话式 |
| **适合场景** | 写代码时实时提示 | 理解、重构、架构级修改 |
| **学习成本** | 低,开箱即用 | 中,需要学习指令 |

**简单总结:**

- **Copilot/Cursor**:边写边补全，像智能输入法
- **Claude Code**:对话式协作，像高级搭档
- **ChatGPT**:通用助手，像顾问

**Claude Code 的优势:**
- 对整个项目的理解能力更强
- 可以通过 Skills 定制行为
- 更适合理解旧代码大规模重构等场景

**Claude Code 的局限:**
- 不如 Copilot 那样无感(需要主动调用)
- 学习曲线稍高

---

## Claude Code 安装与使用

本章只保留 **macOS** 的安装与使用路径，并补充 `cc switch` 管理第三方 API / Token 的做法。

如果你的目标是尽快在 macOS 上把 Claude Code 跑起来，推荐按下面顺序做：

1. 安装 Claude Code CLI
2. 验证安装
3. 配置官方账号或第三方 API
4. 启动第一次会话

---

### 安装 Claude Code CLI

#### 1、前置准备

在安装之前，你需要准备好以下两项：

**① Claude 账号或第三方 API Key**

- 如果你打算直接使用 Claude 官方账号，先访问 [claude.ai](https://claude.ai) 注册或登录
- 如果你打算使用第三方兼容 API，可以先准备好对应平台的 API Key，后面再配置

**② macOS 终端**

- 直接打开系统自带的 `Terminal.app` 即可
- 如果你更偏好现代终端，也可以使用 iTerm2、Ghostty、Kitty、WezTerm 等

#### 2、使用官方脚本安装

官方推荐的安装方式：

~~~
curl -fsSL https://claude.ai/install.sh | bash
~~~

安装完成后，验证是否成功：

~~~
claude --version
~~~

如果终端输出版本号，例如 `1.x.x`，说明安装成功。

#### 3、使用 npm 安装（不推荐）

如果你确实需要通过 npm 安装，先确认 Node.js 可用：

~~~
node --version
~~~

然后执行：

~~~
npm install -g @anthropic-ai/claude-code
claude --version
~~~

#### 4、更新 Claude Code

手动更新：

~~~
claude update
~~~

如果你是通过 Homebrew 安装，也可以使用：

~~~
brew upgrade claude-code
~~~

---

### macOS 常见安装问题

#### 1、提示 `permission denied`

如果是 npm 全局安装权限问题，可以使用：

~~~
sudo npm install -g @anthropic-ai/claude-code
~~~

#### 2、提示 `npm command not found`

- 说明本机还没有安装 Node.js
- 前往 [nodejs.org](https://nodejs.org) 安装后再执行 npm 方案

#### 3、安装速度慢

可以切换 npm 镜像源：

~~~
npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com
~~~

---

### 使用官方 Claude 账号登录

如果你使用的是 Claude 官方账号，在项目目录启动 Claude Code：

~~~
cd /path/to/your/project
claude
~~~

首次启动时会引导你登录。你也可以手动执行：

~~~
/login
~~~

完成授权后，后续一般不需要重复登录。

---

### 如果你更喜欢 VS Code

如果你不喜欢长时间待在终端里，也可以把 Claude Code 放到 VS Code 中使用。

对很多用户来说，`VS Code + Claude Code` 相比纯命令行更顺手，主要是因为：

- **可视化更强**：文件树、标签页、diff 视图、终端都在一个窗口里
- **修改更直观**：Claude 提议改动时，可以直接在编辑器里看并排对比
- **选中上下文更方便**：你可以直接选中代码，让 Claude 只看这一段
- **日常开发更自然**：边看文件边问问题，不用频繁在终端和编辑器之间切换
- **对非终端重度用户更友好**：很多人熟悉 VS Code，但不习惯命令行工作流

CLI 的优势仍然存在，比如更适合远程机器、纯终端环境和脚本化场景；但如果你的目标是“尽快上手并舒服地日常使用”，VS Code 往往更容易接受。

#### 为什么很多人会觉得 VS Code 比命令行更好用

| 维度 | VS Code + Claude Code | 纯命令行 Claude Code |
| --- | --- | --- |
| 代码浏览 | 文件树、标签页、搜索结果一眼可见 | 需要在终端里自己切文件和目录 |
| 改动确认 | 直接看并排 diff，更容易判断要不要接受 | 主要依赖终端里的文本确认 |
| 上下文输入 | 选中代码、`@文件`、拖拽附件都更自然 | 更适合通过命令和路径精确表达 |
| 学习门槛 | 对大多数 IDE 用户更低 | 对熟悉终端的人效率更高 |
| 日常开发体验 | 更像“在编辑器里结对编程” | 更像“在终端里驱动一个代理” |

如果你平时就在 VS Code 里完成大多数工作，那么先用 VS Code 入口，通常比一开始强行切到纯 CLI 更容易坚持下来。

#### 1、在 VS Code 中安装 Claude Code

打开 VS Code，进入扩展市场，搜索 **Claude Code** 并安装。

安装完成后，点击右上角 Claude Code 图标，即可打开 Claude Code 面板。

#### 2、VS Code 里最实用的几个点

- **选中代码即带上下文**：选中一段代码后，Claude 会自动知道你在看哪几行
- **并排修改视图**：Claude 修改文件时，VS Code 会直接展示对比
- **命令菜单更顺手**：输入 `/` 就能打开命令菜单
- **文件与文件夹引用**：直接用 `@文件名` 或 `@目录/` 把上下文带进对话
- **历史对话更容易找回**：在面板顶部即可切换历史会话

#### 3、适合谁优先用 VS Code

以下几类人通常更适合先从 VS Code 入口开始：

- 平时主要就在 VS Code 里写代码
- 不喜欢纯终端交互
- 更依赖可视化 diff 和文件树
- 想先低门槛上手，再慢慢适应 CLI

如果你后面开始频繁做项目级操作、批量改动或远程终端开发，再切回 CLI 也很自然。

---

### 如果出现登录提示

即使你已经配置了第三方 API，在某些 Claude Code 版本里，首次启动时仍然可能出现登录提示页。

这里要区分两件事：

- **账号登录**：Claude 官方的认证流程
- **第三方 API 配置**：你自己提供的兼容 API Key、Base URL 和模型

如果你是打算走第三方 API 路线，建议优先做这些检查：

1. 先确认第三方配置是否真的已经写入当前终端环境
2. 启动后执行 `/status`，检查 `Model`、`Base URL`、`Auth Token` 是否是你期望的值
3. 如果是通过 `cc switch` 管理，确认切换后已经重新打开终端
4. 检查是否把 `ANTHROPIC_AUTH_TOKEN`、`ANTHROPIC_BASE_URL`、`ANTHROPIC_MODEL` 写错了名字

不建议通过修改本地状态文件、伪造认证完成状态等方式“跳过登录校验”。这类方法本质上是在绕过工具的认证流程，后续容易带来兼容性问题，也不利于排查配置错误。

更稳妥的做法是：

- 使用 Claude 官方账号正常完成登录
- 或者把第三方 API 配置彻底校验正确，再用 `/status` 确认当前会话已切到你希望的后端

---

### 使用第三方 API：推荐 `cc switch`

如果你不想直接使用 Claude 官方账号，而是想在 Claude Code 中接第三方兼容 API，推荐使用 `cc switch` 管理配置。

项目地址：
[https://github.com/farion1231/cc-switch/](https://github.com/farion1231/cc-switch/)

发布页：
[https://github.com/farion1231/cc-switch/releases](https://github.com/farion1231/cc-switch/releases)

`cc switch` 的价值主要在于：

- 统一管理多个平台的 API Key / Token
- 统一切换 Base URL 和模型
- 避免手动改一堆环境变量
- 对 Claude Code / Codex / Gemini CLI 这类工具更友好

#### 1、准备你需要的参数

无论你接哪个第三方平台，核心都离不开这几个配置项：

- `ANTHROPIC_AUTH_TOKEN` 或 `ANTHROPIC_API_KEY`
- `ANTHROPIC_BASE_URL`
- `ANTHROPIC_MODEL`

有些平台额外还会用到：

- `ANTHROPIC_SMALL_FAST_MODEL`
- `API_TIMEOUT_MS`
- `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`

#### 2、典型配置思路

以 Anthropic 兼容 API 为例，你通常需要把下面三项填进去：

~~~
ANTHROPIC_AUTH_TOKEN=你的第三方 API Key
ANTHROPIC_BASE_URL=第三方兼容端点
ANTHROPIC_MODEL=你想使用的模型名
~~~

例如常见的兼容形式会像这样：

~~~
ANTHROPIC_AUTH_TOKEN=sk-xxxxxx
ANTHROPIC_BASE_URL=https://example.com/apps/anthropic
ANTHROPIC_MODEL=qwen3-coder-plus
~~~

#### 3、用 `cc switch` 管理 Token / API

推荐做法不是把这些值散落在不同 shell 配置文件里，而是集中交给 `cc switch` 管理：

- 把不同平台的 Token 录入 `cc switch`
- 为每个平台保存一套 Base URL + Model 组合
- 需要切换时直接在 `cc switch` 里切换目标配置
- 切换后再启动 Claude Code

这样做的好处是：

- 不容易把不同平台的 Key 和 URL 混用
- 切换模型供应商更快
- 多套配置更容易回滚和排查

#### 4、建议优先检查的内容

配置完以后，先启动 Claude Code，再执行：

~~~
/status
~~~

重点确认这几项：

- 当前 `Model`
- 当前 `Base URL`
- 当前 `API Key / Auth Token` 是否已加载

如果 `/status` 看到的不是你刚配置的值，优先检查：

- 是否真的完成了 `cc switch` 切换
- 当前终端会话是否重新打开
- Token 名称是不是写成了错误的环境变量
- Base URL 是否用了不兼容的协议路径

---

### 第三方 API 网关示例：Waoo API

如果你想集中管理多个模型供应商，也可以使用像 `Waoo API` 这样的聚合网关。

公开站点：

- 价格页：[https://waooapi.com/pricing](https://waooapi.com/pricing)
- 使用文档：[https://hxc4ukx1w3w.feishu.cn/wiki/UH3LwRYm1ipNUlk3C6AcMrMRnOc](https://hxc4ukx1w3w.feishu.cn/wiki/UH3LwRYm1ipNUlk3C6AcMrMRnOc)

从公开页面可确认，它主打的是统一网关模式，支持把不同模型服务转换成兼容接口供客户端使用。文档中展示的典型用法是把它作为 **Anthropic 兼容后端** 接到 Claude Code 或 `cc switch` 里。

#### 1、最小配置思路

如果按 Anthropic 兼容方式接入，最关键的仍然是这三项：

~~~
ANTHROPIC_AUTH_TOKEN=你的 Waoo API Token
ANTHROPIC_BASE_URL=https://waooapi.com
ANTHROPIC_MODEL=claude-sonnet-4-6
~~~

如果你希望把不同档位模型也提前配好，可以进一步补充：

~~~
ANTHROPIC_DEFAULT_HAIKU_MODEL=claude-sonnet-4-6
ANTHROPIC_DEFAULT_SONNET_MODEL=claude-sonnet-4-6
ANTHROPIC_DEFAULT_OPUS_MODEL=claude-opus-4-6
ANTHROPIC_REASONING_MODEL=claude-sonnet-4-6
~~~

文档示例里还出现了这些可选项：

~~~
CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1
DISABLE_COST_WARNINGS=1
DISABLE_TELEMETRY=1
~~~

#### 2、放进 `cc switch` 的思路

如果你使用 `cc switch`，可以把 `Waoo API` 作为一个自定义供应商来配置，核心就是：

- Token 填你的 `Waoo API Token`
- Base URL 填 `https://waooapi.com`
- 模型填你要用的 Claude 兼容模型名

切换完成后，重新打开终端，再启动 Claude Code，用 `/status` 检查：

- 当前 `Base URL`
- 当前 `Auth Token`
- 当前 `Model`

#### 3、关于价格

如果你很关心成本，可以直接参考它的公开价格页：

[https://waooapi.com/pricing](https://waooapi.com/pricing)

更稳妥的写法不是在文档里固定写死价格数字，而是：

- 直接以价格页实时信息为准
- 把它和你当前官方直连方案做横向对比
- 再决定是否切换

这样文档不容易过时，也避免因为价格变动导致说明失准。

---

### 第三方 API 直配方式（macOS）

如果你暂时不使用 `cc switch`，也可以直接在 macOS 上配置。

最简单的方式是写到 `~/.zshrc` 或 `~/.zprofile` 中，例如：

~~~
export ANTHROPIC_AUTH_TOKEN="YOUR_API_KEY"
export ANTHROPIC_BASE_URL="https://example.com/apps/anthropic"
export ANTHROPIC_MODEL="qwen3-coder-plus"
~~~

保存后执行：

~~~
source ~/.zshrc
~~~

然后启动：

~~~
claude
~~~

再用：

~~~
/status
~~~

确认配置是否生效。

---

### 启动第一次会话

进入你的项目目录后：

~~~
cd /path/to/your/project
claude
~~~

你可以先问 Claude Code 这些问题：

~~~
what does this project do?
~~~

~~~
where is the main entry point?
~~~

~~~
explain the folder structure
~~~

如果你要先试一次最简单的代码改动，也可以直接说：

~~~
在 test.py 文件中添加 hello world 函数
~~~

---

### 卸载（macOS）

如果你是官方脚本安装：

~~~
rm -f ~/.local/bin/claude
rm -rf ~/.local/share/claude
~~~

如果你是 Homebrew 安装：

~~~
brew uninstall --cask claude-code
~~~

如果你是 npm 安装：

~~~
npm uninstall -g @anthropic-ai/claude-code
~~~

如果你还想清掉本地配置和历史记录：

~~~
rm -rf ~/.claude
rm -f ~/.claude.json
~~~

如果你还要删除当前项目里的本地配置：

~~~
rm -rf .claude
rm -f .mcp.json
~~~
---

## Claude Code 如何工作

**简单来说**：Claude Code 就像一个超级聪明的编程搭档，它能在你的电脑上自动帮你写代码、改 bug、运行命令、搜索文件……几乎所有你在终端里能做的事，它都能干！

本节带你彻底搞懂它的底层原理（代理循环、模型、工具等），看完你就知道它为什么这么强大，以及怎么用得更顺手。

---

### Claude 的工作流程

当你给 Claude 一个任务时，它会像人类一样 **循环思考**三个步骤：

1. **收集上下文**（看你的代码、文件、错误信息）
2. **采取行动**（编辑文件、运行命令、搜索等）
3. **验证结果**（检查是否成功，哪里还需要调整）

这三个步骤会不断循环，直到任务完成，当然我们也可以随时可以打断它、给新指示，或者说"换个方法试试"。

Claude 虽然能自主工作，但 **永远听你的指挥**！

~~~
Prompt → 获取上下文 → 执行动作 → 验证结果 →（循环）→ 完成
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/runoob-claude-code-how-it-work-1-scaled.png)

整个循环靠两样东西驱动：
- **模型**（负责思考和推理）
- **工具**（负责真正动手做事）

---

### 模型（Claude 的大脑）

Claude Code 使用 Anthropic 的 Claude 模型来理解代码和规划任务，它能看懂几乎所有编程语言，知道你的项目结构，还能把复杂任务拆成小步骤一步步执行。

**常用模型推荐**：
- **Sonnet**：最适合日常编码任务（速度快、性价比高）
- **Opus**：适合特别复杂的架构设计和深度推理

不过我们国内使用官方的模型不太友好，我们可以切换到其他国内模型。

**切换模型**：
- 在提示框输入 `/model`
- 或启动时用 `claude --model sonnet`

![image](https://www.runoob.com/wp-content/uploads/2026/03/b8a57979-4733-4973-9ac0-0f377abbacaf.png")

---

### 工具（Claude 能真正动手的原因）

没有工具，Claude 只能聊天，有了工具，它就能真正改你的代码！

Claude 内置了五大类工具：
- **文件操作**：读文件、改代码、新建文件、重命名
- **搜索**：按文件名或正则表达式快速找代码
- **执行命令**：运行 npm、git、测试、启动服务器等
- **网络搜索**：查文档、查错误信息
- **代码智能**：查看类型错误、跳转定义、找引用（需安装对应语言插件）

**真实案例**（修复失败测试时）：

1. 运行测试看哪里挂了
2. 阅读错误日志
3. 搜索相关源文件
4. 阅读文件理解逻辑
5. 修改代码
6. 再次运行测试验证

这就是代理循环的实际样子！

**进阶扩展**：你还可以用 skills（技能）、MCP（外部服务）、hooks（自动化）、subagents（子助手）让它更强大。

---

### Claude 可以访问什么

Claude Code 能看到你的整个项目（不像普通 AI 只能看当前打开的文件）：
- 你的项目文件夹及所有子文件
- 终端能执行的任何命令（npm、git、构建工具等）
- 当前 Git 状态（分支、未提交修改、提交历史）
- **CLAUDE.md** 文件（你可以在这里写项目专属规则，让 Claude 每次都记住）
- **自动记忆**（它会记住你的项目习惯，保存在 MEMORY.md 前 200 行）
- 你配置的扩展（MCP、skills 等）

正因如此，它能跨多个文件协调修改、自动跑测试、甚至帮你提交代码。

---

### 执行环境与界面

Claude Code 可以在 **三种环境**里运行：
- **本地**（默认）：完全在你电脑上，权限最高
- **云端**（Anthropic 虚拟机）：适合处理大项目或你本地没有的环境
- **远程控制**：从浏览器操作，但代码仍在你本地

界面支持：终端、VS Code 扩展、JetBrains、claude.ai/code、Slack、CI/CD 等。底层工作原理完全一样！

---

### 跨分支工作（Git 分支切换也能无缝使用）

Claude Code 的每个对话 **都和当前文件夹绑定**（而不是和 Git 分支绑定）。这带来了非常实用的特性：
- 当你 **切换 Git 分支**时（`git checkout` 或 `git switch`），Claude 会 **自动看到新分支的文件**，但你的 **聊天记录保持不变**。
- Claude 依然记得之前讨论过的所有内容和决定，即使跨分支也不会丢失上下文。
- 当你恢复旧对话时，只会显示 **当前文件夹**里的会话记录（不同分支如果在同一文件夹，就共用历史）。

**并行多分支开发神器**：
推荐使用 `git worktrees` 为每个分支创建一个 **独立目录**，这样你就可以同时打开多个 Claude Code 会话，每个分支都有自己完全独立、干净的对话记录，不会互相干扰。

---

### 恢复或分叉会话（继续聊 / 新开一条线）

Claude Code 支持三种灵活的会话操作，让你随时接上之前的进度或尝试不同方案：

##### 1、直接恢复会话（最常用）

命令：

~~~
claude --continue

# 或者

claude --resume
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/session-continuity.svg)

**效果**：
- 使用 **相同的会话 ID**，从上次中断的地方继续
- 完整聊天历史全部恢复
- **注意**：会话范围的权限不会自动恢复，需要你重新批准一次

##### 2、分叉会话（推荐！尝试新方案不影响原对话）

命令：

~~~
claude --continue --fork-session
~~~

**效果**：
- 创建一个 **全新的会话 ID**
- 保留到目前为止的所有聊天历史
- 原会话完全不受影响
- 同样需要重新批准权限

**适用场景**：想试试"另一种实现方式"又不想把原对话搞乱时，就用这个！

##### 3、 多个终端同时使用同一个会话（小心使用）

- 如果你在多个终端窗口都用 `claude --continue` 恢复同一个会话：
- 所有消息会 **交错混在一起**（就像两个人同时在同一个笔记本上写字）
- 对话会变得混乱
- 每个终端当时只能看到自己输入的消息，但后续恢复时会看到全部交错内容

**正确做法**：想并行工作时，**务必使用 `--fork-session`**，给每个终端一个干净独立的新会话。

---

### 上下文窗口（Claude 的记忆容量）

Claude 有上下文容量限制。当快满时：
- 它会 **自动压缩**旧内容
- 你可以输入 `/compact` 手动压缩
- 输入 `/context` 查看当前占用情况

**省空间秘诀**：
- 重要规则写进 **CLAUDE.md**
- 用 skills 和 subagents 减少不必要的上下文占用

---

### 安全机制（检查点 + 权限控制）

**1、检查点（随时后悔）**

Claude 修改文件前会自动备份。只要按两次 **Esc**，或说“撤销刚才的修改”，就能回到之前状态。

**2、权限模式**（按 Shift + Tab 快速切换）：
- **默认**：每次改文件或跑命令都问你
- **自动接受编辑**：只改文件不问，但命令仍会问
- **计划模式**：只分析、不动手，先给你完整计划

你还可以在 `.claude/settings.json` 里白名单信任命令（比如 `npm test` 永远不用问）。

---

### 有效使用 Claude Code 的 6 个实用 Tips

1. **直接问它怎么用自己**
输入："我怎么设置 hooks？" 或 "/init" 它就会手把手教你。
2. **这是对话，不是一次性命令**
先说大概需求 → 它试试 → 你再纠正："这不对，问题是会话处理"
不用每次都重写提示！
3. **随时打断它**
它走偏了？直接输入新指示按回车，它立刻停下来调整。
4. **提示越具体越好**
坏例子： "修复登录错误"
好例子： "持卡人过期的用户结账流程坏了，检查 src/payments/，尤其是令牌刷新。先写个失败测试再修复。"
5. **让它自己验证**
加上测试用例或预期结果，它会跑测试确认。
6. **先探索再实现**
先用 **计划模式**（Shift+Tab 两次）让它分析代码库、给出计划 → 你审核后再让它动手。

---

## Claude Code API 配置

Claude 在国内用，API 其实不是很友好，除了 Claude 官方的模型，我能用其他 AI 模型吗？

本章节会详细教你如何配置多个国内主流 AI 模型的 API，让 Claude Code 支持：

| 厂商/品牌 | 简介 | API 申请入口（点击即达） |
| --- | --- | --- |
| DeepSeek（国产高性价比） | 官方模型：deepseek-chat / deepseek-reasoner | [https://platform.deepseek.com/api_keys](https://platform.deepseek.com/api_keys) |
| 阿里百炼（通义千问） | 阿里云大模型统一入口，支持千问、GLM、Kimi 、MiniMax 等最新版本模型 | [https://bailian.console.aliyun.com](https://bailian.console.aliyun.com?userCode=i5mn5r7m) |
| GLM（智谱清言） | 清华系 ChatGLM 系列，支持 GLM-4、GLM-3-Turbo 等 | [https://open.bigmodel.cn](https://www.bigmodel.cn/glm-coding?ic=EMWK7IPUCE) |
| MiniMax | 国产多模态，支持文本、语音、图像混合调用 | [https://platform.minimaxi.com](https://platform.minimaxi.com) |

进入对应控制台后，注册/登录 → 完成实名认证 → 创建 API Key 即可开始调用。

#### API 管理工具

平台一多，配置起来就麻烦，我们可以使用第三方工具 CC Switch 可以帮我们轻松管理这几个热门工具的 API 配置：[https://github.com/farion1231/cc-switch/](https://github.com/farion1231/cc-switch/)，Windows / macOS / Linux 全支持。

CC Switch 是一个 Claude Code / Codex / Gemini CLI 的全方位辅助工具。

CC Switch 可以帮我们轻松管理这几个热门工具的 API 配置，就好比给你的开发工具箱来了个智能整理助手，所有工具的配置都能在它这有序管理。

![image](https://www.runoob.com/wp-content/uploads/2025/12/claude-code-ccswitch.png)

各平台安装包下载地址：[https://github.com/farion1231/cc-switch/releases](https://github.com/farion1231/cc-switch/releases)。

![image](https://www.runoob.com/wp-content/uploads/2025/12/claude-code-runoob2.png)

具体的操作设置参考文章：[https://www.runoob.com/ai-agent/cc-switch.html](https://www.runoob.com/ai-agent/cc-switch.html)

如果你不闲麻烦，可以参照下文，自行配置。

---

### DeepSeek 接入 Claude Code

**需要的信息：**
- API key： [https://platform.deepseek.com/api_keys](https://platform.deepseek.com/api_keys) 申请 API keys。
- BASE_URL：API 请求的 URL 为 https://api.deepseek.com/anthropic。

#### 环境变量中配置

安装 Claude Code 后，我们在终端中设置以下环境变量：

~~~
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=${DEEPSEEK_API_KEY} # 这里记得设置你申请的 API key
export API_TIMEOUT_MS=600000
export ANTHROPIC_MODEL=deepseek-chat
export ANTHROPIC_SMALL_FAST_MODEL=deepseek-chat
export CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1
~~~

**参数说明：**
- `API_TIMEOUT_MS=600000`：设置 10 分钟超时，防止输出过长触发客户端超时
- `ANTHROPIC_MODEL`：指定使用 deepseek-chat 模型
- `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC=1`：禁用非必要流量

然后进入项目目录，执行 claude 命令，即可开始使用了。

~~~
cd my-project
claude
~~~

![image](https://www.runoob.com/wp-content/uploads/2025/12/aebe4b0f-78f1-40fc-87ce-05f889af4c8e.png)

启动 Claude Code 后，指定使用 DeepSeek：

~~~
claude --model deepseek-chat
~~~

或在交互模式中切换：

~~~
> /model deepseek-chat
~~~

输入 /model 可以查看支持的模型：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ea32a2cb-aa50-4f3b-870a-1aa7a1f47211.png)

参考文档：[https://api-docs.deepseek.com/zh-cn/guides/anthropic_api](https://api-docs.deepseek.com/zh-cn/guides/anthropic_api)

---

### 阿里百炼接入 Claude Code

阿里云百炼的通义千问系列模型支持 Anthropic API 兼容接口，通过修改以下参数，即可在 Claude Code 中调用通义千问系列模型。
- ANTHROPIC_API_KEY（或 ANTHROPIC_AUTH_TOKEN）：替换为百炼 API Key，申请地址：[https://bailian.console.aliyun.com/cn-beijing/?tab=model#/api-key](https://bailian.console.aliyun.com/cn-beijing/?userCode=i5mn5r7m&tab=model#/api-key)。
- ANTHROPIC_BASE_URL：替换为百炼的兼容端点地址 https://dashscope.aliyuncs.com/apps/anthropic。
- 模型名称：替换为百炼支持的模型名称（例如 qwen3-max、qwen3-coder-plus等）

> **兼容 Anthropic API 协议：**
> - Base URL：https://dashscope.aliyuncs.com/apps/anthropic
> - API Key：填入百炼 API Key
> - Model：qwen3-coder-plus

**macOS/Linux：**
创建并打开配置文件 ~/.claude/settings.json。
`~` 代表用户主目录，如果 .claude 目录不存在，需要先行创建，可在终端执行 `mkdir -p ~/.claude` 来创建。
编辑配置文件：

~~~
vim ~/.claude/settings.json
~~~

将 YOUR_API_KEY 替换为百炼 API Key。

~~~
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "YOUR_API_KEY",
    "ANTHROPIC_BASE_URL": "https://dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "qwen3-coder-plus"
  }
}
~~~

重新打开一个新的终端使环境变量配置生效。

**Windows：** 在 CMD 中运行以下命令，设置环境变量：

~~~
setx ANTHROPIC_AUTH_TOKEN "YOUR_API_KEY"
setx ANTHROPIC_BASE_URL "https://coding.dashscope.aliyuncs.com/apps/anthropic"
setx ANTHROPIC_MODEL "qwen3-coder-plus"
~~~

用套餐专属 API Key 替换 YOUR_API_KEY。
打开一个新的 CMD 窗口，运行以下命令，检查环境变量是否生效。

~~~
echo %ANTHROPIC_AUTH_TOKEN%
echo %ANTHROPIC_BASE_URL%
echo %ANTHROPIC_MODEL%
~~~

在 PowerShell 中运行以下命令，设置环境变量：

~~~
# 用套餐专属 API Key 替换 YOUR_API_KEY
[Environment]::SetEnvironmentVariable("ANTHROPIC_AUTH_TOKEN", "YOUR_API_KEY", [EnvironmentVariableTarget]::User)
[Environment]::SetEnvironmentVariable("ANTHROPIC_BASE_URL", "https://coding.dashscope.aliyuncs.com/apps/anthropic", [EnvironmentVariableTarget]::User)
[Environment]::SetEnvironmentVariable("ANTHROPIC_MODEL", "qwen3-coder-plus", [EnvironmentVariableTarget]::User)
~~~

打开一个新的 PowerShell 窗口，运行以下命令，检查环境变量是否生效。

~~~
echo $env:ANTHROPIC_AUTH_TOKEN
echo $env:ANTHROPIC_BASE_URL
echo $env:ANTHROPIC_MODEL
~~~

#### 在 Claude Code 中接入通义千问系列模型

对话期间，执行 /model <模型名称> 命令切换模型。

~~~
/model qwen3-coder-plus
~~~

也可以在项目根目录创建 .claude/settings.json 文件中，并写入模型配置信息永久配置。

![image](https://www.runoob.com/wp-content/uploads/2026/01/6a97af64-c4a5-425b-99a0-3aaf9dc59dbd.png)

~~~
{
  "env": {
    "ANTHROPIC_MODEL": "qwen3-coder-plus",
    "ANTHROPIC_SMALL_FAST_MODEL": "qwen-flash"
  }
}
~~~

启动 Claude，可以看到配置信息：

![image](https://www.runoob.com/wp-content/uploads/2026/01/d811c5e3-f30f-4c1b-9107-774db34b330c.png)

---

### 智谱大模型接入 Claude Code

这部分我们使用 ~/.claude/settings.json 文件来配置大模型，开始前需要到官方平台获取 API key。
编辑或新增 Claude Code 配置文件 ~/.claude/settings.json，新增或修改里面的 env 字段：

~~~
# 注意替换里面的 your_zhipu_api_key 为您上一步获取到的 API Key
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
    "ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
  }
}
~~~

运行 claude 启动 Claude Code，输入 /status 确认模型状态：

![image](https://www.runoob.com/wp-content/uploads/2025/12/5617ff6d-7997-4819-a2eb-300346548c61.png)

如果不是可以输入 /config 来切换模型。

![image](https://www.runoob.com/wp-content/uploads/2025/12/98fa9dc0-efab-4afc-a100-350ad114937c.png)

---

## Coding Plan

本章只保留较中性的技术说明，不展开套餐、优惠或购买引导。

在这份文档里，`Coding Plan` 可以理解为一类面向 AI 编程工具的聚合接入方式：平台提供一个兼容端点，你在 Claude Code 中配置对应的 Base URL、Token 和模型后，就能通过第三方后端使用兼容模型。

---

### 什么时候会用到 Coding Plan

常见场景：

- 想通过国内兼容服务在 Claude Code 中接入第三方模型
- 想统一管理模型访问入口，而不是分别直连多个原始 API
- 想让 Claude Code 使用兼容的 Anthropic 或 OpenAI 风格接口

如果你已经能直接使用官方 Claude 账号，或者已经通过其他兼容 API 正常工作，这一章不一定是必需的。

---

### 核心配置项

无论是方舟、百炼，还是其他兼容服务，Claude Code 里最关键的通常都是这几项：

- `ANTHROPIC_AUTH_TOKEN`
- `ANTHROPIC_BASE_URL`
- `ANTHROPIC_MODEL`

有时还会补充：

- `ANTHROPIC_SMALL_FAST_MODEL`
- `API_TIMEOUT_MS`
- `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC`

---

### 方舟 Coding Plan

如果你使用方舟的 Coding Plan，常见的 Anthropic 兼容配置是：

~~~
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "<ARK_API_KEY>",
    "ANTHROPIC_BASE_URL": "https://ark.cn-beijing.volces.com/api/coding",
    "ANTHROPIC_MODEL": "<MODEL_NAME>"
  }
}
~~~

请替换：

- `<ARK_API_KEY>`：你的 API Key
- `<MODEL_NAME>`：你要使用的模型名，例如 `kimi-k2.5`

如果工具要求 OpenAI 兼容端点，则需要改用对应的 OpenAI 风格 Base URL，而不是直接复用 Anthropic 配置。

**注意：**

- 不同协议的 Base URL 不能混用
- 使用前先确认你接入的工具期望的是 Anthropic 兼容还是 OpenAI 兼容
- 配置完成后，建议在 Claude Code 中执行 `/status` 检查当前 Base URL、Token 与模型是否生效

---

### 百炼 Coding Plan

如果你使用阿里百炼的 Coding Plan，常见的 Anthropic 兼容配置是：

~~~
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "YOUR_API_KEY",
    "ANTHROPIC_BASE_URL": "https://coding.dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "qwen3.5-plus"
  }
}
~~~

如果工具需要 OpenAI 兼容方式，则应改用该平台提供的 OpenAI 兼容地址，而不是直接套用上面的 Anthropic 配置。

**注意：**

- 专用 API Key 与按量计费 API Key 可能不是同一套，不要混用
- Base URL 必须和当前使用的协议一致
- 模型名需要写成当前服务支持的名称，而不是想当然地照搬别的平台

---

### 推荐验证步骤

完成配置后，建议按下面顺序验证：

1. 重新打开终端
2. 启动 Claude Code
3. 执行 `/status`
4. 确认以下信息是否正确：
   - 当前 `Base URL`
   - 当前 `Auth Token`
   - 当前 `Model`

如果值不对，优先检查：

- 配置文件是不是写在了正确位置
- 变量名有没有拼写错误
- Base URL 是否使用了错误协议
- 当前终端是否重新加载了配置

---

### 最小化建议

如果你的目标只是“让 Claude Code 能稳定接第三方后端”，建议优先做到这三件事：

1. 先选定一个兼容服务，不要同时配很多个平台
2. 只保留一套明确可工作的 Base URL + Token + Model
3. 用 `/status` 做最终确认，而不是凭感觉判断是否生效

这样比堆很多套餐说明、平台对比和活动链接更实用，也更容易排错。
