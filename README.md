# 📚 KB Creator — 基于 Karpathy LLM Wiki 理念的知识库生成器

一键生成开箱即用的 Obsidian 知识库，让 AI 帮你把散乱的收藏编译成结构化的知识系统。

> 灵感来源：[Andrej Karpathy 的 LLM Knowledge Bases](https://x.com/karpathy/status/2039805659525644595)（2026.04.03）

---

## 💡 为什么需要这个？

大多数人的知识管理长这样：

```
收藏文章 → 放进收藏夹 → 再也不看 → 知识腐烂
```

即使用了 AI，也是每次对话从零开始，没有积累：

```
问 AI 问题 → 得到回答 → 下次再问 → AI 完全忘了上次聊什么
```

**Karpathy 的洞察**：不要让 AI 帮你找答案，让 AI 帮你**建知识**。

```
丢原始材料 → AI 编译成知识库 → 基于知识库提问和写作 → 新洞察回流知识库 → 越用越强
```

本项目把这套方法论封装成了一个**即开即用的工具**——告诉 AI 你想建什么领域的知识库，2 分钟生成完整的脚手架。

---

## 🏗️ 架构概览

```
your-knowledge-base/
├── CLAUDE.md                    # 知识库的"大脑"——告诉 AI 如何工作
├── raw/                         # 📥 你的原始材料（只读，人类策展）
├── research/                    # 🔬 AI 的补充研究（AI 策展）
├── wiki/                        # 📖 编译产物——结构化知识库
│   ├── INDEX.md                 #    知识库索引
│   ├── _compile-log.md          #    编译日志（追踪编译状态）
│   ├── _changelog.md            #    变更日志（所有操作历史）
│   └── {分类文件夹}/             #    按主题自动分类
│       └── {文章}.md
├── outputs/                     # 📝 写作产出和分析报告
├── .claude/commands/            # ⚡ 五个一键命令
│   ├── compile.md
│   ├── lint.md
│   ├── write.md
│   ├── research.md
│   └── health-check.md
├── lock-raw.bat / lock-raw.sh   # 🔒 锁定 raw/ 防止 AI 误改
└── unlock-raw.bat / unlock-raw.sh
```

### 四个文件夹各司其职

| 文件夹 | 谁写入 | 作用 |
|--------|--------|------|
| `raw/` | 你 | 你的原始材料仓库，什么都可以丢，不需要整理 |
| `research/` | AI | AI 发现知识空白时，主动搜索外部信息保存于此 |
| `wiki/` | AI | AI 编译的结构化知识库，自动分类、自动链接 |
| `outputs/` | AI | 基于 wiki 产出的文章、报告、分析 |

---

## ⚡ 五个命令

| 命令 | 作用 | 使用场景 |
|------|------|----------|
| `/compile` | 编译 raw/ 和 research/ 的新材料到 wiki | 丢了新材料后运行 |
| `/lint` | 检查格式并自动修复 | 编译后运行，确保质量 |
| `/write` | 基于 wiki 写文章，新洞察可回流 wiki | 想产出内容时运行 |
| `/research` | AI 主动搜索外部信息补充知识空白 | wiki 信息不足时运行 |
| `/health-check` | 全面体检，发现矛盾和空白 | 定期运行（建议每月） |

---

## 🔄 知识飞轮

这套系统的核心价值不是单个命令，而是它们组成的**复利循环**：

```
你丢材料 → raw/
                ↘
                  /compile → wiki（知识库）
                ↗                ↓
AI 搜索补充 → research/      /write 写文章
                ↑                ↓
           /research        新洞察回流 wiki
                ↑                ↓
     /health-check 发现空白   wiki 更强了
                                 ↓
                           下次产出更好 → ...
```

**每一次深度写作，都在给知识库施肥。** wiki 越强，写出的文章越好，文章里的新洞察又回流 wiki。这就是知识的复利。

---

## 🚀 快速开始

### 前置条件

- [Obsidian](https://obsidian.md/)（免费，用于浏览知识库）
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)（用于运行命令）
- 或任何兼容 Anthropic API 的模型（如 MiniMax M2.7）

#### 推荐：安装 Claudian 插件

[Claudian](https://github.com/YishenTu/claudian) 将 Claude Code 直接嵌入 Obsidian 侧边栏，不用在 Obsidian 和终端之间来回切换，所有斜杠命令在 Obsidian 内即可完成。

安装方式：
1. 从 [Claudian Releases](https://github.com/YishenTu/claudian/releases/latest) 下载 `main.js`、`manifest.json`、`styles.css`
2. 在 vault 的 `.obsidian/plugins/` 目录下创建 `claudian` 文件夹，将文件放入
3. Obsidian 设置 → 第三方插件 → 启用 Claudian
4. 确保已安装 Claude Code CLI 并配置好 API（直接用 Claude 订阅或第三方兼容 API 均可）

### 方式一：使用 Skill 自动生成（推荐）

如果你在 Claude.ai 或 Claude Code 中安装了本 Skill：

1. 对 AI 说 **"帮我创建一个知识库"**
2. 回答 2 个问题（领域 + 文件夹名）
3. 下载生成的 zip，解压即可

AI 会根据你的领域自动定制：
- 知识分类体系（type）
- 文章结构模板
- 完整示例文章
- 文件命名规则
- 时效性规则

### 方式二：手动使用模板

1. Clone 本仓库
2. 复制 `templates/` 中的命令文件到你的 vault 的 `.claude/commands/`
3. 参考 `references/claude-md-template.md` 编写你自己的 `CLAUDE.md`
4. 创建 `raw/`、`research/`、`wiki/`、`outputs/` 文件夹

### 开始使用

#### 通过 Claudian 在 Obsidian 内操作（推荐）

安装 Claudian 后，所有操作都在 Obsidian 侧边栏完成，无需打开终端：

1. 点击侧边栏的 Claudian 图标打开对话面板
2. 输入 `/compile` 开始编译
3. 用 `@[[文件名]]` 可以引用 vault 中的文件作为上下文
4. 所有五个斜杠命令均可直接使用

#### 通过终端操作

```bash
# 进入你的知识库目录
cd path/to/your-knowledge-base

# 启动 Claude Code
claude

# 丢一些材料到 raw/ 后，开始编译
> /compile

# 检查格式
> /lint

# 基于 wiki 写文章
> /write 帮我写一篇关于 xxx 的深度分析

# AI 主动补充研究
> /research xxx 的最新进展

# 定期体检
> /health-check
```

---

## 📋 CLAUDE.md 详解

`CLAUDE.md` 是整个知识库的灵魂——它告诉 AI 如何编译和维护你的知识。

### 固定部分（所有知识库通用）

- **编译哲学**：自下而上，不预设框架，让材料决定结构
- **raw/ 只读规则**：AI 绝不修改你的原始材料
- **格式强制要求**：必须有 frontmatter、必须用 `[[]]` 链接
- **INDEX.md 自动归类**：分类由 AI 根据内容自动生成和调整
- **编译/查询/维护的任务流程**

### 动态部分（根据领域定制）

| 模块 | 说明 | 示例 |
|------|------|------|
| 领域定位 | 知识库的核心描述 | "商业·投资·创业" |
| type 分类体系 | 知识分几种类型 | company / concept / framework |
| 文章结构模板 | 每种 type 的章节 | 公司类要"商业模式""护城河" |
| 示例文章 | 完整的标杆文章 | 飞轮效应 / ReAct 模式 |
| 文件命名规则 | 中文优先的命名示例 | ✅ `飞轮效应.md` ❌ `flywheel-effect.md` |
| 时效性规则 | 哪些 evergreen 哪些 volatile | 基础理论 evergreen，公司数据 volatile |

---

## 🔧 核心机制

### 编译日志（避免重复编译）

`wiki/_compile-log.md` 记录每个 raw/ 和 research/ 文件的编译状态。`/compile` 只处理新增和修改的文件，不会重复编译。

### 增量更新

新材料如果和已有 wiki 文章主题重叠，不会创建重复文章，而是**合并更新**到已有文章中。wiki 文章随材料积累越来越厚实。

### 变更日志

`wiki/_changelog.md` 记录所有操作历史。每次 /compile、/lint、/write、/research、/health-check 都会留下记录。

### 知识回流

`/write` 写完文章后，可以将其中的**新洞察**、**新概念**、**新关联**提取回流到 wiki，而不是把整篇文章塞进 wiki。原文保留在 outputs/。

### 来源追溯

wiki 文章的 `sources` 字段清楚标注每条信息来自 `raw/`（你的材料）还是 `research/`（AI 补充），你随时知道知识的来源。

---

## 📁 项目结构

```
kb-creator/
├── SKILL.md                           # Skill 主文件（定义交互流程）
├── references/
│   └── claude-md-template.md          # CLAUDE.md 生成模板
└── templates/
    ├── compile.md                     # /compile 命令
    ├── lint.md                        # /lint 命令
    ├── write.md                       # /write 命令
    ├── research.md                    # /research 命令
    ├── health-check.md                # /health-check 命令
    ├── lock-raw.bat / lock-raw.sh     # 锁定 raw/
    └── unlock-raw.bat / unlock-raw.sh # 解锁 raw/
```

---

## 🌟 使用案例

本工具已测试生成的知识库类型：

- **AI & Agent 技术知识库**：论文、框架、设计模式
- **商业·投资·创业知识库**：公司分析、商业模式、投资框架
- **个人操作系统知识库**：决策框架、人物方法论、思维模型
- **身体管理知识库**：训练方法、饮食营养、护肤

理论上适用于任何领域——只要你有材料可以丢进 raw/，AI 就能帮你编译成结构化的知识库。

---

## 📖 灵感与致谢

- [Andrej Karpathy](https://x.com/karpathy) — LLM Knowledge Bases 方法论的提出者
- [Karpathy 的 GitHub Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — 原始 idea file
- [Obsidian](https://obsidian.md/) — 最适合这套工作流的知识管理工具
- [Claudian](https://github.com/YishenTu/claudian) — 将 Claude Code 嵌入 Obsidian 侧边栏的插件，体验更流畅
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) — 驱动整套系统的 AI 编程工具

---

## 📝 License

MIT
