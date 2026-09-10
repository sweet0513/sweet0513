# Concept Learning Workspace

一个用于**沉淀 AI 概念学习资料**的小仓库：里面放了一个自己设计的项目级 Skill，以及由它产出的概念学习笔记。

核心想法很简单：学一个新概念不应该每次从零写提示词。**把"怎么整理一份合格的学习资料"这件事本身沉淀成 Skill**，之后换任何一个新概念，都走同一套流程、产出同一种结构、经过同一轮自检。

---

## 一、仓库用途

1. **沉淀方法**：把概念学习的加工流程（检索 → 核验 → 用自己的话解释 → 出题 → 自证来源）写成可复用的 Skill。
2. **沉淀结果**：为每个概念产出一份结构统一、来源可核查、可以拿来考自己的 Markdown 资料。
3. **记录关系**：用一个独立文件说明概念之间的相互影响，而不是把概念孤立地放成一堆笔记。
4. **留下核查痕迹**：每份资料都带访问日期、来源支撑说明和存疑清单，方便日后判断"这句话还能不能信"。

---

## 二、目录结构

```
.
├── README.md                                  # 本文件
├── .gitignore                                 # 排除密钥、个人信息与环境文件
├── .workbuddy/
│   └── skills/
│       └── concept-learning/
│           └── SKILL.md                       # ★ 项目级 Skill
└── learning-notes/
    ├── INDEX.md                               # 概念索引（Skill 自动维护）
    ├── agent.md                               # 概念资料 ①：Agent
    ├── llm-context.md                         # 概念资料 ②：大模型的上下文
    ├── skill.md                               # 概念资料 ③：Skill
    └── concept-relationship.md                # 概念关系说明（含 Mermaid 图）
```

---

## 三、Skill 的存放路径

```
.workbuddy/skills/concept-learning/SKILL.md
```

Skill 名称：**`concept-learning`**，版本 `1.0.0`。

`SKILL.md` 顶部的 YAML 元数据是触发依据，`description` 同时说明了"它做什么"和"什么时候用"：

```yaml
name: concept-learning
description: 把任意一个新概念（例如 Agent、大模型上下文、Skill、RAG、MCP、向量检索）转化为一份可复习、可自测、来源可核查的结构化学习资料。当用户说"帮我学一下某个概念""整理一份概念学习资料""把这个概念写成学习笔记""我没搞懂 X，帮我拆开讲"时使用。……
```

这个 Skill **不是为本次三个概念写的一次性提示词**。它接收的是"一个概念名称"，走的是与概念无关的固定流程，所以换 `RAG`、`MCP`、`向量检索` 任何一个新概念都能用。

Skill 内部明确了六件事：

| 要求项 | 在 SKILL.md 中的位置 |
|---|---|
| 适用场景 / 不适用场景 | 第一节 |
| 输入信息（必需项与缺省处理） | 第二节 |
| 生成步骤 | 第三节（第 0–8 步） |
| 输出结构 | 第五节（固定的 11 节模板） |
| 资料来源要求 | 第四节（硬性规则 6 条） |
| 自检要求 | 第六节（提交前 11 条检查清单） |

其中"资料来源要求"里写了几条硬规则，是整个 Skill 最重要的部分：**不许编造链接；必须有可达性检查记录；每条来源要说明它支撑了哪一句结论；无法核实的内容必须进"存疑清单"而不是混进正文。**

---

## 四、如何在 WorkBuddy 中调用它

### 4.1 发现（不需要手动注册）

WorkBuddy 的项目级 Skill 遵循 `<项目目录>/.workbuddy/skills/<skill-name>/SKILL.md` 的约定。在这个目录下打开任务时，WorkBuddy 会自动发现它——启动时只把 `name` 和 `description` 加载进系统提示（约 100 token），正文在你真正用到时才读进来。这就是渐进式披露：**装着不等于用着，不用不花钱。**

### 4.2 三种调用方式

