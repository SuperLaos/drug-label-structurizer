# JSON Schema — StructuredDrugLabel（draft-07）

机器可读层数据字典。用于把一份结构化说明书输出为可入库 / 可检索 / 可构建 RAG 的 JSON。
缺项字段统一填 `"未提及"` 或 `"不适用"`，不得留空。

Machine-readable data dictionary. Used to emit a structured label as JSON for database / retrieval / RAG.
Missing fields must be filled with `"未提及"` (Not Mentioned) or `"不适用"` (Not Applicable) — never left blank.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "StructuredDrugLabel",
  "type": "object",
  "required": ["meta", "product", "indications", "dosage", "adverse_reactions", "contraindications"],
  "properties": {
    "meta": {
      "type": "object",
      "description": "来源与版本元数据，对应 SPL 的文档级结构 / Source & version metadata (SPL document-level)",
      "required": ["source_file", "data_source_statement"],
      "properties": {
        "source_file": { "type": "string", "description": "源文件名 / Source file name" },
        "approval_date": { "type": "string", "description": "核准日期，如 2019-10-31 / Approval date, e.g. 2019-10-31" },
        "revision_date": { "type": "string", "description": "最近修改日期 / Latest revision date" },
        "version": { "type": "string", "description": "说明书版本号 / Label version number" },
        "data_source_statement": { "type": "string", "description": "数据来源声明（是否联网补充）/ Data-source statement (whether web-supplemented)" },
        "annotations": {
          "type": "array",
          "description": "标注汇总 / Annotation summary",
          "items": { "type": "string", "enum": ["未提及", "不适用", "联网补充", "监管差异"],
            "description": "四标签：未提及 Not Mentioned / 不适用 Not Applicable / 联网补充 Web Supplement / 监管差异 Regulatory Difference" }
        }
      }
    },
    "product": {
      "type": "object",
      "description": "药品主数据，对应 SPL 产品信息 + NMPA【成份】等 / Product master (SPL product info + NMPA 【成份】 etc.)",
      "properties": {
        "generic_name": { "type": "string", "description": "通用名 / Generic name" },
        "trade_name": { "type": "string", "description": "商品名 / Trade name" },
        "english_name": { "type": "string", "description": "英文名 / English name" },
        "pinyin": { "type": "string", "description": "汉语拼音 / Pinyin" },
        "active_ingredients": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "name": { "type": "string", "description": "成分名 / Ingredient name" },
              "chem_name": { "type": "string", "description": "化学名 / Chemical name" },
              "formula": { "type": "string", "description": "分子式 / Formula" },
              "molecular_weight": { "type": "string", "description": "分子量 / Molecular weight" }
            }
          }
        },
        "excipients": { "type": "array", "items": { "type": "string" }, "description": "辅料 / Excipients" },
        "dosage_form": { "type": "string", "description": "剂型 / Dosage form" },
        "route": { "type": "string", "description": "给药途径 / Route of administration" },
        "strengths": { "type": "array", "items": { "type": "string" }, "description": "规格 / Strengths" },
        "appearance": { "type": "string", "description": "性状 / Description (appearance)" },
        "storage": { "type": "string", "description": "贮藏 / Storage" },
        "packaging": { "type": "string", "description": "包装 / Packaging" },
        "shelf_life": { "type": "string", "description": "有效期 / Shelf life" },
        "standard": { "type": "string", "description": "执行标准 / Executed standard" },
        "approval_number": { "type": "string", "description": "批准文号 / Approval number" },
        "mah": { "type": "string", "description": "上市许可持有人 / Marketing Authorization Holder" },
        "manufacturer": { "type": "string", "description": "生产企业 / Manufacturer" },
        "atc": { "type": "string", "description": "可选：ATC 分类编码 / Optional: ATC code" },
        "nhsa_code": { "type": "string", "description": "可选：医保编码 / Optional: NHSA (medical insurance) code" },
        "black_box_warning": { "type": "string", "description": "黑框级警告摘要 / Black-box warning summary" }
      }
    },
    "indications": {
      "type": "object",
      "description": "适应症 / Indications",
      "properties": {
        "text": { "type": "string", "description": "适应症正文 / Indication narrative" },
        "items": {
          "type": "array",
          "description": "适应症条目 / Indication items",
          "items": {
            "type": "object",
            "properties": {
              "category": { "type": "string", "description": "类别 / Category" },
              "detail": { "type": "string", "description": "详述 / Detail" },
              "population": { "type": "string", "description": "适用人群 / Population" },
              "limitation": { "type": "string", "description": "限制/慎用 / Limitation" }
            }
          }
        }
      }
    },
    "dosage": {
      "type": "array",
      "description": "用法用量，按适应症/人群拆分，便于检索 / Dosage & administration, split by indication/population for retrieval",
      "items": {
        "type": "object",
        "properties": {
          "population": { "type": "string", "description": "人群 / Population" },
          "initial_dose": { "type": "string", "description": "起始剂量 / Initial dose" },
          "maintenance": { "type": "string", "description": "维持剂量 / Maintenance dose" },
          "monitoring": { "type": "string", "description": "监测要求 / Monitoring" }
        }
      }
    },
    "adverse_reactions": {
      "type": "object",
      "description": "不良反应 / Adverse reactions",
      "properties": {
        "clinical": { "type": "array", "items": { "type": "string" }, "description": "临床试验主要 ADR / Key trial ADRs" },
        "by_frequency": {
          "type": "array",
          "description": "按频次分类 / Grouped by frequency",
          "items": {
            "type": "object",
            "properties": {
              "system_organ": { "type": "string", "description": "系统器官类（MedDRA SOC）/ System organ class (MedDRA SOC)" },
              "frequency": { "type": "string", "description": "频次（CIOMS 定义）/ Frequency (per CIOMS)" },
              "reaction": { "type": "string", "description": "反应 / Reaction" }
            }
          }
        },
        "serious": { "type": "array", "items": { "type": "string" }, "description": "严重警示（感染/肿瘤等）/ Serious warnings (infection/tumor etc.)" },
        "post_marketing": { "type": "array", "items": { "type": "string" }, "description": "上市后经验 / Post-marketing experience" }
      }
    },
    "contraindications": {
      "type": "array",
      "description": "禁忌 / Contraindications",
      "items": {
        "type": "object",
        "properties": {
          "condition": { "type": "string", "description": "禁忌情形 / Condition" },
          "detail": { "type": "string", "description": "说明 / Detail" }
        }
      }
    },
    "precautions": {
      "type": "object",
      "description": "注意事项要点集合 / Precautions",
      "properties": {
        "renal_toxicity": { "type": "string", "description": "肾毒性 / Renal toxicity" },
        "hepatic_toxicity": { "type": "string", "description": "肝毒性 / Hepatic toxicity" },
        "infection": { "type": "string", "description": "感染风险 / Infection risk" },
        "tumor": { "type": "string", "description": "肿瘤风险 / Tumor risk" },
        "tdm": { "type": "string", "description": "血药浓度监测 / Therapeutic drug monitoring (TDM)" },
        "vaccine": { "type": "string", "description": "疫苗注意 / Vaccine caution" }
      }
    },
    "special_populations": {
      "type": "object",
      "description": "特殊人群用药 / Special populations",
      "properties": {
        "pregnancy": { "type": "string", "description": "妊娠 / Pregnancy" },
        "lactation": { "type": "string", "description": "哺乳 / Lactation" },
        "pediatric": { "type": "string", "description": "儿童 / Pediatric" },
        "geriatric": { "type": "string", "description": "老年 / Geriatric" },
        "renal_impairment": { "type": "string", "description": "肾功能损害 / Renal impairment" },
        "hepatic_impairment": { "type": "string", "description": "肝功能损害 / Hepatic impairment" }
      }
    },
    "drug_interactions": {
      "type": "object",
      "description": "药物相互作用 / Drug interactions",
      "properties": {
        "food": { "type": "array", "items": { "type": "string" }, "description": "食物相互作用 / Food interactions" },
        "increases_level": { "type": "array", "items": { "type": "string" }, "description": "升高浓度 / Increases level" },
        "decreases_level": { "type": "array", "items": { "type": "string" }, "description": "降低浓度 / Decreases level" },
        "affects_others": {
          "type": "array",
          "description": "影响其他药 / Affects other drugs",
          "items": { "type": "object", "properties": { "drug": { "type": "string", "description": "药物 / Drug" }, "advice": { "type": "string", "description": "建议 / Advice" } } }
        },
        "mechanism": { "type": "string", "description": "机制 / Mechanism" },
        "drug_interaction_type": { "type": "string", "enum": ["强", "中", "弱"], "description": "相互作用强度等级 / Interaction strength: Strong / Moderate / Weak" }
      }
    },
    "overdosage": {
      "type": "object",
      "description": "药物过量 / Overdosage",
      "properties": {
        "symptoms": { "type": "string", "description": "症状 / Symptoms" },
        "management": { "type": "string", "description": "处理 / Management" }
      }
    },
    "clinical_trials": { "type": "string", "description": "关键临床试验与循证数据（可含表格）/ Key clinical trials & evidence (may include tables)" },
    "pharmacology_toxicology": { "type": "string", "description": "药理毒理 / Pharmacology & toxicology" },
    "pharmacokinetics": {
      "type": "object",
      "description": "药代动力学 / Pharmacokinetics",
      "properties": {
        "general": { "type": "string", "description": "概述 / General" },
        "special_populations": { "type": "string", "description": "特殊人群药代 / PK in special populations" }
      }
    }
  }
}
```

## 字段与 NMPA 章节映射速查 | Field ↔ NMPA Chapter Mapping

| JSON 字段 JSON field | 对应 NMPA 章节 NMPA chapter |
|---|---|
| product.generic_name / trade_name / active_ingredients / excipients | 【成份】 Ingredients |
| product.appearance | 【性状】 Description |
| product.strengths | 【规格】 Strength |
| dosage | 【用法用量】 Dosage & Administration |
| indications | 【适应症】 Indications |
| adverse_reactions | 【不良反应】 Adverse Reactions |
| contraindications | 【禁忌】 Contraindications |
| precautions | 【注意事项】 Precautions |
| special_populations | 【孕妇及哺乳期妇女用药】【儿童用药】【老年用药】 Pregnancy / Lactation / Pediatric / Geriatric |
| drug_interactions | 【药物相互作用】 Drug Interactions |
| overdosage | 【药物过量】 Overdosage |
| clinical_trials / pharmacology_toxicology / pharmacokinetics | 【临床试验】【药理毒理】【药代动力学】 Trials / Pharm-Tox / PK |
| product.storage / packaging / shelf_life / standard / approval_number / manufacturer | 【贮藏】【包装】【有效期】【执行标准】【批准文号】【生产企业】 Storage / Packaging / Shelf life / Standard / Approval No. / Manufacturer |
