# Wiki Schema

> 这是 Claude 的行为配置文件（Schema）。它定义了这个知识库的结构、约定和工作流。
> 每次会话开始时，Claude 应先阅读此文件，再阅读 `wiki/index.md`，再开始工作。
> 本文件是 [obsidian-archivist](https://github.com/Zarrc/obsidian-archivist) 的组成部分：聚焦 **wiki 知识库轨**；认证备考请使用独立的 [cert-tracker](https://github.com/Zarrc/cert-tracker)。

---

## 仓库结构

```
/
├── CLAUDE.md              ← 本文件：Schema 配置（Claude 的"说明书"）
├── raw/                   ← 原始资料层（只读，不可修改）
│   ├── README.md
│   ├── 素材/              ← 文章内嵌图片的本地存储
│   └── (按主题或日期组织)
├── wiki/                  ← 知识库层（Claude 全权维护）
│   ├── index.md           ← 知识域导航（指向各域）
│   ├── log.md             ← 操作日志（仅追加）
│   └── (知识域)/          ← 按知识域归类，如 AI/
│       ├── index.md       ← 域内索引（置顶新奇观点 + 子话题列表）
│       ├── (子话题)/      ← 按子话题细分，如 AI/Harnessing/
│       │   ├── 来源/      ← 来源摘要页：每篇原始资料的提炼
│       │   ├── 实体/      ← 实体页：人、公司、产品、工具等
│       │   ├── 概念/      ← 概念页：原理、方法论、技术术语
│       │   └── 对比/      ← 对比分析页：跨来源横向综合
│       └── (更多子话题)/
├── Project/               ← 项目笔记
├── Self/                  ← 个人笔记
├── Cert/ (可选)           ← 认证备考资料（工作流见 cert-tracker）
└── outputs/               ← 查询输出
```

### 知识域与子话题

```
wiki/AI/                    ← 知识域示例（可按需换成任何主题）
├── index.md                ← ⭐ 新奇观点 + 子话题导航
├── Harnessing/             ← 子话题：Agent 系统工程
├── Agents/                 ← 子话题：Agent 模式
├── Skills/                 ← 子话题：Skill 工程
└── Models/                 ← 子话题：模型选型
```

每个域下的子话题可以灵活增减——资料多了随时拆，小了就合并。

### 层级规则

| 层级 | 目录 | 谁可以修改 |
|------|------|-----------|
| 原始资料 | `raw/` | **只有用户**，Claude 只读 |
| 知识库 | `wiki/` | **只有 Claude**，用户可读 |
| Schema | `CLAUDE.md` | 用户和 Claude 共同演进 |

---

## 工具箱与代理（Toolbox & Agents）

> Claude Code 工具档案分区。主代理、子代理、技能的 md 档案**严格隔离**，互不混放。

| 分区 | 路径 | 内容 |
|------|------|------|
| **主代理档案** | `CLAUDE.md`（本文件） | 主代理行为规范 + Wiki Schema |
| **子代理档案** | `.claude/agents/` | 专用代理定义（如 `notes-archivist`） |
| **技能工具箱** | `.claude/skills/` | 可复用 Skill 工具 |

> 完整清单见 `.claude/README.md`（工具箱总览）。

### 笔记子代理工作流（Notes-Archivist Workflow）

1. **子代理执行**：用户提供资料 → 主代理派 `notes-archivist` 子代理执行摄取 / 建页 / 统计 / 练习题等维护，产出**直接写入当前 vault**
2. **用户审阅**：子代理完成后，用户确认改动
3. **主代理总结**：用户同意后，主代理做**最终总结归纳**与面向用户的汇报

> 子代理只产出笔记文件，**不做**面向用户的最终总结；最终归纳权在「用户 → 主代理」。

---

## Wiki 页面约定

### Frontmatter（YAML）

所有 wiki 页面都应有以下 frontmatter：

```yaml
---
type: entity | concept | source | comparison
tags: [tag1, tag2]
sources: [来源文件名或 URL]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### 页面类型说明

| 类型 | 目录（在主题域内） | 命名格式 | 说明 |
|------|-------------------|---------|------|
| `entity` | `wiki/(主题域)/实体/` | `实体名.md` | 具体的人、组织、产品 |
| `concept` | `wiki/(主题域)/概念/` | `概念名.md` | 核心原理、方法论、术语 |
| `source` | `wiki/(主题域)/来源/` | `YYYY-MM-DD 标题.md` | 原始资料的摘要和要点 |
| `comparison` | `wiki/(主题域)/对比/` | `对比主题.md` | 跨来源横向对比分析 |

主题域示例：`AI/Harnessing/`、`AI/Agents/`、`AI/Models/` 等。  
Wiki 链接格式：`[[AI/Harnessing/实体/OpenAI]]` 或直接用 `[[OpenAI]]`（唯一文件名时）。

### 交叉引用

- 页面间用 Obsidian Wiki-link：`[[页面名]]`
- 提到的每个实体/概念都应链接到对应页面
- 如果对应页面不存在，先创建再引用
- **重要**：每个 `wiki/来源/` 摘要页的顶部必须用干净的 wiki-link 链接到对应的 raw 文件，例如：`[[raw/Effective harnesses for long-running agents]]`，**不要**用反引号包住，否则 Obsidian 图谱视图无法识别链接

### 创建时机规则（Karpathy 自下而上原则）

**核心思想**：Wiki 里的概念必须从多篇来源中自然浮现，而不是对单篇文章的过度抽象。

| 页面类型 | 创建条件 |
|---------|---------|
| 实体页 | 同一实体（人/公司/产品）在 **≥2 篇不同来源** 中被明确提及 |
| 概念页 | 同一概念（原理/方法/术语）在 **≥2 篇不同来源** 中被讨论 |
| 对比页 | 对比主题已在 **≥2 篇来源** 中分别出现不同立场或数据 |
| 来源摘要页 | 每篇原始资料都对应一个来源摘要页（**始终创建**，不受此规则约束） |

**具体流程**：

1. **第 1 篇来源出现**：在来源摘要页的 `tags` 和 Related Concepts 中列出该概念（但**不创建**独立概念页）
2. **第 2 篇来源出现**：检查前一来源摘要是否提到过该概念 → **是** → 创建概念/实体/对比页；**否** → 继续等待
3. **跨来源识别**：读新文章时，主动回溯已有来源摘要，发现与当前内容的重叠 → 触发建页

> 这个规则与 Ingest 工作流第 4 步（"更新相关概念页"）配合使用：如果概念只出现在当前一篇里，先列在来源页中，不创建独立页；如果发现已有来源页提到过，则立即创建。

---

## 工作流

### 1. Ingest（摄取新资料）

当用户说"摄取 XXX"或"处理 XXX"时：

1. **阅读** `raw/` 中的目标文件（含图片）
2. **讨论** 与用户确认关键要点和侧重点
3. **创建** `wiki/(主题域)/来源/YYYY-MM-DD 标题.md` 摘要页
4. **更新** 相关的实体页（不存在则创建）
5. **更新** 相关的概念页（不存在则创建）
6. **更新** `wiki/index.md`：添加新页面到对应表格，更新 Stats
7. **追加** `wiki/log.md`：格式 `## [YYYY-MM-DD] ingest | 标题`

> 一次摄取可能触及 10-15 个 wiki 页面，这是正常的。

### 2. Query（查询）

当用户提问时：

1. **阅读** `wiki/index.md` 找到相关页面
2. **深入** 阅读相关页面及其链接
3. **综合** 生成回答，标注来源页面
4. **归档**（可选）：如果回答有价值，将其写入 `wiki/对比/` 作为新页面
5. **追加** `wiki/log.md`：格式 `## [YYYY-MM-DD] query | 问题简述`

### 3. Lint（健康检查）

当用户说"检查 wiki"或"lint"时：

检查以下问题并逐一修复：

- [ ] 有无**互相矛盾**的说法（不同页面对同一事实描述不一致）
- [ ] 有无**孤儿页面**（没有任何入链的页面）
- [ ] 有无**提到但缺页**的实体/概念（有链接但目标页不存在）
- [ ] 有无**过时信息**（被新资料推翻但未更新的内容）
- [ ] `index.md` 是否与实际文件同步
- [ ] 有无值得新建的汇总/对比页

追加日志：`## [YYYY-MM-DD] lint | 发现 N 个问题`

### 4. 认证备考（Cert Prep）

本 Schema 聚焦 **wiki 知识库轨**，**不重复定义**认证备考工作流。

认证备考（按 Domain 归纳笔记、生成练习题、进度条等）请使用独立的 [cert-tracker](https://github.com/Zarrc/cert-tracker) 仓库：把资料放进它的 `resources/`，按其 `CLAUDE.md` 工作流处理。

如果当前 vault 里已有 `Cert/` 结构，处理其中的资料时参照 cert-tracker 的约定执行。

---

## Index 维护规则

`wiki/index.md` 的每一行格式：

```markdown
| [[wiki/(主题域)/来源/2026-04-06 标题]] | 一句话简介 | YYYY-MM-DD |
```

每次 ingest 或创建新页面后必须更新 index，同步更新 Stats 数字。

**新奇观点置顶规则**：  
index.md 顶部应设 `## ⭐ 新奇观点（Pinned）` 区块，列出：
- 反直觉或突破性的洞察
- 每个观点附简要说明 + 来源链接
- 标 ⭐ 的为强力推荐，标 💡 的为值得关注

当新资料带来真正新颖的视角时，更新此区块。

---

## Log 格式

```markdown
## [YYYY-MM-DD] 操作类型 | 标题

- 操作说明 bullet 1
- 操作说明 bullet 2
```

操作类型固定为：`init` / `ingest` / `query` / `lint` / `update`

---

## 图片处理

- 原始资料中的图片应下载到 `raw/素材/` 目录
- wiki 页面引用图片用：`![[素材/图片名.png]]`
- Claude 读取 Markdown 时先处理文本，再单独读取图片文件获取视觉信息

---

## 快速参考

```bash
# 查最近 5 条日志
grep "^## \[" wiki/log.md | head -5

# 统计 wiki 页面数（不含 index/log）
find wiki -name "*.md" | grep -v index | grep -v log | wc -l

# 统计某子话题页面数
find wiki/AI/Harnessing -name "*.md" | grep -v index | grep -v log | wc -l

# 查找孤儿页面（无入链）
# 请求 Claude 执行 lint 操作
```

---

*Schema 版本：v2.0 — 2026-09-04*（obsidian-archivist 公开发行版：聚焦 wiki 知识库轨；认证备考见 cert-tracker）  
*Schema 版本：v1.5 — 2026-08-17*（新增：工具箱与代理分区、笔记子代理工作流）  
*如需调整结构或约定，直接修改本文件，Claude 下次会话时会遵循新规则。*
