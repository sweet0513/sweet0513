# Skill（技能 / Agent Skill）

## 0. 元信息

- **概念名称**：Skill（Agent Skill / 技能；本项目语境即 WorkBuddy 项目级 Skill）
- **语境领域**：AI Agent 工程 / 可复用的任务知识
- **一句话定位**：把"做某类任务的程序性知识"（操作步骤 + 脚本 + 参考材料）打包成**可被 Agent 发现并按需加载**的文件包，让通用 Agent 在专项任务上稳定发挥
- **假设**：本文按 Anthropic 提出的 Agent Skills 开放范式与 WorkBuddy/CodeBuddy 的实现来写；这与"写一段一次性 prompt"有本质区别（见 §4）。

---

## 1. 概念的个人解释

**一句话定义**：Skill 是一个带元数据的文件夹——里面装着教 Agent 干一类活的"说明书 + 工具 + 参考资料"，Agent 只在遇到匹配任务时才打开它。

**类比**：Skill = **给新员工（Agent）做的岗位速成手册 / 入职培训包**。
- 员工很聪明（模型很强），但不知道你公司"报销单必须贴发票、金额超 500 元要总监批、系统叫费控宝"。
- 手册（SKILL.md）一页一页写着：什么时候用本手册、按什么步骤操作、注意什么红线；再附上常用表格模板（reference 文件）和现成小工具（scripts）。
- 新员工平时不带整本手册到处跑（浪费），只在**接到报销类任务时**翻到对应章节照做。干得多了，还能把新经验写回手册，下个人/下次更快。

**"它到底解决什么问题"**：模型的能力像"聪明但没有行业常识的新人"。用 prompt 口头叮嘱，信息一多就会"忘"（占用上下文、说多了还互相干扰）。Skill 把程序性知识**外部化、模块化、按需加载**：
- 写一次、处处用（可移植）；
- 不用时零负担（只有 name + description 常驻系统提示）；
- 可多人共享、可版本管理（放 Git 里），把个人经验沉淀成团队资产。

---

## 2. 核心机制或组成

**物理结构**：一个 Skill = 一个目录，核心文件是 `SKILL.md`，可选带 `scripts/`（脚本）、`references/`（参考资料）、`assets/`（静态资源）。

```text
my-skill/
├── SKILL.md        # 元数据(YAML frontmatter) + 操作指令（核心）
├── scripts/        # 可选：可执行脚本（如 python 处理文档）
├── references/     # 可选：详细参考资料（大段知识放这里）
└── assets/         # 可选：模板、图片等静态资源
```

**SKILL.md 的必需元数据**（frontmatter，标准仅要求两个字段）：
- `name`：技能名（小写、连字符分隔，唯一标识）；
- `description`：这个技能**做什么 + 什么时候用**。它决定 Agent 会不会在任务中"想到"调用它。

**核心机制：渐进式披露（progressive disclosure）**——信息分三层、按需加载，控制上下文开销：

| 层级 | 内容 | 何时进入上下文 |
| --- | --- | --- |
| 1 | 每个已装 Skill 的 `name` + `description` | 启动时预载入系统提示，供 Agent 判断"该不该用" |
| 2 | 命中的 `SKILL.md` 正文指令 | 任务与 description 匹配时，完整加载执行 |
| 3 | `references/`、`scripts/`、`assets/` 内容 | 执行中按需读取 / 运行脚本 |

这样**可以挂载很多技能，但上下文只被"正在用的那个"占用**——数量多、负担小。

**特性**（官方总结）：
- **可组合（Composable）**：多个 Skill 可叠加，由 Agent 协调先后；
- **可移植（Portable）**：同一格式跨产品（Claude 应用 / Claude Code / API / 各家支持 Agent Skills 的客户端）；
- **高效（Efficient）**：按需加载，不拖慢响应；
- **强大（Powerful）**：可含可执行代码，把"生成代码让模型执行"替代"让模型凭空吐结果"。

**与周边的关系**：
- Skill 教 Agent"**怎么做成一类事**"（步骤与程序知识）；
- Tool / MCP 给 Agent"**能调用什么外部能力**"（API、数据库等连接器）；
- Context 是"当前这次任务放什么信息"（见 `context.md`）；
- Skill 本身也是**一组被管理好的上下文**——它把可复用的指令沉淀下来，需要时再注入。

---

## 3. 一个具体应用场景

**场景（来自官方文档技能）**：让 Claude "把这个 PDF 里的表单字段提取出来 / 把报表导出成 Excel"。

**背景**：模型"读得懂" PDF，但"操作" PDF（填表、提取字段）不是它直接擅长的事；让它现写处理脚本每次都不稳定。

