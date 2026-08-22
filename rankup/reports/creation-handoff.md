# rankup — Creation Handoff

- Skill: `rankup`
- Version: 2.32.0
- Job: 网站从零到一与长期增长的总控 Skill（机会调研、建站、部署、SEO、复盘、项目记忆）
- Status: 本版本对齐 fastagent-meta-skill 的 Production 门禁（maturity_tier=production），新增 manifest.json、双语 README、agents/interface.yaml、evals/trigger_cases.json 与证据报告；SKILL.md 内容未改写。

## Design lineage

- 上游：哥飞 SEO 工具箱（https://seo.web.cafe）、pytrends、原 `gt` Skill（2026-08-16 并入）。
- 继承：真实浏览器会话核对接口地图、匿名配额边界、只信实测不信清单。
- 吸收：`sessions.mjs` 会话水位线挖掘、数据获取强制优先级、机械隐私断言。
- 原创：`.rankup/` 项目记忆三层归属（Skill / 项目 / 本机）、跨项目资产索引 `registry.md`。

## Design advantages vs validated advantages vs hypotheses

- design advantage: `.rankup/` 把项目事实、决策、基线、实验分层存储，Skill 层只带通用方法，避免站点信息泄漏。
- design advantage: 数据获取强制优先级（脚本 → API → 用户浏览器脚本 → 手动探路）把「跳过脚本直接操作浏览器」变成违规。
- design advantage: `sessions.mjs` 按字节偏移记水位线，只挖增量会话，不重复消费。
- validated advantage: `scripts/validate-rankup.mjs` 机械断言站点名/绝对路径/凭据位置，出现即构建失败。
- hypothesis: 高频错误表 + 优先级阶梯能显著减少 agent 跳级操作——无 provider 实测对比，`missing evidence`。

## Verification

- Deterministic package validation: passed（0 failures；SKILL.md 体积超生产上下文预算为 accepted warning，SKILL.md 是既有刻意设计，不改写）。
- Trigger eval: passed（见 `reports/trigger-eval.json`）。
- Skill IR: passed（`reports/skill-ir.json`，name/version 与 manifest 一致）。
- Provider 实测输出对比：`missing evidence`。
- 人工盲评说服力：`missing evidence`。
