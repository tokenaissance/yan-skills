# backlink — Prior Art Research

Version researched for: 3.1.0

## Summary

`backlink` 的批量投放运维层、分析模板与数据方法论来自两个公开上游；浏览器取数与证据链是
在实跑中独立沉淀的。本次复盘记录每个上游的 keep/adapt/reject/invent。

## Studied sources

### flaqai/backlink_skills（MIT）

- Why studied: `backlink` 的批量投放运维层来自该项目：幂等键与队列分片、验证优先、逐动作授权、断点恢复、锚文本策略、报告纪律。
- Learned (keep): 大批量投放不该单目标循环——正确做法是先只读预检整批、把验证码集中成人工队列。
- Adapted (apply): 完整写进 `references/batch-campaign.md`；与 `inspect-page.mjs` / `safe-fill.mjs` / `release-submit-guard.mjs` 三道闸整合。
- Rejected: 不照搬其 Skill 本身（不带渠道数据，URL 由使用者提供）；清单入口一律实测复核。
- Applied in: `scripts/*`（批量泳道）、`references/batch-campaign.md`。

### aaron-he-zhu/seo-geo-claude-skills（Apache-2.0）

- Why studied: 提供外链质量评分矩阵、分析模板与外联模板。
- Learned (keep): 外链画像分析与外联文案的可复用模板结构。
- Adapted (apply): 保留为 `references/link-quality-rubric.md`、`references/analysis-templates.md`、`references/outreach-templates.md`，保留 Apache-2.0 许可证与归属。
- Rejected: 不把模板当「有数据支撑的外链画像」——模板能描述画像却拿不到画像，实测定级必须另走浏览器通道。
- Applied in: `references/` 三份模板。

### 第三方外链清单（社区流传，如 flaqai 公开的 Free-backlink-list.md 743 条渠道）

- Why studied: 验证「500 个免费外链网站」这类清单的真实开放率。
- Learned (keep): 实测后的开放率（5.5%）本身就是最有价值的信息。
- Adapted (apply): 归一化与差异对比结果记在 `references/instant-publish.md`；死链中一部分仍返回 200、内容已被改成加密货币推广页。
- Rejected: 不把「看别人清单上有」当证据。
- Applied in: `data/submission-targets.json` 的实测字段、`scripts/third-party-list-ingest.mjs`。

## Keep / adapt / reject / invent

- keep: 验证优先的批量泳道；逐动作授权；断点恢复；模板与归属保留。
- adapt: 把上游运维层安全内建进三道闸；把分析模板挂在以外链命名的 Skill 下，同时注明通用场景。
- reject: 未实测的目录清单；自动提交；把确认通知当已发布证据。
- invent: 台账证据链（candidate → … → rel_verified，indexed 必须指名引擎）、通用虚拟滚动表格提取器 `harvest.browser.js`、两级行数自查。

## Evidence boundaries

- 仓库不含任何账号与密钥；面板入口是公开 URL，账号活在使用者浏览器会话里。
- 每条渠道状态必须有实测支撑；`indexed` 必须指名引擎。
- 未经 provider 实测或人工盲评的输出对比，需明确标注 `missing evidence`。
