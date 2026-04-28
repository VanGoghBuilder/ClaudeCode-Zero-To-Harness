# init-project-best-practices Skill 使用说明

`init-project-best-practices` 是一个用于初始化 Claude Code / Harness Engineering 项目的项目级 Skill。

它适合两类场景：

- **空项目初始化**：按最佳实践创建 `CLAUDE.md`、`docs/`、`.claude/`、`scripts/ai/run-checks.sh` 等最小 harness 结构。
- **已有代码整理后初始化**：先盘点现有代码、命令、文档和模块边界，再保守添加 harness 文件，避免破坏原有代码结构。

## 它解决什么问题

很多团队第一次接入 Claude Code 时，会把规则散落在聊天记录里：

- 项目目标靠口头说明
- 测试命令靠临时提醒
- 模块边界靠人脑记忆
- 子代理、命令、hooks 每个项目重复配置

这个 Skill 的目标是把这些规则沉淀为版本化文件，让 Claude Code 每次进入项目时都能读到同一套上下文和工作流。

## 包位置

随本文档发布的 Skill 包位于：

```text
skills/init-project-best-practices/SKILL.md
```

在当前完整工作仓库中，对应的项目级安装位置是：

```text
.claude/skills/init-project-best-practices/SKILL.md
```

## 安装方式

### 安装为项目级 Skill

在目标项目根目录执行：

```bash
mkdir -p .claude/skills/init-project-best-practices
cp guide/skills/init-project-best-practices/SKILL.md \
  .claude/skills/init-project-best-practices/SKILL.md
```

如果你是从独立发布仓库复制文件，确保最终结构是：

```text
你的项目/
└── .claude/
    └── skills/
        └── init-project-best-practices/
            └── SKILL.md
```

重启或刷新 Claude Code 会话后，可以用 `/skills` 查看是否已加载。

### 安装为个人全局 Skill

如果希望所有项目都能使用：

```bash
mkdir -p ~/.claude/skills/init-project-best-practices
cp guide/skills/init-project-best-practices/SKILL.md \
  ~/.claude/skills/init-project-best-practices/SKILL.md
```

个人全局 Skill 适合个人长期复用；项目级 Skill 更适合团队共享和版本控制。

## 使用方式

安装后，在 Claude Code 中进入目标项目根目录，然后输入：

```text
/init-project-best-practices
```

也可以用自然语言触发：

```text
按最佳实践初始化这个 Claude Code 项目。
```

```text
这个项目已经有代码了，先整理现有结构，再按最佳实践初始化 harness。
```

建议第一次使用时明确说明项目状态：

```text
/init-project-best-practices
这是一个已有 TypeScript 服务，请保留现有目录结构，只补充 CLAUDE.md、docs、commands、agents 和检查脚本。
```

## 初始化后的推荐结构

Skill 会引导 Claude Code 根据项目实际情况收敛到类似结构：

```text
.
├── CLAUDE.md
├── README.md
├── docs/
│   ├── architecture.md
│   ├── module-boundaries.md
│   └── testing-playbook.md
├── .claude/
│   ├── settings.json
│   ├── agents/
│   │   ├── code-reviewer.md
│   │   └── test-runner.md
│   └── commands/
│       ├── implement.md
│       └── review-pr.md
└── scripts/
    └── ai/
        └── run-checks.sh
```

这不是强制脚手架。已有代码项目应优先保留现有目录，只补充能提升 Claude Code 协作质量的文件。

## 空项目初始化流程

当目标目录没有明显业务代码时，Skill 会优先创建 harness 骨架：

1. 写入 `CLAUDE.md`，记录项目目标、非目标、架构、命令和工作流。
2. 创建 `docs/architecture.md`、`docs/module-boundaries.md`、`docs/testing-playbook.md`。
3. 创建 `.claude/settings.json`、`.claude/commands/` 和 `.claude/agents/`。
4. 在存在真实技术栈时创建 `scripts/ai/run-checks.sh`。
5. 更新 `README.md` 作为人类读者入口。

如果项目还没有技术栈，不应虚构 `npm test`、`pytest`、`go test` 等命令。

## 已有代码整理后初始化流程

当目标项目已经有代码时，Skill 会先要求 Claude Code 盘点当前状态：

- 入口文件和主要模块
- 构建、测试、lint、typecheck 命令
- 现有文档和 README
- 模块边界和高风险区域
- 当前未提交改动

然后再保守补充 harness 文件：

1. 用观察到的事实更新或创建 `CLAUDE.md`。
2. 只在能降低歧义时新增 `docs/` 文档。
3. 用现有工作流生成 `.claude/commands/`。
4. 只添加职责清晰的 `.claude/agents/`。
5. 只把真实存在的检查命令写入 `scripts/ai/run-checks.sh`。

初始化不应该顺手移动源码、重命名模块、重构架构或新增依赖。需要这些操作时，应让用户单独确认。

## 验收清单

完成后，让 Claude Code 汇报：

- 项目被判断为空项目还是已有代码项目
- 新增或更新了哪些文件
- 发现了哪些真实命令
- 是否运行了检查命令
- 哪些后续事项需要人类确认

你也可以要求它执行：

```text
检查这次初始化是否符合 init-project-best-practices Skill 的验收清单。
```

## 与模板目录的关系

`template/` 更像一套可复制的最小样板；`init-project-best-practices` Skill 更像一个初始化流程说明。

- 想快速复制一套固定结构：用模板。
- 想让 Claude Code 根据当前项目状态判断怎么初始化：用 Skill。
- 已有代码项目：优先用 Skill，而不是直接覆盖式复制模板。

## 注意事项

- 不要覆盖已有 `CLAUDE.md`、`.claude/settings.json`、docs 或 scripts，必须先读取再修改。
- 不要把敏感文件、密钥或本地私有配置写进共享文档。
- 不要为不存在的技术栈编造检查命令。
- 不要在初始化阶段自动提交或推送，除非用户明确要求。
- 如果要添加自动 hooks，先确认命令足够稳定且运行成本可接受。
