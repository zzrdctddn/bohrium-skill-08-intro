# 引言与相关工作写作 Skill（intro-related-work-writer）

一个把科研材料转成"可核验引言成稿"的 Agent Skill。核心：论断-引用绑定 + 比较矩阵 + 空白推导 + 贡献-证据对齐 + 写作核对分离。

## 目录结构
- `SKILL.md` —— Skill 本体（含 YAML frontmatter、工作流、输出契约）
- `references/` —— 方法细则、自检清单、字段字典、论断类型规则
- `assets/` —— 成稿模板
- `examples/` —— 最小调用示例（已标注为示例，非真实案例）
- `GITHUB_URL.txt` —— 提交时填 GitHub 公开链接

## 使用方式
1. 将本目录作为 Skill 安装到 Agent（平台约定路径 `/skills/weekly-skill`）。
2. 输入 `/input/case.json` 与 `task.md`，自然语言下指令如"写引言"。
3. 按 SKILL.md 的 SOP 执行，输出到 `/app/output/result.json` 与 `introduction.md`。

## 依赖
仅需文本处理能力；无外部运行时依赖。引用格式规则内置于 SKILL.md 与 references。

## 适用与限制
- 适用：CS/AI/数据等学科的中英文引言起草与相关工作整理。
- 限制：材料为空或引用无法核验时会标记 partial/blocked，不编造事实；不替代领域专家对结论价值的判断。

## 评分对应
实际效果(40)、使用增益(25)、创新增益(15)、可靠体验(10)、生态贡献(10) 的支撑材料分别见 SKILL.md 第 3/2/2/6/8 节与 references。
