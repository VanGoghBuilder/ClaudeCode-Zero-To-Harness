## Claude Code 钩子

Claude Code 的钩子（Hooks）本质上是：

> **在特定生命周期事件上自动触发的用户自定义命令。**

它和普通提示词的最大区别在于：

- 提示词是在“请求 Claude 去做”
- 钩子是在“事件发生时强制执行”

所以如果你想把某些动作从“最好执行”变成“必须执行”，钩子就是最合适的机制。

---

### 一、钩子适合解决什么问题

常见场景包括：

- **消息通知**：Claude 等待输入或请求权限时，自动提醒你
- **自动格式化**：编辑 `.ts` 文件后自动跑 `prettier`
- **操作日志**：记录 Claude 执行过哪些命令
- **规范校验**：发现不符合规则的输出时立即反馈
- **敏感文件保护**：阻止修改 `.env`、`.git/` 等路径

相比“在 `CLAUDE.md` 里提醒 Claude 要这样做”，钩子是更可靠的硬规则。

---

### 二、使用钩子前的安全提醒

钩子会直接调用本地 shell 命令，因此你要把它当成“会自动执行的脚本”来看。

风险主要包括：

- 命令写错导致误删文件
- 钩子泄露本地敏感信息
- 来源不明的脚本被放进钩子后自动执行

建议：

1. 注册前先逐行审查命令
2. 尽量使用你自己能完全理解的脚本
3. 不要把敏感密钥直接硬编码进钩子
4. 先在项目级别试，再决定是否升到用户级全局生效

---

### 三、钩子事件类型

Claude Code 提供了一组生命周期事件，你可以在这些事件发生时绑定命令。

| 事件名称 | 触发时机 | 常见用途 |
| --- | --- | --- |
| `PreToolUse` | 工具调用前 | 拦截敏感修改、做前置校验 |
| `PermissionRequest` | 需要权限确认时 | 自动批准或拒绝特定操作 |
| `PostToolUse` | 工具调用完成后 | 格式化、补日志、自动修正 |
| `UserPromptSubmit` | 用户提交提示词后 | 在 Claude 开始处理前补充上下文 |
| `Notification` | Claude 发通知时 | 改成桌面提醒、短信等 |
| `Stop` | Claude 完成一次响应时 | 执行收尾动作 |
| `SubagentStop` | 子代理结束时 | 处理子代理结果 |
| `PreCompact` | 即将压缩上下文前 | 自定义压缩前处理 |
| `SessionStart` | 新会话或恢复会话时 | 加载初始化环境 |
| `SessionEnd` | 会话结束时 | 保存结果、清理临时文件 |

如果你刚开始用，最有价值的通常是：

- `PreToolUse`
- `PostToolUse`
- `Stop`
- `SessionStart`

---

### 四、快速入门：记录 Bash 命令日志

下面是一个很适合新手的例子：**把 Claude 执行过的 Bash 命令记到日志里。**

#### 1、前置准备

建议先安装 `jq`，因为很多钩子示例会用它来解析 Claude 传入的 JSON：

~~~
brew install jq
~~~

#### 2、基本思路

我们希望实现的效果是：

- 只有 Claude 调用 Bash 时才触发
- 把执行的命令和说明写入日志文件

#### 3、配置示例

把下面这段放进 `~/.claude/settings.json` 或项目级 `.claude/settings.json`：

~~~
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '\"\\(.tool_input.command) - \\(.tool_input.description // \"无描述信息\")\"' >> ~/.claude/bash-command-log.txt"
          }
        ]
      }
    ]
  }
}
~~~

#### 4、如何验证

在 Claude Code 里让它执行：

~~~
帮我执行 ls 命令
~~~

然后在终端查看：

~~~
cat ~/.claude/bash-command-log.txt
~~~

如果你能看到类似下面的记录，就说明钩子工作正常：

~~~
ls - Lists files and directories
~~~

---

### 五、几个常见实用示例

下面这些例子比“讲概念”更有落地价值。

#### 示例 1：编辑 TypeScript 后自动格式化

~~~
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | { read file_path; if echo \"$file_path\" | grep -q '\\.ts$'; then npx prettier --write \"$file_path\"; fi; }"
          }
        ]
      }
    ]
  }
}
~~~

适合：

- 前端项目
- TypeScript 项目
- 你希望 Claude 写完就自动规整格式

#### 示例 2：Markdown 输出后自动清理格式

适合你现在这种文档整理项目：Claude 写完 Markdown 后，自动做一次格式清理。

钩子配置：

~~~
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/markdown_formatter.py"
          }
        ]
      }
    ]
  }
}
~~~

配套脚本可以放在：

~~~
.claude/hooks/markdown_formatter.py
~~~

然后给它执行权限：

~~~
chmod +x .claude/hooks/markdown_formatter.py
~~~

#### 示例 3：需要输入时发桌面通知

~~~
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude Code 提示' '请你输入指令或确认权限'"
          }
        ]
      }
    ]
  }
}
~~~

这个例子更适合 Linux；在 macOS 上通常要换成你自己的通知命令。

#### 示例 4：禁止修改敏感文件

~~~
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json, sys; data=json.load(sys.stdin); path=data.get('tool_input',{}).get('file_path',''); sys.exit(2 if any(p in path for p in ['.env', 'package-lock.json', '.git/']) else 0)\""
          }
        ]
      }
    ]
  }
}
~~~

这个思路很实用：让 Claude 在“想改敏感文件”之前就被挡住。

---

### 六、配置文件放哪里

| 配置级别 | 路径 | 生效范围 |
| --- | --- | --- |
| 用户级 | `~/.claude/settings.json` | 所有项目 |
| 项目级 | `.claude/settings.json` | 当前项目 |
| 本地项目级 | `.claude/settings.local.json` | 当前项目，不纳入版本控制 |

如果你不确定从哪里开始，建议：

- 先用项目级配置试验
- 稳定后再提升到用户级配置

这样风险最小。

---

### 七、一个最小可用的 Hooks 配置骨架

如果你只是想先搭一个能扩展的基础骨架，可以从下面开始：

~~~
{
  "hooks": {
    "PreToolUse": [],
    "PostToolUse": [],
    "Stop": [],
    "SessionStart": []
  }
}
~~~

然后按需往里面一点点加。

---

### 八、使用钩子的实践建议

最后给几个很实用的建议：

1. **先做简单钩子，再做复杂钩子**
   先从日志、格式化这类低风险动作开始。

2. **优先做“可重复收益高”的动作**
   比如自动格式化、自动检查敏感文件，比复杂提示词更稳定。

3. **不要一上来就把所有事件都接满**
   事件太多、逻辑太重，会让排障变复杂。

4. **先项目级，再全局**
   项目里跑顺了，再放到 `~/.claude/settings.json`。

5. **把钩子看作工程基础设施**
   它不是“装饰性配置”，而是你和 Claude 协作时的控制层。

如果一句话总结：

> **CLAUDE.md 负责告诉 Claude 应该怎么做，Hooks 负责在关键节点上强制执行。**
