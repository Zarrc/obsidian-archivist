# 🗂️ Obsidian Archivist

> 给 Claude Code 用的 **Obsidian 知识库 Agent 工具箱** —— 把"第二大脑"变成可持续生长的结构化知识库。
>
> 丢一篇文章/链接/摘要进来，主代理派 `notes-archivist` 子代理自动完成：**来源摘要 → 概念/实体提炼 → 交叉引用 → 索引与日志更新**。基于 Karpathy 的"自下而上建概念"方法论，知识从多篇来源中自然浮现，而不是单篇文章的堆砌。

本仓库打包了一套**已在真实 Obsidian vault 中运行**的部署（Schema + 子代理 + 5 个技能），可直接复制进你的 vault 使用。

---

## ✨ 特性

| 特性 | 说明 |
|------|------|
| 📥 **来源摄取（Ingest）** | 读 `raw/` 资料（含图片）→ 写 `wiki/(主题域)/来源/` 摘要页 |
| 🧠 **自下而上建概念** | 同一概念/实体在 **≥2 篇来源** 中出现才建独立页（Karpathy 规则），避免过度抽象 |
| 🔗 **全库交叉引用** | Obsidian Wiki-link 图谱；来源页顶部干净链接回 raw 文件 |
| 📊 **索引 + 新奇观点** | `wiki/index.md` 自动同步 + `⭐ 新奇观点` 置顶区 |
| 🧹 **Lint 健康检查** | 孤儿页 / 缺页链接 / 矛盾说法 / index 失同步一键排查 |
| 📜 **操作日志** | `wiki/log.md` 仅追加，每次操作留痕 |

## 📦 包含什么

```
obsidian-archivist/
├── CLAUDE.md               ← 主代理 Schema（wiki 知识库行为规范，"说明书"）
├── .claude/
│   ├── README.md           ← 工具箱总览
│   ├── agents/
│   │   └── notes-archivist.md   ← 知识库维护子代理（摄取/建页/lint）
│   └── skills/             ← 5 个 Obsidian 技能（原样内置）
│       ├── obsidian-markdown/   · obsidian-bases/
│       ├── json-canvas/         · obsidian-cli/
│       └── defuddle/
└── template-vault/         ← 新 vault 的目录骨架（照抄即用）
```

**5 个技能**均来自 [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)（MIT 许可），原样复制：

| Skill | 用途 |
|-------|------|
| `obsidian-markdown` | wikilinks / embeds / callouts / properties 等专属语法 |
| `obsidian-bases` | Obsidian Bases（`.base`）数据库视图 |
| `json-canvas` | JSON Canvas（`.canvas`）节点/连线编辑 |
| `obsidian-cli` | CLI 操作 vault、插件/主题开发 |
| `defuddle` | 网页 → 干净 Markdown（读链接时省 token） |

## 🚀 安装（~1 分钟）

1. **复制工具箱**到你的 vault 根目录：

   ```bash
   # 在你的 vault 根执行（把本仓库 clone 到临时位置后）
   cp CLAUDE.md .claude/    你的vault根/
   ```

   （或把 `.claude/` 与 `CLAUDE.md` 手动拖进 vault 根。）

2. **建目录骨架**（vault 为空时）：参照 `template-vault/`，创建 `raw/` `wiki/` `Project/` `Self/` `outputs/`。

3. **启动 Claude Code**：在 vault 根目录打开会话即可。Claude 会自动读取 `CLAUDE.md` + `wiki/index.md`。

### 开始使用

```text
你：摄取 raw/xxx 这篇（或贴一个 URL / 摘要）
Claude：派 notes-archivist → 产出 来源页/概念页/实体页 → 更新 index + log → 你审阅
你：检查 wiki / lint
Claude：查孤儿页、缺页链接、矛盾、index 失同步
```

> 💡 会话开始即让 Claude 先读 `CLAUDE.md`，行为即生效。

## 🧠 目录约定

| 目录 | 层级 | 说明 |
|------|------|------|
| `raw/` | 原始资料（**用户专属**） | 只读，Claude 不修改 |
| `wiki/` | 知识库（**Claude 维护**） | 按知识域/子话题组织：`来源/` `实体/` `概念/` `对比/` |
| `Project/` · `Self/` | 项目 / 个人笔记 | 用户自由 |
| `outputs/` | 查询输出 | 可随时清理 |

核心机制见 `CLAUDE.md`（页面 frontmatter 约定、Karpathy 创建时机规则、Ingest / Query / Lint 工作流、Index 维护、Log 格式）。

## 🔗 与 cert-tracker 的关系

认证备考（Domain 笔记、练习题、进度条）是**另一套独立系统** [cert-tracker](https://github.com/Zarrc/cert-tracker)，专门用于"按考试 Domain 自动归纳笔记 + 生成练习题 + 追踪进度"。

- 本仓库**刻意不重复** cert 备考工作流——如果你两者都在用，它们的分工是：
  - **知识/文章/想法** → 本仓库（wiki 知识库）
  - **考证/刷题/进度** → cert-tracker
- 两个仓库同源于同一套 vault 部署，`CLAUDE.md` 历史版本中曾包含 cert 工作流，现已拆出以避免两处维护。

## 📄 许可与归属

本仓库以 **[MIT](LICENSE)** 发布。

- 仓库结构、Schema、`notes-archivist` agent：© 2026 Zarrc，MIT。
- `.claude/skills/` 下 5 个 skill **原样复制**自 [kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)，版权归原作者 [Steph Ango / kepano](https://github.com/kepano)，同样以 MIT 发布 —— 其**原始版权声明已按要求保留**在 [LICENSE](LICENSE) 末尾。
