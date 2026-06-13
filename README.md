# 论文转中国发明专利

这是一个面向中国高校研究生、科研人员和技术团队的通用 Agent
指令与工具包，用于将论文、学位论文、技术报告、源代码和科研手稿
整理为可追溯、可复核的中国发明专利申请初稿。

本项目不绑定特定 Agent，可配合 Claude、Codex、Cursor 以及其他能够读取项目指令、访问本地文件并运行脚本的 AI Agent 使用。

> 本项目用于辅助技术梳理和专利初稿撰写，不构成法律意见，也不能替代专利代理师或律师的专业审核。

## 适用对象

- 希望将论文进一步整理为专利初稿的硕士、博士研究生；
- 需要准备专利交底材料或成果转化材料的高校科研人员；
- 从事人工智能、计算机视觉、信号处理、自动控制、机械和电子信息等研究的团队；
- 需要对照论文和已有专利，检查技术特征支持关系的使用者。

尤其适合包含算法流程、数学公式、模型结构、数据处理步骤和实验验证的论文。

## 核心能力

- 提取论文中的技术问题、技术步骤、公式、参数和技术效果；
- 建立“论文来源位置 - 专利技术特征 - 技术作用”的证据台账；
- 区分论文贡献、实验配置、未来工作和可专利技术特征；
- 在论文支持的前提下生成方法、设备和计算机可读存储介质权利要求；
- 分别生成权利要求书、说明书、说明书摘要和摘要附图；
- 将公式生成为 Word 原生 Office Math 对象，可直接复制和编辑；
- 生成与独立权利要求步骤一致的黑白方法流程图；
- 生成主动采样、特征提取和模型融合等方法论附图；
- 将主流程图同时作为摘要附图和说明书附图；
- 检查权利要求编号、引用关系和常见结构问题；
- 对缺少依据的内容使用 `[TO CONFIRM]` 标记，避免自动虚构。
- 为论文正文、公式、附图和补充材料建立稳定来源 ID；
- 将每项权利要求的实质技术特征映射到证据台账；
- 按阶段保存来源地图、技术清单、证据台账和权利要求策略；
- 在生成 DOCX 前自动检查草稿完整性、支持关系和质量门槛。

## 工作模式

Skill 会根据任务自动选择模式：

| 模式 | 适用任务 | 主要输出 |
| --- | --- | --- |
| `full-draft` | 生成完整申请初稿 | 权利要求书、说明书、摘要、附图和审阅稿 |
| `claim-set` | 只起草或修改权利要求 | 权利要求、特征映射和检查报告 |
| `disclosure-analysis` | 先判断论文如何专利化 | 发明构思、证据台账、保护策略和待确认问题 |
| `paper-patent-audit` | 对照论文与已有专利 | 双向特征映射和支持关系报告 |

同时会识别 PDF、扫描 PDF、粘贴文本或包含代码、附图和已有专利的
混合项目，并针对算法软件、装置系统、工艺材料或混合型发明加载不同规则。

## 完整生成流程

```mermaid
flowchart TD
    A["输入盘点与公开风险标记"] --> B["建立全文来源地图"]
    B --> C["建立术语、公式、附图和输入输出清单"]
    C --> D["建立技术特征证据台账"]
    D --> E["形成发明构思与权利要求策略"]
    E --> F["起草并检查权利要求"]
    F --> G["撰写说明书、公式和附图"]
    G --> H["最后撰写摘要"]
    H --> I["结构、溯源和质量门槛校验"]
    I --> J["生成分立 DOCX、SVG、PNG 和审阅报告"]
```

这个顺序可以避免直接根据论文摘要“扩写专利”，也可以避免先写说明书后
发现权利要求、公式和附图无法对齐。

## 输出内容

Agent 的讨论和分析可以使用用户指定的语言，但正式专利交付文件统一使用中文，包括：

- `权利要求书.docx`
- `说明书.docx`
- `说明书摘要.docx`
- `摘要附图.docx`
- `完整审阅稿.docx`
- 流程图和方法论附图的 `SVG`、`PNG`
- 可重复编辑和重新生成的结构化 `JSON`
- 权利要求结构检查报告
- 草稿溯源和质量门槛验证报告

说明书中的核心公式采用 Word 原生可编辑公式，而不是将 LaTeX 字符串直接作为普通文本写入。

## 仓库结构