**方式一：自然语言触发（推荐）**

在对话框直接说需求，不必提"Skill"这个词：

```
帮我学一下 RAG 这个概念，整理成一份学习资料
```

```
把 MCP 整理成 learning-notes 下的一份概念笔记
```

模型会根据 `description` 与请求的匹配情况自动加载这个 Skill 并按其步骤执行。

**方式二：显式调用**

在输入框用 `/` 唤起技能列表（输入框提示为「`@` 引用对话文件，`/` 调用技能与指令」），选择 `concept-learning` 后补上概念名称。

**方式三：修订已有资料**

```
更新一下 learning-notes/agent.md，Anthropic 那边出了新文章
```

按 SKILL.md 第二节的规定，同名概念**不会新建文件**，而是在原文件上修订并追加"更新记录"。

### 4.3 如果没被触发

在「技能 → 已安装」里确认 `concept-learning` 处于启用状态（关闭的技能不参与自动调用），并确认当前任务的工作目录就是本仓库根目录。如果请求本身是"一句话能答完"的问题，Skill 会刻意不触发、直接口头回答——这是设计好的行为，不是故障。

### 4.4 产出落点

默认写入 `learning-notes/<slug>.md`，并在 `learning-notes/INDEX.md` 中登记一行。也可以指定别的路径。

### 4.5 复用示例（换概念不改 Skill）

| 说法 | 预期产出 |
|---|---|
| 帮我学一下向量检索 | `learning-notes/vector-search.md` |
| 整理一份 MCP 的学习资料 | `learning-notes/mcp.md` |
| 我没搞懂 RAG | 一句话口头解释 + 询问是否需要沉淀成资料 |

---

## 五、已生成的学习资料

| # | 概念 | 文件 | 图解 | 学术来源 | 自测题 | 内容要点 |
|---|---|---|---|---|---|---|
| ① | **Agent（智能体）** | [`learning-notes/agent.md`](learning-notes/agent.md) | 2 | 6 篇 | 7（★2/★★3/★★★2） | Thought → Action → Observation 循环；CoALA 的记忆模块划分（含**程序性记忆**）；**"下一步由代码还是模型决定"这条判据**；Reflexion 的反思机制；为什么不总是该用 Agent |
| ② | **大模型的上下文** | [`learning-notes/llm-context.md`](learning-notes/llm-context.md) | 3 | 7 篇 | 7（★2/★★3/★★★2） | 自注意力的成对关系；**Lost in the Middle 的 U 型曲线**；长度本身伤推理（ACL 2024）；**标称窗口 ≠ 有效窗口**（COLM 2024）；RoPE 的距离衰减；压缩 / 结构化笔记 / 子 Agent |
| ③ | **Skill（智能体技能）** | [`learning-notes/skill.md`](learning-notes/skill.md) | 2 | 4 篇 | 7（★2/★★2/★★★3） | **这个概念的学术位置**（CoALA 程序性记忆 + Voyager 技能库 + LATM 成本摊销 + MemGPT 虚拟内存）；目录 + `SKILL.md` 最小结构；**渐进式披露三层**；靠描述匹配触发；与 MCP、提示词、微调的区别；安装前安全检查 |
| ④ | **概念关系说明** | [`learning-notes/concept-relationship.md`](learning-notes/concept-relationship.md) | 3 | — | — | 三者的角色对比表；上下文如何影响 Agent 的工作（四条因果链）；Skill 如何沉淀可复用知识（含时序图）；**机制层 / 行为层 / 资产层的学术锚点图**；关于这组关系本身的常见误解 |

每份概念资料都按同一个模板组织，逐项覆盖了作业要求的内容：

