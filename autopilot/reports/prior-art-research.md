# autopilot — Prior Art Research

Version researched for: 1.0.0

## Summary

`autopilot` 是独立原创的自动化执行 Skill，无第三方上游。本次记录对既有 Agent 执行模式的调研，
以及为何不直接复用某个已有实现。

## Studied sources

### Agent 无人值守执行模式（loop / goal / agent-mode）

- Why studied: autopilot 依赖运行时提供 `loop/goal` + `agent-mode` 作为执行载体。
- Learned (keep): 长任务的执行载体是运行时能力，Skill 层应专注「拆解 + 判定」而非重造执行器。
- Adapted (apply): autopilot 不自己实现部署/E2E 的运行时，它负责调查、分类、XML 阶段计划、选 Skill、定完成判定。
- Rejected: 不把「建议怎么做」的方案当作交付物；不跳阶段、不半途而废。

### 同类自动化 Skill（通用「agent 自动执行」类能力）

- Why studied: 评估「把模糊指令直接转给 Agent」是否有现成更优方案。
- Learned (keep): 多数实现止步于「给步骤」；缺少显式的完成判定与不跳阶段纪律。
- Adapted (apply): autopilot 显式约定「调用即授权全自动执行，中途不确认」。
- Rejected: 不复制任何具体实现；保持无依赖。

## Keep / adapt / reject / invent

- keep: 长任务执行载体交给运行时（loop/goal + agent-mode）。
- adapt: 把「拆解 → 选 Skill → 完成判定 → 无人值守执行」固化为不跳阶段的流水线。
- reject: 给方案不执行；半途停下来问；跳阶段。
- invent: XML 阶段计划 + 完成判定 + 全自动部署/E2E/code review 的完整闭环。

## Evidence boundaries

- 本 Skill 无依赖、不含密钥；执行边界由使用者运行的 Agent 运行时决定。
- 未经 provider 实测或人工盲评的执行质量对比，需明确标注 `missing evidence`。
