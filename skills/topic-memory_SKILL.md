---
name: topic-memory
description: |
  话题栈管理器。由 output-engine 调用，不直接面向用户。
  负责话题状态的读写、切换、继承和归档。
  覆盖：激活/挂起/借道/新话题/归档 五种状态的完整生命周期。
---

# Topic Memory

## 两层模型

```
话题层（topic）：管「是不是同一件事」
  同一个项目，从立项到上线，始终是同一个 topic
  topic.id 不变，key_decisions / open_threads 全程继承

阶段层：管「当前在项目哪个阶段，做没做完」
  阶段推进 = 同 topic 内 task_type / clarity 变化，不触发 topic 切换
  每个阶段有产出物料清单（写入 progress.md 的 ## Stage Deliverables 部分）和门禁检查
```

## context.md 中的 topic_stack 结构

```markdown
## topic_stack

active:
  id: topic_001
  scene_domain: 后端开发              # Q1 写入，技术类优先用白名单词，其他自由填写，1-3词
  task_type: execute                  # Q1 写入，封闭值：analyze/design/execute/review
  clarity: high                       # Q1 写入，封闭值：low/mid/high
  progress: 开发进行中                # Q1 写入，AI 自由填写，描述当前任务节点（非业务节点）
  is_engineering: true                # Q1 Step 2 推理判断，由 output-engine 写入/更新
  biz_scene:                          # Q2.5 写入，每次对话首轮更新
    user_role: 产品经理               # 发起问题的用户本人在组织里的职责
    stage: 立项期                     # 用户所处的业务节点（非任务节点，与 progress 不同）
    stakeholders:
      - role: CTO
        focus: 技术可行性
  active_roles:                       # Q3 写入，动态推断，每轮更新
    - role_name: 系统架构师
      focus: 接口契约·扩展点
  key_decisions:
    - 选了微服务拆分，放弃单体——原因：团队规模已超10人
    - 用户表暂不分库，待 DAU 超50万再评估
  open_threads:
    - 权限模块接口契约尚未确认
    - 缓存策略待定
    # 工程任务的 Additional Findings 也写这里，前缀 [AF]
    # 预算超出确认记录写入 key_decisions，前缀 [预算确认]
    # bugs.md 新增风险同步写入这里，前缀 [AF]，风险解决后移除
  stage_history:
    # 话题维度的阶段轨迹（与 progress.md 的 ## Stage History 分工：
    #   此处记录话题内各阶段的推进轨迹；progress.md 记录执行维度的阶段完成记录）
    # entered_at 写入规则：当前对话轮次编号，从第1轮开始计，每次用户发言+1
    - { scene_domain: 系统架构, task_type: design, clarity: low, entered_at: 第3轮, completed_at: 第8轮 }
    - { scene_domain: 后端开发, task_type: execute, clarity: high, entered_at: 第9轮, completed_at: null }

suspended:
  - id: topic_000
    scene_domain: 产品策略
    task_type: design
    clarity: low
    snapshot: "讨论到用户分层方案，待确认付费用户权限边界"
    suspended_at: 第12轮对话

archived: []
```

---

## 五种状态定义

```
激活（active）
  当前进行中的话题
  完整记录：scene_domain / task_type / clarity / progress / is_engineering / biz_scene / active_roles / key_decisions / open_threads
  同一时刻只有一个

挂起（suspended）
  真切换时，原话题压栈保存快照
  保存：一句话摘要 + open_threads + suspended_at
  可以有多个

借道（detour）
  临时离轨，不入栈，不存档，不更新 topic_stack
  回复结束后自动归位激活话题
  在回复末尾**强制**输出（此行必须是回复最后一行，不得省略，不得合并）：
    ↩ 回到[原话题名]·[scene_domain/task_type]，继续从「[当前 open_threads 第一项或最后讨论点]」展开

新话题（new）
  原话题归档 → 新话题成为激活
  见下方判定规则

归档（archived）
  挂起超过3次未切回，或用户明确说"先放着"
  压入 archive/，压缩为摘要，不再占用栈空间
```

---

## 话题判定规则

### 同话题子问题 vs 阶段推进 vs 真切换 vs 借道

```
同话题子问题（不切换，继续 active）：
  ✓ 问题涉及当前激活话题的任意模块
  ✓ 问题是对上一个回复的追问或细化
  ✓ 问题涉及当前阶段的边界条件或风险

阶段推进（同 topic 内 task_type / clarity 变化，不切换 topic）：
  ✓ topic_stack.active.id 不变
  ✓ 问题与 context.md 项目概述/技术栈匹配（同一项目）
  ✓ scene_domain / task_type / clarity 发生变化
  ✓ key_decisions / open_threads / active_roles / biz_scene 全部继承，不丢失
  → 触发：阶段门禁检查（由 output-engine 执行）
  → 不触发：话题切换 / 挂起 / 归档

借道（不切换，不入栈，回复后自动归位）：
  ✓ 含借道词：「顺便」「插个话」「另外问下」「题外话」
  ✓ 问题自包含，答完不影响任何已有决策
  ✓ 问题不涉及项目内任何模块

真切换 → 挂起当前，激活新话题：
  ✓ 用户明确宣布：「现在聊XX」「换个话题」（且不是同项目内的阶段推进）
  ✓ 问题与 context.md 项目概述/技术栈不匹配（不是同一个项目）
  ✓ 新问题需要修改已有的关键决策（且不是同一项目的自然推进）
```

