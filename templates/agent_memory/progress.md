# progress.md

<!--
  本文件合并两份职责：
  1. Stage Deliverables  — 各阶段产出物料清单（文档型），门禁检查依据
  2. Task State Machine — 工程执行阶段的任务进度（代码型），原有字段全保留
  非工程阶段只使用 Stage Deliverables，Task State Machine 留空。
-->

## Stage Deliverables

<!-- AI 在 output-engine Q1 Step 3 识别到新阶段后，调用 engineering-mode 规则十二 动态生成此清单，不查表 -->
<!-- P0 = 必须完成，否则阶段门禁不通过；P1 = 建议完成，不阻止推进 -->

| # | 物料名称 | 产出标准 | 优先级 | 状态 |
|---|---------|---------|--------|------|
|   |         |         |        |      |

<!--
  状态说明：
  □ = 未完成（门禁锁定，AI 输出范围限制在本阶段）
  ✓ = 已完成

  完成判定：AI 已输出该物料的完整内容，且用户无追问或明确确认
  状态更新方式：Memory Diff [静默] 写回
    progress.md Stage Deliverables[物料名称].状态: □ → ✓
-->

<!-- 阶段完成条件：所有 P0 状态 = ✓ -->
<!-- AI 检查到所有 P0 ✓ 后，触发「阶段完成引导」（engineering-mode 规则十三） -->

---

## Task State Machine

<!-- 工程执行阶段使用（is_engineering = true），非工程阶段留空 -->
<!-- 由 engineering-mode 规则二初始化和维护，所有更新通过 Memory Diff [静默] 写回 -->

```yaml
Goal:          # 当前目标，一句话（来源：key_decisions + 项目概述）
Current Step:  # 正在做什么（来源：Pending 第一项）
Completed:     # 已完成的子任务
Pending:       # 还没做的（来源：Stage Deliverables P0/P1 物料名称）
Risks:         # 已识别的风险（来源：bugs.md 状态=开放的条目摘要）
```

<!-- 工程执行阶段门禁额外检查：Pending 清空 + bugs.md 无开放 P0 条目，才允许进入下一阶段 -->

---

## Stage History

<!-- 执行维度的阶段完成记录（与 context.md topic_stack.stage_history 分工：
     此处记录各阶段何时完成；context.md 记录话题维度的阶段推进轨迹） -->
<!-- 每次阶段完成时追加一条（由 engineering-mode 规则十三 触发写入），不删除，用于追溯 -->
<!-- 格式：{ scene_domain, task_type, entered_at, completed_at } -->
<!-- entered_at / completed_at = 第 N 轮对话（每次用户发言+1，从第1轮开始计）；completed_at = null 表示进行中 -->

<!--
  示例：
  - { scene_domain: 产品运营, task_type: design, entered_at: 第3轮, completed_at: 第8轮 }
  - { scene_domain: 后端开发, task_type: execute, entered_at: 第9轮, completed_at: null }
-->