- **个人解释**（第 3 节：一句话定义 + 大白话解释 + 类比/反例）
- **核心机制或组成**（第 4 节，每张图解都配一句"这张图的重点是什么"）
- **一个具体应用场景**（第 5 节，具体到"为什么非它不可"和"上线前怎么验证"）
- **容易混淆的问题与使用边界**（第 6、7 节）
- **可核查的资料来源链接**（第 9 节：**学术来源与行业文档分表列出**，每条标注「评审状态」+ 支撑了哪句结论 + 访问日期）
- **可视化讲解**：每个概念至少 2 张图（1 张 Mermaid 结构/流程图 + 1 张表达"量"的图），示意图形标注"非原始数值"
- **自测题按难度递进**：★ 基础（概念辨析）→ ★★ 应用（机制推理）→ ★★★ 综合（设计与迁移）
- 额外还有：学习目标、核心问题、存疑清单、更新记录

---

## 六、人工核查与修改记录

AI 参与了检索、起草和整理，但**没有一段解释是直接搬运对话结果的**。以下是实际做过的核查动作，以及在这些动作之后被改掉的地方。

### 6.1 逐条核验来源链接的真实性

对全部 14 条候选链接跑了可达性检查，命令是：

```bash
curl -s -o /dev/null -w "%{http_code}  %{url_effective}\n" -L --max-time 25 \
  -A "Mozilla/5.0" "<URL>"
```

结果与处理：

| 链接 | 结果 | 处理 |
|---|---|---|
| Anthropic 的 4 篇工程博客 | `200` | 直接引用 |
| agentskills.io、Claude 文档、WorkBuddy 文档（2 篇） | `200` | 直接引用 |
| Lilian Weng 博客 | `200` | 直接引用 |
| arXiv 的 4 篇论文（1706.03762 / 2201.11903 / 2210.03629 / 2307.03172 / 2302.04761） | `000`（本机网络策略拦截，DNS 可解析但 TLS 失败） | **没有直接删掉，也没有假装是 200**。改为抓取页面正文逐项核对标题、作者名单、提交日期与 arXiv 编号，确认页面存在且内容与引用一致，并在资料中如实标注"经抓取核验，直连被网络策略拦截" |

**这是本次核查中最重要的一条修正**：AI 一开始倾向于把这些链接当作"已确认"。实际跑下来发现 arXiv 直连不通，如果不去验证，就会在资料里写下一个自己没确认过的状态。

### 6.2 用原始页面核对元数据，而不是相信二手转述

- **ReAct**：核对了作者共 7 人（Shunyu Yao 等），v1 提交于 2022-10-06、v3 为 2023-03-10 的 ICLR camera-ready 版本。资料里写的是**提交日期 + 版本日期**，而不是模糊的"2022 年"。
- **Toolformer**：核对到作者 8 人、v1 提交于 2023-02-09。AI 初稿曾把它描述成"训练模型调用工具的主流路线"，修改为**准确表述**："模型自学何时调用哪个 API 的另一条独立路径"——原文只发了 v1，没有后续版本，不宜夸大成熟度。
- **Chain-of-Thought**：核对到 v1 为 2022-01-28，作者 9 人。
- **Lost in the Middle**：核对到 v1 为 2023-07-06，且**已被 TACL 2023 收录**（这一点是二手文章普遍漏写的，写进资料能提升来源等级）。
- **Agent Skills 作为开放标准的发布时间**：来自 Anthropic 那篇工程博客内部的更新说明（December 18, 2025），**没有找到独立的标准版本号**，因此在资料中明确标注了这一点，而不是当作"官方标准发布日期"来引用。

### 6.3 删掉、收窄或降级的表述

这些是 AI 初稿里写过、但经核查后被我改掉的内容：

