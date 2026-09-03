---
name: drug-label-structurizer
display_name: 药品说明书结构化
display_name_en: Drug Label Structurizer
description_zh: 将药品说明书（docx/pdf/图片）解析并结构化整理为标准化12模块骨架+机器可读JSON Schema，支持多形态输出（MD/HTML/JSON/患者版/速查卡/对比表），内置QA复核与强制人工（药师）复核。
description_en: Parses and structurally organizes drug package inserts (docx/pdf/images) into a standardized 12-module skeleton + machine-readable JSON Schema, with multi-format outputs (MD/HTML/JSON/patient version/quick-card/comparison table), built-in QA checklist and mandatory pharmacist review.
description: "将药品说明书（docx / pdf / 图片）解析并结构化整理为标准化文档。对齐 NMPA 规范并兼容 FDA SPL 双层结构；提供 12 模块骨架、机器可读 JSON Schema、四类来源标注与多形态输出（MD/HTML/JSON/患者版/速查卡/对比表），内置 QA 复核与强制人工（药师）复核。 / Parses and structurally organizes drug package inserts (docx / pdf / images) into standardized documents, aligned with NMPA guidelines and FDA SPL dual-layer structure; provides a 12-module skeleton, machine-readable JSON Schema, four source-annotation rules, and multi-format outputs (MD / HTML / JSON / patient / quick-card / comparison), with built-in QA and mandatory pharmacist review. Triggers: structured drug label, drug label organization, label-to-JSON, patient version, multi-drug comparison, drug knowledge-base extraction, SPL structuring, 药品说明书结构化, 药品说明书整理, 说明书转JSON, 患者版说明书, 多药对比, 药品知识库字段提取, SPL结构化."
version: 0.1.0
agent_created: true
type: skill
---

# drug-label-structurizer（结构化药品说明书）

> 将非结构化的原始药品说明书转化为「标准 12 模块骨架 + 机器可读 JSON Schema + 多形态输出」的结构化文档，并对四类来源进行规范标注，最终强制进入人工（药师）复核环节。
>
> Transforms unstructured drug package inserts into standardized documents with a 12-module skeleton, machine-readable JSON Schema, and multi-format outputs, with four source-annotation rules and a mandatory human (pharmacist) review step.

---

## 0. 背景与定位 | Background & Positioning

### A. 现状与痛点（为什么要做这件事）| Status Quo & Pain Points

**当前开展工作的方式 | Current workflow**：药品说明书是以 Word / PDF 形式发布的法定文件，内容密集（数十页）、专业性强、章节混杂。医药从业者（药师、医师、医学编辑、医药代表、合规人员）日常需要从中快速提取关键信息（适应症、用法用量、相互作用、禁忌、不良反应）用于审方、用药指导、知识库录入、患者教育与监管申报。目前普遍依靠：①人工通读 + 复制粘贴摘录；②用通用 AI 对话工具零散提问、手动拼凑。

Drug package inserts are legally published as Word / PDF files — dense (dozens of pages), highly technical, and inconsistently structured. Pharmacists, physicians, medical writers, reps, and compliance staff routinely need to extract key facts (indications, dosing, interactions, contraindications, adverse reactions) for prescription review, patient counseling, knowledge-base entry, education, and regulatory filing. Today this relies on: (1) manual reading + copy-paste, or (2) ad-hoc general-purpose AI chats manually stitched together.

**存在的问题 | Problems**：
1. **效率低 | Low efficiency**：一份说明书动辄数十页，人工提取与排版耗时长，难以规模化。
2. **不统一 | Inconsistent**：不同人提取的字段、命名、颗粒度不一致，无法跨药品横向对比，也无法直接入库。
3. **可追溯性弱 | Weak traceability**：哪些是说明书原文、哪些是补充的，容易混淆，带来合规与用药安全风险。
4. **缺机器可读层 | No machine-readable layer**：纯文本 / Markdown 不便检索、入库、构建 RAG 或药品知识图谱。
5. **缺患者视角 | No patient view**：原说明书"看不懂、看不清"，患者版需人工改写，难以批量产出（国内适老化政策已明确要求改进）。
6. **合规风险高 | High compliance risk**：通用 AI 易臆造剂量、禁忌或遗漏黑框警告，缺少强制复核与免责机制。

**本技能如何解决 | How this skill helps**：将上述流程规范化、模板化——统一 12 模块骨架与字段 Schema（对齐 NMPA 规范 + FDA SPL 双层结构），规范四类来源标注，内置多形态输出与 QA 复核清单，使"说明书 → 结构化文档"可复用、可追溯、可入库、可对比。

