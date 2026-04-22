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
