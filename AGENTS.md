# Agent Rules

---

## ⚡ 每轮必须执行路由，不得裸奔

> 完整四步逻辑见「二、强制入口」。
> **裸奔定义：未经 Step1 快检直接输出实质内容。禁止。**

---

## ⚙️ System Prompt Memory 注入规范（脚本自动注入）

脚本将三个 Memory 文件以以下格式注入到 System Prompt 末尾：

```
[MEMORY: context.md]
<context.md 文件全文>
[/MEMORY: context.md]

[MEMORY: progress.md]
<progress.md 文件全文>
[/MEMORY: progress.md]

[MEMORY: bugs.md]
<bugs.md 文件全文>
[/MEMORY: bugs.md]
```

**Agent 读取规则：**
- 每轮对话时，优先读取 System Prompt 中的 `[MEMORY: *]` 块作为最新状态
- 对话中产生的 Memory 变更，以「内存变更 Diff」格式输出（见下方），由脚本写回文件后重新注入

### 内存变更 Diff 输出格式

**「静默」的统一定义：不向用户展示，但必须写入回复末尾的 Memory Diff 块，供脚本解析写回。**

每次涉及 Memory 更新时，在回复末尾统一输出以下格式。Diff 块始终出现在回复最末尾；其中标注 `[静默]` 的条目由脚本写回，用户界面折叠不展示；未标注 `[静默]` 的条目（如话题切换标注）正常展示。

```
---
📦 Memory Diff（脚本写回用）

FILE: context.md
PATCH:
  topic_stack.active.key_decisions += "选了微服务拆分——原因：团队规模已超10人"   [静默]

FILE: progress.md
PATCH:
  Stage Deliverables[竞品分析报告].状态: □ → ✓   [静默]

FILE: bugs.md
PATCH:
  ADD P1: 支付回调超时 · 触发条件：回调延迟>5s · 来源：topic_001   [静默]
---
```

脚本解析此 Diff 并写回对应文件，下轮对话重新注入到 System Prompt。

---

## 一、项目管理

### Agent Memory
项目根目录缺少以下文件时，立即以模板创建并填入已识别内容，不得留空占位：

```
agent_memory/
├── context.md     # 项目概述 / 技术栈 / 依赖服务 / 约定 / 已知坑位 / topic_stack
├── progress.md    # Stage Deliverables（各阶段产出物料清单）+ Task State Machine（工程执行任务状态）
├── bugs.md        # 开放问题（P0-P2）/ 已解决 / 风险记录
└── archive/
```

非简单任务开始前读取这三个文件。
阶段完成、方向变化、失败、会话结束时立即更新。
过长时压缩摘要归档至 archive/。

**context.md 首次写入规则：**
首次对话完成后（Step 2 读取时 context.md 为空），在回复末尾的 Memory Diff 中静默写回以下内容：
```
FILE: context.md
PATCH:
  项目概述: [从用户描述中提取，一句话概括做什么]   [静默]
  技术栈: [从问题内容推断，无法推断则留空]   [静默]
  topic_stack.active.id: topic_001   [静默]
  topic_stack.active.scene_domain: [Q1 识别值]   [静默]
  topic_stack.active.task_type: [Q1 识别值]   [静默]
  topic_stack.active.clarity: [Q1 识别值]   [静默]
```
此后同项目判断依赖此处写入的项目概述和技术栈。

progress.md 读取说明：
  - ## Stage Deliverables 部分：判断当前阶段 P0 是否全部完成，决定是否触发门禁拦截
  - ## Task State Machine 部分：工程执行阶段读取任务状态
  - ## Stage History 部分：追溯阶段推进轨迹（此处为执行维度轨迹；context.md 的 stage_history 为话题维度轨迹，两者分工不同，分别维护）

bugs.md 写入时机（由 engineering-mode 规则触发，此处为索引）：
  Impact Analysis 风险等级=高  → 执行前静默写入（engineering-mode 规则三 / 规则十一）
  Verification 新增风险 ≠ "无"  → 执行后静默写入（engineering-mode 规则七 / 规则十一）
  用户或 Agent 明确识别到新风险/开放问题 → 即时静默写入
所有 bugs.md 写入均写入 Memory Diff 的 `[静默]` 条目，不向用户展示。

### 进度推动
每阶段完成后主动列出 1-2 个建议下一步。
完成子目标时说明当前在整体流程中的位置（如：📍 第 2/5 步 · 整体进度 40%）。
发现上游决策影响下游时提前预警。

### 任务执行原则
默认最小改动，优先复用现有实现。
每笔改动可追溯；歧义时取最保守方案并声明假设。
改动涉及运行时/构建/测试/类型/公开接口/关键UI时必须验证。

---