This skill standardizes and templatizes the workflow — a unified 12-module skeleton and field Schema (aligned with NMPA + FDA SPL dual-layer), four source-annotation rules, built-in multi-format outputs and a QA checklist — making "insert → structured document" reusable, traceable, indexable, and comparable.

### B. 场景目标 | Scenario Goals

- **要解决的核心问题 | Core problem**：把"非结构化说明书 → 标准化、可检索、可入库文档"的过程，从"每人每次重做"变成"有标准、有模板、有质量门禁"的技能化流程。
- **期望达成的效果 | Expected outcomes**：
  1. 统一骨架与字段（对齐 NMPA + SPL），多药可横向对比、可聚合检索；
  2. 输出机器可读 JSON，支撑药品知识库 / RAG / 智能审方系统；
  3. 规范「未提及 / 不适用 / 联网补充 / 监管差异」四类标注，保证来源可追溯、合规安全；
  4. 一键生成患者版（适老化）、单药速查卡、多药对比表，覆盖临床与患者的双视角需求；
  5. 内置 QA 清单与强制人工（药师）复核，降低合规与用药安全风险。

### C. 预计需要的知识库 | Expected Knowledge Bases

- **法规规范 | Regulations**：NMPA《化学药品及生物制品说明书通用格式和撰写指南》及历次修订；《药品说明书和标签管理规定》（局令第24号）；FDA SPL 实施指南 / DailyMed 样例；ICH M5 药品字典数据元素；《中药注册管理专门规定》（2023年第20号）；《药品说明书适老化及无障碍改革试点工作方案》（2023年第142号）。
- **编码体系 | Coding systems**：ATC 分类、国家医保编码、药品本位码、UNII / NDC（跨境场景）。
- **术语标准 | Terminologies**：MedDRA 系统器官分类与 CIOMS 频次定义（用于不良反应结构化）。
- **证据来源 | Evidence**：临床指南与专家共识库（支撑【联网补充】的可靠出处）。
- **监管公告库 | Regulatory bulletins**：NMPA / FDA / EMA / MHRA 等官方公告（支撑【监管差异】比对）。

### 一句话定位 | One-line Positioning

drug-label-structurizer 将非结构化的原始药品说明书（Word / PDF / 图片）转化为**标准 12 模块骨架 + 机器可读 JSON Schema + 多形态输出（MD / HTML / 患者版 / 速查卡 / 多药对比）**的结构化文档，并对「未提及 / 不适用 / 联网补充 / 监管差异」四类情况进行规范标注，最终强制进入**人工（药师）复核**环节，实现"可读、可检索、可入库、可追溯、可对比"的药品信息整理，且**只做信息整理与呈现，不替代临床判断、不生成诊疗建议**。

drug-label-structurizer turns an unstructured drug package insert (Word / PDF / image) into a structured document with a **12-module skeleton + machine-readable JSON Schema + multi-format outputs (MD / HTML / patient / quick-card / comparison)**, applying the four annotation rules (`Not Mentioned` / `Not Applicable` / `Web Supplement` / `Regulatory Difference`), and ends with a **mandatory human (pharmacist) review**. It makes drug information readable, retrievable, indexable, traceable, and comparable — and **only organizes & presents information; it does not replace clinical judgment or generate medical advice**.

---

## 1. 适用场景与输入 | Applicable Scenarios & Inputs

| 输入形态 Input | 处理方式 Handling |
|---|---|
| `.docx` 说明书原件 | 优先用文档解析工具提取文本与表格，保留章节层级 / Parse with a doc tool, keep section hierarchy |
| `.pdf` 说明书 | 用 OCR / PDF 解析，注意表格与脚注还原 / OCR / PDF parse; recover tables & footnotes |
| 图片（扫描件 / 截图） | OCR 后按文本处理；低质量图片需提示用户补清晰源 / OCR then treat as text; ask for a clearer source if low-quality |
| 纯文本 / 用户粘贴 | 直接按章节骨架归类 / Map directly onto the skeleton |

**输出形态 | Output formats**：Markdown（md）、HTML（带目录与返回顶部）、JSON（机器可读，可入库/RAG）、患者版、速查卡、多药对比表。
Outputs: Markdown, HTML (with TOC & back-to-top), JSON (machine-readable, for DB/RAG), patient version, quick-reference card, multi-drug comparison table.

