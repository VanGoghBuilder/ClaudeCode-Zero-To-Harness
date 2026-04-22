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
