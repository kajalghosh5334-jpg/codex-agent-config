# Codex Agent Config

一套完整的 Codex CLI AI Agent 行为规则配置体系，包含强制路由、输出引擎、话题记忆、工程执行规范、视觉渲染等模块。

## 快速开始

### 1. 放置 AGENTS.md
将 `AGENTS.md` 放在你要使用 Codex 的项目根目录下（或放在 $HOME 作为用户级全局规则）。

### 2. 安装 Skills
将 `skills/` 目录下的 5 个 SKILL.md 复制到 Codex 的 skills 目录：

```bash
cp skills/*_SKILL.md ~/.codex/skills/
```

注意：需要根据文件名去掉 `_SKILL` 后缀或保持原样，取决于你的 Codex 配置。参考你已有的 skill 命名规则。

### 3. 放置模板
将 `templates/agent_memory/` 下的三个模板复制到 Codex 模板目录：

```bash
cp templates/agent_memory/*.md ~/.codex/templates/agent_memory/
```

### 4. Skill 索引（可选）
`skill_index.md` 是 skill 的关键词匹配索引表。**这个文件需要根据你实际安装的 skills 来维护**——里面的 skill_id 必须与 `~/.codex/skills/` 目录下的实际 skill 文件对应。

如果你的 skills 内容和本仓库不同，请：
- 将 `skill_index.md` 内容替换为你自己的索引
- 或删除不存在的 skill_id 条目

## 体系结构

```
codex-agent-config/
├── AGENTS.md                          # 主体规则（快检 → 读 Memory → 阶段锁定 → 路由分发）
├── README.md                          # 本文件
├── skill_index.md                     # Skill 关键词索引（需按实际安装情况调整）
├── skills/
│   ├── output-engine_SKILL.md         # 五问流程引擎（Q1-Q5）
│   ├── engineering-mode_SKILL.md      # 工程执行规则集（is_engineering=true 时激活）
│   ├── topic-memory_SKILL.md          # 话题栈管理器
│   ├── v-channel-visualizer_SKILL.md  # V 通道视觉渲染引擎
│   └── render-datamodel_SKILL.md      # 数据模型 ERD 渲染
└── templates/
    └── agent_memory/
        ├── context.md                 # 项目上下文模板
        ├── progress.md                # 任务进度模板（含 Stage Deliverables）
        └── bugs.md                    # 问题风险模板
```

## 核心工作流

```
用户输入
  ↓
Step 1 · 快检（简单闲聊？→ 直接答）
  ↓
Step 2 · 读 Memory（agent_memory/ 三文件）
  ↓
Step 3 · 阶段锁定（P0 物料未完成？→ 门禁拦截）
  ↓
Step 4 · 路由 → output-engine 五问流程
              ├── Q1 场景识别 + 同项目判断
              ├── Q2 阶段 + 身份 + 目标
              ├── Q3 角色推断
              ├── Q4 影响评估
              └── Q5 汇总 → 输出决策
                  ├── is_engineering=true → engineering-mode
                  ├── 深度工程态 → v-channel-visualizer
                  └── 状态变化 → topic-memory 更新
```

## 关键概念

- **裸奔**：未经快检直接输出实质内容，禁止
- **阶段门禁**：当前阶段 P0 物料未完成时，禁止进入下一阶段
- **五问流程**：所有正式任务的统一入口
- **话题栈**：active / suspended / archived 三层话题管理
- **V 通道**：视觉渲染与文本路由并行，深度工程态强路由
- **工程模式**：代码执行时的额外安全规则（影响分析、修改预算、注意力预算等）

## 注意事项

- 这套配置是为 Codex CLI 设计的，部分 skill 引用需要你本地已安装对应 skill
- `skill_index.md` 中的 skill 列表是我个人的配置，你需要根据 `~/.codex/skills/` 实际内容调整
- 首次使用请确保 `agent_memory/` 目录在项目根目录存在（AGENTS.md 会自动提示创建）
