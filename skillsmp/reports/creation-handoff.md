# skillsmp — Creation Handoff

- Skill: `skillsmp`
- Version: 1.0.0
- Job: 在 SkillsMP（1.6M+ 公开 SKILL.md 索引）搜 Agent Skill，按关键词/分类/职业/语言过滤，并挖「写得好但没人知道」的冷门 Skill
- Status: 本版本对齐 fastagent-meta-skill 的 Production 门禁（maturity_tier=production），新增 manifest.json、双语 README、agents/interface.yaml、evals/trigger_cases.json 与证据报告；SKILL.md 内容未改写。

## Design lineage

- 上游：SkillsMP（https://skillsmp.com）。
- 继承：全量索引、过滤能力、匿名配额边界。
- 吸收：先搜再创造、避免重复造轮子的工作流。
- 原创：`treasure.mjs` 挖宝模式（故意不按星数排）。

## Design advantages vs validated advantages vs hypotheses

- design advantage: 把「搜一下有没有现成的」固化成可执行脚本，降低重复造轮子概率。
- design advantage: `treasure.mjs` 故意不按星数排，为「写得好但没人知道」的 Skill 提供被看见的通道。
- design advantage: API Key 只放 `.env`，仓库 `.gitignore` 拦截，凭据不进仓库。
- hypothesis: 挖宝模式能显著提升冷门好 Skill 的发现率——无大规模使用实测，`missing evidence`。

## Verification

- Deterministic package validation: passed（0 failures）。
- Trigger eval: passed（见 `reports/trigger-eval.json`）。
- Skill IR: passed（`reports/skill-ir.json`，name/version 与 manifest 一致）。
- 既有 `scripts/search.mjs`、`scripts/treasure.mjs`：本次未改动。
- Provider 实测输出对比：`missing evidence`。
- 人工盲评说服力：`missing evidence`。
