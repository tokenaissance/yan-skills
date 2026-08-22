# autopilot — Creation Handoff

- Skill: `autopilot`
- Version: 1.0.0
- Job: 一句话到无人值守执行完（调查、分类、XML 阶段计划、选 Skill、完成判定、自动部署/E2E/code review）
- Status: 本版本对齐 fastagent-meta-skill 的 Production 门禁（maturity_tier=production），新增 manifest.json、双语 README、agents/interface.yaml、evals/trigger_cases.json 与证据报告；SKILL.md 内容未改写。

## Design lineage

- 上游：无第三方上游（独立原创）。
- 继承：Agent 运行时（loop/goal + agent-mode）作为执行载体。
- 吸收：「把个人经验编译成 Agent 可执行源码」的 Skill 生态思路。
- 原创：XML 阶段计划 + 完成判定 + 不跳阶段的全自动闭环。

## Design advantages vs validated advantages vs hypotheses

- design advantage: 「调用即授权全自动执行」的显式契约，杜绝中途反复追问。
- design advantage: 阶段计划 + 完成判定先行，避免拆得太碎或标准模糊。
- design advantage: 自动部署 + 自动 E2E + 自动 code review，不跳阶段。
- hypothesis: 显式完成判定能显著提升无人值守执行的可验收性——无 provider 对比实测，`missing evidence`。

## Verification

- Deterministic package validation: passed（0 failures；SKILL.md 体积超生产上下文预算为 accepted warning）。
- Trigger eval: passed（见 `reports/trigger-eval.json`）。
- Skill IR: passed（`reports/skill-ir.json`，name/version 与 manifest 一致）。
- Provider 实测输出对比：`missing evidence`。
- 人工盲评说服力：`missing evidence`。
