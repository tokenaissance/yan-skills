# skill-link-check

> Skill 目录审计——「为什么这个 Skill 没生效」十次里有八次是符号链接的问题。

[![English](https://img.shields.io/badge/Docs-English-black)](README.md)
[![中文](https://img.shields.io/badge/Docs-%E4%B8%AD%E6%96%87-red)](docs/README.zh-CN.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](../LICENSE)

检查 `.agents/skills` 和 `.claude/skills` 是否遵守「前者存真源、后者用符号链接镜像」的约定，输出孤儿目录、缺失链接、重复目录、断链和错误目标，并给出需人工复核的修复命令。支持 JSON 证据输出。**它只报告，不动你的目录。**

```bash
npx skills add yan-labs/yan-skills --skill skill-link-check -g -y
```

## Why I built this

Skill 装多了以后，「为什么这个 Skill 没生效」十次里有八次是链接的问题：Skill 被直接创建成 `.claude/skills/<name>/` 真实目录，却没有进入 `.agents/skills/`。它看似已安装，实际不会随正常备份、同步或迁移流程保存。

## Natural-language examples

- 「检查一下 skill 链接，为什么 skill 没生效。」
- 「审计 .agents/skills 和 .claude/skills 的目录布局。」
- 「这个 skill 在 .claude/skills 里能加载，但在 .agents/skills 里找不到，怎么回事。」
- 「为什么 skill 没识别到，检查一下是不是链接断了。」
- 「帮我看一下重复的 skill 目录和断链。」

## What it produces

一份布局审计报告：每个问题的类别、数量、路径和需人工复核的修复命令；支持 `--json` 证据输出。无问题时明确报告 clean。

## One complete workflow

1. **Scope**: 确定要审计的作用域（项目级与/或全局 `.agents/skills`、`.claude/skills`）。
2. **Scan**: 读取两个目录树，检查 `SKILL.md` 发现机制。
3. **Diff**: 比对真源与镜像，识别孤儿、缺失链接、重复、断链、错目标。
4. **Report**: 输出分类结果与建议命令，供人工复核；不自动修改任何目录。

## Installation

```bash
# 首次全局安装，或更新失败时重新安装
npx skills add yan-labs/yan-skills --skill skill-link-check -g -y

# 更新已安装的全局 Skill
npx skills update skill-link-check -g -y
```

若使用项目级安装，去掉 `-g`；项目级更新使用 `npx skills update skill-link-check -p -y`。

## Prerequisites

- [ ] Python 3.10+：`python3 --version`

## Usage

```bash
python3 skill-link-check/check.py --scope all --json
```

## Local quality gate

本包通过 fastagent-meta-skill 的 `validate_skill.py` Production 门禁（0 failures）：

```bash
python3 /path/to/fastagent-meta-skill/scripts/validate_skill.py skill-link-check
python3 /path/to/fastagent-meta-skill/scripts/trigger_eval.py skill-link-check --cases evals/trigger_cases.json
```

## Troubleshooting

| 问题 | 常见原因 | 处理方式 |
|---|---|---|
| 报告一大堆孤儿 | 手动创建了 `.claude/skills/<name>/` 真实目录 | 移到 `.agents/skills/<name>/`，再建符号链接 |
| Skill 缺失链接 | 镜像目录没有对应链接 | 用报告的修复命令重建，复核后再执行 |
| 断链 | 真源被移动/删除 | 检查目标路径，重建或清理 |
| 想自动修复 | 本 Skill 只报告 | 手动执行报告给出的命令，或自己写迁移脚本 |

## Design philosophy

「只报告、不动目录」是刻意边界：目录布局属于使用者工作区，任何自动移动都可能覆盖未备份的改动。审计的价值在于把「哪有问题、怎么修」变成可复核的证据，而不是替使用者做决定。

## Credits and sources

无第三方上游。约定（`.agents/skills` 存真源、`.claude/skills` 符号链接镜像）来自 Agent 技能生态的通用目录规范。

## Security and evidence boundary

- 本 Skill 不自动移动、删除或覆盖任何被审计目录；所有修复命令仅供人工复核后执行。
- 无依赖第三方网络；不含任何密钥。
- 未经 provider 实测或人工盲评的输出对比，需明确标注 `missing evidence`。

## License

MIT（见仓库根 LICENSE）。