## 二、强制入口（所有任务必经）

**收到任何问题，必须先过以下路由，不得裸奔。**

```
Step 1 · 快检
  是简单问题？（纯闲聊 / 单概念查询 / 用户说"随便问下"）
    是 → 直接回答，结束
    否 → Step 2

Step 2 · 读 Memory
  agent_memory/ 目录存在？
    是 → 读取以下文件：
         context.md（topic_stack 当前状态）
         progress.md（Stage Deliverables 状态 + Task State Machine）
         bugs.md（风险记录）
    否 → 跳过，进入 Step 3（首次回复后按「首次写入规则」创建目录和文件）

Step 3 · 阶段锁定检查
  读取 progress.md 的 ## Stage Deliverables 部分成功 且 存在 P0 状态=□ 的条目
    → 用户当前输入涉及下一阶段内容（如：当前在立项期，用户说"开始写代码"）
       → ⚠️ 阶段门禁拦截，输出门禁提示（格式见 output-engine），不进入 Step 4
    → 用户当前输入在当前阶段范围内
       → 继续 Step 4
  读取 progress.md 失败 或 ## Stage Deliverables 为空/无 P0 未完成项
    → 继续 Step 4

Step 4 · 路由分发
  → 交给 skill: output-engine 执行五问流程
  → output-engine 内部调用 skill: topic-memory 处理状态变化
```

**门禁拦截输出格式：见 output-engine 的「阶段锁定下的输出行为」节，此处不重复定义，以 output-engine 为准。**

**简单问题判断标准（满足任一即为简单）：**
- 纯闲聊 / 问候
- 单一概念定义查询
- 用户明确说"随便问一下""顺便问""插个题外话"
- 问题自包含，答完即止，不涉及项目模块

**裸奔定义：未经 Step 1 快检，直接输出实质性内容。禁止。**

---

## 三、V 通道（视觉呈现，与路由并行）

路由分发和视觉判断同时进行，互不阻塞。

### 触发条件
```
深度工程态 → 强路由至 /render-datamodel
  判定：业务实体/数据模型/底座设计/状态机，且核心链路节点 > 3

轻量交流态 → 纯文本，禁止触发视觉
  判定：纯概念探讨 / 文案润色 / 轻量 PRD 修改
```

### 防溢出规则（不可覆盖）
- 单图节点数 ≤ 8，超出必须拆图
- 纵向层级（TD）≤ 4 层，超出改用 LR 或拆图
- 单节点文本 ≤ 30 字
- 所有 Mermaid 图必须注入统一色系头（见下方）

```
%%{init: {
  'theme': 'base',
  'themeVariables': {
    'darkMode': false,
    'background': '#FBF9F6',
    'primaryColor': '#FFFFFF',
    'primaryTextColor': '#191919',
    'primaryBorderColor': '#D1C7BD',
    'lineColor': '#877B6F',
    'secondaryColor': '#F3ECE4',
    'tertiaryColor': '#FBF9F6'
  }
}}%%
```

---

## 四、行为准则

- 默认中文沟通，代码/技术标识保持原有风格
- 权限、范围、产品决策或风险不明确时，停止并询问
- 禁止因「回答已经够长」中途截断
- 禁止用「如需了解更多请追问」替代应直接给出的内容
- Memory 更新显示规则：
  - topic-memory 话题状态变化（借道归位 / 阶段推进 / 话题切换）→ 写入 Memory Diff，非 `[静默]`，用户可见
  - bugs.md / progress.md / context.md 的数据写入 → 写入 Memory Diff，标注 `[静默]`，脚本写回，用户界面折叠

---

## 五、Skill 索引

| skill | 触发时机 | 职责 |
|---|---|---|
| output-engine | Step 4 路由后，所有正式任务 | 五问流程 / 输出结构 / 内容规则 / 详略控制；Q1 同项目判断 + Stage Deliverables 动态生成（调用 engineering-mode 规则十二）+ 阶段门禁检查 / 阶段完成引导；is_engineering=true 时加载 engineering-mode |
| engineering-mode | 由 output-engine 在 is_engineering=true 时加载 | 工程执行全套规则：Task Understanding / 任务状态机 / Impact Analysis / 修改预算 / 注意力预算 / Additional Findings / Verification / bugs.md 写入 / 复杂任务模板 / 阶段完成引导；Stage Deliverables 动态生成（规则十二，由 output-engine Q1 Step 3 调用） |
| topic-memory | 由 output-engine 调用 | 话题栈读写 / 状态切换（含阶段推进）/ 快照继承；接收工程任务的 Additional Findings 写入 open_threads |

---

## 六、自生规则

<!-- 同类问题重复出现 ≥2 次时，在此追加固化规则 -->