| 初稿的问题 | 修改后 |
|---|---|
| 给 Lilian Weng 标注了具体职务 | **删除职务**。她的任职情况随时间变化，二手来源互相矛盾；资料只保留"作者 + 标题 + 日期 + 链接"，不写会过期的身份信息 |
| 列出 Anthropic 那篇 context engineering 的四位作者姓名 | **删除**。该名单只出现在二手页面，未在原文确认，不能作为事实写入 |
| "当前主流 Agent 框架都基于 ReAct" | **收窄为**"ReAct 确立了 Thought/Action/Observation 这一基本范式" |
| 引用 Anthropic 文中的框架清单（Rivet、Vellum 等） | **删除**。Anthropic 原文开头已明确标注"tooling landscape 自 2024-12 起已有变化"。因此资料只保留**依然成立的判据**，不引用会过期的工具名 |
| 把"Claude 玩宝可梦"当作机制案例写进正文 | **移出正文**，只放进"存疑清单"。该细节只在二手渠道见过，未回原文核实 |
| 列出"支持 Agent Skills 的客户端清单" | **删除**。这类清单时效性短，改为在存疑清单中提示"需要时查官方 Client Showcase 并现场核对" |
| 把 U 型曲线当成"当前模型的普遍规律" | **加限定**。原文实验用的是 GPT-3.5-Turbo、Claude-1.3 一代，资料明确写出"应视为需要自己验证的假设"，并给出验证方法 |
| 一个腾讯云社区的 Skill 教程被当作事实来源 | **降级为理解辅助**。其中"Skill 管流程、插件管工具"的区分与 WorkBuddy 官方文档一致，正式引用改为**官方文档** |

### 6.4 用自己的话重写了全部解释

三份资料的"个人解释""核心机制"等段落是先读完原始来源、合上之后重写，再回头对照来源检查是否说错或说过头。举两个例子：

- ReAct 的 HotpotQA 数字（完整 ReAct 35.1% vs Act-only 25.7%）是**回到论文核对后写进去的**，AI 初稿里只有一个模糊的"明显更好"。
- "上下文是工作内存，不是仓库"这个说法，以及"工具存在的理由不是让模型变强，而是让上下文能拿到它装不下的东西"这个推论，是我自己在读完 Anthropic 那篇之后重新组织的表述——**原文没有这个句子**。

### 6.5 结构上的修改

- Skill 初稿把输出模板写成了针对 Agent / 上下文 / Skill 三个概念的具体小节 → **改成带占位符的通用模板**，否则它就不是 Skill 而是提示词。
- 给 Skill 增加了 `learning-notes/INDEX.md` 的登记步骤，让"自动组织学习材料"这件事有实际落点。
- 给资料模板增加了第 10 节「存疑与待确认」和第 11 节「更新记录」——AI 初稿没有这两节，导致"哪些内容还没核实"在文件里完全看不出来。

### 6.6 第二轮核查：把来源从"以行业文档为主"升级为"以学术来源为主"

第一轮的来源结构偏工程（Anthropic 博客 + WorkBuddy 文档占了多数）。第二轮补入 11 篇学术文献，并做了一件更重要的修改：**给每条学术来源加上「评审状态」标注**。

**（1）核实 11 篇论文的元数据**，逐项核对标题、作者名单、v1 提交日期、最新版本日期、Comments 字段、Related DOI。其中一手页面明确给出同行评审信息的五篇：

| 论文 | 编号 | 一手页面给出的评审信息 |
|---|---|---|
| Lost in the Middle | 2307.03172 | Comments 明确写 "Accepted for publication in TACL, 2023" |
| Same Task, More Tokens | 2402.14848 | Comments 明确写 "Accepted to ACL 2024" |
| RULER | 2404.06654 | Comments 明确写 "COLM 2024" |
| CoALA | 2309.02427 | Comments 明确写 "v3 is TMLR camera ready version" |
| A Survey on LLM based Autonomous Agents | 2308.11432 | 页面给出期刊 Related DOI：10.1007/s11704-024-40231-1 |

**（2）抓到一个"打状态码发现不了"的错误。** 我一开始把 LATM（Large Language Models as Tool Makers）的编号写成了 `2305.19370`。跑可达性检查时它返回 **200——看起来完全正常**。但打开页面核对标题才发现，`2305.19370` 实际是 *Blockwise Parallel Transformer for Large Context Models*，完全是另一篇论文。LATM 的正确编号是 `2305.17126`（检索确认 + 页面标题核对一致）。

