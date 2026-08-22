# skill-link-check — Creation Handoff

- Skill: `skill-link-check`
- Version: 1.0.0
- Job: 审计 `.agents/skills` 与 `.claude/skills` 布局，报告孤儿/缺失/重复/断链/错目标并给出可复核修复命令
- Status: 本版本对齐 fastagent-meta-skill 的 Production 门禁（maturity_tier=production），新增 manifest.json、双语 README、agents/interface.yaml、evals/trigger_cases.json 与证据报告；SKILL.md 内容未改写。

## Design lineage

- 上游：无第三方上游（约定来自 Agent 技能生态通用目录规范）。
- 继承：真源/镜像分离 + 符号链接镜像。
- 吸收：审计证据完整性作为完成条件。
- 原创：孤儿/缺失/重复/断链/错目标五类分类 + JSON 证据输出。

## Design advantages vs validated advantages vs hypotheses

- design advantage: 只报告、不动目录，杜绝覆盖未备份改动。
- design advantage: 审计证据完整性作为完成条件——发现问题是有效结果，不为了让检查通过而擅自修复。
- design advantage: 支持 JSON 证据输出，可接入 CI 或定期巡检。
- hypothesis: 五类分类能覆盖绝大多数 Skill 目录漂移——无大规模样本实测，`missing evidence`。

## Verification

- Deterministic package validation: passed（0 failures；SKILL.md 体积超生产上下文预算为 accepted warning）。
- Trigger eval: passed（见 `reports/trigger-eval.json`）。
- Skill IR: passed（`reports/skill-ir.json`，name/version 与 manifest 一致）。
- 既有 `tests/` 与 `check.py`：本次未改动。
- Provider 实测输出对比：`missing evidence`。
- 人工盲评说服力：`missing evidence`。