---

## 2. 核心原则（必须遵守）| Core Principles (Must Follow)

1. **不增不减 | No addition, no omission**：凡说明书原文已有的内容，忠实提取，不臆造、不润色事实；凡原文未提及的字段，填 `"未提及"` 或 `"不适用"`，**不得留空误导**。Extract faithfully; never fabricate. Missing fields must be `"未提及"` / `"不适用"` — never left blank.
2. **来源可追溯 | Source traceable**：严格标注每一条信息的出处（说明书原文 / 联网补充 / 监管差异），并保留源文件指纹（文件名、核准日期、修改日期、版本号）。Tag every fact's origin and keep the source fingerprint (file name, approval date, revision date, version).
3. **标注规范统一 | Unified annotation**：使用第 5 节的四种标准标签。Use the four standard tags from Section 5.
4. **人工复核前置 | Human review first**：任何面向临床/患者的输出，必须在文末附带"免责与复核声明"，并提示须经药师/医师审核。Every clinical/patient output must carry a disclaimer + review note and prompt for pharmacist/physician review.
5. **合规与安全 | Compliance & safety**：不上传患者隐私信息；跨境内容标注监管差异而非统一结论。Never upload patient PII; for cross-border content, annotate regulatory differences rather than asserting one conclusion.

---

## 3. 标准章节骨架（12 模块）| Standard 12-Module Skeleton

统一使用以下骨架，并与 NMPA 规范章节一一映射（顺序可调，但命名建议一致）。
Use the skeleton below and map it 1:1 to NMPA chapters (order may shift, but keep names consistent).

| # | 模块 Module | 对应 NMPA 章节 NMPA chapter |
|---|---|---|
| 一 | 药品基本信息 Basic Information | 【成份】【性状】【规格】【贮藏】【包装】【有效期】【执行标准】【批准文号】【生产企业】+ 黑框警告 |
| 二 | 适应症 Indications | 【适应症】 |
| 三 | 用法用量 Dosage & Administration | 【用法用量】 |
| 四 | 药理毒理 Pharmacology & Toxicology | 【药理毒理】 |
| 五 | 药代动力学 Pharmacokinetics | 【药代动力学】 |
| 六 | 循证医学证据 Evidence | （NMPA 无独立章节时，将说明书内记载的临床试验/药代数据集中呈现，标注"来源：本说明书"） |
| 七 | 联合用药（药物相互作用）Drug Interactions | 【药物相互作用】 |
| 八 | 不良反应 Adverse Reactions | 【不良反应】 |
| 九 | 禁忌症 Contraindications | 【禁忌】 |
| 十 | 注意事项 Precautions | 【注意事项】 |
| 十一 | 特殊人群用药 Special Populations | 【孕妇及哺乳期妇女用药】【儿童用药】【老年用药】+ 肝/肾功能损害 |
| 十二 | 药物过量 Overdosage | 【药物过量】 |

> 说明 | Note：NMPA 部分章节（如【临床试验】）可并入模块六或独立成节；生物制品可补充【免疫原性】【保存运输】；中药可补充【功能主治】【用法用量】差异表述。
> Some NMPA sections (e.g. 【临床试验】) may merge into Module 6 or stand alone; biologics may add 【免疫原性】【保存运输】; TCM may add 【功能主治】.
>
> ⚠️ **中药特殊要求**：依据《中药注册管理专门规定》（2023年第20号），中药说明书【禁忌】【不良反应】【注意事项】中任何一项为"尚不明确"的，满3年后申请再注册时将不予再注册。因此中药品种若出现"未提及"，须明确提示用户需按法规要求完善相关内容。
>
> ⚠️ **TCM Special Requirement**: Per the *Provisions on the Administration of Traditional Chinese Medicine Registration* (2023 No. 20), if any of 【禁忌】,【不良反应】,【注意事项】 is "尚不明确" in a TCM label, renewal registration will not be approved after 3 years. For TCM drugs with "未提及", explicitly prompt the user to完善 the content per regulatory requirements.

---

## 4. 字段 Schema（JSON，draft-07）| Field Schema (JSON, draft-07)

完整的机器可读层数据字典见 `references/json_schema.md`（严格按 NMPA 章节 + SPL 产品主数据映射，缺项字段强制填 `"未提及"` / `"不适用"`）。核心必填字段（`required`）：
Full machine-readable dictionary: `references/json_schema.md` (NMPA chapters + SPL product master; missing fields forced to `"未提及"` / `"不适用"`). Core required fields:

