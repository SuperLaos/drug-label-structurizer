# drug-label-structurizer（结构化药品说明书）

> 将非结构化的原始药品说明书转化为「标准 12 模块骨架 + 机器可读 JSON Schema + 多形态输出」的结构化文档，并对四类来源进行规范标注，最终强制进入人工（药师）复核环节。
>
> Transforms unstructured drug package inserts into standardized documents with a 12-module skeleton, machine-readable JSON Schema, and multi-format outputs, with four source-annotation rules and a mandatory human (pharmacist) review step.

---

## 目录 | Table of Contents

- [简介 | Introduction](#简介--introduction)
- [功能特性 | Features](#功能特性--features)
- [目录结构 | Directory Structure](#目录结构--directory-structure)
- [安装 | Installation](#安装--installation)
- [快速开始 | Quick Start](#快速开始--quick-start)
- [工作流 | Workflow](#工作流--workflow)
- [输出形态 | Output Formats](#输出形态--output-formats)
- [标注规范 | Annotation Rules](#标注规范--annotation-rules)
- [合规与免责 | Compliance & Disclaimer](#合规与免责--compliance--disclaimer)
- [许可证 | License](#许可证--license)

---

## 简介 | Introduction

**中文**：drug-label-structurizer 是一款用于把药品说明书（docx / pdf / 图片）解析并结构化整理为标准化文档的技能。它对齐 NMPA《化学药品及生物制品说明书通用格式和撰写指南》并兼容 FDA SPL 的「叙述 + 编码」双层结构，提供标准 12 模块骨架、机器可读 JSON Schema、四类来源标注（未提及 / 不适用 / 联网补充 / 监管差异），并支持多形态输出：Markdown、HTML（带目录）、JSON、患者版（适老化）、单药速查卡、多药横向对比表，内置 QA 复核清单与强制人工（药师）复核机制。法规依据包括《药品说明书和标签管理规定》（局令第24号）、《中药注册管理专门规定》（2023年第20号）及《药品说明书适老化及无障碍改革试点工作方案》（2023年第142号）。

**English**: drug-label-structurizer is a skill that parses and structurally organizes drug package inserts (docx / pdf / images) into standardized documents. It aligns with NMPA's *General Format and Writing Guidelines for Chemical and Biological Product Labels* and is compatible with the FDA SPL narrative-plus-encoded dual-layer structure. It provides a standard 12-module skeleton, machine-readable JSON Schema, four types of source annotations (Not Mentioned / Not Applicable / Web Supplement / Regulatory Difference), and supports multiple output formats: Markdown, HTML (with TOC), JSON, patient version (elderly-friendly), single-drug quick-reference card, and multi-drug comparison table, with a built-in QA checklist and mandatory human (pharmacist) review mechanism. Regulatory references include the *Provisions on the Administration of Drug Package Inserts and Labels* (Order No. 24), the *Special Provisions on Traditional Chinese Medicine Registration* (2023 No. 20), and the *Pilot Work Plan for Age-friendly and Accessible Drug Package Insert Reform* (2023 No. 142).

**适用场景 | Use cases**: 结构化说明书 / 药品说明书整理 / 说明书转 JSON / 患者版说明书 / 多药对比 / 药品知识库字段提取 / drug label 或 SPL 结构化。

---

## 功能特性 | Features

- **12 模块标准骨架**：对齐 NMPA 规范章节，多药可横向对比、可聚合检索。
  Standard 12-module skeleton aligned with NMPA chapters, enabling cross-drug comparison and aggregated retrieval.
- **机器可读 JSON Schema**：基于 NMPA 章节 + FDA SPL 产品主数据映射，缺项字段强制填 `未提及` / `不适用`，可入库 / RAG / 知识图谱。
  Machine-readable JSON Schema (NMPA chapters + FDA SPL product master mapping); missing fields must be filled with `Not Mentioned` / `Not Applicable`, ready for database / RAG / knowledge graph.
- **四标签标注规范**：`未提及` / `不适用` / `【联网补充】` / `【监管差异】`，保证来源可追溯、合规安全。
  Four annotation rules for source traceability and compliance.
- **六种输出形态**：Markdown、HTML（带目录）、JSON、患者版（适老化）、单药速查卡、多药对比表。
  Six output formats covering both clinical and patient perspectives.
- **QA 复核清单 + 强制人工复核**：降低合规与用药安全风险。
  Built-in QA checklist plus mandatory human review to reduce compliance and medication-safety risks.
- **合规与免责声明模板**：内置，提示须经药师/医师审核。
  Bundled compliance & disclaimer template reminding that outputs require pharmacist/physician review.
- **法规依据完整**：对齐 NMPA 规范，覆盖《药品说明书和标签管理规定》（局令第24号）、《中药注册管理专门规定》（2023年第20号）及适老化改革试点要求。
  Complete regulatory references aligned with NMPA standards, including Order No. 24, TCM Special Provisions (2023 No. 20), and age-friendly reform requirements.
- **防幻觉校验**：QA 清单包含高风险字段（剂量/禁忌/严重不良反应）逐字比对原文的检查项。
  Hallucination prevention: QA checklist includes word-for-word verification of high-risk fields (dosage/contraindications/serious ADRs) against source text.

---

## 目录结构 | Directory Structure

```
drug-label-structurizer/
├── SKILL.md                          # 主文件：定位、原则、12 模块骨架、字段 Schema、标注规范、输出生成器、QA 清单、免责模板、工作流
├── README.md                         # 中英双语说明（本文件）
├── references/                       # 规范与数据字典（机器可读层）
│   └── json_schema.md                # 完整 JSON Schema（字段字典，对齐 NMPA 章节 + FDA SPL 产品主数据）
└── assets/                           # 排版与模板资源
    └── label_html_template.html      # HTML 排版模板：单栏 720px 移动端适配、黑框警告/数据来源提示块、自动目录(TOC)、返回目录/返回顶部、表格斑马纹、章节色块
```

| 文件 File | 作用 Purpose |
|---|---|
| `SKILL.md` | 技能全部逻辑与规范的总入口 / Master entry for all logic and rules |
| `README.md` | 中英双语发布说明 / Bilingual release docs |
| `references/json_schema.md` | JSON 输出的机器可读字段标准 / Machine-readable field standard for JSON output |
| `assets/label_html_template.html` | HTML 排版母版 / HTML typesetting master template |

---

## 安装 | Installation

**中文**：本技能兼容 WorkBuddy 用户级技能目录。将本仓库克隆或复制至用户技能目录即可：

```bash
# 方式一：克隆仓库到技能目录
git clone <this-repo-url> "$HOME/.workbuddy/skills/drug-label-structurizer"

# 方式二：解压发布包
unzip drug-label-structurizer.zip -d "$HOME/.workbuddy/skills/"
```

**English**: This skill is compatible with the WorkBuddy user-level skills directory. Clone or copy this repo into the user skills directory:

```bash
# Option A: clone the repo into the skills directory
git clone <this-repo-url> "$HOME/.workbuddy/skills/drug-label-structurizer"

# Option B: unzip the release package
unzip drug-label-structurizer.zip -d "$HOME/.workbuddy/skills/"
```

> 路径说明 | Path note: Windows 用户对应目录为 `C:\Users\<用户名>\.workbuddy\skills\`。On Windows, the path is `C:\Users\<username>\.workbuddy\skills\`.

---

## 快速开始 | Quick Start

**中文**：在 WorkBuddy 中调用本技能，并附上说明书源文件（docx / pdf / 图片）或粘贴文本，说明需要的输出形态即可。例如：

- "帮我把这份环孢素软胶囊说明书结构化，输出 Markdown 和 JSON"
- "把这份说明书整理成患者版（适老化）"
- "把 A、B 两种药做成横向对比表"

**English**: Invoke this skill in WorkBuddy and attach the package insert source (docx / pdf / image) or paste the text, then specify the desired output format. Examples:

- "Structurize this Cyclosporine Soft Capsule insert and output Markdown and JSON."
- "Produce a patient (elderly-friendly) version of this label."
- "Build a side-by-side comparison table for Drug A and Drug B."

---

## 工作流 | Workflow

1. **确认输入**：接收源文件（docx/pdf/图片/文本），确认格式与清晰度。
   Confirm input: receive source (docx/pdf/image/text), check format & clarity.
2. **解析提取**：用文档解析 / OCR 还原文本与表格，保留章节层级。
   Parse & extract: use document parsing / OCR to recover text and tables, preserve section hierarchy.
3. **按骨架归类**：将内容映射到 12 模块 + JSON Schema 字段。
   Map to skeleton: align content to the 12 modules + JSON Schema fields.
4. **标注处理**：对未提/不适用/联网补充/监管差异 规范打标。
   Annotate: apply the four standard tags as appropriate.
5. **生成输出**：按需求选 MD / HTML / JSON / 患者版 / 速查卡 / 对比表。
   Generate output: pick MD / HTML / JSON / patient / quick-card / comparison.
6. **QA 复核**：走 QA 清单逐项核对。
   QA review: go through the QA checklist item by item.
7. **附声明**：加数据来源 + 免责 + 复核提示。
   Attach statements: data source + disclaimer + review note.
8. **交付并提示人工复核**。
   Deliver and prompt for human review.

> **方法论补充 | Methodology Note**：基于 FDA 相关研究（Gray et al., 2023），段落级分类准确率达 94-96%，建议对核心章节（适应症/警示/不良反应）进一步拆分为句子级分类，以提升监管审查效率。
> *Methodology note: Based on FDA research (Gray et al., 2023), paragraph-level classification achieves 94-96% accuracy. For core sections (indications/warnings/adverse reactions), consider sentence-level classification for improved regulatory review efficiency.*

---

## 输出形态 | Output Formats

| 形态 Format | 说明 Description |
|---|---|
| Markdown | 默认人读版，12 模块 + 表格 + 提示块 / Default human-readable version |
| HTML | 可发布排版：TOC 锚点、返回顶部、移动端适配 / Publish-ready with TOC & mobile styling |
| JSON | 机器可读，可入库 / RAG / 知识图谱 / Machine-readable for DB / RAG / KG |
| 患者版 | 适老化、通俗、原篇幅 10%–20% / Elderly-friendly, plain language |
| 单药速查卡 | 仿 SPL Highlights，一屏提炼关键要素 / Highlights-style one-screen summary |
| 多药对比表 | 按核心参数并排对比 2+ 药品 / Side-by-side comparison of 2+ drugs |

---

## 标注规范 | Annotation Rules

| 标签 Tag | 含义 Meaning | 用法 Usage |
|---|---|---|
| `未提及` / `Not Mentioned` | 说明书未载该信息 | 字段缺项时填，不臆造 / Fill on missing field; never fabricate |
| `不适用` / `Not Applicable` | 该药品/场景不适用 | 如"儿童用药：不适用" / e.g. "Pediatric use: N/A" |
| `【联网补充】` / `Web Supplement` | 经检索补充 | 须标注并对应文末参考来源编号 / Must cite a reference at the end |
| `【监管差异】` / `Regulatory Difference` | 不同国家/地区口径不同 | 须注明来源地区 / Must note the source region |

**规则 | Rule**: `【联网补充】` 与 `【监管差异】` 必须在文末「参考来源」列出可核查链接/文献；纯原文整理须明确声明「未联网补充」。`Web Supplement` and `Regulatory Difference` must list verifiable references at the end; pure original-text整理 must explicitly state "no web supplementation."

---

## 合规与免责 | Compliance & Disclaimer

> 整理依据：《XXX 说明书》（商品名 / 持证商 / 批准文号）。本文件内容严格取自该说明书原文，[未引入任何外部来源 / 已标注联网补充并对应来源]。本文件仅供信息整理与学习参考，**不构成任何诊疗建议**，具体临床用药请遵说明书、最新指南及医师/药师专业判断。AI 整理结果须经具备资质的医药专业人员复核后方可使用。

> Basis: *XXX Package Insert* (brand / MAH / approval number). Content is strictly drawn from the original insert, [no external sources / web supplements cited]. For information organization and study only — **not medical advice**. Clinical use must follow the insert, latest guidelines, and professional judgment. AI output must be reviewed by a qualified pharmaceutical professional before use.

---

## 相似项目参考 | Related Projects

以下为同类开源项目，供参考对标：

| 项目 | 类型 | 相似点 | 差异点 | 链接 |
|---|---|---|---|---|
| **FDA Molecule Intelligence Agent** | 开源工具 | FDA 药品标签结构化、RAG、防幻觉 | 面向 FDA SPL，非 NMPA | `github.com/mahmadza/fda-agent` |
| **Drug-RAG-Chatbot** | 开源工具 | 药品说明书结构化、答案溯源 | 面向通用药品，非 NMPA 12模块 | `github.com/1hgffg801/Drug-RAG-Chatbot` |
| **MedOrder Lite** | 开源工具 | AI 辅助药品说明书解析 | 面向临床试验，支持 OpenAI/Claude/DeepSeek | `github.com/liqi3333/med-order-lite-v1` |
| **ChatWiki doc-to-skill** | 开源工具 | 文档一键转 Skill | 通用文档，非药品专用 | `github.com/zhimaAi` |

**差异化定位**：本技能可能是国内首个对齐 NMPA 规范 + FDA SPL 双层的 WorkBuddy Skill，具有独特定位。
**Differentiation**: This skill may be among the first WorkBuddy Skills in China to align with both NMPA standards and FDA SPL dual-layer structure, offering unique positioning.

---

## 许可证 | License

本技能按 MIT 许可证发布，可自由使用、修改与再分发。
Released under the MIT License — free to use, modify, and redistribute.

> 免责重申 | Reiterate: 本技能仅用于信息整理与学习，不替代临床/监管专业判断。This skill is for information organization and study only; it does not replace clinical or regulatory judgment.