这条修正后来被写进了 Skill 的硬规则：**绝对不要凭记忆写 arXiv 编号，必须检索确认后打开页面核对标题**——因为编号记错一位时，状态码检查发现不了。

顺带一提，那篇被误认的论文本身是长上下文领域的正经工作（分块计算压内存、可训练序列长度达原生 Transformer 的 32 倍），所以它被保留下来用在了 `llm-context.md` 的 4.1 节——但引用的是它**真实的标题和结论**。

**（3）确立了「评审状态」的判定标准，并接受它带来的"保守"。** 规则是：只有一手页面明确写了 camera-ready / accepted，或给出指向期刊的 Related DOI，才标「同行评审」；其余一律标「预印本」。

这条规则的直接后果是：**Attention Is All You Need、Reflexion、Toolformer、Voyager、LATM、RoPE 都被标成了「预印本」**——尽管它们实际上都在顶会发表过。我选择保留这个结果，理由是：

- 表格里每一个「同行评审」标记，读者都能自己在 arXiv 页面上复核到；
- 反过来，如果按"常识"标成会议论文，读者去复核时会发现 arXiv 页面上根本找不到这个信息。

判定标准已写进每份资料的第 9.3 节和 Skill 第四节第 7 条，避免读者误以为"预印本"等于"没发表"。

**（4）把行业文档降级到单独的表。** 三份资料的来源都改成双表结构（9.1 学术 / 9.2 行业）。并在 `skill.md` 的第一段就明确写出：**Agent Skills 是行业开放标准，不是同行评审成果**，它的机制依据来自 CoALA 的程序性记忆、Voyager 的技能库、LATM 的成本摊销。这一条是刻意加的——不这样写，"有学术来源"很容易变成一种包装。

**（5）补了 9 处图解，并标注哪张是示意。** Agent 加主循环图与判据决策图；上下文加组成图、U 型曲线示意、长任务三手段图；Skill 加渐进式披露三层图；关系说明加"机制层 / 行为层 / 资产层"因果图。其中 **U 型曲线那张明确标注"示意，非论文原始数值"**——因为我没有复现论文实验，抄不出精确百分比，把形状当成数据是误导。

**（6）重排了自测题。** 第一轮的三份资料里题目难度是混排的（例如把需要跨场景设计的问题排在纯记忆题前面）。这一轮统一改成 ★ 基础 / ★★ 应用 / ★★★ 综合 三档递增，每档 2–3 道，共 7 道/份，并新增 3 道综合题。同时把这条要求写进 Skill 的第七步与自检清单（含"禁止把难题排在简单题前面""★★★ 档必须真的需要综合多处机制，而不是 ★ 档换个说法"），防止下次又混排。

### 6.7 仍需你本人确认的部分

以下几条我无法代你判断，建议提交前过一遍：

- [ ] 第 6.4 节中"用自己的话重写"的程度是否符合你的要求——建议你随机挑两段，对着来源读一遍。
- [ ] 自测题的难度档位是否匹配你的学习阶段（★★★ 档目前是按"能设计方案并说明依据"的水准出的）。
- [ ] **「评审状态」的保守判定是否可接受**：Attention Is All You Need、Reflexion、Voyager、LATM 等被标为「预印本」。如果你希望按会议版标注，需要你自己去会议论文集页面核实后改（`learning-notes/INDEX.md` 和每份资料第 9.1 节都要同步改）。
- [ ] `learning-notes/agent.md` 第 10 节里关于 Anthropic 文章"框架清单已过时"的判断，是否与你的实际使用场景一致。
- [ ] 三份资料里的 Mermaid 图在 GitHub 上是否正常渲染（本机无法渲染预览，只做了语法层面的检查）。
- [ ] 本地已把更换后的内容接在远端历史之上（父提交 = 远端 HEAD `9bbc7a4`），**只剩 `git push -u origin main` 需要你在自己的终端执行**（见 8.4）。