**怎么做**：Anthropic 团队给模型装了一个 **PDF Skill**——SKILL.md 里写明"遇到 PDF 表单处理用我"，references 里有字段映射规范，scripts/ 里有现成、验证过的 Python 处理脚本。
1. 用户说"提取这份 PDF 的表单字段"；
2. Agent 启动时读过 PDF Skill 的 description，判断任务匹配 → 加载该 Skill 的完整指令；
3. Agent 按指令跑 `scripts/` 里的脚本处理文件，再把结果组织成答案。

**为什么这样有效**：用**确定性的脚本**做机器擅长的事、用模型做判断和沟通——比"让模型裸写处理逻辑"又快又稳；且同一套 PDF 技能在所有支持 Agent Skills 的客户端通用（可移植），团队可把它放进 Git 共享维护。

**本仓库的实例**：`.workbuddy/skills/concept-explainer/` 就是一个项目级 Skill——它把"生成概念学习资料"的完整方法论（适用场景、输入、步骤、输出结构、自检清单）固化下来；任何人（或未来的 AI 会话）只要说要学一个新概念，就调用它产出符合统一结构、可核查的资料（本目录下三份资料即用它所述结构生成）。

---

## 4. 容易混淆的问题 / 使用边界

1. **Skill ≠ 一次性提示词（prompt）**
   一次性 prompt 每次都要重写、无法跨会话复用；Skill 是可寻址、可发现、可版本管理的**资产**。描述句如果写不好，Agent 甚至不会主动调用它。
2. **Skill ≠ Slash Command（斜杠命令）**
   按 CodeBuddy/WorkBuddy 官方文档的区分：Slash Command 侧重"由用户显式敲 `/xxx` 触发、可带参数"，偏交互快捷键；Skill 侧重"由 Agent 依据任务**自动发现并加载**"的程序知识包。两者目录/机制不同，不要混用。
3. **Skill ≠ MCP / Tool**
   MCP 解决"**连上外部系统**"（工具连接协议）；Skill 解决"**知道这类事怎么做**"（步骤化知识）。实践上两者互补：Skill 可指示 Agent"调哪个 MCP 工具、按什么顺序、怎么校验结果"。
4. **使用边界：来源信任与安全**
   Skill 可以携带**可执行代码**，等于给 Agent 更多权限——恶意/来源不明的 Skill 可能诱导数据外泄或执行危险操作。官方明确警告：只装可信来源的 Skill，陌生来源要审计后再用。
5. **Skill 不是银弹**
   Skill 好 ≠ 任务自动做好：内部步骤写不清、描述写太泛（激活不准）、把大量原文堆进 SKILL.md（上下文臃肿）都会让技能失效。它把"知识沉淀"这件事做好，但不替代良好的任务设计。

---

## 5. 资料来源（可核查）

| # | 来源（机构 / 标题） | URL | 引用内容 | 核验 |
| --- | --- | --- | --- | --- |
| 1 | Anthropic 工程博客：《Equipping agents for the real world with Agent Skills》 | https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills | Skill 定义、SKILL.md 结构、渐进式披露、安全警告 | 200，2026-09-04 |
| 2 | Claude 官方博客：《Introducing Agent Skills》 | https://claude.com/blog/skills | 四大特性（可组合/可移植/高效/强大）与产品支持 | 200，2026-09-04 |
| 3 | GitHub：anthropics/skills（官方技能仓库与 Agent Skills 规范） | https://github.com/anthropics/skills | 示例技能库、`./spec` 规范目录、SKILL.md 模板 | 200，2026-09-04 |
| 4 | Agent Skills 开放标准站点 | https://agentskills.io/specification | 开放规范原文 | 站点由官方仓库指引，2026-09-04 标注 |
| 5 | CodeBuddy/WorkBuddy 官方文档：《Skills 技能系统》 | https://cnb.cool/codebuddy/codebuddy-code/-/git/raw/main/docs/skills.md | WorkBuddy 视角：SKILL.md 格式、与 Slash Command 的区别、项目级技能目录 | 文档索引中登记，2026-09-04 标注 |

**链接核验记录**：于 **2026-09-04** 验证。#1–#3 均返回 200，可直接访问。#4、#5 由官方仓库/官方文档索引指引得到，为长期维护页面；其中 #5 为 CodeBuddy 开源文档仓库中的 skills 文档原始地址，若需阅读建议打开其 Blob/网页版（`https://cnb.cool/codebuddy/codebuddy-code/-/blob/main/docs/skills.md`）。

> 提示：Agent Skills 于 2025-10-16 发布、2025-12-18 宣布开放标准化，生态（多客户端支持）仍在快速演进，规范细节以 agentskills.io 最新版为准。