```text
Paper-to-patent-Skill/
├── README.md
├── SKILL.md
├── manifest.yaml
├── requirements.txt
├── static/
│   ├── core/
│   │   ├── principles.md
│   │   ├── workflow.md
│   │   └── output-contract.md
│   └── fragments/
│       ├── source/
│       ├── task/
│       └── invention/
├── evals/
│   └── evals.json
├── references/
│   ├── cn-patent-drafting-guide.md
│   ├── corpus-derived-patterns.md
│   ├── corpus-pair-audit.md
│   ├── draft-schema.md
│   └── patent-figure-guide.md
├── scripts/
│   ├── audit_claims.py
│   ├── build_patent_package.py
│   ├── extract_pdf_text.py
│   ├── init_patent_project.py
│   ├── math_to_omml.py
│   ├── render_flowchart_svg.py
│   ├── render_patent_docx.py
│   └── validate_patent_draft.py
└── tests/
    └── test_validation.py
```

- `SKILL.md`：供 Agent 阅读的精简路由指令；
- `manifest.yaml`：根据输入、任务和发明类型选择需要加载的规则；
- `static/`：所有任务必需的核心流程和按需加载的场景规则；
- `references/`：专利撰写规则、结构化草稿格式和附图规范；
- `scripts/`：项目初始化、PDF 提取、草稿校验、公式转换、附图生成、
  DOCX 打包和权利要求检查工具；
- `evals/` 与 `tests/`：行为契约和自动校验测试。

## 推荐整体工作目录

实际使用时，建议将本项目的指令文件、论文材料和输出目录放在同一个工作目录中。Agent 打开该目录后，可以同时读取 `SKILL.md` 和论文。

### 单篇论文

```text
paper-to-patent-project/
├── SKILL.md
├── references/
├── scripts/
├── paper/
│   └── paper.pdf
├── supplementary/
│   ├── source-code/
│   ├── experiment-settings.md
│   └── inventor-notes.md
├── source-figures/
│   ├── method.png
│   └── architecture.png
├── existing-patent/
│   └── patent.pdf
└── outputs/
```

其中：

- `paper/` 存放需要转换的论文；
- `supplementary/` 存放代码、实验参数和发明人补充说明；
- `source-figures/` 存放论文方法图、模型结构图等原始图片；
- `existing-patent/` 可选，用于论文与已有专利的对照分析；
- `outputs/` 存放生成的专利文件、附图和结构化草稿。

### 多篇论文批量处理

如果需要处理多篇论文，建议为每篇论文建立独立编号：

```text
paper-to-patent-project/
├── SKILL.md
├── references/
├── scripts/
├── cases/
│   ├── 001/
│   │   ├── paper.pdf
│   │   ├── supplementary/
│   │   ├── source-figures/
│   │   ├── existing-patent/
│   │   └── outputs/
│   ├── 002/
│   │   ├── paper.pdf
│   │   ├── supplementary/
│   │   ├── source-figures/
│   │   ├── existing-patent/
│   │   └── outputs/
│   └── 003/
│       └── ...
└── shared-notes/
```

不要仅依靠相同编号认定论文和专利一定互相对应，仍需由 Agent 阅读内容并检查技术特征关系。

## 环境要求

- Python 3.10 或更高版本；
- 能够读取项目文件和执行本地脚本的 AI Agent；
- Microsoft Word 或其他能够正确打开 DOCX 的办公软件。

安装 Python 依赖：

```bash
python -m pip install -r requirements.txt
```

Windows 也可以使用：

```powershell
py -3 -m pip install -r requirements.txt
```

## 通用使用方法

克隆或下载仓库：

```bash
git clone https://github.com/snipp-zha/Paper-to-patent-Skill.git
```

将论文放入 `paper/`，然后让 Agent 先读取 `SKILL.md`：

```text
请先阅读并遵循当前项目中的 SKILL.md。
分析 paper/paper.pdf，并生成一套中国发明专利申请初稿。
分别生成中文权利要求书、中文说明书、中文说明书摘要和摘要附图。
保留论文中的核心公式，并将其生成为 Word 原生可编辑公式。
生成主方法流程图和核心方法论附图。
```

也可以先创建标准工作目录：

```bash
python scripts/init_patent_project.py my-patent-project --paper path/to/paper.pdf
```

该命令会创建：

```text
my-patent-project/
├── SKILL.md
├── manifest.yaml
├── requirements.txt
├── static/
├── references/
├── scripts/
├── paper/
├── supplementary/
│   ├── source-code/
│   └── inventor-notes.md
├── source-figures/
├── existing-patent/
├── work/
│   └── 00-intake.json
└── outputs/
```

初始化命令默认把 Skill 的核心文件复制到新项目，因此该项目目录可以单独
交给任意支持读取本地规则和运行 Python 的 Agent。只想创建材料目录时，
使用 `--no-embed-skill`。

如果 Agent 支持项目规则、系统指令或上下文文件，可将 `SKILL.md` 作为该项目的长期规则来源。

## 不同 Agent 的接入方式

### Claude

让 Claude 直接读取 `SKILL.md`，或者在项目的 `CLAUDE.md` 中写入：

