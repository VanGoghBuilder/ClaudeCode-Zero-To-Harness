# 日常使用与交互

> 阅读提示：如果你还没有建立 Claude Code 的基本定位，建议先读 [概览与安装](./01-overview-and-setup.md)；如果你想先理解为什么这些日常协作习惯最后会进入 harness，可以对照阅读 [Claude Code：从 Zero 到 Harness](./00-zero-to-harness.md) 和 [Claude Code 的 Harness Engineering 总纲](./06-harness-engineering-guide.md)。

## 包含章节

- Claude Code 交互模式
- Claude Code 控制与模式
- Claude Code 斜杠 / 命令
- Claude Code 输出样式
- Claude Code 检查点
- Claude Code 实战
- Claude Code 并行任务

---

## Claude Code 交互模式

与网页版 Claude 不同，Claude Code 并不是一个单纯的聊天工具，而是一个 **具备工程上下文、权限控制和执行能力的开发协作系统**。
因此，它在处理任务时，引入了三种核心的 **思维模式**：
- **Ask（询问）**
- **Plan（规划）**
- **Edit（编辑）**

**理解这三种模式的边界与职责，是高效、安全使用 Claude Code 的关键。**

终端执行其实不用手动切换，Claude 会自动切换，在 VS Code 插件中可以手动切换模式：