```
meta（来源与版本：source_file / approval_date / revision_date / version / data_source_statement / annotations）
product（药品主数据：generic_name / trade_name / active_ingredients / dosage_form / route / strengths / storage / approval_number / mah / 等）
indications（适应症：text + items[]）
dosage（用法用量：按适应症/人群拆分的数组）
adverse_reactions（不良反应：clinical / by_frequency / serious / post_marketing）
contraindications（禁忌：数组）
drug_interactions（药物相互作用：food / increases_level / decreases_level / affects_others / mechanism / drug_interaction_type）
```

每个药品一份 JSON；多药对比可输出数组。One JSON per drug; multi-drug comparison may output an array.

---

## 5. 标注规范（四标签）| Annotation Rules (Four Tags)

在正文或 JSON 中，对任何"非说明书原文直出"的内容使用以下标签：
In the document or JSON, tag any content that is not directly from the original insert:

| 标签 Tag | 含义 Meaning | 用法 Usage |
|---|---|---|
| `未提及` / `Not Mentioned` | 说明书未载该信息 | 字段缺项时填，不臆造 / Fill on missing field; never fabricate |
| `不适用` / `Not Applicable` | 该药品/场景不适用 | 如"儿童用药：不适用" / e.g. "Pediatric use: N/A" |
| `【联网补充】` / `Web Supplement` | 说明书未涵盖、经检索补充 | 须标注并对应文末参考来源编号 / Must cite a reference at the end |
| `【监管差异】` / `Regulatory Difference` | 不同国家/地区监管口径不同 | 如中英 MHRA 差异，须注明来源地区 / e.g. China–UK MHRA diff; note the source region |

**规则 | Rule**：`【联网补充】` 与 `【监管差异】` 必须在文末"参考来源"列出可核查链接/文献；纯原文整理须明确声明"未联网补充"。
`Web Supplement` and `Regulatory Difference` must list verifiable references at the end; pure original-text work must explicitly state "no web supplementation."

---

## 6. 输出生成器 | Output Generators

### 6.1 Markdown（默认，人读）| Markdown (default, human-readable)
- 使用二级标题（`## 一、…`）作为 12 模块，`###` 作小节。Use `##` for the 12 modules, `###` for subsections.
- 优先用表格呈现"项目 / 内容"。Prefer tables for "item / content".
- 黑框警告、数据来源用引用块或加粗提示框。Black-box warnings & data source in blockquotes / callout boxes.
- 文末附"数据来源声明 + 免责声明 + 复核提示"。End with data-source + disclaimer + review note.

### 6.2 HTML（可发布排版）| HTML (publish-ready)
- 单栏 max-width 720px，移动端友好。Single column, mobile-friendly.
- 顶部"黑框警告 + 数据来源"提示块。Top callouts for black-box warning + data source.
- 自动生成**目录（TOC）锚点 + 每节"返回目录" + 返回顶部**。Auto TOC anchors + "back to TOC" + "back to top".
- 表格斑马纹、章节色块，便于长文档阅读。Zebra tables & section blocks for long docs.
- 样式模板见 `assets/label_html_template.html`。Template: `assets/label_html_template.html`.

### 6.3 JSON（机器可读，可入库 / RAG）| JSON (machine-readable)
- 严格按 `references/json_schema.md` 输出。Follow `references/json_schema.md`.
- 缺项字段填 `"未提及"` / `"不适用"`。Missing fields → `"未提及"` / `"不适用"`.

### 6.4 患者版（适老化 / 通俗版）| Patient Version (elderly-friendly)
- 面向患者，文字缩减、语言通俗、突出"怎么吃、注意什么、出事找谁"。For patients: plain language, cut length, highlight "how to take / what to watch / who to call".
- 保留：药品名称、干什么用、怎么用、常见/严重不良反应、禁忌、存储。Keep: name, purpose, usage, common/serious ADR, contraindications, storage.
- 长度目标为原说明书的 10%–20%，药师审核后发布。Target 10%–20% of original length; publish after pharmacist review.
- **适老化规范参考**：依据《药品说明书适老化及无障碍改革试点工作方案》（2023年第142号），可同步提供大字版、简化版、电子版、语音播报等多种无障碍格式。
- **Elderly-friendly reference**: Per the *Pilot Work Plan for Age-friendly and Accessible Drug Package Insert Reform* (2023 No. 142), consider providing multiple accessible formats: large-print, simplified, electronic, and voice broadcast versions.

