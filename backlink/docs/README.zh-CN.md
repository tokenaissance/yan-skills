# backlink 中文介绍

> 外链与登录态数据——去哪发、能不能发、发完有没有真的生效，全部带证据。

[![English](https://img.shields.io/badge/Docs-English-black)](../README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](../LICENSE)

`backlink` 是完整的外链生命周期 Skill：发现机会、评估资格、用你自己的登录态浏览器安全填表、**每次提交都要你点头**、再用证据验证结果。SKILL.md 是 XML 结构（v3.1）——这个 Skill 的主体是法则和路由，打了标签的块让「我刚才违反了哪一条」这个问题有名字可答。

**34 个脚本 + 20 篇参考文档 + 4 个机读数据文件。**

```bash
npx skills add yan-labs/yan-skills --skill backlink -g -y
```

## 为什么做这个

独立站做完之后，真正决定排名的往往不是内容，而是外链。但「500 个免费外链网站」这类清单 90% 是垃圾：一份第三方清单去重后 235 个目录，三批并行实测验完，直接能提交的只有 13 个（开放率 5.5%），另有 48 个已经死了。**这个比例本身就是最有价值的信息。** `backlink` 把「实测过的入口」变成可提交 PR、有 JSON Schema、有 CI 门禁的数据库。

## 它比普通做法多什么

| 能力 | 普通外链清单 | backlink |
|---|---|---|
| 数据资产 | 复制粘贴、没人维护 | 492 个入口 + 27 个免费渠道 + 141 个付费平台，每条有实测支撑 |
| 浏览器操作 | 手点界面，结果不可比 | 只用自己的登录态 Chrome（OpenCLI），脚本永不输入密码 |
| 提交安全 | 一键群发 | 三道闸：inspect → safe-fill → release-submit-guard，人点头才放行 |
| 验证 | 「提交成功」就当成功 | 台账证据链 candidate → … → rel_verified，indexed 必须指名引擎 |
| 批量投放 | 整批卡在第一个验证码 | 只读预检整批、验证码集中成人工队列、断点恢复 |
| 后台取数 | 没 API 就手抄 | 通用虚拟滚动表格提取器，任何无 API 的 SaaS 报表都能用 |

## 你可以直接这样说

- 「找一下 example.com 的外链来源，验证并评估外链质量。」
- 「把这些目录站的外链机会整理出来，填表但不真正提交。」
- 「用 Similarweb 找竞争对手的反链，把后台报表数据抓下来导出。」
- 「分析导出的外链 CSV：质量、毒性、锚文本多样性、竞争对手缺口。」
- 「把这次提交的台账核一遍，确认哪些是 public、indexed 还是 nofollow。」

## 它会产出什么

```text
backlink/
├── SKILL.md              ← 法则 + 路由 + 工作流入口
├── CONTRIBUTING.md       ← 证据规则与数据模型
├── data/                 ← 数据库：submission-targets / free-channels / paid-platforms / index-submission
├── references/           ← 20 篇参考文档（浏览器法则、批量投放、harvest、授权数据源…）
├── scripts/              ← 34 个脚本（health / targets-select / inspect-page / safe-fill / ledger…）
├── evals/                ← 触发评测与输出评测
└── agents/interface.yaml ← 跨 Agent 接口
```

## 一套完整工作流

1. **Qualify**：`targets-select.mjs` 从 492 条入口库取一个批次（open / captcha / account …）。
2. **Inspect**：`inspect-page.mjs` 探页面有没有可提交表单、登录与验证码状态。
3. **Fill**：`safe-fill.mjs` 填一份你已经审过的 payload，**永不提交**。
4. **Approve**：`release-submit-guard.mjs` 只在你对这一次提交明确点头之后放行。
5. **Verify**：`ledger.mjs` 逐项核对公开页 URL、重定向与 `rel` 属性，写入证据链。
6. **Harvest**：批量取数用 `harvest-*` 脚本 + `harvest.browser.js`，断点可续。

## 安装与验证

```bash
# 交互式
npx skills add yan-labs/yan-skills --skill backlink

# 全局静默安装
npx skills add yan-labs/yan-skills --skill backlink -g -y

# 更新
npx skills update backlink -g -y
```

## 前置条件

- [ ] Node.js 18+：`node --version`
- [ ] 浏览器动作前跑健康检查：`node backlink/scripts/health.mjs`
- [ ] 驱动你自己的 Chrome 需要 [OpenCLI](https://github.com/) 及其 Chrome 扩展（纯数据/方法部分不需要）
- [ ] 令牌可选：`backlink/.env`（见下方配置）

## 令牌配置

令牌只放 `backlink/.env`，绝不入库：

```bash
TOOLS_SHARE_DASHBOARD_URL=      # 你自己的数据面板入口
TOOLS_SHARE_APP_ORIGIN=         # 落地 origin，用来校验点开的是哪个产品
TOOLS_SHARE_APP_ORIGIN_SEMRUSH= # 另一张卡的 origin
```

数据库、方法论与填表守卫完全不依赖任何面板，不配也能用。

## 常用脚本

```bash
# 任何浏览器动作之前
node backlink/scripts/health.mjs

# 看看 492 条入口库 / 取一个批次
node backlink/scripts/targets-select.mjs --stats
node backlink/scripts/targets-select.mjs --cohort open

# 探页面表单/登录/验证码；填已审过的 payload（永不提交）
node backlink/scripts/inspect-page.mjs --url https://example.com/submit
node backlink/scripts/safe-fill.mjs --session <name> --scan ./scan.json --payload ./payload.json

# 批量流量筛查 / 台账
node backlink/scripts/similarweb-batch.mjs --domains-file domains.txt --out traffic.jsonl
node backlink/scripts/ledger.mjs list --state public

# 改数据必跑（CI 跑的就是这条）
node backlink/scripts/validate-data.mjs
```

## 本地质量门禁

本包通过 fastagent-meta-skill 的 `validate_skill.py` Production 门禁（0 failures）：

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py backlink
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py backlink --cases evals/trigger_cases.json
```

## 常见问题 / Troubleshooting

| 问题 | 常见原因 | 处理方式 |
|---|---|---|
| 打开网页没反应 | OpenCLI 未启动或扩展未装 | 跑 `health.mjs`，按输出修复 |
| 填表填错地方 | 没先 `inspect-page` 探表单 | 先探页再填；DOM 指纹不匹配就重探 |
| 卡在第一个验证码 | 单目标循环整批提交 | 走批量泳道：只读预检整批，验证码集中成人工队列 |
| 台账状态看起来对、数据是错的 | 现代数据网格没有 `<table>` | 两级行数自查，把虚拟滚动和正则盲区分开报 |
| 链接跳到 `/out.php?id=123` | 不是你以为的那个链接 | 公开页逐项核对 URL、重定向、`rel` 属性 |

## 设计哲学

Skill 的本质是「把个人经验编译成 Agent 可执行的源码」。`backlink` 的红线是证据：**每条渠道状态都要有实测支撑，不接受「我看别人清单上有」。** 你说它 `open-form`，那就是你自己打开过那个表单；你说它 `indexed`，那就得指名是哪个引擎。一个真实的被拒绝是小损失，一个未验证的进入是大损失。

## 致谢与来源

- **[flaqai/backlink_skills](https://github.com/flaqai/backlink_skills)**（MIT）—— 批量投放运维层：幂等键与队列分片、验证优先、逐动作授权、断点恢复、锚文本策略与报告纪律。见 `references/batch-campaign.md`。
- **[aaron-he-zhu/seo-geo-claude-skills](https://github.com/aaron-he-zhu/seo-geo-claude-skills)**（Apache-2.0）—— `references/` 下质量评分矩阵、分析模板与外联模板。
- 原 `backlink-analyzer` 与 `browser-harvest` 于 2026-08-16 并入本 Skill 并删除，资产保留在 `references/` 下并保留上游许可证与归属。

Upstream inspiration: https://github.com/flaqai/backlink_skills; https://github.com/aaron-he-zhu/seo-geo-claude-skills

## 安全与证据边界

- **仓库里不含任何账号和密钥。** 面板入口是公开 URL，账号活在你自己的浏览器会话里。
- 只用自己的登录态浏览器；脚本永不输入密码；会话按对话隔离，标签页不许串。
- 投出去不算数。台账证据链 candidate → qualified → filled → submitted → public → indexed@<engine> → rel_verified，每一跳都要证据。
- 数据文件是资产，参考文档是「怎么用它，以及怎么不骗自己」。全部机读、可提 PR、有 JSON Schema、有 CI 门禁。

## 许可证

除另有标注的第三方内容外采用 MIT License。`references/` 下三份分析模板来自上游 Apache-2.0 项目，许可证副本与归属说明保留在同目录。
