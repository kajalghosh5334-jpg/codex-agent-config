# progress.md

<!--
  本文件合并两份职责：
  1. Stage Deliverables  — 各阶段产出物料清单（文档型），门禁检查依据
  2. Task State Machine — 工程执行阶段的任务进度（代码型），原有字段全保留
  非工程阶段只使用 Stage Deliverables，Task State Machine 留空。
-->

## Stage Deliverables

<!-- AI 在 Q1 识别 scene × stage 后动态生成此清单，不查表 -->
<!-- P0 = 必须完成，否则阶段门禁不通过；P1 = 建议完成，不阻止推进 -->

| # | 物料名称 | 产出标准 | 优先级 | 状态 |
|---|---------|---------|--------|------|
|   |         |         |      |      |

<!--
  状态说明：
  □ = 未完成（门禁锁定，AI 输出范围限制在本阶段）
  ✓ = 已完成（AI 已向用户产出并确认）
  状态由 AI 在用户完成物料后更新，更新前输出：
    更新Memory · progress.md：[物料名称] → ✓
-->

<!-- 阶段完成条件：所有 P0 状态 = ✓ -->
<!-- AI 检查到所有 P0 ✓ 后，触发「阶段完成引导」（output-engine 规则十三） -->

---

## Task State Machine

<!-- 工程执行阶段使用，非工程阶段留空 -->
<!-- 以下字段为 output-engine 规则二（任务状态机）原始定义，全部保留 -->

```yaml
Goal:          # 当前目标，一句话
Current Step:  # 正在做什么
Completed:     # 已完成的子任务
Pending:       # 还没做的
Risks:         # 已识别的风险
```

<!-- 工程执行阶段门禁额外检查：Pending 清空 + bugs.md 无新增 P0，才允许进入下一阶段 -->

---

## Stage History

<!-- 每次阶段推进时追加一条，不删除，用于追溯 -->
<!-- 格式：{ scene, stage, entered_at, completed_at } -->
<!-- entered_at  = 第 N 轮对话；completed_at = null 表示进行中，完成后填入轮次 -->

<!--
  示例：
  - { scene: 产品策略, stage: 立项期, entered_at: 第3轮, completed_at: 第8轮 }
  - { scene: 技术架构, stage: 立项期, entered_at: 第9轮, completed_at: null }
-->
