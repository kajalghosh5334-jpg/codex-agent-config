# context.md

## 项目概述
<!-- 一句话描述这个项目是什么 -->

## 技术栈
<!-- 语言 / 框架 / 数据库 / 部署方式 -->

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
  scene:               # 技术架构 / 产品策略 / 工程执行 / 数据分析 / 运营流程
  stage:               # 立项期 / 开发期 / 上线前 / 复盘期
  roles: []            # 激活的角色视角列表
  key_decisions: []    # 已确认的关键决策，每条格式：选了X，放弃Y——原因Z
                       # 工程任务预算超出确认记录格式：[预算确认] 超出原因 · 用户确认：是
  open_threads: []     # 未结线索，待确认项
                       # 工程任务 Additional Findings 格式：[AF] 发现：XX · 建议：XX
  stage_history: []    # 阶段推进历史，每项格式：{ scene, stage, entered_at, completed_at }
  # 工程执行任务时，详细进度见 progress.md 任务状态机（Goal/Current Step/Risks）

suspended: []          # 挂起的话题快照列表

archived: []           # 已归档话题（详细内容在 archive/ 目录）
