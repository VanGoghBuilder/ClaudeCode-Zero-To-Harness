# Claude Code Harness Engineering Guide

> 目标：把 OpenAI/Codex 语境里的 Harness Engineering 方法论，翻译成今天的 Claude Code 用户可以直接落地的做法。

## 一句话定义

Claude Code 的 Harness Engineering，就是用版本化的上下文、权限、工具、验证回路和角色分工，稳定地驾驭 Claude Code 的行为。

## Claude Code 自带的 harness 原语

- `CLAUDE.md`
- `.claude/settings.json`
- hooks
- MCP
- subagents
- slash commands

重点不是从零造 agent framework，而是在这些原语之上，补齐你项目自己的控制系统。

## 五层结构

| 层 | 目标 | Claude Code 对应物 |
|---|---|---|
| Memory | 让它知道“我们这里怎么做事” | `CLAUDE.md` |
| Permissions | 让它知道“什么能做，什么不能做” | `.claude/settings.json` |
| Tools | 让它能安全地接触外部系统 | MCP、scripts、commands |
| Delegation | 让复杂任务分角色、分上下文处理 | subagents |
| Back-pressure | 让“完成”必须经过验证 | hooks + test/lint/typecheck/review |

## 最小可行目录

```text
your-project/
├── CLAUDE.md
├── .claude/
│   ├── settings.json
│   ├── agents/
│   └── commands/
├── docs/
├── scripts/ai/
└── ...
```

## 最小起步方案

先做这 5 件事：

1. 写一个 40 行以内的 `CLAUDE.md`
2. 写一个 `scripts/ai/run-checks.sh`
3. 用 `.claude/settings.json` 隐藏敏感文件
4. 用 `Stop` hook 强制通过检查再结束
5. 建一个 `code-reviewer` subagent

## 一个关键原则

同一个问题出现两次，就不要再靠人提醒，应该把它写进 harness。

## 实施顺序

### Phase 1

- 建 `CLAUDE.md`
- 补 `docs/architecture.md`
- 明确 build/test/lint/typecheck 命令

### Phase 2

- 加 `run-checks.sh`
- 用 `Stop` hook 卡最终验证
- 把敏感区放进 permissions deny

### Phase 3

- 增加 `code-reviewer`、`test-runner`
- 把高频流程做成 slash commands

### Phase 4

- 按需上 MCP
- 建立 issue -> code -> review 的链路

## 常见误区

- 把 `CLAUDE.md` 当作万能药
- 把 harness 等同于“写很多提示词”
- 工具越多越强
- subagents 越多越高级
- 只做生成，不做垃圾回收

## 建议

优先做 deterministic 的 guide 和 sensor。不要一开始就把所有希望寄托在“再加一个更聪明的评审 agent”上。
