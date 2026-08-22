# skillsmp 中文介绍

> 在 SkillsMP（1.6M+ 公开 SKILL.md 的索引）里搜 Agent Skill，并专门挖那些「写得好但没人知道」的冷门 Skill。

[![English](https://img.shields.io/badge/Docs-English-black)](../README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](../LICENSE)

搜 [SkillsMP](https://skillsmp.com) —— 目前最大的公开 Agent Skill 索引（1.6M+ 个 SKILL.md，来自 GitHub，覆盖 Claude Code、Codex、ChatGPT）。按关键词、分类、职业、语言过滤，并故意不按星数排序地挖冷门好 Skill。**动手写新 Skill 之前，先来这里搜一遍。**

```bash
npx skills add yan-labs/yan-skills --skill skillsmp -g -y
```

## 为什么做这个

要动手写一个新 Skill 之前，别人写过的概率比你以为的高。反复造轮子的成本不只是时间——你已经存在的同类 Skill 往往踩过你没踩过的坑。SkillsMP 让「这个领域已经有哪些 skill」变成一个可搜索的问题，而不是一次凭印象的赌。

## 它比普通做法多什么

| 能力 | 普通目录/搜索 | skillsmp |
|---|---|---|
| 覆盖范围 | 星数靠前的几百个 | 1.6M+ 全量索引 |
| 挖宝 | 按星数排，好货沉底 | `treasure.mjs` 故意不按星数排 |
| 过滤 | 关键词单层 | 关键词 + 分类 + 职业 + 语言 |
| 输出 | 只能看网页 | `--json` 供自动化 |
| 配额 | 需付费 | 匿名 50 次/天，配 Key 500 次/天 |

## 你可以直接这样说

- 「在 SkillsMP 里搜一下 skill。」
- 「帮我搜一个写得好但没人知道的冷门 skill。」
- 「有没有现成的 skill，避免重复造轮子。」
- 「这个领域已经有哪些 skill，帮我搜一下。」
- 「在 skills 市场找 agent skill。」

## 它会产出什么

```text
skillsmp/
├── SKILL.md            ← 怎么搜、怎么挖宝、两个必须知道的坑
├── .env.example        ← 复制成 .env 填 API Key（可选；.env 已被忽略，绝不提交）
└── scripts/
    ├── search.mjs      直搜。可翻页、可按分类/职业/语言过滤，可 --json
    └── treasure.mjs    ★ 挖宝。故意不按星数排
```

## 一套完整工作流

1. **Install**：`npx skills add yan-labs/yan-skills --skill skillsmp -g -y`。
2. **Search**：`node scripts/search.mjs "关键词" --limit 20`（匿名 50 次/天、10 次/分钟）。
3. **Filter**：按分类/职业/语言收窄。
4. **Treasure**：需要「写得好但没人知道」的，跑 `node scripts/treasure.mjs`。
5. **Decide**：判断该领域已有方案，避免重复造轮子。

## 安装与验证

```bash
# 首次全局安装，或更新失败时重新安装
npx skills add yan-labs/yan-skills --skill skillsmp -g -y

# 更新已安装的全局 Skill
npx skills update skillsmp -g -y
```

## 前置条件

- [ ] Node.js 18+：`node --version`
- [ ] 网络可访问 SkillsMP
- [ ] API Key 可选（匿名即可用）：`cp .env.example .env`

## 令牌配置

Key 从 <https://skillsmp.com/docs/api> 生成。它是凭据，**只放 `.env`，绝不进仓库**（仓库的 `.gitignore` 已拦了 `*/.env`）。

```bash
SKILLSMP_API_KEY=
```

## 常用脚本

```bash
# 直搜（可翻页、可过滤、可 --json）
node scripts/search.mjs "关键词" --limit 20

# 挖宝：写得好但没人知道的冷门 Skill
node scripts/treasure.mjs
```

## 本地质量门禁

本包通过 fastagent-meta-skill 的 `validate_skill.py` Production 门禁（0 failures）：

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py skillsmp
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py skillsmp --cases evals/trigger_cases.json
```

## 常见问题 / Troubleshooting

| 问题 | 常见原因 | 处理方式 |
|---|---|---|
| 匿名配额超了 | 50 次/天、10 次/分钟 | 等一分钟再试；或配 Key 提到 500 次/天 |
| 想按星数找热门 | 那是 search 的活 | 热门用 `search.mjs`，挖宝用 `treasure.mjs`，别混 |
| Key 报 401 | Key 无效或格式不对 | 重新在 skillsmp.com/docs/api 生成 |

## 设计哲学

「热门的不一定好，好货经常沉底」——`treasure.mjs` 故意不按星数排序，是为了让「写得好但没人知道」的 Skill 有机会被看见。搜索的价值在于降低「重复造轮子」的概率，而不是证明某条结果有多流行。

## 致谢与来源

- **[SkillsMP](https://skillsmp.com)** —— 本 Skill 的搜索目标与数据源；API Key 配额规则以 SkillsMP 为准。

## 安全与证据边界

- API Key 是凭据，只放 `.env`，绝不进仓库；仓库 `.gitignore` 已拦 `*/.env`。
- 本 Skill 只读索引与元数据，不执行、不安装搜到的 Skill；第三方 Skill 的运行风险由使用者自行评估。
- 未经 provider 实测或人工盲评的搜索结果对比，需明确标注 `missing evidence`。

## 许可证

MIT（见仓库根 LICENSE）。
