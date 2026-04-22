## Claude Code Git 工作流

Claude Code 深度集成了 Git 功能，可以用自然语言完成几乎所有 Git 操作，包括创建提交、管理分支、处理合并冲突，以及利用 Git Worktree 实现并行工作流。本章详细介绍如何在 Claude Code 中高效使用 Git。

---

### 基础 Git 操作

#### 1、查看变更

用自然语言询问即可获取 Git 状态：

~~~
我改了哪些文件？
~~~

~~~
这次的改动有哪些内容？
~~~

~~~
最近 10 次提交都改了什么？
~~~

~~~
检查一下当前的 git 状态
~~~

Claude 会自动执行相应的 Git 命令并展示结果。

#### 2、提交代码

简单提交：

~~~
提交这次的改动
~~~

~~~
把这次的修改提交
~~~

带描述的提交：

~~~
把这次的修改提交，commit 信息说明修复了登录验证的 bug
~~~

Claude 会根据实际改动内容自动生成符合规范的 commit 信息。

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

~~~
查看所有分支
~~~

~~~
删除已合并的分支
~~~

---

### 处理合并冲突

#### 1、自动解决简单冲突

~~~
帮我解决合并冲突
~~~

~~~
这个文件有冲突，帮我看看哪个版本是正确的
~~~

Claude 会分析冲突内容，根据项目上下文选择合适的版本或提出解决方案。

#### 2、人工介入解决复杂冲突

当冲突过于复杂时，Claude 会向你说明各种选择的利弊：

~~~
这个冲突涉及架构设计，你来决定保留哪个方案：
A. 保留我们的实现...
B. 保留引入的优化...
~~~

> Claude Code 在执行任何文件修改前都会展示修改内容并请求你的确认。对于合并冲突这类高风险操作，更要仔细审查每个修改。

---

### Git Worktree 并行工作流

Git Worktree 允许你在不同目录中同时处理不同的分支，而无需切换分支或克隆仓库。这是 Claude Code 中最强大的并行工作方式之一。

#### 1、使用 Claude 创建 Worktree

启动 Claude 时指定 worktree：

~~~
claude --worktree feature-auth
~~~

这会自动：
- 在 `.claude/worktrees/feature-auth/` 创建新目录
- 基于 `origin/HEAD`（远程默认分支）创建新分支
- 在新目录中启动独立 Claude 会话

同时开多个 worktree：

~~~
claude --worktree bugfix-123
~~~

~~~
claude --worktree
~~~

不指定名称时，Claude 会自动生成有趣的名字（如 `bright-running-fox`）。

#### 2、Worktree 属性

| 属性 | 值 |
| --- | --- |
| 位置 | `<repo>/.claude/worktrees/<name>` |
| 分支命名 | `worktree-<name>` |
| 基础分支 | 远程 `origin/HEAD` 指向的分支 |

#### 3、Worktree 生命周期

| 场景 | 行为 |
| --- | --- |
| 没有做出任何修改 | Worktree 和分支自动删除 |
| 存在变更或提交 | Claude 提示你选择保留或删除 |
| Claude 崩溃导致孤立 worktree | 超过 `cleanupPeriodDays` 设置的天数后自动删除 |

#### 4、复制 .gitignore 文件到 Worktree

在项目根目录创建 `.worktreeinclude` 文件，指定需要复制到 worktree 的 gitignore 文件：

~~~
.env
.env.local
config/secrets.json
~~~

只有匹配某个模式 **且** 被 gitignore 的文件才会被复制。

#### 5、手动管理 Worktree

如果需要更多控制，可以手动管理：

~~~

# 创建 worktree 并指定分支

git worktree add ../project-feature-a -b feature-a

# 使用已有分支创建 worktree

git worktree add ../project-bugfix bugfix-123

# 在 worktree 中启动 Claude

cd ../project-feature-a && claude

# 完成后清理

git worktree list
git worktree remove ../project-feature-a
~~~

> 建议将 `.claude/worktrees/` 加入 `.gitignore`，避免 worktree 内容在主仓库中显示为未跟踪文件。

---

### 子代理 Worktree 隔离

子代理可以使用 worktree 隔离功能，实现完全独立的并行工作：

方式一：让 Claude 自动处理

~~~
让子代理使用 worktree 来并行处理这些任务
~~~

方式二：在子代理配置中指定

### 实例

---name:experimental-refactordescription:在隔离的 worktree 中尝试重构方案isolation:worktree # 在临时 worktree 中运行tools:Read, Write, Edit, Bash

---

你可以在隔离环境中自由修改，不会影响主分支。
完成后总结改动和方案是否成功。

#### 子代理 Worktree 特点

- 每个子代理自动获得独立的 worktree
- 任务完成后自动清理 worktree
- 修改不会影响主仓库
- 适合探索性任务和方案对比

---

### Pull Request 工作流

#### 1、创建 PR 的步骤

**第一步：让 Claude 总结变更**

~~~
总结一下我对认证模块做的改动
~~~

**第二步：生成 PR**

~~~
创建一个 PR
~~~

**第三步：完善 PR 描述**

~~~
在 PR 描述中补充更多关于安全改进的内容
~~~

#### 2、PR 会话关联

