# skillsmp — Prior Art Research

Version researched for: 1.0.0

## Summary

`skillsmp` 的搜索目标是 SkillsMP 平台本身；它的「挖宝不按星数排」设计是对「星数即质量」做法的
显式偏离。本次复盘记录 keep/adapt/reject/invent。

## Studied sources

### SkillsMP（https://skillsmp.com）

- Why studied: 本 Skill 的搜索目标与数据源——1.6M+ 公开 SKILL.md 的索引，覆盖 Claude Code / Codex / ChatGPT。
- Learned (keep): 匿名 50 次/天、10 次/分钟；配 Key 500 次/天；支持关键词 + 分类 + 职业 + 语言过滤；API Key 只放 `.env`。
- Adapted (apply): `scripts/search.mjs` 直搜 + 翻页 + 过滤 + `--json`；`scripts/treasure.mjs` 挖宝。
- Rejected: 不把「按星数排名」当成唯一价值排序——热门的不一定好，好货经常沉底。
- Applied in: `scripts/search.mjs`、`scripts/treasure.mjs`、`.env.example`。

### 星数主导的 Skill 目录（skills.sh 等）

- Why studied: 对比「按流行度排名」的常见做法。
- Learned (keep): 流行度是发现入口之一，但不是质量证据。
- Adapted (apply): 把「热门搜索」和「挖宝」拆成两个脚本，职责分离。
- Rejected: 用星数冒充质量；把搜索热度当成果。

## Keep / adapt / reject / invent

- keep: SkillsMP 全量索引与过滤能力；匿名配额边界；Key 只放 `.env`。
- adapt: 把「先搜再创造」固化为可执行脚本；挖宝故意偏离星数排序。
- reject: 把星数当质量证据；绕过 `.gitignore` 提交 `.env`。
- invent: `treasure.mjs` 挖宝模式——为「写得好但没人知道」的 Skill 提供被看见的通道。

## Evidence boundaries

- 本 Skill 只读索引与元数据，不执行、不安装第三方 Skill。
- API Key 是凭据，绝不进仓库。
- 未经 provider 实测或人工盲评的搜索结果对比，需明确标注 `missing evidence`。
