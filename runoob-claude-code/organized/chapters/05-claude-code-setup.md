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

**需要的信息： **
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

**参数说明： **
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

可以直接购买资源包，这样用起来更划算：[https://cn.aliyun.com/benefit?from_alibabacloud=&userCode;=i5mn5r7m](https://cn.aliyun.com/benefit?from_alibabacloud=&userCode=i5mn5r7m)
- 模型名称：替换为百炼支持的模型名称（例如 qwen3-max、qwen3-coder-plus等）
- 百炼的 Coding Plan 套餐：[https://www.aliyun.com/benefit/scene/codingplan](https://www.aliyun.com/benefit/scene/codingplan?source=5176.29345612&userCode=i5mn5r7m)
> **兼容 Anthropic API 协议： **
> - Base URL：https://coding.dashscope.aliyuncs.com/apps/anthropic
> - API Key：填入 Coding Plan 套餐专属 API Key
> - Model：qwen3-coder-plus
**macOS/Linux： **
创建并打开配置文件 ~/.claude/settings.json。
~ 代表用户主目录，如果 .claude 目录不存在，需要先行创建，可在终端执行 mkdir -p ~/.claude 来创建。
编辑配置文件：
~~~
vim ~/.claude/settings.json
~~~
将 YOUR_API_KEY 替换为 Coding Plan 套餐专属 API Key。

~~~
{
"env": {
"ANTHROPIC_AUTH_TOKEN": "YOUR_API_KEY",
"ANTHROPIC_BASE_URL": "https://coding.dashscope.aliyuncs.com/apps/anthropic",
"ANTHROPIC_MODEL": "qwen3-coder-plus"
}
}
~~~
重新打开一个新的终端使环境变量配置生效。

**Windows： **在 CMD 中运行以下命令，设置环境变量：

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

---### 智谱大模型接入 Claude Code这部分我们使用 ~/.claude/settings.json 文件来配置大模型，开始前需要到官方平台获取 API key：[GLM Coding Plan](https://www.bigmodel.cn/claude-code?ic=EMWK7IPUCE)。
编辑或新增 Claude Code 配置文件 ~/.claude/settings.json ，新增或修改里面的 env 字段
~~~

# 注意替换里面的your_zhipu_api_key 为您上一步获取到的 API Key

{
"env": {
"ANTHROPIC_AUTH_TOKEN": "your_zhipu_api_key",
"ANTHROPIC_BASE_URL": "https://open.bigmodel.cn/api/anthropic",
"API_TIMEOUT_MS": "3000000",
"CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": 1
}
}
~~~运行 claude 启动 Claude Code，输入 /status 确认模型状态：

![image](https://www.runoob.com/wp-content/uploads/2025/12/5617ff6d-7997-4819-a2eb-300346548c61.png)

如果不是可以输入 /config 来切换模型。

![image](https://www.runoob.com/wp-content/uploads/2025/12/98fa9dc0-efab-4afc-a100-350ad114937c.png)