---

## 七、版本与安全

### 7.1 版本

| 项 | 值 |
|---|---|
| Skill 名称 / 版本 | `concept-learning` / **1.1.0** |
| 资料生成日期 | 2026-09-10 |
| 资料最近修订 | 2026-09-10（第二轮：补学术来源、加图解、重排自测题） |
| 来源访问日期 | 2026-09-10 |
| 分支 | `main` |

v1.1.0 相对 v1.0.0 的变化：输出模板新增"可视化要求"（至少 2 张图）；自测题改为强制三档递进；来源改为学术 / 行业双表并强制标注「评审状态」；新增"不要凭记忆写 arXiv 编号"的硬规则与对应自检项。详见 [`.workbuddy/skills/concept-learning/SKILL.md`](.workbuddy/skills/concept-learning/SKILL.md)。

Skill 的版本号写在 `SKILL.md` 的 frontmatter 里。每次修改正文并影响到输出结构时，应当递增这个版本号，以便区分"用哪一版 Skill 生成的资料"。概念资料的末尾都有「更新记录」，两者配合使用。

### 7.2 安全

- **没有上传任何敏感信息。** 本仓库不包含 API Key、Token、密码、Webhook 地址、个人隐私信息或任何形式的凭证。
- `.gitignore` 已经排除了以下几类内容：
  - **凭证类**：`.env` / `*.key` / `*.pem` / `*.pfx` / `secrets.json` / `credentials.json` / 含 `token` 的 json
  - **可能带凭证的配置**：`.workbuddy/mcp.json` 与 `.workbuddy/.mcp.json`（MCP 配置里可能包含 Webhook 与密钥）
  - **个人与本地状态**：`.workbuddy/memory/`、`.workbuddy/plans/`、`.workbuddy/file-history/`、`.workbuddy/logs/`、本地数据库文件
  - **环境与系统噪声**：`node_modules/`、虚拟环境、`.vscode/`、`.DS_Store` 等
- **注意 `.workbuddy/skills/` 没有被忽略**——Skill 定义是本仓库要交付的内容，必须提交。
- **关于 Skill 本身的安全**：SKILL.md 是纯文本指令，不含脚本、不发起任何外部调用、不读写本仓库之外的文件，可以逐行审阅确认。作为对比，Anthropic 官方文档提醒过：Skill 可以捆绑可执行脚本，**恶意 Skill 可能引导模型以与其声明用途不符的方式调用工具或执行代码**。因此安装第三方 Skill 前应检查其中所有文件，而不只是 `SKILL.md`。本仓库里的所有内容都是自有文本，不存在这个风险。
- 提交前建议自查一次：`git status` 确认没有意外文件进入暂存区，`git diff --cached` 逐行看一遍内容。

---

## 八、Git 与推送

### 8.1 目标仓库

| 项 | 值 |
|---|---|
| 仓库 | `sweet0513/sweet0513` |
| 地址 | <https://github.com/sweet0513/sweet0513.git> |
| 可见性 | public |
| 默认分支 | `main` |
| remote 名 | `origin`（已在本地配置好） |
| 远端当前 HEAD | `9bbc7a4` |

> 早前一轮曾把目标误设为 `sweet0513/cyh0509`。`cyh0509` 里只有一个自动生成的占位 README，与本作业无关；本仓库的真正目标是 `sweet0513/sweet0513`。

### 8.2 远端已有什么（上一版作业）

`sweet0513/sweet0513` 里已经有一版同一份作业，共 4 个提交：`Initial commit` → `Add files via upload` → `Add .gitignore to exclude sensitive files and directories` → `Add initial documentation for AI Agent concepts`。

