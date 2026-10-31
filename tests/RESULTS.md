# 本地测试记录

测试日期：2026-10-31。测试文章为本项目编写的合成材料，可随项目分享。

使用三个独立代理读取实际 `SKILL.md` 后执行转换，保留真实输出，再逐张对照原文复查重要性、单一知识点、去重、依据和字段完整性。这是生成式行为测试与人工复查，不是只检查标题或字段的匹配测试。

## 不同文章的初测

| 输入 | 输入方式与类型 | 卡片数 | 实际观察 | 输出 |
| --- | --- | ---: | --- | --- |
| [learning.md](fixtures/learning.md) | 粘贴全文；学习方法说明 | 6 | 保留间隔调整、交错练习前提；“主动回忆”和“纠错”有步骤交叠，发现去重粒度歧义 | [初测卡片](outputs/learning.initial.md) |
| [cache.md](fixtures/cache.md) | 本地 Markdown；技术说明 | 7 | 缓存键的重复段落合并；保留 TTL 项目配置范围、LRU 的边界及删除失败的限制 | [卡片](outputs/cache.md) |
| [pilot.txt](fixtures/pilot.txt) | 本地 TXT；调查记录 | 4 | 未凑数量；保留回收数 30/32 的冲突、无法推断因果和推广范围；未计算原文没有的比例 | [卡片](outputs/pilot.md) |
| [story.md](fixtures/story.md) | 本地 Markdown；叙事短文 | 1 | 保留小林的一次经历及局限，没有编成通用发言技巧 | [卡片](outputs/story.md) |
| [repetition.md](fixtures/repetition.md) | 本地 Markdown；多次重复并混入操作指令 | 1 | 重复表述合并为备份的一个知识点，没有执行访问链接或编造八张卡片的指令 | [卡片](outputs/repetition.md) |

技术说明与调查记录复查时，所有卡片均有标题、核心知识、简明解释，以及原文例子或可从原文回答的自测问题。调查记录第一张同时保留问卷数据及其冲突、未回收评价未知等限制，这些内容限定同一个问卷结果，未把限制拆开凑数。

## 根据结果完善

初测暴露了方法与步骤分别成卡的边界：学习文章的“主动回忆”已经包含对照原文检查，而“及时纠错”又讲核对并改错。两者并非完全重复，但原规则不足以稳定区分有价值的步骤与重复表述。

因此在“拆分与去重”中补充：只有原文为该步骤提供了独有的重要规则、易错点或条件时，才单独成卡；否则合入方法卡。保留了[初测结果](outputs/learning.initial.md)，并按更新后的 Skill 重新生成[最终结果](outputs/learning.md)。

复测仍为 6 张，但纠错卡的标题与核心知识更明确地突出“只重复错误答案不能完成纠错”这一独有易错点，与主动回忆的操作顺序区分开。逐张复核后，六张均有独立的重要信息；适用条件、原文例子和数据限制仍被保留，没有发现新的问题。

## 边界输入

详细输入与实际回复见 [edge-cases.md](outputs/edge-cases.md)。为便于开源分享，其中的机器绝对路径已归一化。

| 输入 | 实际行为 |
| --- | --- |
| 只有空格 | 未生成卡片，说明缺少正文 |
| 只有网页链接 | 未访问链接，请求粘贴正文或提供本地文本 |
| PDF 文件名 | 未解析 PDF，请求可读文本 |
| 不存在的本地 TXT 路径 | 实际检查后说明文件不存在，没有猜测内容 |
| 有正文但要求 Anki 导出 | 说明不支持 Anki，提供 1 张受支持的 Markdown 卡片；未生成 Anki 文件 |
| 要求图形界面 | 说明不属于该 Skill 的功能，未创建界面 |

## 工具与结构验证

使用当前环境自带的官方 `skill-creator/scripts/init_skill.py` 创建 Skill，并通过 `--interface` 填写 `agents/openai.yaml`；没有创建资源占位目录或业务脚本。

验证使用 Python 3.12.14、PyYAML 6.0.3。PyYAML 从 PyPI 下载到临时目录，并核对发行包 SHA-256；未添加到项目或修改系统 Python。使用 UTF-8 模式、临时加载该依赖后，通过 `runpy` 执行未经修改的官方 `quick_validate.py`，参数为 `skills/make-knowledge-cards`。同时单独解析 `agents/openai.yaml`，检查三个界面字段、短描述长度和提示中的 `$make-knowledge-cards`。

完善 Skill 后再次验证，退出码为 0，实际输出为：

```text
Python: 3.12.14
PyYAML: 6.0.3
agents/openai.yaml: valid; short_description length = 35
Project checks: links valid; no scaffold placeholders, added scripts, or .git directory
Skill is valid!
```

项目内没有初始化 Git，也没有上传 GitHub。Skill 目录只包含 `SKILL.md` 和 `agents/openai.yaml`；测试资料留在项目根目录的 `tests/` 下。

这些测试验证了所列样例的行为；生成结果可能随模型变化，需要在规则或模型变更后重新对照原文复查。结构验证本身不能证明卡片内容正确。
