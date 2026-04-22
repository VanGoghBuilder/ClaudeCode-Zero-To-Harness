## skill-creator 使用

在 Claude Code 生态中，Skill（技能） 是扩展 Agent 能力的重要机制。

Skill 本质上是一个模块化知识包，可以给 Claude 添加：
- 专业领域知识
- 固定工作流程
- API / 工具使用方式
- 模板和脚本

简单理解：

~~~
Skill = 给 AI 写的一份操作说明书。
~~~

而 skill-creator 就是 Anthropic 官方提供的 Skill 开发助手，帮助开发者创建、优化和打包技能。

GitHub 地址：[https://github.com/anthropics/skills/tree/main/skills/skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator)

#### Skill 是什么

在 Claude Code 中，Skill 是一种 可复用的能力扩展包。

一个 Skill 通常包含：

~~~
skill-name/
├── SKILL.md        # 核心说明（必须）
├── scripts/        # 可执行脚本
├── references/     # 文档或知识
├── assets/         # 附加资源
~~~

其中最重要的是 SKILL.md。

示例：

~~~

---

name: video-tool
description: Video processing CLI for editing and transcribing videos

---

# Video Tool Skill

Use the video-tool CLI to process videos.

## Quick Start

video-tool video download -u URL
video-tool generate transcript -i video.mp4
~~~

SKILL.md 的作用就是：教 Claude 如何使用某个工具或完成某个流程。

#### 安装 skill-creator

首先安装 Anthropic 的 skills 集合。

~~~
npx skills add https://github.com/anthropics/skills --skill skill-creator
~~~

或者：

~~~
claude install anthropics/skills/skill-creator
~~~

安装完成后，你就可以在 Claude 中调用：

~~~
/skill-creator
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/49085a23-ff2a-4ca5-af60-f9f1872b8f4b.png)

skill-creator 提供完整的 Skill 开发流程工具链。

安装完成后，本地会下载该 Skill，其中包含：

~~~
skills/skill-creator/
├── SKILL.md          ← 核心说明文件
├── agents/           ← 内置的评审助手
├── eval-viewer/      ← 测试结果可视化工具
├── references/       ← 参考文档（数据格式说明等）
└── scripts/          ← 自动化脚本（打包、测评等）
~~~

Skill Creator 的工作流程可以概括为一个循环：

~~~
想清楚需求
    ↓
起草 SKILL.md
    ↓
设计测试用例
    ↓
运行测试（有 Skill vs 没有 Skill，对比效果）
    ↓
评估结果（看报告 + 打分）
    ↓
根据反馈修改 SKILL.md
    ↓
重复，直到满意
    ↓
打包成 .skill 文件
~~~

---

### 用 skill-creator 创建 Skill

#### 需求询问

Claude 会先问你几个问题，帮你把需求想清楚。你不需要一次说完所有细节，像聊天一样回答就行。

**Claude 通常会问： **

1. 这个 Skill 具体做什么？
2. 什么情况下触发（用户说什么、上传什么文件）？
3. 输出是什么格式？
4. 有没有特殊要求（固定模板、特定格式规范……）？

**示例对话： **

~~~
你：我想做一个 Skill，把会议录音的文字稿整理成结构化的会议纪要。

Claude：好的，我来问几个问题帮你确认需求：
1. 纪要里需要包含哪些内容？（比如：时间、参与人、决议、行动事项……）
2. 输出格式是 Word 文档、Markdown，还是直接在对话里回复？
3. 有没有固定的纪要模板？

你：需要包含会议主题、时间、参与人、讨论要点、决议事项、
    下一步行动（含负责人和截止日期）。
    输出 Word 文档。
    有模板，我来上传。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/328e2713-f38c-4079-a832-555b7a19e9f0.png)

可以按 Tab 键或方向按键切换菜单详情，并提交。

这个阶段你的目标： 把所有细节和边界情况说清楚，不要跳过，后面测试时踩的坑大多源于这里没说清楚。

也可以直接回车，它也能帮我们创建：

![image](https://www.runoob.com/wp-content/uploads/2026/03/f5d438c9-dc20-4465-9d30-c22edaf87f3e.png)

之后 Claude 会写出 SKILL.md 草稿，大致长这样：

![image](https://www.runoob.com/wp-content/uploads/2026/03/7d25c5a6-fba5-41ae-99f7-1421774459ee.png)

接下来还会创建参考文件：

![image](https://www.runoob.com/wp-content/uploads/2026/03/6eddc6e5-d124-43ae-9fd6-3e81b2b0aa48.png)

创建完成后，会验证技能结构是否正确，等待就好了。

验证通过！就会打包 Skill，并展示目录结构：

![image](https://www.runoob.com/wp-content/uploads/2026/03/4c93a5ac-6e28-48eb-82e9-a23b3568783f.png)

测试下，输入会议内容：

~~~
整理以下会议纪要：一、会议基本信息
  会议主题：学习平台内容优化会议
  参会人员：张三、李四、王五
  会议时间：2025-XX-XX 14:00-15:00
  记录人：张三
  二、会议内容
  讨论当前团队在编程教学中使用的案例素材，一致认为runoob教程简洁易懂，适合作为新手入门参考。
  确定后续内部培训将优先采用 runoob 上的基础语法、实战小案例进行讲解。
  安排李四整理 runoob 中 Python、Java 高频知识点，形成内部速查文档。
  下次会议检查文档完成情况。
  三、待办事项
  李四：整理 runoob 核心知识点文档，下周一前完成。
  全体：提前熟悉 runoob 对应章节，便于下次讨论。
~~~

![image](https://www.runoob.com/wp-content/uploads/2026/03/c1fc4b18-f5d5-4c3c-8a28-d6ffc7c02410.png)