| 文件 | 大小 |
|---|---|
| `.gitignore` | 648 B |
| `.workbuddy/memory.md` | 6.3 KB |
| `README.md` | 6.4 KB |
| `learning-materials/agent.md` | 8.3 KB |
| `learning-materials/concept-relationship.md` | 8.1 KB |
| `learning-materials/context.md` | 7.6 KB |
| `learning-materials/skill.md` | 8.9 KB |

**其中有一个关键缺口**：那份 README 写着自己的 Skill 位于 `.workbuddy/skills/concept-explainer/SKILL.md`，但**整个 `.workbuddy/skills/` 目录并不在仓库里**。也就是说，作业要求的第一条（"仓库中必须存在 `.workbuddy/skills/<名称>/SKILL.md`"）在上一版里实际上是缺失的。本仓库把这一条补上了。

### 8.3 本次替换会改动什么

| 动作 | 文件 |
|---|---|
| **删除** | `.workbuddy/memory.md`、`learning-materials/agent.md`、`learning-materials/context.md`、`learning-materials/skill.md`、`learning-materials/concept-relationship.md` |
| **覆盖** | `README.md`、`.gitignore` |
| **新增** | `.gitattributes`、`.workbuddy/skills/concept-learning/SKILL.md`、`learning-notes/`（INDEX + 三份资料 + 关系说明） |

替换后的完整目录结构见第二节。

### 8.4 需要你执行的命令

本地已经全部准备完毕，**只剩推送一步**：

| 已完成的本地步骤 | 结果 |
|---|---|
| `git remote set-url origin …/sweet0513/sweet0513.git` | remote 已指向正确仓库 |
| `git fetch origin` | 远端的完整历史（4 个提交）已下载到本地 |
| `git reset --soft 9bbc7a4` + `git commit` | 本仓库内容已接在远端历史之上（新提交的父提交 = 远端 HEAD `9bbc7a4`） |
| 校验 | 父提交就是远端 HEAD → 推送是 **fast-forward**，不需要 `--force` |

你在自己的终端运行：

```bash
cd "D:/统计学/2026-09-10-17-26-15"
git push -u origin main
```

如果弹出凭据窗口，用 GitHub 账号登录；如果用 Personal Access Token，把 Token 当作密码填入（需要 `repo` 或 `public_repo` 权限）。

> 关于网络：本机到 `github.com` 的连接是**时通时断**的。会话中多次重试后曾成功连上 GitHub 并进入认证阶段，但因沙箱内没有可用凭证而止步。所以推送必须由你完成。如果第一次超时，**多试几次**即可。

推送后确认：

```bash
git log --oneline -3
git status          # 应显示 "Your branch is up to date with 'origin/main'"
```

### 8.5 旧内容还能找回来吗

能。上述流程**不是** force push，而是把新提交**接在远端原有历史之上**（父提交 = `9bbc7a4`），所以那 4 个旧提交仍然完整保留在历史里。需要时：

```bash
git checkout 9bbc7a4552bd97ff895c0eb3ef365ad52589571a    # 查看替换前的完整状态
```

也可以在 GitHub 上打开该 commit 页面逐个查看旧文件。

这正是选择"接在历史上"而不是 `--force` 的原因：**一旦发现不对，退回一步就能恢复。**

### 8.6 备用方案：从全新空仓库开始

```bash
git init -b main
git add .
git status                    # 确认没有敏感文件被加入暂存区
git diff --cached             # 逐行看一遍将要提交的内容
git commit -m "docs: concept-learning Skill 与概念学习资料"
git remote add origin https://github.com/<用户名>/<仓库名>.git
git push -u origin main
```

> 提示：`git add .` 之后**务必先跑 `git status`**。`.gitignore` 已经排除了凭证与隐私类文件，但仍然值得人工确认一次没有意外文件被加入。

---

## 九、参考来源汇总

三份资料的第 9 节各自列了对应来源，这里按类型汇总一遍（访问日期均为 2026-09-10）：

**一手论文**（★ 标记为一手页面明确给出同行评审信息的条目；其余为预印本）

