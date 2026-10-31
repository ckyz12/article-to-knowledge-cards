# make-knowledge-cards

把粘贴的文章或本地 Markdown/TXT 文件整理成便于理解和复习的知识卡片。通常输出 **5–8 张**，每张只讲一个重要知识点，包含标题、核心知识、简明解释，以及原文例子或自测问题。

优先保证忠于原文、去重和知识价值。材料不足时可以少于 5 张；没有可提取知识时说明原因。原文没有例子时改用能从原文回答的自测题，不补造事实。

## 项目解决什么问题

文章往往包含重复表述、背景叙述和分散的重点，直接用于复习不够方便。本项目提供一个供 AI 助手使用的 Skill，把这些材料整理成每张只讲一个知识点的卡片，同时保留原文的条件、限制和观点归属。

## 主要功能

- 处理粘贴的正文，以及本地 `.md`、`.markdown`、`.txt` 文件。
- 提取重要知识、合并重复内容，通常生成 5–8 张卡片，最多 8 张；信息不足时减少数量，没有可提取知识时说明原因。
- 每张包含标题、核心知识、简明解释，以及原文例子或可从原文回答的自测问题。
- 保留原文的不确定性和信息冲突，不补充外部事实或虚构例子。
- 默认以用户的语言在对话中输出 Markdown；明确指定保存路径时，可由助手写入文件。

当前不支持网页抓取、PDF 处理、Anki 导出或图形界面。

## 安装方法

前提：有一个能读取 Skill 指令和本地文件的 AI 助手，例如 Codex。本项目是指令型 Skill，没有独立可执行程序，使用时不需要安装 Python、Node.js 或项目依赖；AI 助手自身的安装、登录和使用权限需另行准备。

取得项目文件后，有两种本地使用方式：

1. **在当前项目直接使用**：用助手打开项目目录，在对话中明确要求读取 `skills/make-knowledge-cards/SKILL.md`。这种方式无需复制安装，示例见下文。
2. **让 Codex 自动发现**：把整个 `skills/make-knowledge-cards` 文件夹复制到目标项目的 `.agents/skills/` 下，形成以下结构：

   ```text
   .agents/skills/make-knowledge-cards/
   ├── SKILL.md
   └── agents/
       └── openai.yaml
   ```

   如需跨项目使用，可复制到用户目录的 `~/.agents/skills/` 下。安装后确认 Skill 出现在宿主的技能列表中；未出现时重启 Codex。安装位置依据 [OpenAI 官方 Skill 文档](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills)。复制前检查目标位置，避免覆盖已有同名 Skill。

项目中的 `skills/` 是源文件存放位置，本身不保证自动发现。其他宿主请按其 Skill 加载方式配置。

## 使用方法

Skill 位于 [`skills/make-knowledge-cards`](skills/make-knowledge-cards)，由支持 Skill 的 AI 助手执行，不是独立命令行程序。运行时不需要额外脚本或第三方依赖。

在当前项目中，可以明确要求助手读取 Skill 后处理材料：

```text
请读取 skills/make-knowledge-cards/SKILL.md，按其中规则把下面的文章转换为知识卡片：
（粘贴文章）
```

若宿主已发现或加载这个 Skill，可直接调用：

```text
$make-knowledge-cards 请处理本地文件 tests/fixtures/cache.md。
```

默认在对话中输出 Markdown。需要保存时指定输出文件路径。文件夹放在项目的 `skills/` 下，不代表所有宿主都会自动发现它。

例如，在当前项目中处理文件并保存结果：

```text
请读取 skills/make-knowledge-cards/SKILL.md，按其中规则处理 tests/fixtures/cache.md，
将知识卡片保存到 cache-cards.md，保留原始文章。
```

相对路径按当前项目目录解析，也可以提供助手有权读取的绝对路径。请提供完整正文或有效文件路径；只有网址或文件名时，助手不会猜测文章内容。

## 输入与输出示例

输入：

```text
备份是保留原文件，同时在另一个位置保存副本。
换句话说，备份需要原文件和另一个位置的副本同时存在。
```

输出：

```markdown
材料去重后只有 1 个独立知识点，因此输出 1 张卡片。

### 卡片 1：备份要保留原文件

- **核心知识**：备份是在保留原文件的同时，在另一个位置保存副本。
- **简明解释**：原文件与另一个位置的副本需要同时存在。
- **自测问题**：备份时，原文件需要保留吗？（参考答案：需要。）
```

## 开发与验证

使用官方 `skill-creator/scripts/init_skill.py` 初始化；Skill 本身只有 `SKILL.md` 和 `agents/openai.yaml`。不同文体的输入、实际生成结果和复查记录保存在 [`tests/`](tests/)。

在提供官方 `skill-creator` 的环境中运行结构验证（需要 Python 和 PyYAML）：

```text
python -X utf8 <skill-creator目录>/scripts/quick_validate.py skills/make-knowledge-cards
```

结构验证不衡量内容质量，行为测试需要对照原文复查。详见 [`tests/RESULTS.md`](tests/RESULTS.md)。

采用 [MIT 许可证](LICENSE)。
