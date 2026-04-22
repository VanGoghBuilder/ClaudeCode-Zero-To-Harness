## Claude Code 控制 Chrome 浏览器

Claude Code 的 Chrome 集成功能，让你可以直接从命令行（CLI）或 VS Code 扩展中控制浏览器。Claude 能够：
- 打开网页、点击按钮、填写表单
- 读取浏览器控制台日志（console errors、network requests、DOM 状态）
- 与任何你已登录的网站交互（Gmail、Notion、Google Docs 等）
- 录制浏览器操作为 GIF 文件
- 在多个标签页之间协同工作

**核心优势**：Claude 共享你浏览器的登录状态，无需额外的 API 密钥或重新认证。

> 这个功能目前处于 Beta 阶段，允许你在终端（CLI）或 VS Code 中用自然语言指令，让 Claude 直接控制你的 Chrome 浏览器，进行导航、点击、输入、调试、数据提取等操作，无需手动切换窗口。

---

### 安装与准备

#### 前置条件

使用该功能前，你需要满足以下要求：

| 条件 | 说明 |
| --- | --- |
| Claude Code 版本 | v2.0.73 或更高 |
| Chrome 扩展版本 | Claude in Chrome v1.0.36 或更高 |
| Anthropic 账户 | 需要 **直接付费计划**（Pro、Max、Teams 或 Enterprise） |
| 不支持的渠道 | 通过 Amazon Bedrock、Google Vertex AI、Microsoft Foundry 访问的用户 **不可用** |

#### 安装步骤

**1. 安装浏览器扩展**：
- 打开 Chrome/Edge，访问 [Chrome Web Store - Claude](https://chromewebstore.google.com/detail/claude/fcoeoabgfenejglbffodgkkbkcdhcgfn)。
- 点击"添加至 Chrome"并安装。
- 安装后建议重启浏览器，确保 Native Messaging Host 正确加载。

![image](https://www.runoob.com/wp-content/uploads/2026/03/8f9ff3fd-6d17-4058-9e37-97cbc03445a3.png)

**2. 安装/更新 Claude Code**：
- 按照官方快速入门安装 Claude Code（CLI 或 VS Code 扩展）。

**3. 权限与连接**：
- 首次运行时，扩展会自动安装 Native Messaging Host。
- 常见路径（macOS 示例）：`~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.anthropic.claude_code_browser_extension.json`

---

### 如何启动并连接

##### 在 CLI 中启动

- **推荐方式**：直接带 Chrome 启动
  ~~~
  claude --chrome
  ~~~
- 或在已有会话中启用：

然后选择启用或重新连接。
  ~~~
  /chrome
  ~~~
- **设为默认**（方便但会增加上下文消耗）：
在 `/chrome` 菜单中选择 "Enabled by default"。

#### 在 VS Code 中使用

- 在提示框中输入 `@browser` + 你的指令，例如：
  ~~~
  @browser 打开 localhost:3000，检查控制台是否有错误
  ~~~

#### 检查连接状态

在 Claude Code 会话中输入 `/chrome`，可以查看状态、管理权限、重新连接扩展。

---

### 基本使用方法

Claude Code 通过 **自然语言提示**控制浏览器，无需编写复杂代码。Claude 会自动解析你的指令，执行相应操作，并返回结果（包括截图、控制台日志、提取的数据等）。 **核心能力**：
- 导航：打开网址
- 交互：点击元素、输入文字、滚动页面
- 读取：查看 DOM、控制台错误、网络请求
- 自动化：表单填写、数据提取、跨标签页操作
- 其他：录制 GIF 演示、保存 CSV 等

**提示技巧**：
- 描述要清晰、逐步（例如"先打开网址，然后点击搜索框，输入 XXX 并告诉我结果"）。
- 可以结合本地文件（如读取 CSV 批量操作）。
- 长任务时，Claude 会分步执行并报告进度。

---

### 实用示例

**示例 1：简单导航与交互**

~~~
去到 https://www.baidu.com，点击搜索框，输入 "python"，告诉我出现的搜索结果。
~~~

**示例 2：本地网页测试与调试**

~~~
我刚更新了登录表单验证。请打开 localhost:3000，用无效数据提交表单，检查错误消息是否正确显示，并查看控制台日志。
~~~

**示例 3：表单自动化（结合本地文件）**

~~~
我有一个 contacts.csv 文件，里面有客户联系方式。对于每一行，打开 crm.example.com，点击“添加联系人”，填写姓名、邮箱和电话，然后提交。
~~~

**示例 4：Google Docs 等已登录网站操作**

~~~
根据最近的 Git 提交，起草一份项目更新，并添加到我的 Google Doc（网址：docs.google.com/document/d/abc123）。
~~~

**示例 5：数据提取与保存**

~~~
打开产品列表页面，提取每个商品的名称、价格和库存状态，保存为 CSV 文件。
~~~

**示例 6：录制演示 GIF**

~~~
录制一个 GIF，展示从购物车添加商品到结账确认的完整流程。
~~~

**示例 7：多站点工作流**

~~~
查看我明天的日历，对于每个有外部参会者的会议，查找他们公司的网站并添加备注。
~~~

