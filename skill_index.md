# Skill 索引表

> ⚠️ 本文件中的 skill 列表是维护者的个人配置。如果你 fork 或使用本仓库，请根据你 `~/.codex/skills/` 目录下实际安装的 skill 来调整此索引表。
>
> **调整方法**：
> 1. 运行 `ls ~/.codex/skills/` 查看已安装的 skill
> 2. 删除此表中不存在的 skill_id 行
> 3. 补充你独有的 skill_id（格式：`| skill_id | 能力描述 | 触发关键词 |`）
>
> 当前版本总计记录了维护者的 skill 配置，仅供参考。

---

## 核心系统 Skills（必须安装，本仓库提供）
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| output-engine | 五问流程 + 输出结构 + 阶段门禁 | 非闲聊全场景 |
| engineering-mode | 工程执行规则（is_engineering=true 时由 output-engine 加载） | 写代码,改代码,执行 |
| topic-memory | 话题栈读写 + 状态切换 | 自动调用 |
| v-channel-visualizer | V 通道视觉渲染（门卫判定 + 渲染决策） | 数据模型,架构图,可视化 |
| render-datamodel | 数据模型 ERD + 交互 Widget 生成 | ERD,数据模型,表结构 |

---

## 构建与开发
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| build-agent | 代码实现、文件修改、UI/UX开发 | 实现,开发,写代码,修改 |
| plan-agent | 架构规划、需求拆解、API调研 | 架构,规划,拆解,设计方案 |
| qa-agent | 测试、安全审查、性能基准、E2E | 测试,安全,性能,E2E |
| ship-agent | 交付、文档、部署验证、合流 | 部署,交付,合流,上线 |
| design-agent | 设计规范、UI/UX系统、色彩字体 | 设计规范,UI,色彩,字体 |

## 审查与审计
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| review | PR审查、SQL安全、LLM信任边界 | 审查,review,SQL安全,PR |
| cso | 首席安全官模式，安全审计 | 安全审计,权限,漏洞,CSO |
| design-review | 设计师视角QA | 设计审查,视觉QA |
| devex-review | 开发者体验审计 | 开发体验,DX,易用性 |
| plan-ceo-review | CEO视角计划审查 | 战略审查,CEO视角,商业判断 |
| plan-eng-review | 工程视角计划审查 | 技术评审,工程可行性 |
| plan-design-review | 设计视角计划审查 | 设计评审,体验评估 |

## 浏览器与搜索
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| browse | 无头浏览器QA测试 | 浏览器测试,无头,自动化 |
| anysearch | 实时搜索引擎 | 搜索,查资料,最新,实时 |
| agent-reach-ops | 17个平台搜索与阅读 | 多平台,信息收集,调研 |
| scrape | 网页数据抓取 | 抓取,爬取,数据提取 |
| defuddle-build | 从网页提取干净markdown | 网页清洗,提取正文 |

## 工具与实用
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| careful | 安全约束模式 | 谨慎,高风险,不可逆 |
| guard | 防护约束 | 防护,guard,保护 |
| health | 代码质量仪表盘 | 代码质量,健康度,技术债 |
| benchmark | 性能回归检测 | 性能,基准,回归 |
| investigate | 系统调试与根因分析 | 调试,排查,根因,debug |
| codex | Codex CLI封装，代码审查 | codex,代码审查 |

## 设计与前端
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| frontend-design-build | 生产级前端界面 | 前端,界面,组件,生产级 |
| design-consultation | 设计咨询 | 设计咨询,方向,风格选择 |
| design-shotgun | 多设计方案生成 | 多方案,发散,设计选项 |
| design-html | 设计终稿 HTML/CSS | 设计稿,HTML,CSS,还原 |
| ui-ux-pro-max-build | 50+风格，161调色板 | 多风格,调色板,视觉方案 |

## 部署与发布
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| ship | 发布流程，版本号，CHANGELOG | 发版,release,推送GitHub,上传,推代码,PR |
| land-and-deploy | 合入与部署 | 合入,部署,主干 |
| canary | 部署后金丝雀监控 | 金丝雀,灰度,监控 |
| setup-deploy | 部署配置 | 部署配置,环境初始化 |

## 文档与知识
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| document-release | 发布后文档更新 | 文档更新,发布说明 |
| learn | 项目知识管理 | 知识库,项目经验,沉淀 |
| context-save | 上下文保存 | 保存进度,上下文,中断 |
| context-restore | 上下文恢复 | 恢复进度,继续上次 |
| make-pdf | Markdown转PDF | PDF,转换,打印 |
| markitdown-skill-build | 任意文件转Markdown | 转markdown,文件转换 |

## Obsidian 生态
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| obsidian-cli-ops | Obsidian CLI，读写搜索笔记/标签 | Obsidian,笔记,标签 |
| obsidian-markdown-build | Obsidian风格Markdown | wikilinks,嵌入,标注,属性 |
| obsidian-bases-build | Obsidian Bases编辑 | Bases,视图,筛选,公式 |
| json-canvas-build | JSON Canvas文件编辑 | canvas,节点,可视化图 |
| sync-gbrain | 保持gbrain与代码库同步 | gbrain,同步 |
| setup-gbrain | 安装gbrain CLI + 数据库初始化 | gbrain,安装,初始化 |

## 测试
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| qa | 系统QA测试 + 迭代修bug | QA,测试,bug修复 |
| qa-only | 仅报告型QA | QA报告,只测试 |
| playwright-best-practices-qa | Playwright最佳实践 | Playwright,E2E,Page Object |

## 规划与创意
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| brainstorming-plan | 创意工作前必用 | 头脑风暴,创意,发散 |
| office-hours | YC办公时间模式 | 商业判断,YC,投资人视角 |
| karpathy-guidelines-plan | 减少LLM常见编码错误 | LLM编码,最佳实践 |
| retro | 周度工程回顾 | 复盘,回顾,retro |
| multi-agent | 多agent任务简报 | 多agent,并行,协作 |
| using-superpowers-ops | 技能使用入门 | 入门,怎么用,技能介绍 |

## 图像
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| imagegen | AI生成/编辑位图 | 生成图片,图像,AI绘图 |

## 文档查询
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| openai-docs | OpenAI产品/API最新官方文档 | OpenAI,API文档,官方文档 |

## 技能管理
| skill_id | 能力描述 | 触发关键词 |
|----------|----------|------------|
| skill-creator | 技能创建指南 | 新建skill,创建技能 |
| skill-creator-ops | 创建/修改/测量技能性能 | skill管理,修改技能 |
| skill-installer | 从列表或GitHub安装技能 | 安装技能,skill安装 |
| find-skills-ops | 帮用户发现和安装技能 | 找技能,有什么skill |
