---
name: kb-creator
description: 一键生成基于 Karpathy LLM Wiki 理念的 Obsidian 知识库脚手架。当用户提到"创建知识库"、"新建知识库"、"帮我搭一个知识库"、"Obsidian vault"、"LLM Wiki"、"卡帕西知识库"，或者想要一个可以用 Claude Code 编译维护的 Markdown 知识库时，触发此 skill。也适用于用户想要生成 CLAUDE.md、设置 raw/wiki/outputs 结构、或提到 Karpathy 知识库方法论的场景。即使用户只是模糊地说"帮我整理知识"或"我想建一个学习系统"，也应该考虑触发此 skill。
---

# 知识库脚手架生成器

基于 Karpathy LLM Wiki 理念，一键生成开箱即用的 Obsidian 知识库。用户只需回答几个问题，即可获得一个完整的 zip 包，解压后配合 Claude Code 直接使用。

## 核心理念

这套知识库遵循"自下而上"的编译哲学：
- raw/ 存放原始材料（只读）
- LLM 将 raw/ "编译"为结构化的 wiki/
- wiki 的结构、分类、重点方向完全由输入材料决定，不预设框架
- 通过 /compile、/lint、/health-check 命令维护知识库质量

## 交互流程

### Step 1：收集信息

向用户提出以下问题（可以根据对话上下文跳过已知信息）：

**必问：**
1. 你想创建什么领域的知识库？（如：AI & Agent、商业投资、医学、烹饪、法律……）
2. vault 文件夹想叫什么名字？（英文或中文均可，如 `AI-Agent-KB`、`商业知识库`）

**可选（如果用户不确定，由你根据领域推断）：**
3. 你在这个领域常收集什么类型的材料？（文章、论文、视频笔记、财报、代码……）
4. 这个领域的知识大致可以分哪几种类型？（如商业领域有 company/concept/framework/industry 等，不知道的话我来推断）

### Step 2：生成 CLAUDE.md

这是整个 Skill 的核心步骤。根据用户的回答，参照 `references/claude-md-template.md` 中的模板生成 CLAUDE.md。

**生成规则：**

1. 阅读 `references/claude-md-template.md` 获取完整模板结构
2. 模板中标注为 `{{动态}}` 的部分需要根据用户的领域定制生成
3. 模板中没有标注的部分原样保留（这些是所有知识库通用的规则）
4. type 分类体系：根据领域特点设计 4-7 个 type，每个 type 都要有简短说明
5. 文章结构模板：为每种 type 设计合理的章节结构
6. 示例文章：选择该领域中一个具有代表性的中等复杂度概念，写一篇完整的示例文章。示例文章必须包含：完整 frontmatter、摘要段、多个 `[[]]` 链接、所有章节。示例文章的主题选择标准：
   - 选一个该领域的基础概念（不要太专业冷门，也不要太泛泛）
   - 确保能自然地展示多种 type 之间的链接关系
   - 确保能展示中文命名和英文专有名词保留的规则
7. 时效性规则：根据领域特点说明哪些内容 evergreen、哪些 volatile
8. outputs/ 描述：列出该领域常见的输出类型
9. 个人笔记处理：根据领域特点说明如何提炼用户的随手笔记
10. raw/ 描述：列出该领域常见的原始材料类型
11. 文件命名规则中的示例：给出 3-4 个贴合该领域的 ✅ 和 ❌ 命名示例

### Step 3：组装固定文件

从 `templates/` 目录复制以下固定文件（这些文件不随主题变化）：

- `templates/compile.md` → `.claude/commands/compile.md`
- `templates/lint.md` → `.claude/commands/lint.md`
- `templates/health-check.md` → `.claude/commands/health-check.md`
- `templates/write.md` → `.claude/commands/write.md`
- `templates/research.md` → `.claude/commands/research.md`
- `templates/lock-raw.bat` → `lock-raw.bat`
- `templates/unlock-raw.bat` → `unlock-raw.bat`
- `templates/lock-raw.sh` → `lock-raw.sh`
- `templates/unlock-raw.sh` → `unlock-raw.sh`

### Step 4：创建文件夹结构

确保 zip 中包含以下空文件夹（通过在每个空文件夹中放一个 `.gitkeep` 文件实现）：

- `raw/.gitkeep`
- `research/.gitkeep`
- `wiki/.gitkeep`
- `wiki/_changelog.md`（初始内容为 `# 变更日志\n\n暂无记录。`）
- `outputs/.gitkeep`

### Step 5：打包为 zip

将所有文件打包为一个 zip 文件，命名为 `{vault-name}.zip`。

最终 zip 内的结构：
```
{vault-name}/
├── CLAUDE.md
├── raw/
│   └── .gitkeep
├── research/
│   └── .gitkeep
├── wiki/
│   ├── .gitkeep
│   └── _changelog.md
├── outputs/
│   └── .gitkeep
├── .claude/
│   └── commands/
│       ├── compile.md
│       ├── lint.md
│       ├── health-check.md
│       ├── write.md
│       └── research.md
├── lock-raw.bat
├── unlock-raw.bat
├── lock-raw.sh
└── unlock-raw.sh
```

### Step 6：交付

使用 `present_files` 工具将 zip 文件交给用户，并附上简短的使用说明：

```
你的知识库已生成！使用方法：

1. 解压 zip 到你想要的位置
2. 用 Obsidian 打开解压后的文件夹作为 vault
3. 往 raw/ 里丢你的原始材料（文章、笔记、截图等）
4. Windows 用户运行 lock-raw.bat 锁定 raw/，Mac 用户运行 chmod +x lock-raw.sh && ./lock-raw.sh
5. 在终端中 cd 到 vault 目录，运行 claude，然后输入 /compile 开始编译
6. 编译完成后用 /lint 检查格式，/write 产出文章，/research 补充研究，/health-check 做健康检查

五个命令速查：
- /compile   → 编译 raw/ 和 research/ 的新材料到 wiki
- /lint      → 检查格式并自动修复
- /write     → 基于 wiki 写文章，新洞察可回流 wiki
- /research  → AI 主动搜索补充知识空白
- /health-check → 全面体检，发现矛盾和空白
```

## 注意事项

- 所有文件使用 UTF-8 编码
- CLAUDE.md 中的示例文章必须足够完整和规范，因为它是 LLM 编译时的唯一参照标准
- 文件命名规则中文优先，专有名词保留英文
- 不要在 zip 中包含任何二进制文件或大文件