### 6.5 单药速查卡（Highlights）| Single-Drug Quick Card
- 仿 SPL Highlights：一屏提炼黑框警告、最关键适应症、禁忌、重点不良反应、用法用量要点。SPL-style Highlights: one screen with black-box warning, key indication, contraindications, key ADRs, dosing essentials.

### 6.6 多药横向对比表 | Multi-Drug Comparison Table
- 输入 2+ 药品 JSON，按核心参数（适应症、关键不良反应、禁忌、特殊人群、相互作用）并排生成对比表，辅助循证决策。Input 2+ drug JSONs; compare side-by-side on indications, key ADRs, contraindications, special populations, interactions.

---

## 7. 质量复核清单（QA）| QA Checklist

交付前逐项核对 | Verify before delivery:

- [ ] 12 模块齐全，章节命名与 NMPA 一致 / All 12 modules present, names match NMPA
- [ ] 所有字段来自说明书原文或已规范标注（无臆造）/ All fields from original or properly tagged (no fabrication)
- [ ] 缺项已填 `未提及` / `不适用`，无空白误导 / Missing fields filled `未提及` / `不适用`, no blanks
- [ ] `【联网补充】` / `【监管差异】` 均有文末来源 / Web Supplement & Regulatory Difference have end references
- [ ] 剂量、频次、单位、百分比与原文逐字核对 / Dose, frequency, units, percentages verified verbatim
- [ ] 黑框警告 / 禁忌 / 严重不良反应未被遗漏或弱化处理 / Black-box / contraindications / serious ADRs not omitted or softened
- [ ] JSON 通过 Schema 校验（required 字段齐全）/ JSON passes Schema (required fields complete)
- [ ] **幻觉检查（Hallucination check）**：高风险字段（剂量/禁忌/严重不良反应）已逐字比对原文，无 AI 臆造内容 / High-risk fields (dosage/contraindications/serious ADRs) verified word-for-word against source; no AI fabrication
- [ ] 数据来源声明 + 免责声明 + 复核提示已附 / Data-source + disclaimer + review note attached
- [ ] **已提示须经药师/医师人工复核** / **Prompted for pharmacist/physician human review**

---

## 8. 免责与合规声明模板 | Disclaimer & Compliance Template

> 整理依据：《XXX 说明书》（商品名 / 持证商 / 批准文号）。本文件内容严格取自该说明书原文，[未引入任何外部来源 / 已标注联网补充并对应来源]。本文件仅供信息整理与学习参考，**不构成任何诊疗建议**，具体临床用药请遵说明书、最新指南及医师/药师专业判断。AI 整理结果须经具备资质的医药专业人员复核后方可使用。

> Basis: *XXX Package Insert* (brand / MAH / approval number). Content is strictly drawn from the original insert, [no external sources / web supplements cited]. For information organization and study only — **not medical advice**. Clinical use must follow the insert, latest guidelines, and professional judgment. AI output must be reviewed by a qualified pharmaceutical professional before use.

---

## 9. 工作流步骤（Step-by-Step）| Workflow Steps

1. **确认输入 | Confirm input**：接收源文件（docx/pdf/图片/文本），确认格式与清晰度。Receive source; check format & clarity.
2. **解析提取 | Parse & extract**：用文档解析 / OCR 还原文本与表格，保留章节层级。Recover text & tables, keep hierarchy.
3. **按骨架归类 | Map to skeleton**：将内容映射到 12 模块 + JSON Schema 字段。Map to 12 modules + JSON fields.
4. **标注处理 | Annotate**：对未提/不适用/联网补充/监管差异 规范打标。Apply the four tags.
5. **生成输出 | Generate output**：按用户需求选 MD / HTML / JSON / 患者版 / 速查卡 / 对比表。Pick format per request.
6. **QA 复核 | QA review**：走第 7 节清单。Run the Section 7 checklist.
7. **附声明 | Attach statements**：加数据来源 + 免责 + 复核提示。Add data source + disclaimer + review note.
8. **交付并提示人工复核 | Deliver & prompt review**。

---

## 参考资源 / Reference Resources

- `references/json_schema.md` —— 完整 JSON Schema（机器可读层字段字典）/ Full JSON Schema (machine-readable field dictionary)
- `assets/label_html_template.html` —— HTML 排版模板（TOC + 返回顶部 + 章节色块）/ HTML typesetting template (TOC + back-to-top + section blocks)

---

## 技能目录结构 | Skill Directory Structure

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