- ★ Vaswani et al., *Attention Is All You Need*, arXiv:1706.03762 — <https://arxiv.org/abs/1706.03762>
- Wei et al., *Chain-of-Thought Prompting Elicits Reasoning in Large Language Models*, arXiv:2201.11903 — <https://arxiv.org/abs/2201.11903>
- ★ Yao et al., *ReAct: Synergizing Reasoning and Acting in Language Models*, arXiv:2210.03629（ICLR 2023）— <https://arxiv.org/abs/2210.03629>
- Schick et al., *Toolformer: Language Models Can Teach Themselves to Use Tools*, arXiv:2302.04761 — <https://arxiv.org/abs/2302.04761>
- Shinn et al., *Reflexion: Language Agents with Verbal Reinforcement Learning*, arXiv:2303.11366 — <https://arxiv.org/abs/2303.11366>
- Su et al., *RoFormer: Enhanced Transformer with Rotary Position Embedding*, arXiv:2104.09864 — <https://arxiv.org/abs/2104.09864>
- ★ Wang et al., *A Survey on Large Language Model based Autonomous Agents*, arXiv:2308.11432（Frontiers of Computer Science）— <https://arxiv.org/abs/2308.11432>
- ★ Sumers, Yao, Narasimhan, Griffiths, *Cognitive Architectures for Language Agents*, arXiv:2309.02427（TMLR 2024）— <https://arxiv.org/abs/2309.02427>
- ★ Liu et al., *Lost in the Middle: How Language Models Use Long Contexts*, arXiv:2307.03172（TACL 2023）— <https://arxiv.org/abs/2307.03172>
- ★ Levy, Jacoby, Goldberg, *Same Task, More Tokens*, arXiv:2402.14848（ACL 2024）— <https://arxiv.org/abs/2402.14848>
- ★ Hsieh et al., *RULER: What's the Real Context Size of Your Long-Context Language Models?*, arXiv:2404.06654（COLM 2024）— <https://arxiv.org/abs/2404.06654>
- Liu & Abbeel, *Blockwise Parallel Transformer for Large Context Models*, arXiv:2305.19370 — <https://arxiv.org/abs/2305.19370>
- Wang et al., *Voyager: An Open-Ended Embodied Agent with Large Language Models*, arXiv:2305.16291 — <https://arxiv.org/abs/2305.16291>
- Cai et al., *Large Language Models as Tool Makers*, arXiv:2305.17126（ICLR 2024）— <https://arxiv.org/abs/2305.17126>
- Packer et al., *MemGPT: Towards LLMs as Operating Systems*, arXiv:2310.08560 — <https://arxiv.org/abs/2310.08560>

> ★ 的判定标准见每份资料第 9.3 节：只有一手页面明确写了 camera-ready / accepted，或给出指向期刊的 Related DOI，才标为「同行评审」。所以上表中有若干实际已在顶会发表的论文仍标为预印本——这是刻意的保守。

**官方工程博客与文档**
- Anthropic, *Building effective agents* — <https://www.anthropic.com/engineering/building-effective-agents>
- Anthropic, *How we built our multi-agent research system* — <https://www.anthropic.com/engineering/multi-agent-research-system>
- Anthropic, *Effective context engineering for AI agents* — <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>
- Anthropic, *Equipping agents for the real world with Agent Skills* — <https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills>
- Agent Skills 开放标准 — <https://agentskills.io/>
- Claude 文档 · Agent Skills 概览 — <https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview>
- WorkBuddy 文档 · 技能 — <https://www.codebuddy.cn/docs/workbuddy/From-Beginner-to-Expert-Guide/Function-Description/Skills-Market>
- WorkBuddy 文档 · 简介 — <https://www.workbuddy.cn/docs/workbuddy/Overview>

**独立技术博客**
- Lilian Weng, *LLM Powered Autonomous Agents*, 2023-06-23 — <https://lilianweng.github.io/posts/2023-06-23-agent/>