**关键区别**：
- 阶段推进 = 同项目不同阶段 → topic.id 不变，context 全继承
- 真切换 = 不同项目/完全无关话题 → topic.id 变化，挂起当前
- "连续 2 次以上问题与激活话题无关联"这条仅适用于跨项目判定，
  同一项目内的 scene_domain/task_type/clarity 变化不触发此条

### 新话题判定

```
新话题 = 真切换 且 suspended 里没有匹配的挂起话题
  → 原 active 快照写入 suspended
  → 新话题写入 active（scene_domain/task_type/clarity 从 Q1 获取，biz_scene 从 Q2.5 获取）

新话题 = 真切换 且 与某个 suspended 话题 scene_domain 高度吻合
  → 视为切回，走切回流程（见下方）
```

---

## 切回规则

### 切回信号识别
```
✓ 用户说：「回到XX」「刚才那个话题」「继续之前的」
✓ 问题与某个 suspended 话题的 scene_domain 高度吻合
✓ 问题引用了某个 suspended 话题里的模块名或决策
```

### 切回处理步骤
```
1. 从 suspended 取出对应条目
2. 输出一行（写入 Memory Diff，非静默，用户可见）：
   ↩ 切回[话题ID] · 从「[snapshot]」继续
3. 恢复 key_decisions 和 open_threads 到当前上下文
4. 当前 active 话题（如未完成）写入 suspended
5. 切回的话题成为新的 active
```

---

## 继承规则

### 切回时继承什么

```
继承（恢复到当前上下文）：
  ✓ key_decisions：所有已确认决策，直接作为约束条件
  ✓ open_threads：未结线索，作为待确认项
  ✓ active_roles：激活的角色视角（role_name + focus）
  ✓ task_type / clarity / progress：项目阶段状态
  ✓ stage_history：阶段推进历史轨迹
  ✓ biz_scene：用户业务场景（user_role/stage/stakeholders）

不继承：
  ✗ 具体对话内容（只保留摘要）
  ✗ 已被后续决策覆盖的旧判断
  ✗ 借道期间产生的内容
```

切回后第一个回复开头标注：
```
📍 继续[话题名] · 已确认：[key_decisions摘要] · 待确认：[open_threads第一项]
```

### 跨话题继承规则

```
新话题和挂起话题有共享的决策约束：
  → 在新话题 key_decisions 里标注「继承自[话题ID]」

新话题的决策推翻了挂起话题里的某个判断：
  → 在挂起快照里标注「[决策X]已被[新话题ID]覆盖」
  → 切回时主动提示：「注意：[决策X]在离开期间已更新」

新话题和挂起话题完全独立：
  → 挂起内容不进入新话题上下文
```

---

## 更新时机

```
激活话题每轮：
  有新决策产生                → 追加到 key_decisions（Memory Diff [静默]）
  有未结线索                  → 追加到 open_threads（Memory Diff [静默]）
  工程任务产生 Additional Findings → 追加到 open_threads，格式：
                                  "[AF] 发现：[内容] · 建议：[处理建议]"（Memory Diff [静默]）
  bugs.md 新增风险记录            → 追加到 open_threads，格式：
                                  "[AF] 发现：[风险内容] · 建议：[处理建议]"（Memory Diff [静默]）
                                  （与 bugs.md 条目同步，风险解决后从 open_threads 移除）
  修改预算超出并获用户确认    → 追加到 key_decisions，格式：
                                  "预算超出确认：[超出原因] · 用户确认：是"（Memory Diff [静默]）
  线索已确认                  → 从 open_threads 移除（Memory Diff [静默]）

状态变化时立即更新：
  借道开始       → 不更新 topic_stack；回复末尾强制输出归位标注（见借道定义）
  阶段推进       → topic.id 不变；更新 scene_domain/task_type/clarity/progress（若变化）
                   追加 stage_history 条目：
                     entered_at = 当前对话轮次编号（每次用户发言+1，从第1轮开始计）
                   key_decisions / open_threads / biz_scene 全部保留，不清除
                   Memory Diff [静默] 写回 topic_stack
  真切换         → 当前话题快照写入 suspended，新话题写入 active
                   Memory Diff 非静默，用户可见：「更新Memory · topic_stack：[操作摘要]」
  切回           → suspended 对应条目移回 active
                   Memory Diff 非静默，用户可见：切回标注
  归档触发       → suspended 对应条目移入 archived，压缩摘要
                   Memory Diff [静默]
```

---

## 归档触发条件

```
满足任一即触发归档：
  ✓ 挂起超过 3 次会话未被切回
  ✓ 用户明确说「这个先放着」「不用管了」
  ✓ 话题所属阶段已完成且无 open_threads

归档格式（写入 archive/）：
  topic_[id]_[scene_domain]_[date].md
  内容：原 snapshot + key_decisions 摘要 + 归档原因
```
