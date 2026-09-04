# 🧰 工具箱（Toolbox）

> Claude Code 工具档案总览。主代理、子代理、技能三个分区**严格隔离**，各司其职。
> 本目录是 [obsidian-archivist](https://github.com/Zarrc/obsidian-archivist) 的组成部分；安装与总览见仓库根 `README.md`。

## 分区结构

| 分区 | 路径 | 内容 | 谁维护 |
|------|------|------|--------|
| **主代理档案** | `CLAUDE.md`（vault 根） | 主代理行为规范 + Wiki Schema（"说明书"） | 用户 + Claude 共同演进 |
| **子代理档案** | `.claude/agents/` | 每个子代理一个 .md（frontmatter 定义名称/描述/工具） | Claude 按需创建 |
| **技能工具箱** | `.claude/skills/` | 可复用 Skill 工具（SKILL.md） | 用户安装 / Claude 创建 |

## 子代理清单

| 名称 | 用途 |
|------|------|
| [`notes-archivist`](agents/notes-archivist.md) | 知识库维护专员：wiki 摄取 / 建页 / 统计 / lint |

## 技能清单

| 技能 | 用途 |
|------|------|
| `defuddle` | 网页内容提取 |
| `json-canvas` | JSON Canvas 文件编辑 |
| `obsidian-bases` | Obsidian Bases 数据库 |
| `obsidian-cli` | Obsidian CLI 操作 |
| `obsidian-markdown` | Obsidian Markdown 语法（callouts / embeds / properties） |

> 5 个技能来自 [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)（MIT），原样内置。

## 隔离约定

1. 主代理、子代理、技能的 md 档案**严格分文件夹存放，互不混放**
2. 新增子代理 → 放 `.claude/agents/`，并在本 README 清单登记
3. 新增技能 → 放 `.claude/skills/`，并在本 README 清单登记
4. `commands/` 只放斜杠命令定义

## 笔记工作流（Notes Workflow）

1. **子代理执行**：用户提供资料 → 主代理派 `notes-archivist` 执行摄取 / 建页 / 统计 / lint，产出**直接写入当前 vault**
2. **用户审阅**：子代理完成后，用户检查改动
3. **主代理总结**：用户同意后，主代理做**最终总结归纳**与汇报

> 子代理只产出笔记文件，不做面向用户的最终总结；最终归纳权在「用户 → 主代理」。
> 认证备考不是本工具箱的职责，请使用独立的 [cert-tracker](https://github.com/Zarrc/cert-tracker)。
