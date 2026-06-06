---
name: v-channel-visualizer
description: |
  V 通道渲染引擎。由 output-engine 门卫判定触发后调用，不直接面向用户。
  负责渲染决策（图类型路由）和渲染规范（SVG/Mermaid/Chart.js/D3/HTML stepper）。
  门卫判定已在 output-engine 完成，本文件只处理「如何渲染」，不处理「是否渲染」。
---

# V-Channel Visualizer

## 定位

由 `output-engine` 门卫判定通过后调用：`skill: v-channel-visualizer`。
不输出中间过程，只输出最终视觉渲染指令，嵌入 Q5 输出结构对应位置。

---

## 渲染决策

### 图类型路由表

```
数据模型 / 数据库设计   → Mermaid erDiagram
                        → 节点 > 8 时拆为多张 erDiagram

技术架构 / 系统设计     → Structural SVG
                        → 节点 > 8 按模块拆图；层级 > 4 改用 LR 布局或拆图

教学 / 解释类           → Illustrative SVG（空间隐喻，直觉优先）
                        → 循环/周期类叠加 HTML stepper

流程 / 状态机           → Flowchart SVG
                        → 状态 > 5 改用 HTML stepper

数据对比 / 图表         → Chart.js（柱状/折线/饼图）
                        → ≤ 12 类别禁止自动跳过标签

UI / 界面 mockup        → HTML widget（嵌入 card 容器）

地理分布                → D3 choropleth
```

### 教学场景特殊路由

```
讲解 LLM / Transformer      → Illustrative SVG（token行 + layer slabs + attention threads）
讲解 TCP / 网络协议          → Flowchart SVG + Illustrative SVG 组合
讲解 事件循环 / 生命周期     → HTML stepper（禁止环形图）
讲解 架构对比（微服务 vs 单体）→ Structural SVG（并列对比布局）
```

---

## 防溢出规则（渲染前强制校验，不可覆盖）

| 规则 | 限制值 | 违反时处理 |
|---|---|---|
| 单图节点数 | ≤ 8 | 拆为多图，每图标注「图 1/N」 |
| 纵向层级（TD） | ≤ 4 层 | 改用 LR 布局；仍超出则拆图 |
| 单节点文本 | ≤ 30 字 | 截断并在图下方附完整文本 |
| Mermaid 色系 | 强制注入 unified head | 所有 Mermaid 图必须注入，无例外 |

---

## Mermaid 统一色系 Head

所有 Mermaid 图必须包含以下 head，不可省略，不可修改：

```
%%{init: {
  'theme': 'base',
  'themeVariables': {
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

## SVG / HTML 渲染规范

### 配色体系

场景 → 梯度路由：

```
技术架构 / 系统图     → c-blue, c-purple
数据模型 / 状态机     → c-teal, c-green
业务 / 产品           → c-coral, c-pink
风险 / 警告           → c-amber, c-red
中性背景 / 辅助       → c-gray
```

亮色模式（默认）：50 fill + 600 stroke + 800 标题 / 600 副标题

### SVG 布局规则

```
viewBox 固定 0 0 680 H，H = 最底元素坐标 + 20px
安全区域：x=40 至 x=640，y=40 至 y=(H-40)
背景透明
节点间距 ≥ 60px，节点内 padding ≥ 24px
同一内容类型节点高度一致（单行 44px / 双行 56px）
0.5px 描边用于图表边框和连线
所有连接线 <path> 或 <polyline> 必须设置 fill="none"
```

### HTML widget 规则

```
无 <!DOCTYPE>、<html>、<head>、<body> 标签，直接输出内容片段
无深色/彩色外容器背景（透明，宿主 UI 提供背景）
标题 15px/500，副标题 14px/500，正文 13px/400
圆角优先 var(--border-radius-lg)（12px）
图表高度：300px 默认；水平柱状图 (bar数量 × 40 + 80)px
```

---

## 工程执行模式附加规则

```
is_engineering = true 时：
  单次渲染图数量 ≤ 3
  超出时停止，说明原因，等待确认
  渲染任务不干扰主任务目标
  渲染期间禁止处理未授权问题
```

---

## 禁止行为

```
禁止因「内容不够复杂」跳过教学场景的视觉触发
禁止因「图太大」违反防溢出规则（必须拆分）
禁止用 Mermaid 默认色系替代统一色系 head
禁止将渲染决策过程输出给用户
```
