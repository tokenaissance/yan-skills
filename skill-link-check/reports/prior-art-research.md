# skill-link-check — Prior Art Research

Version researched for: 1.0.0

## Summary

`skill-link-check` 是轻量目录审计 Skill，无第三方上游。它固化的约定（`.agents/skills` 存真源、
`.claude/skills` 符号链接镜像）来自 Agent 技能生态的通用目录规范。

## Studied sources

### Agent 技能目录规范（.agents/skills ↔ .claude/skills）

- Why studied: 多个 Agent 运行时（Claude Code、Codex、Cursor）按 `SKILL.md` 目录发现技能，「为什么 skill 没生效」经常是符号链接漂移。
- Learned (keep): 真源与镜像分离 + 符号链接镜像是最稳的安装布局；直接建实体目录会丢失同步。
- Adapted (apply): 审计逻辑围绕「真源在 `.agents/skills`、镜像在 `.claude/skills`」展开，输出孤儿/缺失/重复/断链/错目标。
- Rejected: 不自动移动、删除或覆盖任何目录——审计只报告，修复交人工。

## Keep / adapt / reject / invent

- keep: 真源/镜像分离约定；JSON 证据输出。
- adapt: 把「手动核对目录布局」固化为可重复的审计脚本。
- reject: 自动修复（可能覆盖未备份改动）；把审计当「退出码 0」的任务。
- invent: 审计证据完整性作为完成条件（发现问题是有效结果）。

## Evidence boundaries

- 本 Skill 不修改被审计目录；无网络依赖；不含密钥。
- 未经 provider 实测或人工盲评的输出对比，需明确标注 `missing evidence`。
