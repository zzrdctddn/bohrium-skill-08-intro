---
name: intro-related-work-writer
description: 把论文材料（PDF/事实底稿/实验记录）转成可直接入稿的"引言 + 相关工作"成稿，并保证每一条论断、引用、贡献都能回溯到来源。当用户要求"写引言""写相关工作""补研究空白""提炼贡献""核对引用覆盖""整理研究背景"时使用。输出含四段成稿、论断-引用绑定表、相关工作比较矩阵、空白推导链、贡献-证据对齐表与自检结果。
version: 1.0.0
license: MIT
---

# 引言与相关工作写作（Evidence-Anchored Introduction Writer）

> 面向真实科研材料，把"写引言"从凭感觉行文变成**可核验的工程流程**。核心不是生成漂亮的段落，而是让每一句话都能回答："依据在哪里？"

## 1. 用途与适用边界

**解决什么**：基础 Agent 写引言的常见失败——"近年来受到广泛关注"式空转铺垫、相关工作按时间流水账罗列、研究空空白天而降、贡献夸大或与实验对不上、引用张冠李戴。本 Skill 用"写作与核对分离 + 论断强制绑定来源"消除这些失败。

**触发场景**：写/改写论文引言；整理相关工作；推导研究空白；提炼贡献条目；核对引用覆盖与元数据；把中文底稿写成英文引言。

**不该用**：材料为空且不允许联网核验时（应报告材料缺口而非硬写）；用户只要语言润色而不要结构重组时（转用学术语言润色 Skill）。

**输入**：`/input/case.json`（含 `case_id`、任务说明、论文材料或事实底稿、可用文献库）与 `task.md`。**输出**：`/app/output/result.json`（字段见第 4 节）+ 可编辑的 `introduction.md`。

## 2. 核心方法（EAF-CM）

本 Skill 的方法名为 **EAF-CM = Evidence-Anchored Funnel + Comparison Matrix**，四个不可跳过的机制：

1. **论断-引用绑定表（Claim–Citation Ledger）**：成稿中每一个实质论断分配 `claim_id`，标注类型（`in_paper_fact` 本文事实 / `prior_result` 他人结论 / `author_judgement` 作者判断）。任何 `prior_result` 必须有可定位来源；查不到就标 `unverified` 并进入人工确认，绝不写成事实。
2. **相关工作比较矩阵（Comparison Matrix）**：先定比较维度（问题设定 / 方法路线 / 数据与评价 / 适用条件与局限），再按维度分组排列工作，**禁止按年份罗列**。每个格子填"该工作在此维度的取舍 + 来源"。
3. **空白推导链（Gap Derivation Chain）**：研究空白必须由"已引工作的共同限制 / 未覆盖场景 / 互相冲突的结论"三步推出，最后连到本文工作如何填补。禁止无来源的"然而这一问题尚未得到充分研究"。
4. **贡献-证据对齐表（Contribution–Evidence Map）**：每条贡献对应正文的一个具体图表/实验/公式编号；对不上的贡献删除或降级表述。

另外贯穿一条铁律：**写作与核对分离**——先完成草稿，再用全新视角独立执行第 5 节自检，两遍不可合并。

## 3. 工作流（SOP）

严格按顺序执行，每步都有检查点；检查点不过则回退该步。

**Step 0 · 输入解析与边界确认**
- 读 `case.json` 与 `task.md`，提取：目标体裁/受众/语言/引用格式、可用材料清单、明确禁止事项。
- 检查点：材料是否足以支撑引言四要素？缺口登记到 `uncertainties`，不要静默跳过。

**Step 1 · 事实底稿抽取（Fact Inventory）**
- 通读材料，产出事实清单：研究问题、关键方法、数据集、主要结果（带数值）、局限。每条记录来源位置（页码/章节/图表号）。
- 检查点：数值与单位逐条与原文比对；材料里没有的数字一个都不许出现。

**Step 2 · 文献筛选与比较矩阵**
- 从文献库/材料中筛出关键工作、代表性工作、近期进展。
- 建立比较矩阵（维度 × 工作），逐格填取舍与来源；同维度内比较，不跨维度混比。
- 检查点：每篇工作至少命中一个维度；元数据（作者、年份）齐全，缺项标 `unverified`。

**Step 3 · 四段成稿（Funnel）**
按漏斗顺序写，段段承接：
- 3.1 **研究背景**：从领域脉络 → 具体问题 → 技术约束 → "为什么现在值得做"。每段末尾挂 `claim_id`。
- 3.2 **相关工作**：按 Step 2 的分组维度叙述，同维度比较取舍，段末点出该组共同局限。
- 3.3 **研究空白**：用 Step 3.2 的局限执行空白推导链，显式写出"本文如何填补"。
- 3.4 **贡献条目**：3–5 条，每条对齐 Step 1 的证据与正文图表位置。
- 检查点：四段齐全；每个实质论断都有 `claim_id`；无来源的判断标注为作者判断。

