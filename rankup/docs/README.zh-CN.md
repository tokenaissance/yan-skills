# rankup 中文介绍

> 网站从零到一与长期增长的总控 Skill——把一个站点从「这个词能不能做」一路带到「上线三个月后该改哪一页」。

[![English](https://img.shields.io/badge/Docs-English-black)](../README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](../LICENSE)

`rankup` 不重复实现 Wrangler、Stripe 或趋势工具，它负责把这些能力串成一条长期可维护的网站工作流，并且在每个项目上记住你做过什么。**它是网站全生命周期的总控。**

```bash
npx skills add yan-labs/yan-skills --skill rankup -g -y
```

## 为什么做这个

独立站最大的成本不是工具，是「从头再来一遍」：这次做站踩过的坑，下次做站还要再踩。`rankup` 把机会调研、建站、部署、SEO、复盘每一步固化成脚本、数据文件和判定规则，让 AI Agent 直接照着执行，并把项目事实写回 `.rankup/` 项目记忆。

## 它比普通做法多什么

| 能力 | 手工/普通做法 | rankup |
|---|---|---|
| 项目上下文 | 靠记忆，跨会话丢失 | `.rankup/` 项目记忆，启动先对账线上真实状态 |
| 机会调研 | 开十几个标签页比对 | `seo-webcafe.mjs`（哥飞工具箱）+ `gt.py`（Google Trends） |
| 数据获取 | 随口写、随手点 | 强制优先级：脚本 → API → 用户浏览器脚本 → 手动探路 |
| 建站部署 | 每站重踩脚手架坑 | TanStack Start + Cloudflare Workers/D1/R2 默认栈 |
| 经验沉淀 | 留在对话里永远没人读 | `sessions.mjs` 挖会话，把纠正和踩坑落进文档 |
| 隐私边界 | 常把站点名写进 Skill | 机械断言，Skill 层禁止站点名/绝对路径/凭据 |

## 你可以直接这样说

- 「rankup init 一个新网站，用 TanStack Start 建站并部署到 Cloudflare。」
- 「帮我看一下 `ai headshot generator` 这个词能不能做站。」
- 「网站上线三个月了，GSC 显示已抓取但未编入索引，该怎么处理。」
- 「用 Google Trends 对比两个关键词热度，帮我选一个能建站的方向。」
- 「rankup review：这周的项目进展复盘一下。」

## 它会产出什么

```text
your-project/
├── .rankup/            ← 项目记忆：事实、决策、基线、实验、发布、日志
│   ├── facts.md
│   ├── decisions.md
│   ├── baselines.md
│   └── registry.md     ← 本机跨项目资产索引（gitignore 排除）
└── (你的网站，按默认栈搭建)
```

项目层的站点名、数字与凭据位置**留在各自项目的 `.rankup/`**，Skill 层只带剥离站点后仍然成立的通用方法。

## 一套完整工作流

1. **Context**：恢复项目上下文，与线上真实状态对账（域名解析、Cloudflare、GSC、支付，一律实时查询）。
2. **Research**：用 `seo-webcafe.mjs` / `gt.py` 查趋势、关键词难度、SERP 盘面、域名前世。
3. **Define**：页面、数据模型、架构与实施计划，产出 roadmap 与 P0–P2。
4. **Init**：初始化 Monorepo（绿地走批准过的 TanStack Start 脚手架），已上线老站从当前阶段接入。
5. **Deploy**：Cloudflare Workers/D1/R2 部署，验证真实域名、SSR、API、上传、鉴权、回调。
6. **Grow**：技术 SEO、内容、索引、CTR、AI 搜索优化；合规分发（外链交给 `backlink`）。
7. **Review**：只读体检 + `sessions.mjs` 挖对话，把新经验写回 `.rankup/`，进入下一轮。

## 安装与验证

```bash
# 交互式
npx skills add yan-labs/yan-skills --skill rankup

# 全局静默安装
npx skills add yan-labs/yan-skills --skill rankup -g -y

# 更新
npx skills update rankup -g -y
```

## 前置条件

- [ ] Node.js 18+：`node --version`
- [ ] Python 3.10+（`gt.py` 首次运行自动建 venv 装 pytrends）：`python3 --version`
- [ ] 令牌可选：`rankup/.env`（`SEO_WEBCAFE_TOKEN`、`SEO_WEBCAFE_COOKIE`，见下方配置）

## 令牌配置

令牌只放 `rankup/.env`，绝不入库（仓库 `.gitignore` 已排除，`validate-rankup.mjs` 有机械断言）：

```bash
SEO_WEBCAFE_TOKEN=     # 只有 kd 命令需要，wc_mcp_ 开头，去 /kd/docs 自助生成
SEO_WEBCAFE_COOKIE=    # 可选。给了就把配额从访客 10/日 提到登录 100/日、VIP 500/日
```

大部分子命令匿名即可跑；`gt.py` 完全不需要令牌。

## 常用脚本

```bash
# 关键词难度 + 前九名盘面 / 页面体检 / SERP 归因 / 外链估价 / 域名前世
node rankup/scripts/seo-webcafe.mjs kd "ai headshot generator"
node rankup/scripts/seo-webcafe.mjs audit https://example.com/page
node rankup/scripts/seo-webcafe.mjs serp "keyword"
node rankup/scripts/seo-webcafe.mjs history example.com

# Google Trends
python3 rankup/scripts/gt.py compare "keyword a" "keyword b"

# 项目体检（只读）/ 挖会话经验
node rankup/scripts/review.mjs --project-root . --days 30
node rankup/scripts/sessions.mjs --project-root . --days 14 --new-only --dump

# 改完 Skill 必跑
node rankup/scripts/validate-rankup.mjs
```

## 本地质量门禁

本包通过 fastagent-meta-skill 的 `validate_skill.py` Production 门禁（0 failures）：

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py rankup
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py rankup --cases evals/trigger_cases.json
```

## 常见问题 / Troubleshooting

| 问题 | 常见原因 | 处理方式 |
|---|---|---|
| `kd` 401 | 匿名配额或没配令牌 | 配 `SEO_WEBCAFE_TOKEN`；`chat` 强制登录 |
| 脚本没跑、直接手工操作 | 跳过数据获取优先级 | 先查脚本清单，命中即停，禁止重造轮子 |
| 项目信息被写进 Skill | 站点名/路径进了 Skill 层 | 跑 `validate-rankup.mjs`，断言会构建失败 |
| 会话经验永远没人读 | 从没挖过对话记录 | `sessions.mjs --dump` 找纠正、结论、根因、推翻 |

## 设计哲学

Skill 不是一份不可改的标准答案，而是把个人经验编译成 Agent 可执行的源码。先装、先跑一个真实任务，然后 fork：删掉不属于你的规则，加入你自己的判断与边界。`rankup` 的红线是「先查脚本清单，禁止重造轮子」——现写的实现每次形状都不一样，结果不可比。

## 致谢与来源

- **[哥飞](https://seo.web.cafe)** —— 选词与体检能力建立在他的 SEO 工具箱之上；`references/webcafe-experiences.md` 的十五条裁定也来自他公开的经验帖。
- 原 `gt` Skill（Google Trends 与选词工作流）于 2026-08-16 并入本 Skill，现为 `references/trends.md` 加 `scripts/gt.py`。

## 安全与证据边界

- Skill 层不含站点名、域名、流量数字、property ID、绝对路径与凭据位置；`validate-rankup.mjs` 机械断言，出现即构建失败。
- 项目层事实留在各项目 `.rankup/`；本机层 `registry.md` 含项目路径，被 gitignore 排除且有断言拦住 `git add -f`。
- 所有数据获取优先级明确，未经实测的结论要标注，不把估计当证据。

## 许可证

MIT（见仓库根 LICENSE）。
