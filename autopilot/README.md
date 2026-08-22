# autopilot

> 一句话到无人值守执行完——把 bug 修了、补测试、优化性能，中途不问你。

[![English](https://img.shields.io/badge/Docs-English-black)](README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](docs/README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](../LICENSE)

扔一句「把 bug 修了」「优化下性能」，它自动调查、分类、拆成 XML 阶段计划、选 Skill、定完成判定，然后以无人值守模式通过 `loop/goal` + `agent-mode` 完整执行到底——包括自动部署、自动 E2E、自动 code review，不跳阶段。

**调用 autopilot = 授权全自动无人值守执行，中途不问你。** 无依赖。

```bash
npx skills add yan-labs/yan-skills --skill autopilot -g -y
```

## Why I built this

模糊任务最费的不是执行，是「拆」。一个宽泛指令如果每次都从零拆，拆法不同、深度不同、完成标准不同，结果不可比，还得反复追问。`autopilot` 把「拆解 + 执行 + 验证」固化成一条不跳阶段的流水线：调查 → 分类 → XML 阶段计划 → 选 Skill → 定完成判定 → 无人值守跑到底。

## Natural-language examples

- 「autopilot，把 bug 修了。」
- 「把这个功能做完，你来拆。」
- 「优化性能，你自己搞定。」
- 「直接跑，把代码扫一遍。」
- 「调查一下为什么 XX，帮我规划并自动部署。」

## What it produces

一个执行到位的任务结果，包含：阶段计划、完成判定、自动部署、E2E 验证与 code review 结论。不会只给你一个「建议怎么做」的方案然后停住。

## One complete workflow

1. **Investigate**: 调查任务上下文。
2. **Classify**: 判断类型与边界。
3. **Plan**: 拆成 XML 阶段计划，选 Skill，定完成判定。
4. **Execute**: 无人值守跑到底（loop/goal + agent-mode）。
5. **Verify**: 自动部署、自动 E2E、自动 code review，不跳阶段。
6. **Wrap**: 收尾并报告结果。

## Installation

```bash
# 首次全局安装，或更新失败时重新安装
npx skills add yan-labs/yan-skills --skill autopilot -g -y

# 更新已安装的全局 Skill
npx skills update autopilot -g -y
```

若使用项目级安装，去掉 `-g`；项目级更新使用 `npx skills update autopilot -p -y`。

## Prerequisites

- [ ] 一个支持 `loop/goal` + `agent-mode` 的 Agent 运行时（如 Claude Code / Codex）
- [ ] 明确理解：调用即授权全自动执行

## Local quality gate

本包通过 fastagent-meta-skill 的 `validate_skill.py` Production 门禁（0 failures）：

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py autopilot
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py autopilot --cases evals/trigger_cases.json
```

## Troubleshooting

| 问题 | 常见原因 | 处理方式 |
|---|---|---|
| 半途停下来问 | 运行时没有 `agent-mode` / 授权边界被掐断 | 用 `loop/goal` 重新挂载，明确无人值守授权 |
| 拆得太碎 | 阶段计划粒度不合适 | 先定完成判定再拆阶段，阶段按判定对齐 |
| 期望它给方案而不是执行 | 没意识到调用了 autopilot | 只对「授权全自动执行」的任务使用 |

## Design philosophy

autopilot 不是「多此一举的自动化」，它把「拆解和执行分离」固化下来：调用它，等于把判断权和执行权一起交出去。用之前先想清楚——这条任务你是否真的愿意让它无人值守跑到尾。

## Credits and sources

无第三方上游。设计受 Skill 生态中「把个人经验编译成 Agent 可执行源码」的思路启发。

## Security and evidence boundary

- 调用 autopilot 即授权全自动无人值守执行，包括自动部署与自动 code review——请只对你有把握的任务使用。
- 本 Skill 无依赖、不含任何密钥；执行边界由你运行的 Agent 运行时决定。
- 未经 provider 实测或人工盲评的输出对比，需明确标注 `missing evidence`。

## License

MIT（见仓库根 LICENSE）。