**Step 4 · 引用绑定与格式**
- 为每条 `prior_result` 绑定引用，按指定格式（顺序编码/著者-出版年/GB-T 7714 等）排版；一句多引、多句一引按格式规则处理。
- 检查点：正文引用与文末条目一一对应；无孤儿引用、无未被引用条目（除非任务允许）。

**Step 5 · 独立核对（与写作分离）**
- 逐条走 `references/checklist.md`；生成 `contribution_evidence_map` 与 `fact_source_map`；把无法核验的论断降级或标 `uncertainty`。
- 检查点：自检表全部通过或每项未通过都有 `change_log` 记录。

**Step 6 · 输出**
- 写 `introduction.md`（可编辑成稿）+ `result.json`（结构化结果，含 `case_id` 与全部输出契约字段）。

## 4. 输出契约（result.json）

```json
{
  "case_id": "<原样带回>",
  "status": "success | partial | blocked",
  "artifact": {
    "background": "<研究背景正文>",
    "related_work": "<相关工作正文>",
    "gap": "<研究空白正文>",
    "contributions": ["<贡献条目1>", "..."]
  },
  "claim_citation_ledger": [
    {"claim_id": "C1", "text": "...", "type": "prior_result", "source": "<文献标识>", "location": "p3 §2.1", "confidence": "high|medium|low", "status": "verified|unverified"}
  ],
  "comparison_matrix": {"dimensions": ["问题设定","方法路线","数据与评价","局限"], "cells": [{"work":"...", "dimension":"...", "tradeoff":"...", "source":"..."}]},
  "gap_derivation": [{"from_limitation":"...", "source":"...", "to_gap":"...", "filled_by":"..."}],
  "contribution_evidence_map": [{"contribution":"...", "evidence":"图3 / 表2 / 式(5)", "location":"..."}],
  "fact_source_map": [{"item":"<数值/术语/引用>", "value":"...", "source_location":"..."}],
  "uncertainties": ["..."],
  "human_review_items": ["..."],
  "change_log": [{"step":5, "action":"降级贡献3的表述", "reason":"正文无对应实验"}],
  "self_check": {"four_parts_complete": true, "claims_anchored": true, "citations_matched": true, "contributions_evidenced": true}
}
```

字段原则：`status` 诚实反映完成情况；`partial` 必须写清 `uncertainties`；不得编造 `benchmark` 分数或软件状态。

## 5. 质量自检清单

执行前完整读取 `references/checklist.md`，逐项打钩。关键硬项：

- [ ] 背景、相关工作、空白、贡献四段齐全且承接
- [ ] 每个实质论断有 `claim_id` 与来源/作者判断标注
- [ ] 相关工作按维度分组，非时间罗列
- [ ] 空白由已引文献缺口推出，非空话
- [ ] 每条贡献有正文图表/实验对应
- [ ] 正文引用与文末条目一一对应，元数据齐全
- [ ] 无材料外新增事实、数值、单位
- [ ] 独立核对已完成，未通过项均有 `change_log`

## 6. 异常处理与边界

| 情况 | 处理 |
|---|---|
| 材料不足 | `status=partial`，列出缺口，给出"若补充 X 可继续"的说明，不硬凑 |
| 文献库为空 | 只写基于现有材料的内容，引用覆盖项标 `blocked` |
| 引用查不到原文 | 标 `unverified`，进入 `human_review_items`，成稿中用中性表述 |
| 目标期刊格式未知 | 采用材料指定格式；无则默认顺序编码制并在 `uncertainties` 说明 |
| 语言切换（中→英） | 保留中文术语表，英文用词与之对齐；文化特定表述加注 |
| 空白缺依据 | 不写空白段，标 `blocked` 并说明需要哪些对比文献 |

## 7. 最小调用示例（示意，非真实案例）

**输入**（`case.json` 片段）：
```json
{"case_id":"demo-001","task":"根据给定摘要与3篇文献写引言的研究背景与相关工作","materials":["abstract.txt","refs/"],"cite_style":"numeric"}
```
**调用**：加载本 Skill 后，用户说"用 intro-related-work-writer 写这篇的引言"。

**示意输出**（节选）：
```json
{"case_id":"demo-001","status":"success","artifact":{"background":"……（示意文本）","gap":"……"},"claim_citation_ledger":[{"claim_id":"C1","type":"prior_result","source":"Ref[2]","location":"摘要第3句","status":"verified"}]}
```
> 上述为演示接口用的虚构示例，不代表任何真实案例结果。

## 8. 资源清单

- `references/method.md` —— EAF-CM 方法细则与反例库
- `references/checklist.md` —— 逐项自检清单
- `references/schema.json` —— 输出字段字典
- `references/claim_types.md` —— 论断类型与置信度判定规则
- `assets/introduction_template.md` —— 成稿模板
- `examples/` —— 最小调用示例（标注为示例）