![image](https://www.runoob.com/wp-content/uploads/2026/01/8f7e1a04-58bc-4b01-80a0-101eca066174.png)

---

### 三大交互模式详解

在终端中使用 Claude Code 时，你 **不需要手动切换模式开关**，Claude 会根据你的指令内容，自动判断当前应进入哪一种模式。

不过， **作为使用者，你必须在 Prompt 中清楚表达自己的意图**，否则 Claude 很容易做出与你预期不一致的行为。

#### 1、Ask 模式：只看不动（只读分析）

**Ask 模式是 Claude Code 的默认安全模式。**

##### 使用场景

当你处于以下状态时，应优先使用 Ask 模式：
- 看不懂现有代码
- 刚接手一个新项目
- 想定位 Bug 的原因，但还没想好怎么修
- 想确认某段逻辑是否合理

##### 模式特点

- Claude **可以读取代码**
- 可以进行分析、解释、推理
- **绝对不会修改任何文件**
- 不会执行任何 Shell 命令

你可以把 Ask 模式理解为：

> **一位经验丰富、但双手放在背后的高级工程师。**

##### 示例指令

~~~
解释一下 src/auth.ts 里的 JWT 验证流程
~~~

~~~
在这个项目里，数据库连接是在哪里初始化的？
~~~

~~~
这段代码为什么在高并发下可能出问题？
~~~

> 专业建议：
> **不确定要不要改代码时，一律先 Ask。**

---

#### 2、Plan 模式：谋定而后动（只规划，不执行）

**Plan 模式是 Claude Code 中最像架构师思维的模式。**

##### 使用场景

当你面对的是 **复杂或影响范围较大的改动**时，应该使用 Plan 模式，例如：
- 新增一个核心模块
- 重构已有接口或业务流程
- 引入新技术（缓存、中间件、鉴权方案等）
- 涉及多个文件、多个层级的修改

##### 模式特点

- Claude 不会直接改代码
- 会先给出 **完整的实施方案**
- 通常以步骤列表（TODO List）的形式呈现
- 你确认方案后，才进入 Edit 阶段执行

Plan 模式的核心价值在于：

> **把先写代码变成先达成共识。**

##### 示例指令

~~~
我想给现有的 API 增加一个 Redis 缓存层，请先给我一个实施方案
~~~

~~~
计划一下如何把这个项目的样式从 CSS 改为 Tailwind CSS
~~~

~~~
如果要拆分这个模块，合理的步骤应该是什么？
~~~

> 实战建议：
> **Plan 模式用得越多，返工概率就越低。**

#### 3、Edit 模式：直接执行（可写代码）

**Edit 模式是 Claude Code 真正动手的阶段。**

##### 使用场景

当你已经明确知道：
- 要改哪
- 改成什么
- 改动风险可控

此时就可以直接进入 Edit 模式。

##### 模式特点

- Claude 会定位相关文件
- 生成精确的 Diff（差异修改）
- 可能会请求执行测试、构建等命令
- 所有写入操作都需要你确认

Edit 模式下，你的角色是：

> **代码的最终审查者，而不是旁观者。**

##### 示例指令

~~~
把登录接口的响应码从 200 改为 201
~~~

~~~
修复 main.py 第 45 行的类型错误
~~~

~~~
在不改变现有行为的前提下，重构这个函数的可读性
~~~

> 专业习惯：
> **描述越具体，Claude 改得越安全。**

---

### 核心斜杠命令（`/`）

斜杠命令用于 **控制 Claude Code 的会话状态和行为**，而不是写业务代码。

在终端中输入 `/` 即可触发。

![image](https://www.runoob.com/wp-content/uploads/2026/01/d29e985e-5cb1-43bc-b6fb-e3b290f83bf9.png)

| 命令 | 用途 | 专家提示 |
| --- | --- | --- |
| `/help` | 查看所有命令 | 第一次使用必看 |
| `/cost` | 查看当前会话消耗 | 防止无意识烧 Token |
| `/compact` | 压缩上下文 | 对话变长、变慢时使用 |
| `/reset` | 重置会话 | 切换任务时非常重要 |
| `/docs` | 索引文档 | 让 Claude 参考指定文档 |
| `/review` | 代码审查 | 检查 Git 暂存区改动 |

> 建议：
> **/cost 和 /compact 是长期使用者的高频命令。**

---

### 一个典型的交互流程示例

真实开发中，三种模式往往是 **连续使用的**。

#### 示例流程

**1、Ask**

~~~
为什么我的注册接口一直返回 400 错误？
~~~

*Claude 分析代码和日志，指出是缺少 email 校验。*

**2、Plan**

我想添加 email 格式校验，并把错误信息规范化，你打算怎么做？

*Claude 给出步骤：修改校验逻辑 → 更新测试 → 调整返回结构。*

**3、Edit**

~~~
按计划执行吧
~~~

*Claude 修改文件，并展示 Diff 供你确认。*

> **Ask 是搞清楚问题，Plan 是避免走弯路，Edit 是谨慎执行。**

---

### 交互中的权限确认机制

Claude Code 对系统操作非常克制，任何有风险的行为都会请求你的确认。

#### 常见权限确认

- **写入文件**
会展示代码 Diff，你需要确认后才能写入。
- **执行命令**
例如 `npm test`、`pnpm build`，都会明确询问是否允许。

> **重要提醒：**
> 永远不要在没看 Diff 的情况下直接确认。
> Claude 很强，但依然可能产生错误或幻觉。

---

## Claude Code 操作说明

Claude Code 的输入框不是纯聊天框，而是一个：

> AI + 编辑器 + 命令调度器的融合终端

主要有三类前缀触发器：

| 符号 | 类型 | 本质作用 |
| --- | --- | --- |
| `/` | Command（命令） | 执行内置操作 |
| `@` | Context（上下文） | 引用文件/代码/目录 |
| `!` | Bash 模式 | 直接执行终端命令，stdout/stderr 自动注入上下文 |
| `#` | Memory（记忆注入） | 把内容持久写入 CLAUDE.md 项目记忆，跨会话长期生效，例如：#config.yaml |
| `&` | Async（异步任务） | 后台/云端异步执行任务，不阻塞当前会话，可关闭终端后在 claude.ai/code 查看进度 |
| `\`+Enter | Multiline（多行输入） | 换行不发送，写多行内容，长需求描述一次性写完 |
| 无前缀 | 自然语言 | 普通任务指令 |

---

### `/` —— 操作型命令（最重要）

/ 是 Claude Code 中触发内置命令 /工具的核心符号，类似终端命令行的指令前缀，用于告诉 Claude 执行特定操作而非单纯生成文本。

**核心用途：** 调用内置功能（如代码生成、文件操作、环境执行、插件调用等）。

/ 后紧跟命令关键词，空格后接参数（如文件名、执行命令、修复目标等），是 Claude Code 区分 **自然语言对话**和 **代码操作指令**的关键。

输入 `/` 会弹出命令列表:

![image](https://www.runoob.com/wp-content/uploads/2026/01/23e5a097-f781-4f4b-8597-35d18a7aae29.png)

常见高频命令：

| 命令 | 作用 |
| --- | --- |
| `/help` | 查看全部能力 |
| `/clear` | 清空对话 |
| `/plan` | 进入规划模式 |
| `/model` | 切换模型 |
| `/context` | 查看上下文使用情况 |
| `/export` | 导出对话 |
| `/status` | 环境状态 |
| `/tasks` | 管理后台任务 |
| `/theme` | 主题切换 |
| `/memory` | 编辑 CLAUDE.md |

例如：

```text
/plan 实现一个用户登录模块
```

---

### `@` —— 上下文注入

`@` 后面跟文件名或目录时，Claude Code 会把对应内容真实加载进上下文。

![image](https://www.runoob.com/wp-content/uploads/2026/01/f6f417b7-0f60-4e73-97ec-0e09380f590d.png)

常见写法：

```text
@main.py 帮我检查 bug
@main.py @main2.py 这两个是否有重复逻辑？
@src/ 分析项目结构并给出优化建议
@npm-debug.log 找出失败原因
```

![image](https://www.runoob.com/wp-content/uploads/2026/01/9b1aac90-b242-468d-b25f-c581e648bb72.png)

### `!` —— Bash 命令

在输入前加 `!` 可以直接运行 Bash 命令，无需先让 Claude 解释。

```text
! ls -la
! git status
! npm test
```

输入 `!` 后会进入 Bash 命令模式：

![image](https://www.runoob.com/wp-content/uploads/2026/01/f0a9cb66-6ae1-48c4-8fc3-2f00ff5d9c6e-1.png)

例如查看当前目录：

```text
! ls -la
```

![image](https://www.runoob.com/wp-content/uploads/2026/01/ed966407-b7e5-4a81-b646-738624109e68-1.png)

### Vim 编辑器模式

使用 `/vim` 命令启用 Vim 风格编辑，或通过 `/config` 永久配置。

#### 模式切换

| 命令 | 操作 | 来自模式 |
| --- | --- | --- |
| `Esc` | 进入 NORMAL 模式 | INSERT |
| `i` | 在光标前插入 | NORMAL |
| `I` | 在行首插入 | NORMAL |
| `a` | 在光标后插入 | NORMAL |
| `A` | 在行尾插入 | NORMAL |
| `o` | 在下方打开一行 | NORMAL |
| `O` | 在上方打开一行 | NORMAL |

#### 导航（NORMAL 模式）

| 命令 | 操作 |
| --- | --- |
| `h` / `j` / `k` / `l` | 向左 / 下 / 上 / 右移动 |
| `w` | 跳到下一个单词 |
| `e` | 跳到单词末尾 |
| `b` | 跳到上一个单词 |
| `0` | 跳到行首 |
| `$` | 跳到行尾 |
| `^` | 跳到第一个非空白字符 |
| `gg` | 跳到输入开始 |
| `G` | 跳到输入结束 |
| `f{char}` | 跳到下一个字符出现处 |
| `F{char}` | 跳到上一个字符出现处 |
| `t{char}` | 跳到下一个字符出现处之前 |
| `T{char}` | 跳到上一个字符出现处之后 |
| `;` | 重复最后一个 `f/F/t/T` 动作 |
| `,` | 反向重复最后一个 `f/F/t/T` 动作 |

#### 编辑（NORMAL 模式）

| 命令 | 操作 |
| --- | --- |
| `x` | 删除字符 |
| `dd` | 删除整行 |
| `D` | 删除到行尾 |
| `dw` / `de` / `db` | 删除单词 / 到末尾 / 向后 |
| `cc` | 更改整行 |
| `C` | 更改到行尾 |
| `cw` / `ce` / `cb` | 更改单词 / 到末尾 / 向后 |
| `yy` / `Y` | 复制整行 |
| `yw` / `ye` / `yb` | 复制单词 / 到末尾 / 向后 |
| `p` | 在光标后粘贴 |
| `P` | 在光标前粘贴 |
| `>>` | 缩进行 |
| `<<` | 取消缩进行 |
| `J` | 连接行 |
| `.` | 重复最后一个更改 |

#### 文本对象（NORMAL 模式）

文本对象通常与 `d`、`c`、`y` 等操作符一起使用：

| 命令 | 操作 |
| --- | --- |
| `iw` / `aw` | 内部 / 周围单词 |
| `iW` / `aW` | 内部 / 周围 WORD（空格分隔） |
| `i"` / `a"` | 内部 / 周围双引号 |
| `i'` / `a'` | 内部 / 周围单引号 |
| `i(` / `a(` | 内部 / 周围括号 |
| `i[` / `a[` | 内部 / 周围方括号 |
| `i{` / `a{` | 内部 / 周围花括号 |

### 命令历史

Claude Code 会为当前工作目录维护命令历史：

- 历史按工作目录存储
- 可使用 `/clear` 清除当前会话历史
- 可使用上下箭头浏览历史记录
- 历史扩展 `!` 默认禁用

#### `Ctrl+R` 反向搜索

按 `Ctrl+R` 可以交互式搜索历史命令：

- 开始搜索：按 `Ctrl+R` 激活反向搜索
- 输入查询：键入文本搜索以前的命令，匹配内容会高亮
- 导航匹配：再次按 `Ctrl+R` 查看更早的匹配项
- 接受匹配：按 `Tab` 或 `Esc` 接受当前匹配并继续编辑，按 `Enter` 直接执行
- 取消搜索：按 `Ctrl+C` 取消，或在空搜索上按 `Backspace` 退出

### 后台 Bash 命令

Claude Code 支持把 Bash 命令放到后台执行，适合长时间运行的任务。

#### 运行方式

- 直接要求 Claude 在后台运行命令
- 在 Bash 工具调用时按 `Ctrl+B` 将任务切到后台
- Tmux 用户需要按两次 `Ctrl+B`

#### 主要特性

- 输出会被缓冲，Claude 可通过后台任务工具继续读取
- 每个后台任务都有唯一 ID，便于追踪和取回输出
- Claude Code 退出时会自动清理后台任务

如需禁用后台任务，可将环境变量 `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` 设为 `1`。

#### 常见后台任务类型

- 构建工具（webpack、vite、make）
- 包管理器（npm、yarn、pnpm）
- 测试运行器（jest、pytest）
- 开发服务器
- 长时间运行的进程（docker、terraform）

#### `!` 前缀与后台运行结合

```text
! npm test
! git status
! ls -la
```

Bash 模式下：

- 命令及其输出会进入当前会话上下文
- 可以看到实时进度和输出
- 长时间运行的命令同样可以配合 `Ctrl+B` 放到后台
- 支持基于历史的自动补全

### 按键说明

#### 常规控制

| 快捷键 | 描述 | 上下文 |
| --- | --- | --- |
| `Ctrl+C` | 取消当前输入或生成 | 标准中断 |
| `Ctrl+D` | 退出 Claude Code 会话 | EOF 信号 |
| `Ctrl+G` | 在默认文本编辑器中打开 | 编辑当前提示或自定义响应 |
| `Ctrl+L` | 清除终端屏幕 | 保留对话历史 |
| `Ctrl+O` | 切换详细输出 | 显示工具使用和执行情况 |
| `Ctrl+R` | 反向搜索命令历史 | 搜索以前的命令 |
| `Ctrl+V` / `Cmd+V`（iTerm2）/ `Alt+V`（Windows） | 从剪贴板粘贴图像 | 粘贴图像或图像路径 |
| `Ctrl+B` | 后台运行任务 | Bash 命令和代理都可用 |
| `Left/Right arrows` | 在对话框选项卡之间循环 | 菜单和权限对话框导航 |
| `Up/Down arrows` | 浏览命令历史 | 回忆以前的输入 |
| `Esc` + `Esc` | 回退代码 / 对话 | 恢复到之前的状态 |
| `Shift+Tab` / `Alt+M` | 切换权限模式 | 在自动接受、Plan、正常模式之间切换 |
| `Option+P` / `Alt+P` | 切换模型 | 不清空当前提示 |
| `Option+T` / `Alt+T` | 切换扩展思考 | 需先运行 `/terminal-setup` |

#### 文本编辑

| 快捷键 | 描述 | 上下文 |
| --- | --- | --- |
| `Ctrl+K` | 删除到行尾 | 删除内容可后续粘贴 |
| `Ctrl+U` | 删除整行 | 删除内容可后续粘贴 |
| `Ctrl+Y` | 粘贴已删除文本 | 粘贴 `Ctrl+K` 或 `Ctrl+U` 删除的内容 |
| `Alt+Y` | 循环粘贴历史 | 需在 `Ctrl+Y` 之后使用 |
| `Alt+B` | 光标后退一个单词 | macOS 需配置 Meta 键 |
| `Alt+F` | 光标前进一个单词 | macOS 需配置 Meta 键 |

#### 主题和显示

| 快捷键 | 描述 | 上下文 |
| --- | --- | --- |
| `Ctrl+T` | 切换代码块语法高亮 | 仅在 `/theme` 选择器中有效 |

语法高亮仅在 Claude Code 的原生构建中可用。

#### 多行输入

| 方法 | 快捷键 | 上下文 |
| --- | --- | --- |
| 快速转义 | `\` + `Enter` | 在所有终端中工作 |
| macOS 默认 | `Option+Enter` | macOS 默认设置 |
| Shift+Enter | `Shift+Enter` | 在 iTerm2、WezTerm、Ghostty、Kitty 中开箱即用 |
| 控制序列 | `Ctrl+J` | 输入多行换行符 |
| 粘贴模式 | 直接粘贴 | 适合代码块、日志 |

`Shift+Enter` 在 iTerm2、WezTerm、Ghostty 和 Kitty 中无需配置即可工作。对于其他终端（VS Code、Alacritty、Zed、Warp），运行 `/terminal-setup` 安装绑定。

---

## Claude Code 基础用法

本章介绍 Claude Code 日常开发中最常用的核心功能，包括如何与 Claude 对话、读取和修改代码、执行命令、处理文件，以及一些能大幅提升效率的实用技巧。

---

### 基本对话方式

#### 1、交互模式（最常用）

在项目目录下启动 Claude Code，进入交互模式后即可直接输入问题或指令：

~~~
cd /path/to/your/project
claude
~~~

在交互模式中，直接输入自然语言即可，无需任何特殊格式：

~~~
这个项目是做什么的？
~~~

~~~
帮我找出 src/auth.ts 里的 bug
~~~

~~~
给 UserService 添加一个 getUserById 方法
~~~

用 @ 符合可以指定文件，比如我们在 claude-test 目录下有个 test.html。

![image](https://www.runoob.com/wp-content/uploads/2026/01/77197ff7-29c0-4097-9ed7-45e920d1ca1b.png)

我们可以进入 claude-test：

~~~
cd claude-test
~~~

然后询问：

~~~
@test.html 这个文件是干嘛的
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/01/31049423-800b-447b-9e45-c40feec0952c.png)

#### 2、一次性任务模式

如果只需要执行一个任务，不需要进入交互模式，可以直接在命令后面带上任务描述：

~~~
claude "解释一下 package.json 里的 scripts 字段"
~~~

执行完毕后自动退出，适合在 shell 脚本中使用或快速查询。

#### 3、单次查询模式（-p）

使用 `-p` 参数执行一次性查询，Claude 的回答会直接输出到终端，适合与其他命令组合使用：

~~~
claude -p "这段代码有什么问题" < src/utils.ts
~~~

~~~

# 将 Claude 的分析结果保存到文件

claude -p "分析项目依赖的安全风险" > security-report.md
~~~

#### 4、继续上一次对话（-c）

Claude Code 会保存对话历史。使用 `-c` 参数可以在当前目录继续最近一次未完成的对话，无需重新建立上下文：

~~~
claude -c
~~~

---

### 理解和分析代码

#### 1、了解项目整体情况

刚进入一个陌生项目时，可以先让 Claude 做整体分析：

~~~
这个项目的架构是怎样的？
~~~

~~~
项目用了哪些主要的技术和依赖？
~~~

~~~
请介绍一下项目的目录结构
~~~

Claude Code 会自动读取项目文件进行分析， **你不需要手动指定要读取哪些文件**，它会自行判断需要查看哪些内容。

#### 2、分析特定文件或模块

~~~
解释一下 src/middleware/auth.ts 的工作原理
~~~

~~~
UserRepository 类里有哪些方法？分别是做什么的？
~~~

~~~
这个 SQL 查询语句是什么意思？
~~~

#### 3、追踪代码调用链

~~~
当用户登录时，代码执行流程是怎样的？从入口到数据库全部列出来
~~~

~~~
findUserByEmail 这个函数在哪里被调用？
~~~

~~~
这个接口的数据最终存到了哪张数据库表里？
~~~

#### 4、理解复杂逻辑

~~~
这段递归函数是什么意思？能举个例子说明吗？
~~~

~~~
为什么这里要用 useCallback？不用会怎样？
~~~

~~~
这个正则表达式能匹配什么格式的字符串？
~~~

---

### 修改代码

#### 1、添加新功能

描述你需要的功能，Claude 会找到合适的位置并实现：

~~~
在 UserService 里添加一个 updatePassword 方法，需要验证旧密码
~~~

~~~
给登录接口添加请求频率限制，同一 IP 每分钟最多 5 次
~~~

~~~
创建一个 formatCurrency 工具函数，支持传入货币代码参数
~~~

#### 2、修复 Bug

描述问题现象，Claude 会定位原因并给出修复方案：

~~~
用户登出后刷新页面仍然显示已登录状态，帮我找出原因并修复
~~~

~~~
这个函数在输入为空数组时会报错，修复它
~~~

~~~
控制台报错 "Cannot read properties of undefined (reading 'map')"，帮我定位并修复
~~~

#### 3、重构代码

~~~
把这个 500 行的组件拆分成更小的子组件
~~~

~~~
将 callback 风格的异步代码改为 async/await
~~~

~~~
这几个函数里有大量重复代码，帮我提取成通用函数
~~~

#### 4、审查修改内容

Claude Code 每次修改文件前都会展示 diff，供你审查。你可以在 Claude 提出修改方案后进行讨论：

~~~
先不要改代码，只告诉我你打算怎么修改
~~~

~~~
你刚才的修改有个问题：没有处理并发情况，重新来过
~~~

~~~
这个方案可以，但把函数名改成 validateUserCredentials
~~~

> Claude Code 在执行任何文件修改前都会展示修改内容并请求你的确认。你可以选择 **接受（yes）**、 **拒绝（no）**，或者在确认前继续讨论。不要觉得必须一次接受所有修改——先看，觉得没问题再确认。

---

### 执行命令和测试

#### 1、让 Claude 运行命令

Claude Code 可以直接在终端执行命令，你可以用自然语言描述要做什么：

~~~
运行测试，看看有没有失败的
~~~

~~~
安装 dayjs 这个依赖
~~~

~~~
把项目构建一下，看看有没有报错
~~~

Claude 会根据项目配置（如 `package.json` 中的 scripts 或 `CLAUDE.md` 中的命令）选择正确的命令执行。

#### 2、运行特定测试

~~~
运行 auth 模块的测试
~~~

~~~
运行所有测试并生成覆盖率报告
~~~

~~~
这个测试为什么失败了？帮我修复
~~~

#### 3、查看命令输出

Claude 会将命令输出作为上下文，直接根据输出内容给出下一步建议：

~~~
运行 npm run build，如果有报错帮我修复
~~~

~~~
跑一下 lint，把所有警告都修复掉
~~~

---

### 文件操作

#### 1、创建新文件

~~~
创建 src/utils/date.ts，实现日期格式化相关的工具函数
~~~

~~~
新建一个 Docker Compose 配置文件，包含 PostgreSQL 和 Redis 服务
~~~

~~~
创建 .github/workflows/ci.yml，配置 GitHub Actions 自动运行测试
~~~

#### 2、查找文件和内容

~~~
项目里有没有处理文件上传的代码？在哪里？
~~~

~~~
哪些地方用到了 localStorage？
~~~

~~~
找出所有使用了 console.log 的文件
~~~

#### 3、批量修改

~~~
把所有文件里的 http://api.example.com 替换成 https://api.example.com
~~~

~~~
把 src/components/ 下所有组件的 PropTypes 改为 TypeScript 类型定义
~~~

~~~
给 src/api/ 目录下所有接口函数添加 JSDoc 注释
~~~

---

### Git 操作

Claude Code 深度集成了 Git，可以用自然语言完成几乎所有 Git 操作。

#### 1、查看变更

~~~
我改了哪些文件？
~~~

~~~
这次的改动有哪些内容？
~~~

~~~
最近 10 次提交都改了什么？
~~~

#### 2、提交代码

~~~
提交这次的改动
~~~

~~~
把这次的修改提交，commit 信息说明修复了登录验证的 bug
~~~

Claude 会根据实际改动内容自动生成符合规范的 commit 信息，也可以用快捷命令：

~~~
claude commit
~~~

#### 3、分支管理

~~~
新建一个 feature/user-profile 分支
~~~

~~~
切换到 develop 分支
~~~

~~~
把 main 分支的最新改动合并进来
~~~

#### 4、处理合并冲突

~~~
帮我解决合并冲突
~~~

~~~
这个文件有冲突，帮我看看哪个版本是正确的
~~~

---

### 上下文管理技巧

#### 1、手动指定文件

使用 `@文件路径` 语法可以明确告诉 Claude 需要参考哪个文件，避免它自己去猜：

~~~
参考 @src/types/user.ts 里的类型定义，给 UserService 添加类型注解
~~~

~~~
对照 @docs/api-spec.md 里的规范，检查接口实现是否符合要求
~~~

#### 2、清除上下文

当对话进行了很长时间，或者切换到了完全不同的任务时，建议清除上下文重新开始：

~~~
/clear
~~~

清除后 Claude 的"记忆"回到初始状态，新会话不受之前对话内容的影响。

#### 3、压缩上下文

当感觉 Claude 开始"遗忘"之前的内容，或者对话变得很长时，可以压缩上下文：

~~~
/compact
~~~

这会让 Claude 将当前对话内容总结为精简摘要，释放上下文空间，同时保留关键信息继续工作。

#### 4、合理拆分任务

复杂任务不要一次性全部描述，拆成几个步骤逐步完成效果更好：

❌ 不好的做法：一次性丢给 Claude 太多要求：

~~~
"帮我重构整个用户模块，包括添加类型注解、拆分文件、
优化数据库查询、添加缓存、写测试、更新文档"
~~~

✅ 好的做法：按逻辑顺序拆分成独立步骤：

~~~
第一步："分析 src/user/ 目录的现有结构，告诉我有哪些问题"
第二步："先给所有函数添加 TypeScript 类型注解"
第三步："把超过 200 行的文件拆分成更小的模块"
第四步："优化数据库查询，添加必要的索引"
~~~

---

### 常用斜杠命令

在交互模式中，输入 `/` 会弹出所有可用命令的列表。以下是最常用的斜杠命令：

| 命令 | 功能 | 使用场景 |
| --- | --- | --- |
| `/help` | 查看所有可用命令和功能说明 | 不知道有哪些功能时 |
| `/clear` | 清除对话历史，开始全新会话 | 切换到新任务时 |
| `/compact` | 压缩当前对话上下文，释放空间 | 对话很长时 Claude 开始遗忘 |
| `/init` | 分析项目并生成 CLAUDE.md 文件 | 首次在新项目中使用 |
| `/resume` | 从历史中选择并恢复之前的对话 | 继续昨天未完成的任务 |
| `/undo` | 撤销上一次文件修改 | Claude 的修改效果不对时 |
| `/redo` | 重做被撤销的修改 | 撤销后发现还是原来的好 |
| `/login` | 登录或切换账号 | 需要切换到另一个账号时 |
| `/config` | 查看和修改配置 | 调整 Claude Code 行为设置 |
| `/cost` | 查看本次会话的 Token 用量 | 使用 API 账号时关注成本 |

---

### 实用技巧

#### 1、让 Claude 先分析，再动手

遇到复杂问题，先让 Claude 说明思路，确认方向正确后再执行：

~~~
不要修改代码，先分析登录失败的可能原因，列出你的排查思路
~~~

~~~
你打算怎么实现这个功能？先描述方案，我确认后再写代码
~~~

#### 2、提供错误信息和日志

遇到报错时，直接把错误信息粘贴到对话中，Claude 会根据错误内容定位问题：

~~~
运行时报了这个错误，帮我分析原因：
TypeError: Cannot read properties of null (reading 'userId')
    at getUserProfile (src/services/user.ts:42:18)
    at async ProfileController.getProfile (src/controllers/profile.ts:15:20)
~~~

#### 3、用截图描述 UI 问题

Claude Code 支持多模态输入，可以将截图直接粘贴到对话中，描述 UI 相关的问题：

~~~
[粘贴截图] 这个按钮点击后没有反应，但控制台没有报错，帮我排查
~~~

~~~
[粘贴设计稿截图] 按照这个设计实现 Header 组件
~~~

#### 4、善用"继续"

如果 Claude 的回答被截断，或者你觉得它还没说完，直接说"继续"即可：

~~~
继续
~~~

~~~
还有吗？
~~~

~~~
把剩下的部分也实现了
~~~

#### 5、要求解释修改内容

对于重要的代码修改，可以要求 Claude 解释每处改动的原因：

~~~
修改完之后给我解释一下每处改动的目的
~~~

~~~
这里为什么要用 Promise.allSettled 而不是 Promise.all？
~~~

#### 6、限制 Claude 的操作范围

当你不希望 Claude 随意改动其他文件时，可以明确限定范围：

~~~
只修改 src/auth.ts 这一个文件，不要改其他地方
~~~

~~~
只读取代码不要修改，帮我分析这个模块的性能瓶颈
~~~

---

### 常见工作流示例

#### 工作流一：快速了解新项目

### 实例

~~~
# 第一步：启动并了解项目
> 这个项目是做什么的？用了哪些技术？

# 第二步：了解目录结构
> 介绍一下项目的目录结构，重点介绍核心模块

# 第三步：了解如何运行
> 怎么在本地把这个项目跑起来？

# 第四步：生成项目说明文件
> 帮我生成一个 CLAUDE.md 文件，总结项目的关键信息
~~~

#### 工作流二：修复一个 Bug

### 实例

~~~
# 第一步：描述问题
> 用户反馈：修改个人信息后，页面顶部的用户名没有实时更新

# 第二步：让 Claude 分析原因
> 先分析一下可能的原因，不要修改代码

# 第三步：确认方案后修复
> 你说的第二种原因看起来更可能，按那个思路修复

# 第四步：验证修复
> 运行相关的测试确认修复有效
~~~

#### 工作流三：添加一个新功能

### 实例

~~~
# 第一步：描述需求
> 需要给订单列表添加按状态筛选的功能，状态有：待付款、已付款、已发货、已完成

# 第二步：了解现有代码
> 先看看订单相关的现有代码结构

# 第三步：实现功能
> 在不影响现有功能的前提下实现筛选功能

# 第四步：补充测试
> 为这个新功能写测试用例

# 第五步：提交代码
> 把这次的改动提交
~~~

#### 工作流四：代码审查

~~~

# 审查特定文件

> 帮我审查 src/payment/processor.ts 这个文件，
> 重点关注错误处理、边界情况和安全风险

# 审查 git 改动

> 审查我这次的所有改动，看看有没有明显的问题

# 针对性检查

> 这段代码有没有 SQL 注入的风险？
~~~

---

### 注意事项

- **关于文件修改**：Claude Code 会在修改前展示 diff 并请求确认，不要无脑全部接受，尤其是涉及核心逻辑的修改。
- **关于敏感文件**：可在 `CLAUDE.md` 或 `.claude/settings.json` 中明确标注不允许 Claude 读取或修改的文件（如 `.env`、密钥文件）。
- **关于大型任务**：不要在一个对话中堆积太多未完成任务，完成一个再开始下一个，必要时用 `/clear` 重新开始。
- **关于代码质量**：Claude 生成的代码不一定是最优解，重要模块建议再做一轮 code review。

---

## Claude Code 控制与模式

Claude Code 交互模式的核心能力包括 **键盘快捷键**、**Vim 编辑模式**、**命令历史管理** 和 **后台 Bash 命令运行**，下面是精炼后的要点。

---

### 一、键盘快捷键

快捷键效果会因终端和系统而异。macOS 用户通常需要把 Option 键配置为 Meta；按 `?` 可以查看当前环境可用快捷键。

| 分类 | 快捷键 / 操作 | 核心功能 |
| --- | --- | --- |
| **常规控制** | `Ctrl+C` | 取消当前输入 / 生成 |
|  | `Ctrl+D` | 退出会话 |
|  | `Ctrl+L` | 清屏（保留历史） |
|  | `Ctrl+O` | 切换详细输出 |
|  | `Ctrl+R` | 反向搜索命令历史 |
|  | `Option+P` / `Alt+P` | 切换模型 |
| **文本编辑** | `Ctrl+K` / `Ctrl+U` | 删除到行尾 / 删除整行 |
|  | `Ctrl+Y` | 粘贴删除内容 |
|  | `Alt+B` / `Alt+F` | 光标按单词前后移动 |
| **主题显示** | `Ctrl+T` | 切换代码块语法高亮 |
| **多行输入** | `\` + `Enter` / `Shift+Enter` | 换行输入 |
|  | `Ctrl+J` | 多行换行符 |
| **快速命令** | `/` 开头 | 触发斜杠命令 |
|  | `!` 开头 | 直接运行 Bash 命令 |
|  | `@` | 触发文件路径自动补全 |

---

### 二、Vim 编辑器模式

通过 `/vim` 临时启用，或用 `/config` 做永久配置。

**1、模式切换**

| 命令 | 操作 | 触发场景 |
| --- | --- | --- |
| `Esc` | 进入 NORMAL 模式 | INSERT 模式下 |
| `i` / `I` | 光标前 / 行首插入 | NORMAL 模式下 |
| `a` / `A` | 光标后 / 行尾插入 | NORMAL 模式下 |
| `o` / `O` | 下方 / 上方新开一行 | NORMAL 模式下 |

**2、核心操作（NORMAL 模式）**

- **导航**：`h/j/k/l` 上下左右移动；`w/e/b` 按单词跳转；`gg/G` 跳转到输入开头 / 结尾
- **编辑**：`dd` 删除行；`yy` 复制行；`p/P` 光标后 / 前粘贴；`>>/<<` 缩进 / 取消缩进
- **文本对象**：配合 `d/c/y` 使用，例如 `di"` 删除双引号内内容，`aw` 选中整个单词

---

### 三、命令历史

1. **存储规则**：按工作目录独立存储，`/clear` 可清除当前会话历史
2. **反向搜索（Ctrl+R）**
   - 输入关键词后匹配历史命令并高亮显示
   - 重复按 `Ctrl+R` 可循环查看更早匹配项
   - `Tab` / `Esc` 接受匹配并继续编辑，`Enter` 直接执行，`Ctrl+C` 取消搜索

### 四、后台 Bash 命令

支持异步运行耗时命令，不阻塞当前会话。

1. **运行方式**
   - 让 Claude 在后台运行命令
   - 执行命令时按 `Ctrl+B`（Tmux 用户按两次）
2. **Bash 模式（`!` 前缀）**
   - 输入 `! 命令` 可直接执行 Bash 命令，例如 `! npm test`
   - 命令及输出会进入会话上下文，也支持 `Ctrl+B` 后台运行
3. **适用场景**
   - 构建工具、包管理、测试运行、开发服务器等长时间运行进程

---

## Claude Code 斜杠 / 命令

斜杠命令是 Claude Code 交互式会话里的快捷控制入口。输入 `/` 开头的指令，就能快速调用功能、管理会话和定制工作流。

![image](https://www.runoob.com/wp-content/uploads/2026/01/23e5a097-f781-4f4b-8597-35d18a7aae29.png)

查看所有可用命令：

```text
/help
```

`/help` 会列出所有内置命令、项目自定义命令，以及已连接 MCP 服务器暴露的命令。

命令类型：

| 类型 | 触发方式 | 来源 | 存储位置 | 适用场景 |
| --- | --- | --- | --- | --- |
| **内置命令** | `/command` | 随 Claude Code 安装 | 内部实现 | 会话管理、配置、集成等系统操作 |
| **内置 Skills** | `/skill-name` | 随 Claude Code 附带 | 内部实现 | 代码审查、调试、批处理等 AI 工作流 |
| **项目自定义 Skills** | `/skill-name` | 用户创建目录 + `SKILL.md` | `.claude/skills/<名称>/SKILL.md` | 团队共享的可复用工作流 |
| **项目自定义命令** | `/command` | 用户创建 `.md` 文件 | `.claude/commands/` | 团队共享的重复工作流 |
| **个人自定义 Skills** | `/skill-name` | 用户创建目录 + `SKILL.md` | `~/.claude/skills/<名称>/SKILL.md` | 跨项目通用的个人工作流 |
| **个人自定义命令** | `/command` | 用户创建 `.md` 文件 | `~/.claude/commands/` | 跨项目通用的个人工作流 |
| **MCP 命令** | `/mcp__服务器__命令` | MCP 服务器自动暴露 | 由 MCP 服务器提供 | 调用外部工具 |
| **`!` Shell 命令** | `!bash命令` | 直接执行 | — | 绕过 AI 处理，直接运行终端命令 |

**命令优先级（同名时）**：企业级 > 个人级 > 项目级 > 内置。

Skills 和 Commands 都通过 `/` 触发，但 Skills 是更新一代的格式，支持多文件组织、自动触发和更丰富的 frontmatter 控制；Commands 为旧格式，但仍兼容。

---

### 一、常用内置斜杠命令

内置命令是 Claude Code 自带的核心功能，直接输入就能用。新手优先掌握这几类高频命令：

#### 基础操作命令

| 命令 | 用途 | 新手示例 |
| --- | --- | --- |
| `/help` | 查看所有可用命令及说明（含 MCP 命令） | 输入 `/help` 快速查命令清单 |
| `/exit` | 退出当前交互式会话 | 不想继续聊了，输 `/exit` 直接走 |
| `/clear` | 清除当前对话历史 | 会话太乱？`/clear` 一键清空 |

例如我们使用 /help 命令查看帮助信息，Tab 键可以切换菜单，Esc 退出：

![image](https://www.runoob.com/wp-content/uploads/2026/01/93013ac4-eb3c-4b2c-8d8a-86edb32d778e.png)

#### 会话管理命令

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/clear` | `/clear` | 清除全部对话历史，开始全新会话 | `/clear` | 不可恢复，建议先 `/export` |
| `/compact` | `/compact [侧重点]` | 智能压缩历史对话，保留语义，缩小 Token 占用 | `/compact 保留数据库设计部分` | 与 `/clear` 区别：保留语义摘要而非完全清空 |
| `/resume` | `/resume [id或名称]` | 恢复历史会话；不带参数列出所有可恢复会话 | `/resume task-hub-backend` | 配合 `/rename` 效果更佳 |
| `/rewind` | `/rewind` | 撤销最近操作，含文件修改和对话内容，恢复到上一检查点；新版新增"仅回退代码"选项，可保留对话历史 | 按 `Esc` 两次呼出回退菜单 | 适合 Agent 执行结果不满意时回退 |
| `/rename` | `/rename <名称>` | 为当前会话命名，便于后续 `/resume` 找回 | `/rename auth-module-refactor` | 建议命名规范：`功能-操作` |
| `/export` | `/export [文件名]` | 导出对话为文件；不指定文件名则复制到剪贴板 | `/export debug-session.md` | 支持 Markdown 格式 |
| `/copy` NEW | `/copy` | 将 Claude 最近一次回复复制到剪贴板；若含多个代码块，会弹出交互式选择器 | `/copy` | 比手动框选文本快得多 |
| `/btw` NEW | `/btw <问题>` | 在不影响主任务上下文的情况下，快速插问一个临时问题，回答不会污染主对话流 | `/btw Redis 的默认端口是多少？` | 适合临时查阅而不想干扰当前任务 |
| `/exit` | `/exit` | 安全退出 Claude Code REPL | `/exit` | — |

#### 上下文与记忆

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/context` | `/context` | 可视化上下文窗口使用量，显示各部分（历史/文件/系统提示/Skills）占比 | `/context` | 接近上限时应执行 `/compact` 或 `/clear` |
| `/memory` | `/memory` | 打开编辑器修改 `CLAUDE.md`（项目级与全局级），跨会话持久化项目约定 | `/memory` | 修改后对后续所有会话生效 |
| `/add-dir` | `/add-dir <路径>` | 将额外目录纳入工作范围，适合 monorepo 或跨目录操作 | `/add-dir ../packages/shared` | 可多次调用添加多个目录 |
| `/todos` | `/todos` | 列出当前会话中 Claude 记录的 TODO 事项 | `/todos` | — |
| `/diff` NEW | `/diff` | 查看本次会话中所有文件变更的 diff 摘要，便于回顾 Claude 做了哪些修改 | `/diff` | 与 `/rewind` 配合：先 `/diff` 确认变更，再决定是否回滚 |

#### 项目与配置

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/init` | `/init` | 分析项目结构，自动生成 `CLAUDE.md`，写入技术栈/构建命令/代码规范 | `/init` | 新项目必备第一步 |
| `/config` | `/config` | 以交互式界面管理 Claude Code 全局配置 | `/config` | — |
| `/status` | `/status` | 打开设置状态页，查看当前配置概览 | `/status` | — |
| `/hooks` | `/hooks` | 交互式配置生命周期 Hook（如提交前 lint、保存后格式化） | `/hooks` | 支持 PreToolUse / PostToolUse / Notification / Stop |
| `/permissions` | `/permissions` | 查看或更新工具权限（读文件/写文件/执行命令等） | `/permissions` | 限制权限可提升操作安全性 |
| `/sandbox` | `/sandbox` | 启用隔离沙盒环境执行 Bash 命令，防止影响宿主系统 | `/sandbox` | 适合测试不确定的脚本 |
| `/doctor` | `/doctor` | 诊断安装状态，检查依赖/配置/网络连接是否正常 | `/doctor` | 遇到异常时首先执行 |

#### 模型与输出

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/model` | `/model [模型名]` | 切换底层 AI 模型；不带参数显示交互式选择菜单 | `/model claude-haiku-4-5-20251001` | 见下方模型对照表 |
| `/plan` | `/plan [任务描述]` | 进入计划模式，Claude 先输出执行计划供确认再行动 | `/plan 重构认证模块并补充测试` | 适合复杂多步骤任务，避免走偏 |
| `/output-style` | `/output-style` | 设置响应格式（代码优先/说明优先/简洁模式等） | `/output-style` | — |
| `/theme` | `/theme` | 切换终端界面颜色方案 | `/theme` | — |
| `/statusline` | `/statusline` | 定制终端底部状态栏显示内容 | `/statusline` | — |
| `/vim` | `/vim` | 启用 Vim 键位绑定 | `/vim` | 适合 Vim 用户 |
| `/terminal-setup` | `/terminal-setup` | 安装 `Shift+Enter` 换行绑定，方便输入多行 Prompt | `/terminal-setup` | 首次使用建议配置 |

**可用模型对照表（2026）：**

| 模型标识 | 定位 | 适用场景 |
| --- | --- | --- |
| `claude-sonnet-4-6` | 均衡型（默认） | 日常开发任务，Pro/Max5 用户首选 |
| `claude-haiku-4-5-20251001` | 快速轻量 | 简单查询、自定义命令中节省 Token |
| `claude-opus-4-6` | 高能力旗舰 | 复杂架构规划、高难度代码推理，Max20 用户可作默认 |

#### 代码与工具

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/review` | `/review` | 对最近修改的代码进行结构化 Code Review，按优先级输出改进建议 | `/review` | — |
| `/security-review` | `/security-review` | 针对当前改动进行安全专项审查（SQL 注入/XSS/敏感信息泄露等） | `/security-review` | 上线前必执行 |
| `/pr-comments` | `/pr-comments` | 拉取当前分支关联 PR 的审查意见，在终端中直接处理 | `/pr-comments` | 需配置 GitHub 集成 |
| `/install-github-app` | `/install-github-app` | 配置 GitHub Actions 集成，Claude 自动 Review 新 PR | `/install-github-app` | 生成 `.github/workflows/claude-code-review.yml` |
| `/agents` | `/agents` | 查看、创建和管理专项子代理（DB 专家/安全专家/测试专家等） | `/agents` | — |
| `/bashes` | `/bashes` | 查看当前后台运行的 Bash 进程，可中断或查看输出 | `/bashes` | — |
| `/skills` NEW | `/skills` | 列出所有可用的 Skills（内置 + 项目级 + 个人级），含描述和触发方式 | `/skills` | 上下文预算不足时部分 Skills 会被排除，通过此命令可确认加载状态 |

#### 集成与扩展

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/mcp` | `/mcp` | 管理 MCP 服务器连接（连接/断开/查看可用工具） | `/mcp` | 见第七节 MCP 命令详解 |
| `/ide` | `/ide` | 配置与 VSCode / JetBrains 等 IDE 的联动集成 | `/ide` | — |
| `/plugin` | `/plugin [子命令]` | 管理 Claude Code 插件：安装、卸载、更新、浏览市场 | `/plugin install typescript-lsp` | 官方插件市场含 36 款精选插件 |
| `/teleport` | `/teleport` | 将 claude.ai 网页端发起的会话迁移到本地终端继续（云端 → 本地方向） | `/teleport` | 需安装 GitHub App，仅支持 GitHub 托管仓库 |

**`/plugin` 子命令详情：**

| 子命令 | 功能 |
| --- | --- |
| `/plugin install <名称>` | 安装指定插件 |
| `/plugin list` | 列出所有已安装插件 |
| `/plugin update` | 更新所有已安装插件 |
| `/plugin marketplace` | 浏览官方插件市场 |
| `/plugin uninstall <名称>` | 卸载指定插件 |

#### 统计与账户

| 命令 | 语法 | 功能说明 | 使用示例 | 注意事项 |
| --- | --- | --- | --- | --- |
| `/cost` | `/cost` | 显示当前会话的 Token 消耗量与预估费用 | `/cost` | API 计费用户可见；订阅制用户 Token 已含在套餐内 |
| `/usage` | `/usage` | 查看当前套餐用量、剩余配额和速率限制状态 | `/usage` | — |
| `/stats` | `/stats` | 图表形式展示历史使用数据（每日 Token / 会话数量） | `/stats` | — |
| `/release-notes` | `/release-notes` | 查看 Claude Code 最新版本变更记录 | `/release-notes` | — |
| `/bug` | `/bug` | 将当前会话上下文打包，向 Anthropic 提交 Bug 报告 | `/bug` | — |
| `/login` | `/login` | 登录或切换 Anthropic 账户 | `/login` | 适合多账号开发者 |
| `/logout` | `/logout` | 注销当前账户 | `/logout` | — |
| `/privacy-settings` | `/privacy-settings` | 管理数据使用偏好（是否允许对话用于模型训练等） | `/privacy-settings` | — |
| `/remote-env` | `/remote-env` | 配置远程会话的环境变量 | `/remote-env` | — |

---

### 二、内置 Skills

内置 Skills 是随 Claude Code 附带的预置 AI 工作流。它们与内置命令不同：前者会加载提示词并由 Claude 推理执行，适合复杂分析任务。

**区别：内置命令 vs 内置 Skills**

- 内置命令（如 `/clear`、`/compact`）执行固定逻辑，不依赖 AI 推理，速度快。
- 内置 Skills（如 `/debug`、`/simplify`）会加载详细提示词，结果更丰富。

| 命令 | 功能说明 | 适用场景 |
| --- | --- | --- |
| `/simplify` | 将指定代码或文本简化，提升可读性 | 重构冗长函数、简化过度工程化代码 |
| `/debug` | 对报错信息或异常行为做系统化根因分析 | 定位难以复现的 bug |
| `/batch` | 对多个文件或目标并行执行相同任务 | 大批量重复性改动 |
| `/loop` | 在循环中反复执行某个操作直到满足退出条件 | 自动化重试和持续修复 |
| `/claude-api` | 生成调用 Anthropic API 的示例代码 | 搭建 Claude API 集成原型 |

---

### 三、命令前缀语法

| 前缀 | 语法 | 功能说明 | 示例 | 对比 |
| --- | --- | --- | --- | --- |
| `/` | `/command` | 触发内置命令、内置 Skills 或自定义 Skills / Commands | `/review` | — |
| `!` | `!<bash命令>` | 直接执行 Shell 命令，绕过 AI 处理 | `!git status` | 比“帮我查看 git 状态”更省 Token |
| `@` | `@<文件路径>` | 将文件内容注入当前上下文 | `@src/api/users.ts` | 比手动粘贴代码更精准 |

**`!` 前缀使用示例：**

![image](https://www.runoob.com/wp-content/uploads/2026/01/4d37b1ea-521b-49b5-badf-85636f7e756d.png)

```text
! ls
! git log --oneline -10
! npm test -- --coverage
! cat logs/error.log | tail -50
```

`@` 前缀使用示例：

![image](https://www.runoob.com/wp-content/uploads/2026/01/ec825797-0ed6-43ba-b694-c9bdc16d32e9.png)

```text
对比 @src/auth/v1.ts 和 @src/auth/v2.ts 的实现差异
审查 @src/api/users.ts 中的错误处理逻辑是否完善
```

---

### 四、自定义 Skills（新推荐格式）

Skills 是自定义命令的新一代格式，推荐优先使用。

#### Skills vs Commands 对比

| 特性 | Skills（推荐） | Commands（旧格式） |
| --- | --- | --- |
| 存储路径 | `.claude/skills/<名称>/SKILL.md` | `.claude/commands/<名称>.md` |
| 触发方式 | `/名称` 手动触发，或由 Claude 自动触发 | `/名称` 手动触发 |
| 多文件支持 | 支持附带模板、示例、脚本 | 单文件 |
| Claude 自动触发 | 支持 | 不支持 |
| Agent Skills 标准 | 符合开放标准 | 不符合 |

#### 创建 Skill 步骤

##### 步骤 1：创建目录结构

- **项目 Skill**：`.claude/skills/<名称>/`
- **个人 Skill**：`~/.claude/skills/<名称>/`

```bash
# 创建项目级 Skill
mkdir -p .claude/skills/optimize

# 创建个人级 Skill
mkdir -p ~/.claude/skills/fix-bug
```

##### 步骤 2：编写 `SKILL.md`

```md
---
name: optimize
description: 分析代码性能瓶颈，给出优化建议。当用户提到性能问题时自动触发。
allowed-tools: Read, Grep, Glob
argument-hint: [目标文件或目录]
model: claude-sonnet-4-6
---

分析以下代码的性能瓶颈，给出具体的优化建议，优先考虑时间复杂度和内存占用：
$ARGUMENTS
```

使用时输入：

```text
/optimize src/utils/data-processor.ts
```

#### 子目录命名空间组织

```text
.claude/skills/
├── optimize/            → /optimize
│   ├── SKILL.md
│   └── examples/
├── debug/               → /debug
│   ├── SKILL.md
│   └── scripts/
└── commit/              → /commit
    └── SKILL.md
```

---

### 五、自定义斜杠命令（旧格式，兼容）

如果你有重复使用的提示词，也可以做成自定义命令。

**命令文件存储位置：**

| 作用域 | 存储路径 | 共享方式 | 适用场景 |
| --- | --- | --- | --- |
| 项目级 | `.claude/commands/<命令名>.md` | 纳入 git，团队共享 | 项目专属工作流 |
| 个人级 | `~/.claude/commands/<命令名>.md` | 本地私有 | 通用开发习惯 |

**参数语法：**

| 语法 | 说明 | 命令文件示例 | 调用示例 |
| --- | --- | --- | --- |
| `$ARGUMENTS` | 捕获命令名之后的所有内容 | `分析并修复：$ARGUMENTS` | `/fix 登录接口返回 500，见 logs/error.log` |
| `$1` `$2` | 按空格分割的位置参数 | `修复 Issue #$1，优先级 $2` | `/fix-issue 42 high` |
| ``!`cmd` `` | 执行 Shell 命令并将输出嵌入 Prompt | ``!`git diff HEAD` `` | 自动展开为命令执行结果 |
| `@文件路径` | 将文件内容注入 Prompt | `审查 @src/utils/helpers.ts` | 自动读取文件内容 |

#### 核心原理

自定义命令本质上是 Markdown 文本文件：文件名就是命令名，文件内容就是要执行的提示词。

#### 创建步骤

##### 步骤 1：创建命令目录

- **项目命令**：`.claude/commands/`
- **个人命令**：`~/.claude/commands/`

```bash
mkdir -p .claude/commands
```

##### 步骤 2：写 Markdown 命令文件

```bash
echo "分析这段代码的性能瓶颈，给出具体的优化建议，优先考虑时间复杂度和内存占用：" > .claude/commands/optimize.md
```

#### 给命令加参数

创建 `.claude/commands/fix-issue.md`：

```md
修复 Issue #$ARGUMENTS，要求：
1. 符合项目编码规范
2. 附上测试用例
3. 说明修复思路
```

使用方式：

```text
/fix-issue 123
```

---

### 六、Frontmatter 字段参考

自定义命令文件和 Skills 顶部都可以添加 YAML Frontmatter：

```yaml
---
name: commit
allowed-tools: Bash(git add:*), Bash(git commit:*), Read, Edit
argument-hint: [commit-message]
description: 检查代码质量后提交
model: claude-haiku-4-5-20251001
context: fork
agent: general-purpose
disable-model-invocation: false
user-invocable: true
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate.sh"
          once: true
---
```

#### 字段说明

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `name` | string | Skills 必填 | 定义触发命令名（`/name`）；命令文件默认取文件名 |
| `allowed-tools` | string | 否 | 命令可使用的工具，匹配的工具无需每次人工确认 |
| `argument-hint` | string | 否 | 在 `/help` 列表中显示的参数格式提示，如 `[message]` |
| `description` | string | 否 | 命令功能描述，展示在 `/help` 和 `/skills` 列表中；Skills 中还用于让 Claude 判断是否自动触发 |
| `model` | string | 否 | 强制指定模型；简单任务用 Haiku 可降低成本 |
| `context` | enum | 否 | `fork`：以独立子代理运行；`inline`：在当前会话内运行 |
| `agent` | string | 否 | `context: fork` 时指定代理类型 |
| `disable-model-invocation` | bool | 否 | 为 `true` 时禁止通过 Skill 工具编程调用此命令 |
| `user-invocable` NEW | bool | 否 | 为 `false` 时只允许 Claude 自动触发，用户无法手动 `/调用` |
| `hooks` | object | 否 | 命令执行过程中的生命周期 Hook |

#### `allowed-tools` 写法示例

| 写法 | 含义 |
| --- | --- |
| `Read` | 允许读取任意文件 |
| `Read(src/*)` | 只允许读取 `src/` 目录 |
| `Bash(git *:*)` | 允许所有 git 子命令 |
| `Bash(npm test:*)` | 只允许运行 `npm test` |
| `Read, Grep, Glob` | 组合多个工具 |

#### Hook 触发时机

| 时机 | 说明 | 典型用途 |
| --- | --- | --- |
| `PreToolUse` | 工具调用前 | 提交前运行 lint、写文件前备份 |
| `PostToolUse` | 工具调用后 | 修改后自动格式化、生成变更日志 |
| `Notification` | Claude 发出通知时 | 发送 Slack 消息、桌面通知 |
| `Stop` | Claude 完成响应时 | 记录日志、触发下游任务 |

---

### 七、插件与 MCP 命令

除了自己写，还能通过 **插件** 和 **MCP 服务器**获取更多扩展命令。

命名格式：

```text
/mcp__<服务器名>__<命令名> [参数]
```

#### 常用 MCP 命令示例

| MCP 服务器 | 命令示例 | 功能 |
| --- | --- | --- |
| GitHub | `/mcp__github__list_prs` | 列出当前仓库的 PR |
| GitHub | `/mcp__github__create_issue "Bug标题" high` | 创建 Issue |
| PostgreSQL | `/mcp__postgres__query "SELECT * FROM users LIMIT 10"` | 执行 SQL 查询 |
| Slack | `/mcp__slack__send_message "#dev" "部署完成"` | 发送 Slack 消息 |
| Jira | `/mcp__jira__create_issue "修复登录Bug" high` | 创建 Jira 工单 |
| Figma | `/mcp__figma__get_component "Button"` | 获取 Figma 组件信息 |

查看所有可用 MCP 命令：`/help`（MCP 命令在帮助列表底部单独分组展示）

#### 1. 插件命令

安装 Claude Code 插件后，会自动新增插件专属命令，格式通常是：

```text
/plugin-name:command-name
```

比如安装 Git 插件后，可能会有 `/git:commit` 命令，一键生成规范的 commit 信息。

#### 2. MCP 命令

MCP（模型上下文协议）服务器可以把外部工具（比如 GitHub、Jira）的功能变成斜杠命令，格式：

```text
/mcp__<服务器名>__<功能名> [参数]
```

示例：

```text
/mcp__github__list_prs
/mcp__jira__create_issue "登录按钮失效" high
```

---

### 八、实用命令模板库

#### 模板概览

| 模板名 | 文件路径 | 推荐模型 | 功能 |
| --- | --- | --- | --- |
| 智能 Git 提交 | `.claude/commands/commit.md` | Haiku | 检查调试残留后提交 |
| PR 代码审查 | `.claude/commands/pr-review.md` | Sonnet | 全面分级审查 |
| 智能测试运行 | `.claude/commands/test.md` | Sonnet | 运行并自动修复失败用例 |
| 安全漏洞扫描 | `.claude/commands/security-scan.md` | Opus | OWASP Top 10 扫描 |
| API 文档生成 | `.claude/commands/api-docs.md` | Sonnet | 自动生成 REST API 文档 |
| 代码重构 | `.claude/commands/refactor.md` | Sonnet | 可读性 / DRY / 类型安全重构 |
| 文档链接检查 | `.claude/commands/check-links.md` | Haiku | 批量验证文档中的超链接有效性 |

智能 Git 提交：

```md
---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*), Bash(git commit:*)
argument-hint: [commit-message]
description: 检查代码质量后提交
model: claude-haiku-4-5-20251001
---

## 暂存区内容

!`git diff --cached`

提交前检查以下问题，发现问题则报告并询问是否继续：
1. 遗留的 `console.log` / `print` 调试语句
2. 未处理的 `TODO` / `FIXME` 注释
3. 大段被注释掉的代码
4. 测试文件中的 `.only` / `.skip` 标记

若无问题，使用以下信息提交：$ARGUMENTS
```

PR 代码审查：

```md
---
allowed-tools: Read, Grep, Glob, Bash(git diff:*), Bash(git log:*)
description: 全面的 PR 代码审查
---

## 变更文件

!`git diff --name-only HEAD~1`

## 详细差异

!`git diff HEAD~1`

## 近期提交

!`git log --oneline -5`

按 Critical / Major / Minor 三级优先级输出审查结果，涵盖：
代码逻辑与可读性、安全漏洞、性能隐患（N+1 查询）、测试覆盖率、文档完整性
```

智能测试运行：

```md
---
allowed-tools: Bash, Read, Edit
argument-hint: [test-pattern]
description: 运行测试并自动修复失败用例
---

运行匹配 "$ARGUMENTS" 的测试：
1. 自动检测测试框架（Jest / pytest / Go test）
2. 运行指定模式的测试
3. 若有失败，分析根因并修复代码
4. 重新运行验证修复

不提供参数则运行全量测试并输出覆盖率报告。
```

安全漏洞扫描：

```md
---
allowed-tools: Read, Grep, Glob
description: OWASP Top 10 安全漏洞扫描
model: claude-opus-4-6
---

以安全工程师视角审查代码库：

高危：SQL 注入、XSS、命令注入、硬编码密钥
中危：不安全的反序列化、过时依赖、敏感信息写入日志、CORS 配置过宽
低危：缺失安全响应头、错误信息暴露内部细节

输出格式：漏洞位置 + 危险级别 + 修复建议代码示例
```

API 文档生成：

```md
---
allowed-tools: Read, Glob, Edit
argument-hint: [输出文件路径]
description: 自动生成 REST API 文档
---

扫描 @src/routes/ 和 @src/controllers/，为所有端点生成 Markdown 文档：
- 端点路径与 HTTP 方法
- 请求参数（Query / Body / Path）及类型
- 响应结构（成功与错误）
- 认证要求
- curl 调用示例

保存到：$ARGUMENTS（默认：docs/api.md）
```

代码重构：

```md
---
allowed-tools: Read, Edit, Glob
argument-hint: [目标文件或目录]
description: 代码质量重构
---

对 $ARGUMENTS 进行重构，遵循原则：
- 可读性：变量 / 函数命名语义化，消除魔法数字
- DRY 原则：提取重复逻辑为复用函数
- 单一职责：拆分超过 50 行的函数
- 错误处理：完善异常捕获和错误边界
- TypeScript：消除 `any` 类型，补全类型注解

重构前先输出变更计划，确认后再执行。
```

文档链接检查：

```md
---
allowed-tools: Read, Bash, WebFetch
argument-hint: [文档文件路径]
description: 检查文档中的链接是否全部有效
model: claude-haiku-4-5-20251001
---

读取 $ARGUMENTS 文件，提取所有超链接（http/https），逐一请求并检查响应状态码。

输出格式：
- 有效链接（200）
- 重定向链接（3xx）及目标地址
- 失效链接（4xx/5xx）及建议替代链接

最后输出汇总：共 N 个链接，M 个失效。
```

---

### 附：配置文件说明

建议将 `CLAUDE.local.md` 和 `.claude/settings.local.json` 加入 `.gitignore`，避免个人偏好影响团队成员。

| 文件 / 目录 | 作用 | 版本控制 |
| --- | --- | --- |
| `CLAUDE.md` | 项目级共享指令（技术栈、构建命令、编码规范等） | 纳入 git |
| `CLAUDE.local.md` | 个人私有指令（个人偏好，不共享给团队） | 加入 `.gitignore` |
| `~/.claude/CLAUDE.md` | 全局指令，所有项目通用 | — |
| `.claude/settings.json` | 项目共享配置（工具权限、hooks 等） | 纳入 git |
| `.claude/settings.local.json` | 项目个人配置（覆盖 `settings.json`，不共享） | 加入 `.gitignore` |
| `.claude/skills/` | 项目级自定义 Skills | 纳入 git |
| `.claude/commands/` | 项目级自定义命令（旧格式） | 纳入 git |
| `~/.claude/skills/` | 个人全局 Skills | — |

---

## Claude Code 输出样式

输出样式（Output Style）让你可以定制 Claude Code 的响应风格和交互方式。

它最适合解决这样的问题：

- 你不只想让 Claude Code“会写代码”，还希望它“按某种方式回复”
- 你想让它更偏解释型、教学型，或适配某类非工程任务
- 你希望不同项目拥有不同的交互风格

从本质上说，输出样式是通过修改系统提示（System Prompt）来改变 Claude Code 的工作方式。

---

### 一、内置输出样式

Claude Code 提供 3 种开箱即用的输出样式：

| 样式名称 | 适用场景 | 核心特点 |
| --- | --- | --- |
| **默认样式（default）** | 日常软件工程任务 | 专注高效完成编码、调试、重构等工作，回复简洁直接 |
| **解释性样式（explanatory）** | 边做边学场景 | 在完成任务的同时讲解实现思路、设计模式等知识点 |
| **学习样式（learning）** | 主动实践式学习 | 在关键位置添加 `TODO(human)`，引导你自己完成核心部分 |

如果你只是想让 Claude “解释更多”，通常先试 `explanatory` 就够了；如果你希望它刻意留出练手机会，再考虑 `learning`。

---

### 二、它是怎么生效的

切换输出样式时，Claude Code 会调整系统提示，大致遵循下面的逻辑：

1. 移除默认的“简洁回复、高效输出”等原始约束
2. 根据样式决定是否保留默认的编码相关指令
3. 在系统提示末尾追加新的风格规则
4. 在对话过程中持续按当前样式约束回复

需要特别注意的是：

- 自定义样式默认会剔除部分编码相关指令
- 如果你仍然希望保留默认编码行为，需要显式打开 `keep-coding-instructions: true`

---

### 三、如何切换输出样式

最直接的方法是在 Claude Code 中执行：

```text
/output-style
```

然后在菜单里选择目标样式。

也可以直接指定：

```text
/output-style explanatory
/output-style learning
/output-style default
```

除了命令切换，也可以直接修改配置文件中的 `outputStyle` 字段：

- 项目级：`.claude/settings.local.json`
- 全局级：`~/.claude/settings.json`

通常项目级更适合实验和团队协作，全局级更适合长期固定使用的风格。

---

### 四、如何创建自定义输出样式

如果内置样式不够用，可以自己写一个 Markdown 文件作为输出样式。

#### 1、保存位置

| 级别 | 路径 | 作用范围 |
| --- | --- | --- |
| **用户级** | `~/.claude/output-styles/` | 当前用户所有项目可用 |
| **项目级** | `.claude/output-styles/` | 仅当前项目可用，可提交到 Git 与团队共享 |

#### 2、文件结构

自定义样式通常由两部分组成：

- 顶部的 YAML frontmatter：定义名称、说明和配置项
- 下方的 Markdown 正文：定义角色、规则、格式和特殊处理方式

下面是一份更适合直接照着改的示例：

```md
---
name: data-analyst
description: 专注将复杂数据转化为可视化报告和分析结论
keep-coding-instructions: false
---

# 角色定位

你是一个专业的数据分析助手，擅长使用 Python 处理结构化数据，
并将复杂数据转化为简洁易懂的可视化报告。

## 响应规则

1. 所有分析必须包含「结论 + 数据支撑 + 优化建议」三部分
2. 生成代码时优先使用 Pandas 和 Matplotlib
3. 尽量使用通俗语言解释复杂概念

## 格式要求

1. 结论部分加粗显示
2. 代码块使用 `python` 标记
3. 建议部分使用有序列表呈现

## 特殊场景

1. 遇到缺失数据时，先提示用户补充关键信息，而不是直接报错
2. 生成图表时，默认使用中文标签和浅色主题
```

#### 3、frontmatter 参数

| 参数名 | 是否必填 | 说明 | 默认值 |
| --- | --- | --- | --- |
| `name` | 否 | 显示在 `/output-style` 菜单中的样式名称 | 文件名 |
| `description` | 否 | 样式用途说明 | 无 |
| `keep-coding-instructions` | 否 | 是否保留默认编码相关指令 | `false` |

#### 4、推荐使用步骤

1. 创建一个 `.md` 文件，例如 `data-analyst.md`
2. 放到对应目录中：
   - 全局：`~/.claude/output-styles/`
   - 项目：`.claude/output-styles/`
3. 执行 `/output-style`
4. 在菜单中选择它

---

### 五、它和其他机制有什么区别

输出样式看起来很像 `CLAUDE.md`、自定义命令或者子代理，但作用层次并不一样。

| 对比对象 | 关键区别 |
| --- | --- |
| **输出样式 vs CLAUDE.md** | 输出样式改变交互风格；`CLAUDE.md` 提供项目背景、约定和上下文 |
| **输出样式 vs --append-system-prompt** | 输出样式是整体切换风格；`--append-system-prompt` 是追加要求 |
| **输出样式 vs 子代理（Subagent）** | 输出样式影响主代理整体回复方式；子代理是针对特定任务的独立角色 |
| **输出样式 vs 自定义斜杠命令** | 输出样式决定“怎么说”；斜杠命令决定“做什么” |

一个简单判断方式：

- 你想改变 Claude 的整体说话风格和响应结构，用 **输出样式**
- 你想给项目增加背景和规则，用 **CLAUDE.md**
- 你想快速执行某个固定动作，用 **自定义命令**
- 你想把一类任务交给专门角色处理，用 **子代理**

---

### 六、通用模板

如果你只是想快速做一个自己的输出样式，可以从这个模板开始：

```md
---
name: [样式名称]
description: [一句话说明用途]
keep-coding-instructions: [true 或 false]
---

# 核心定位

[定义 Claude 的角色和专长]

## 响应规则

1. [规则 1]
2. [规则 2]
3. [规则 3]

## 格式要求

1. [格式要求 1]
2. [格式要求 2]
3. [格式要求 3]

## 特殊场景处理

1. [特殊情况 1]
2. [特殊情况 2]
```

如果你不确定从哪里开始，最稳的办法是：

- 先复制默认风格的习惯
- 只增加 2 到 4 条真正重要的规则
- 实际用几次，再慢慢迭代

过长、过细的样式文件往往不会更好，反而更容易让 Claude 遵守不稳定。核心原则还是：**规则少而关键，描述清楚，方便长期维护。**
