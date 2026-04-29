# ClaudeCode Zero to Harness

一套围绕 Claude Code 的公开读物，帮助读者从基础使用一路走到 Harness Engineering。

## 核心读物

如果把这个仓库当成一条完整阅读路径，建议按下面顺序阅读。

### 1. 零基础桥接读物

- [Claude Code：从 Zero 到 Harness](00-zero-to-harness.md)

这篇适合作为入口：先建立对 Claude Code 的正确使用直觉，再过渡到项目上下文和最小可行 harness。

### 2. 方法论与实践

- [Claude Code 的 Harness Engineering 总纲](06-harness-engineering-guide.md)
- [长时间应用开发的 Harness 设计](07-long-running-app-harness.md)
- [init-project-best-practices Skill 使用说明](08-init-project-skill.md)

这三篇承担全局收束、实践延展和可复用初始化能力：前者把 Claude Code 的日常使用、上下文管理、自动化和工程化控制整合成一套 Harness Engineering 框架；第二篇把长时间应用开发中的 planner / generator / evaluator、上下文交接和评估回路落到实操流程里；第三篇提供一个可安装的项目初始化 Skill，用于空项目或已有代码项目的 harness 初始化。

### 3. Claude Code 教程主线

- [概览与安装](01-overview-and-setup.md)
- [项目初始化与上下文](02-project-and-context.md)
- [日常使用与交互](03-daily-usage.md)
- [扩展、自动化与高级能力](04-extension-and-automation.md)
- [参考、环境与工程集成](05-reference-and-ops.md)
- [Claude Desktop 第三方 API 配置](09-claude-desktop-third-party-api.md)

这 5 篇构成从上手、进入项目、到扩展能力和工程集成的主阅读路径。其中 [日常使用与交互](03-daily-usage.md)、[扩展、自动化与高级能力](04-extension-and-automation.md) 和 [参考、环境与工程集成](05-reference-and-ops.md) 已吸收外部 Claude Code 最佳实践材料中的 slash commands、专用 agents 与 Git 模板经验。

## 可复用 Skill 包

- [init-project-best-practices Skill 使用说明](08-init-project-skill.md)
- `skills/init-project-best-practices/SKILL.md`

这个 Skill 会随 `guide/` 发布，读者可以复制到目标项目的 `.claude/skills/init-project-best-practices/SKILL.md`，让 Claude Code 按最佳实践初始化空项目，或在已有代码项目中先整理再初始化 harness。

## 适合谁

- 想系统了解 Claude Code 的个人开发者
- 希望把 AI 协作沉淀为团队工作方式的小团队
- 想从“会用 Claude Code”升级到“能稳定驾驭 Claude Code”的读者

## 仓库说明

- 这个仓库只公开发布面向读者的文档内容。
- 原始脚本、模板、镜像素材和过程文件保留在私有工作仓库中，不在这里公开。
- 如果你想按章节阅读，直接从上面的核心读物顺序进入即可。
