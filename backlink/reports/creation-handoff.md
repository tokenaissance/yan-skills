# backlink — Creation Handoff

- Skill: `backlink`
- Version: 3.1.0
- Job: 完整的外链生命周期（发现、资格评估、安全填表、证据化验证、无 API 后台取数）
- Status: 本版本对齐 fastagent-meta-skill 的 Production 门禁（maturity_tier=production），新增 manifest.json、双语 README、agents/interface.yaml、evals/trigger_cases.json 与证据报告；SKILL.md 内容未改写（XML 法则结构是既有刻意设计）。

## Design lineage

- 上游：flaqai/backlink_skills（MIT）、aaron-he-zhu/seo-geo-claude-skills（Apache-2.0）、社区第三方外链清单。
- 继承：验证优先的批量泳道、逐动作授权、断点恢复、分析/外联模板（保留上游归属）。
- 吸收：三道闸（inspect → safe-fill → release-submit-guard）、只用自己的登录态浏览器、证据化台账。
- 原创：台账证据链（indexed 必须指名引擎）、通用虚拟滚动表格提取器、两级行数自查。

## Design advantages vs validated advantages vs hypotheses

- design advantage: 三道闸把「自动提交」变成违规——每次提交都要人点头。
- design advantage: 台账证据链让「投出去不算数」可机械核查，`indexed` 必须指名引擎。
- design advantage: `harvest.browser.js` 通用提取无 API 后台表格，与外链无关的场景也能复用。
- validated advantage: `data/submission-targets.json` 等 4 个数据文件有 JSON Schema + CI 门禁（`validate-data.mjs`），每条状态有实测支撑。
- hypothesis: 验证优先的批量泳道能显著提升大批量投放的通过率——无 provider 对比实测，`missing evidence`。

## Verification

- Deterministic package validation: passed（0 failures；SKILL.md 体积超生产上下文预算为 accepted warning，XML 法则结构不改写）。
- Trigger eval: passed（见 `reports/trigger-eval.json`）。
- Skill IR: passed（`reports/skill-ir.json`，name/version 与 manifest 一致）。
- Data validation（`scripts/validate-data.mjs`）：既有 CI 门禁，本次未改动数据文件。
- Provider 实测输出对比：`missing evidence`。
- 人工盲评说服力：`missing evidence`。