```markdown
处理论文转专利任务时，请先阅读并遵循 `SKILL.md`。
正式权利要求书、说明书、摘要和附图文字必须使用中文。
```

### Cursor

在 `.cursor/rules/` 中创建项目规则：

```markdown
处理论文转专利任务时，请读取并遵循项目根目录的 `SKILL.md`。
Agent 讨论可以使用用户指定语言，正式中国专利文件必须使用中文。
```

### Codex

使用 Codex 打开本项目工作目录，然后要求其读取 `SKILL.md`。本项目不依赖 Codex 专属元数据。

### 其他 Agent

只要 Agent 支持项目指令、规则文件或上下文文件，即可：

1. 加载 `SKILL.md`；
2. 在需要详细规则时读取 `references/`；
3. 允许 Agent 执行 `scripts/` 中的 Python 工具；
4. 提供论文、补充材料和发明人说明；
5. 要求正式专利交付文件使用中文。

## 推荐输入材料

最少只需提供一篇论文或技术报告，推荐同时提供：

| 材料 | 是否必需 | 用途 |
| --- | --- | --- |
| 论文或技术报告 PDF | 必需 | 提取发明内容和技术依据 |
| 补充材料 | 可选 | 补足实现步骤、参数和替代方案 |
| 源代码 | 可选 | 支持模型、算法和参数描述 |
| 原始方法图或模型结构图 | 可选 | 制作说明书方法论附图 |
| 实验参数和数据集说明 | 可选 | 完善具体实施方式 |
| 发明人说明 | 可选 | 确认实际贡献和工程事实 |
| 已有专利文件 | 可选 | 进行论文与专利的特征映射 |

论文已经公开时，应提供准确的公开日期，并尽快咨询专利专业人员评估新颖性风险。

## 推荐提示词

```text
请先阅读并遵循 SKILL.md。

分析 paper/paper.pdf，生成中国发明专利申请初稿。

要求：
1. 提取论文的技术问题、技术手段和技术效果；
2. 建立专利技术特征与论文页码之间的证据台账；
3. 分别生成中文权利要求书、中文说明书和中文说明书摘要；
4. 将论文支持的核心公式写入说明书；
5. 将公式生成为 Word 原生可编辑公式；
6. 生成与独立权利要求一致的主方法流程图；
7. 为核心中间方法生成方法论附图；
8. 将主流程图同时作为摘要附图；
9. 对缺少依据的内容使用 [TO CONFIRM] 标记，不要虚构。
```

## 论文与已有专利对照

同时提供论文和专利文件时，可以使用：

```text
请阅读 SKILL.md，对照 paper/paper.pdf 和 existing-patent/patent.pdf。
检查每项权利要求在论文中的支持位置，并标记论文独有、专利独有、
专利表述更宽或更窄的技术特征。
```

## 生成结果复核

正式提交前至少检查：

- 独立权利要求是否只保留不可缺少的核心技术特征；
- 每项权利要求特征是否能在论文或补充材料中找到依据；
- 是否误把数据集名称、实验指标或网络层数写成必要限制；
- 最终输出是否明确，例如“缺陷类别识别结果”；
- 公式中的符号、上下标和定义是否一致；
- 附图步骤是否与权利要求中的 `S1`、`S2` 等编号一致；
- 发明人是否按照实际技术贡献确定；
- 申请人和论文公开日期是否经过人工确认；
- 是否已经由专利代理师或其他专业人员复核。

## 命令行工具

提取 PDF 文本：

```bash
python scripts/extract_pdf_text.py paper/paper.pdf --output paper.txt
```

检查 UTF-8 权利要求文本：

```bash
python scripts/audit_claims.py claims.txt
```

从结构化 JSON 生成完整申请包：

```bash
python scripts/validate_patent_draft.py draft.json
python scripts/build_patent_package.py draft.json \
  --output-dir outputs \
  --prefix patent
```

结构化草稿格式参见 `references/draft-schema.md`。

运行自动测试：

```bash
python -m unittest discover -s tests -v
```

## 参考模式

本项目参考了
[`nature-skills`](https://github.com/Yuan1z0825/nature-skills)
的路由文件、按需加载片段、输出契约、测试样例和质量清单组织方式，并将其
应用于论文转中国发明专利流程。

## 重要说明

- 当前文本结构主要面向中国发明专利；
- 正式权利要求书、说明书、摘要和附图标签使用中文；
- 不应根据论文作者顺序自动确定发明人或申请人；
- 不应把未来工作、猜测或实验愿望写入正式权利要求；
- 论文公开可能影响专利新颖性，应重视投稿、答辩、会议展示和网络公开日期；
- 本项目不提供专利授权保证、新颖性结论、侵权分析或法律代理服务。
