# context.md

## 项目概述
<!-- 一句话描述这个项目是什么 -->
<!-- 首次对话后由 AI 静默写入（Memory Diff [静默]），来源：用户描述 -->

## 技术栈
<!-- 语言 / 框架 / 数据库 / 部署方式 -->
<!-- 首次对话后由 AI 静默写入（Memory Diff [静默]），来源：问题内容推断，无法推断则留空 -->

## 依赖服务
<!-- 外部 API / 第三方服务 / 内部服务 -->

## 强制约定
所有非简单问题必须经过 output-engine skill 处理。
简单/非简单的判断标准在 output-engine/SKILL.md 快检规则里。
未经快检直接输出内容 = 裸奔，禁止。

## 已知坑位
<!-- 踩过的坑，避免重复 -->

---

## topic_stack

active:
  id: topic_001
  scene_domain:          # AI 自由填写，1-3词；技术实现类优先用白名单词：
                         # 后端开发 / 前端开发 / 数据库设计 / API设计 / 系统架构 / 运维部署 / 测试验证 / 全栈开发
                         # 其他领域示例：法律咨询 / 学术写作 / 市场策略 / 财务规划 / 产品运营
  task_type:             # 封闭值：analyze / design / execute / review
  clarity:               # 封闭值：low / mid / high
  progress:              # AI 自由填写，当前任务执行节点（非业务节点）
                         # 示例：方案评估中 / 实现进行中 / 上线前验证 / 立项调研中
  is_engineering:        # 封闭值：true / false（由 output-engine Q1 Step 2 推理写入）
  biz_scene:             # Q2.5 写入，每次对话首轮更新
    user_role:           # 发起问题的用户本人在组织里的职责（1-3词）
    stage:               # 用户所处的业务节点（非任务节点）
                         # 示例：立项期 / 方案评审中 / 开发进行中 / 上线前
    stakeholders:
      - role:            # 决策相关方角色
        focus:           # 该相关方最核心的关注点（1条）
  active_roles:          # Q3 写入，动态推断，每轮更新，最多3个
    - role_name:         # AI 推断的角色名，1-4词
      focus:             # 该角色在本问题中最关注的1-2个点
  key_decisions: []      # 已确认的关键决策
                         # 格式：选了X，放弃Y——原因Z
                         # 预算超出确认格式：[预算确认] 超出原因 · 用户确认：是
                         # 门禁覆盖格式：[门禁覆盖] 从[scene_A·task_A]跳至[scene_B·task_B] · 用户确认：是 · 风险自担
  open_threads: []       # 未结线索，待确认项
                         # 工程任务 Additional Findings 格式：[AF] 发现：XX · 建议：XX
                         # bugs.md 新增风险同步格式：[AF] 发现：风险内容 · 建议：处理建议（风险解决后移除）
  stage_history: []      # 话题维度的阶段推进历史（与 progress.md ## Stage History 分工：
                         #   此处记录话题内各阶段轨迹；progress.md 记录执行维度的阶段完成记录）
                         # 每项格式：{ scene_domain, task_type, clarity, entered_at, completed_at }
                         # entered_at = 当前对话轮次编号（每次用户发言+1，从第1轮开始计）
                         # completed_at = null 表示进行中，完成后填入轮次编号
                         # 示例：{ scene_domain: 系统架构, task_type: design, clarity: low, entered_at: 第3轮, completed_at: 第8轮 }

suspended: []            # 挂起的话题快照列表
                         # 每项格式：{ id, scene_domain, task_type, clarity, snapshot, suspended_at }

archived: []             # 已归档话题（详细内容在 archive/ 目录）
