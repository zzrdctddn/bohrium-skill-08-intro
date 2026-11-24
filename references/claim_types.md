# 论断类型与置信度判定

## 论断类型（type）
- **in_paper_fact**：本文材料直接给出的事实（作者自己的方法、实验、数据）。来源=本文稿件位置。
- **prior_result**：引用他人/前序工作的结论。来源必须是可定位文献；无来源则 unverified。
- **author_judgement**：作者的判断、推测、动机。允许无外部来源，但必须显式标注，不得伪装成事实。

## 置信度（confidence）
- **high**：来源逐字可查，位置精确到页/段/图表号。
- **medium**：来源可定位到文献，但需跨段推断。
- **low**：来源模糊、版本不明或存在冲突，必须进 human_review_items。

## 降级规则
核对时若发现某 prior_result 来源不实或位置错误：优先删除该论断；若结构需要，改为 author_judgement 并加"作者认为"限定，且在 change_log 记录。