# AI Agent / Context / Skill 概念学习仓库

> 本仓库用于一份课程作业：通过「项目级 Skill」的实践，系统学习并沉淀 **Agent、大模型的上下文（Context）、Skill** 三个概念。
> 作业要求：① 仓库内有可复用的项目级 Skill；② 三份结构化概念学习资料；③ 一份概念关系说明；④ 完整 README；⑤ 安全提交到 GitHub。

---

## 1. 仓库用途

回答三个递进的问题：

- **Skill 是什么、怎么写**——仓库内沉淀了一个「概念深度学习资料生成器」技能，任何新概念都可以喂给它；
- **三个核心概念怎么学**——`learning-materials/` 下有三份按统一结构生成的学习资料与一份关系说明；
- **Agent / Context / Skill 三者怎么协作**——用文字、表格和 Mermaid 图讲清：上下文决定 Agent 每次判断的依据，Skill 把可复用的任务知识沉淀成可被 Agent 按需加载的资产。

## 2. Skill 的存放路径与调用方式

### 存放路径

```text
.workbuddy/
└── skills/
    └── concept-explainer/      # 技能名：概念深度学习资料生成器
        └── SKILL.md            # YAML 元数据(name/description) + 完整方法论
```

SKILL.md 顶部为 YAML frontmatter（含 `name`、`description`），正文说明适用场景、输入信息、生成步骤、输出结构、资料来源要求与自检清单——**它不绑定任何具体概念**，可接收任意新概念作为主题。

### 在 WorkBuddy 中调用它

项目级技能放在当前工作目录的 `.workbuddy/skills/<技能名>/SKILL.md`，WorkBuddy 会在本项目内发现它，有两种用法：

1. **显式调用（推荐教学/演示场景）**：在本项目目录发起对话，直接要求：
   > 请调用技能 concept-explainer，学习主题：`<任意新概念>`（例如"RAG / 微调 / 函数调用"），输出到 learning-materials/。

2. **自动触发（日常使用）**：技能 `description` 会随系统提示被模型预读；当任务命中描述（"把某概念讲清楚、备课、写学习资料"）时，模型会自动加载该技能并按其流程执行，无需手动点名。

> 官方格式参考：CodeBuddy Code 文档《Skills 技能系统》`docs/skills.md`（仓库内 `skill.md` 学习资料 §5 附了链接）。本技能结构同时参照 Anthropic 的 Agent Skills 规范。

### 想新增一个技能？

在 `.workbuddy/skills/` 下新建目录，放一个带 frontmatter 的 `SKILL.md` 即可；更多约定见上文《Skills 技能系统》文档。

## 3. 已生成的学习资料

| 文件 | 一句话内容 | 面向问题 |
| --- | --- | --- |
| [learning-materials/agent.md](learning-materials/agent.md) | Agent 是什么、由哪四部分组成、六种套路、何时别用 | "让模型自主干完一类活" |
| [learning-materials/context.md](learning-materials/context.md) | 上下文窗口/上下文工程：放什么、怎么组织、放多少 | "模型每次判断的依据从哪来" |
| [learning-materials/skill.md](learning-materials/skill.md) | Skill 的目录结构、渐进式披露机制、四种特性、与 prompt/MCP 的差别 | "怎么把经验沉淀成可复用资产" |
| [learning-materials/concept-relationship.md](learning-materials/concept-relationship.md) | 三者关系的总览表 + Mermaid 协作图 + 两个重点角度的深挖 | "它们之间到底是什么关系" |

每份学习资料都严格遵循 `concept-explainer` 技能定义的统一结构：**个人解释 → 核心机制/组成 → 一个具体应用场景 → 易混淆问题/使用边界 → 可核查资料来源**。

## 4. AI 生成后的人工核查与修改记录

本仓库内容由 AI 辅助生成，交付前进行了以下核查与修改（按时间顺序）：

**① 资料链接逐一核验（2026-09-04，HTTP 状态检查）**
- ✅ 保留并标注 200：Anthropic《Building effective agents》《Effective context engineering for AI agents》《Equipping agents for the real world with Agent Skills》、Claude 官方博客、GitHub anthropics/skills。
- 🔁 替换/删除失效链接：原拟引用的 `anthropic.com/engineering/agent-skills` 返回 404 → 改用同主题真实存在的《Equipping agents for the real world with Agent Skills》；`prompt-engineering-overview` 404 → 从正文来源中移除。
- ⚠️ 如实标注受限来源：OpenAI《Context engineering》被 Cloudflare 拦截（403，非链接失效）、LangChain Agents 概念页 308 重定向、agentskills.io 与 CodeBuddy skills.md 经官方渠道确认存在——均已在各资料 §5 标注核验日期与状态，未冒充"全部直接可达"。

**② 事实与表述校对**
- 概念定义与分类以 Anthropic 官方原文为基准比对（workflow/Agent 光谱、渐进式披露三层、Skill 四特性、lost in the middle 等），避免二手中文博客的走样说法；
- 修正了初稿中把 "Slash Command" 与 "Skill" 混为一谈的表述，按 CodeBuddy 官方文档区分"用户显式触发 / Agent 自动发现"；
- 明确了"Skill 允许携带可执行代码 → 来源信任与安全"这一边界，避免过度美化。

**③ 留给你的复核项（建议人工打开确认）**
- 标为 `403`（OpenAI）与标为"文档索引确认"（agentskills.io、CodeBuddy docs）的链接，建议在浏览器中人工打开一次确认；
- 关系说明中的判断性论述（如"上下文是 Agent 质量天花板"）为主观提炼，请结合官方原文判断是否适用于你的语境。

## 5. 版本与安全

- 本仓库已提交并 push 至 GitHub（origin：`sweet0513/sweet0513`），提交说明见 git log；
- `.gitignore` 已排除 `.env`、密钥证书、凭据类文件、本地记忆目录 `.workbuddy/memory/` 与 IDE/系统临时文件（详见 [.gitignore](.gitignore)）；
- push 前已用 `git status` 检查无敏感文件入库；仓库内未包含任何 API Key、密码或个人隐私信息。

## 6. 目录速览

```text
.
├── README.md
├── .gitignore
├── .workbuddy/
│   └── skills/concept-explainer/SKILL.md   # 项目级技能
└── learning-materials/
    ├── agent.md
    ├── context.md
    ├── skill.md
    └── concept-relationship.md
```
