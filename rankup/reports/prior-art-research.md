# rankup — Prior Art Research

Version researched for: 2.32.0

## Summary

`rankup` 不是从零发明的：它的选词、体检与经验判定建立在公开 SEO 工具与经验帖之上，
数据获取与隐私边界吸收了两套既有自动化思路。本次复盘记录每个上游的 keep/adapt/reject/invent。

## Studied sources

### 哥飞 SEO 工具箱（https://seo.web.cafe）

- Why studied: `rankup` 的 `seo-webcafe.mjs` 直接对接其带后端工具（kd/serp/audit/backlink/worth/history/chat）。
- Learned (keep): 一个脚本打通多个 SEO 子工具；接口地图用真实浏览器会话逐个核对的证据方法。
- Adapted (apply): 接口地图记录在 `references/seo-webcafe.md`，匿名配额 10/日、登录 100/日、VIP 500/日 的边界写进 README 与配置说明。
- Rejected: 不把「看别人清单上有」当证据；KD 估算与实测区分开。
- Applied in: `scripts/seo-webcafe.mjs`、`references/seo-webcafe.md`、`references/webcafe-experiences.md`（十五条可执行裁定）。

### Google Trends / pytrends

- Why studied: `gt.py` 的四个子命令（热度对比、地区分布、相关飙升词、每日热搜）封装公开趋势数据。
- Learned (keep): 趋势数据用于小语种市场探测与「模糊方向收敛成真能做站的词」。
- Adapted (apply): 首次运行自动建 venv 装 pytrends；三套工作流写进 `references/trends.md`。
- Rejected: 不把单点热度当成「一定能做站」的证据。
- Applied in: `scripts/gt.py`、`references/trends.md`。

### 数据获取优先级（脚本 → API → 用户浏览器脚本 → 手动探路）

- Why studied: 反复出现「agent 跳过脚本直接操作浏览器」的高频错误，需要一条强制优先级。
- Learned (keep): 每一级向下的唯一理由是「上一级确实不存在」，不是「我对下一级更熟」。
- Adapted (apply): 写进 SKILL.md 红线，配套高频错误表；沙箱浏览器不在阶梯上（无登录态）。
- Rejected: 允许任何形式的跳级。
- Applied in: `SKILL.md`（红线与数据获取优先级）、`scripts/validate-rankup.mjs` 的机械断言。

## Keep / adapt / reject / invent

- keep: 真实浏览器会话核对接口地图；匿名配额边界；只信实测不信清单。
- adapt: 把哥飞工具箱包成一个零配置可跑的脚本；把趋势工作流固化成三套可复用方法。
- reject: 未实测的结论、跳级操作、把估计当证据。
- invent: `.rankup/` 项目记忆分层（Skill/项目/本机）、`sessions.mjs` 会话水位线挖掘、机械隐私断言。

## Evidence boundaries

- 本文件所有上游判断来自公开资料与实跑；站点名、流量数字、凭据位置不进入本仓库。
- 未经 provider 实测或人工盲评的选词/增长结论，需明确标注 `missing evidence`。