使用 `gh pr create` 创建 PR 时，Claude 会话会自动关联到该 PR：
- 会话链接到 PR 后，可以从 PR 恢复对话
- 使用 `claude --from-pr <number>` 恢复关联的会话

#### 3、使用 GitHub CLI

Claude 了解如何使用 `gh` CLI 工具。如果没有安装 `gh`，Claude 可以读写 GitHub API，但功能有限。

~~~

# Claude 可以执行的操作

gh pr create --title "Fix login bug"
gh pr view --comments
gh pr diff
gh issue create --title "Bug report"
~~~

---

### 会话管理与 Git 集成

#### 1、恢复之前的会话

| 命令 | 功能 |
| --- | --- |
| `claude --continue` | 继续当前目录最近的对话 |
| `claude --resume` | 打开会话选择器或按名称恢复 |
| `claude --from-pr 123` | 恢复与特定 PR 关联的会话 |

#### 2、会话选择器 Git 功能

| 快捷键 | 功能 |
| --- | --- |
| `B` | 筛选当前 git 分支的会话 |

#### 3、会话元数据显示

会话选择器显示以下信息：
- 会话名称或初始提示
- 上次活动距今时间
- 消息数量
- Git 分支（如果有）

#### 4、分支会话

使用 `/branch`、`/rewind` 或 `--fork-session` 创建的会话会分组在根会话下，方便管理。

---

### 计划模式与安全分析

计划模式（Plan Mode）使用只读操作安全地分析代码库，不会执行任何写操作。

#### 启动计划模式

~~~
claude --permission-mode plan
~~~

或者在会话中运行一次性查询：

~~~
claude --permission-mode plan -p "分析认证系统并提出改进建议"
~~~

#### 在 settings.json 中配置默认模式

### 实例

{
"permissions": {
"defaultMode": "plan"
}
}

---

### 典型工作流示例

#### 工作流一：多任务并行开发

~~~

# 场景：同时开发三个功能，但不想切换分支

> 启动三个 worktree，分别处理登录重构、支付集成和性能优化
> 在每个 worktree 中独立工作，完成后合并到主分支
~~~

#### 工作流二：Bug 修复流程

~~~

# 第一步：描述问题

> 用户反馈：用户登出后刷新页面仍然显示已登录

# 第二步：创建修复分支

> 创建一个 bugfix/session-cookie 分支来修复这个问题

# 第三步：分析与修复

> 先分析可能的原因，在 bugfix 分支中修复

# 第四步：提交并创建 PR

> 提交修复并创建一个 PR
~~~

#### 工作流三：代码审查

~~~

# 审查特定文件

> 帮我审查 src/payment/processor.ts，重点关注错误处理

# 审查 git 改动

> 审查我这次的所有改动，看看有没有明显的问题

# 在独立分支中审查

> 创建一个 worktree 来审查这个重构方案
~~~

#### 工作流四：功能开发与验证

~~~

# 主会话：实现新功能

> 实现用户资料编辑功能

# 子代理：并行运行测试

> 在子代理中运行所有测试，只返回失败的测试和根因

# 子代理：检查代码规范

> 使用子代理审查代码是否符合项目规范

# 完成后：创建 PR

> 创建一个 PR 并添加详细的描述
~~~

---

### 非 Git 版本控制系统

对于 SVN、Perforce 或 Mercurial 用户，Claude Code 支持通过钩子扩展：

#### 配置自定义钩子

在 `.claude/settings.json` 中配置：

### 实例

{
"hooks": {
"WorktreeCreate": "./scripts/create-worktree.sh",
"WorktreeRemove": "./scripts/remove-worktree.sh"
}
}

这些钩子替换默认的 git 行为。使用钩子脚本时，在脚本内部复制本地配置文件，而不是使用 `.worktreeinclude`。

---

### Anthropic 内部团队最佳实践

- **多开 worktree**：同时跑 3-5 个 git worktree，每个开一个独立会话，这是团队公认的提效最佳实践
- **复杂任务先规划**：使用 `plan` 模式让一个 Claude 写计划，另一个当幕僚审查
- **错误后更新 CLAUDE.md**：每次纠错后加一句"更新你的 CLAUDE.md，别再犯同样的错"
- **验证环节专门进计划模式**：确保验证过程安全可控

---

### 常见问题

**Q：Worktree 和分支有什么区别？**

Worktree 是独立的目录，可以在不同目录同时工作；分支是同一目录中的不同提交历史。Worktree 更适合需要同时处理多个复杂任务的场景。

**Q：Claude 创建的 Worktree 会不会影响主仓库？**

不会。Worktree 有独立的目录，内容不会影响主仓库的未提交更改。

**Q：如何让 Claude 在特定分支上工作？**

先切换分支再启动 Claude，或者使用 `claude --worktree <name>` 自动创建分支。

**Q：Worktree 太多会不会占用太多空间？**

Git Worktree 共享仓库历史，新目录只包含分支差异，所以占用空间很小。

**Q：子代理的 Worktree 什么时候清理？**

正常完成时，Claude 会提示你选择保留或删除。因崩溃孤立的 worktree 会在超过 `cleanupPeriodDays` 设置的天数后自动删除。

