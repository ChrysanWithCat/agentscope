# D-02 Harness 对比矩阵（18 框架 × 六元组）

> 依据 B-02 六元组坐标统一对比。**L**=控制循环、**I_act**=动作接口、**S**=状态持久化、**编排**=多智能体、**安全**=护栏、**定位**=一句话。数据来自各框架官方文档与 2026 年多源对比。

## 总览矩阵

| 框架 | 语言 | L 控制循环 | I_act 动作接口 | S 状态持久化 | 多智能体编排 | 安全 | 一句话定位 |
|---|---|---|---|---|---|---|---|
| **AgentScope**（本项目） | Python | 自由 ReAct（_next_action 状态机） | Toolkit 注册（函数/MCP/Skill）+内置编码工具 | AgentState+Storage（Redis/SQL，会话级） | 团队工具+A2AAgent+SOP+GoalPipeline | PermissionEngine（工具级决策）+沙箱 | 生产级可组合 SDK+服务层 |
| **LangGraph** | Py/JS | 显式 StateGraph（节点/边） | LangChain 工具+MCP | **节点级 checkpoint**（durable） | 子图+supervisor+handoff 边 | HITL interrupt+LangSmith 审计 | stateful 生产工作流默认 |
| **OpenAI Agents SDK** | Py/JS | ReAct（Runner 内部） | @function_tool+MCP | Sessions（会话级，无 checkpoint） | **handoff 交接链** | Guardrails（循环外输入/输出） | 3 概念最简 SDK+交接 |
| **Claude Agent SDK** | Py/TS | Agent loop+interrupt | 工具调用+MCP 原生 | 会话/项目级 | subagent 递归委派 | safety-first（系统级） | "Agent Harness"官方样本（Claude Code 同源） |
| **smolagents** | Python | 极简循环 | **CodeAct（写代码执行）** | 无（ephemeral） | 简单支持 | 沙箱（E2B） | ~1000 行最小 harness |
| **PydanticAI** | Python | 单代理循环+Graph | @tool 装饰器 | 会话级（in-memory） | 多 agent+tool approval gates | **类型安全+依赖注入** | type-safe 结构化输出 |
| **CrewAI** | Python | Crew 协作+Flows 事件流 | @tool+LiteLLM | In-memory+记忆后端可插拔 | **角色化 Crew** | 人工审批 | 角色化多智能体原型最快 |
| **AutoGen/AG2** | Python | 会话循环+GroupChat | 工具/代码执行 | 对话状态（研究向） | **群聊发言调度** | HITL | 会话式多智能体研究 |
| **Microsoft Agent Framework** | .NET/Py | pipeline 有界循环+CodeAct 预览 | function invocation+CodeAct | 服务调用级历史+Hosted Agents | **A2A 原生**+团队 | middleware 审批+Foundry 遥测 | AutoGen+SK 继任者，五层 harness |
| **Google ADK** | Py/JS | Runner 循环+callbacks | 工具+MCP+A2A | **Session 一等公民**+分层记忆 | A2A 互通 | callback 观测 | 云原生+双协议 |
| **LlamaIndex** | Python | Workflows 事件驱动图 | 工具+QueryEngine（检索即工具） | Workflow 状态 | 多 agent 工作流 | 事件流可观测 | 从 RAG 长出 agent |
| **Haystack** | Python | Pipeline 组件图 | 工具组件+HITL 拦截 | Document Store 持久化 | 多 agent 支持 | HITL 组件 | 大规模搜索/RAG 型 agent |
| **Mastra** | TypeScript | Agent+Workflows 步骤图 | 工具+MCP 原生 | 会话/记忆+RAG | 多 agent+LLM 路由 | 可观测 tracing | TS-first 生态 leader |
| **Vercel AI SDK** | TypeScript | 轻量循环+前端驱动 | 工具+streamText | 前端协商 | 多 agent（UI 编排） | SDK 层校验 | LLM→浏览器的流式管道 |
| **Hermes Agent** | TypeScript | 自主循环+**自我改进回路** | 工具+环境工具 | 经验持久化 | 多 agent | 弱护栏（自主） | self-improving 自主 agent |
| **OpenClaw** | TypeScript | 持续运行循环 | 工具广度（浏览器/IM/邮件…） | 长期状态（个人上下文） | 多 agent/工具编排 | 权限确认 | 自主个人助理 |
| **DeepSeek Harness** | 多语言 | 可插拔循环（插件） | 工具/MCP（插件化） | 存储插件化 | 插件化编排 | 沙箱插件化 | "一切皆插件"（Cordis 内核） |
| **应用型：Claude Code/Codex/Aider** | — | 编码循环（递归/市场/单代理） | 文件+执行+搜索 | 项目级 | 递归组合/插件分发/无 | 确认+沙箱+审计 | coding agent 生产形态 |

## 沿六元组的横向洞察（3 条）

1. **L 是最大分水岭**：自由循环（AgentScope/OpenAI/smolagents）vs 显式图（LangGraph/LlamaIndex/Haystack/Mastra）vs 会话驱动（AutoGen）——**确定性需求越高，越倾向显式拓扑**。
2. **S 决定生产成熟度**：节点级 checkpoint（LangGraph）> 会话级（AgentScope/OpenAI/ADK）> 无（smolagents）——持久化粒度=可恢复性=能跑多长的任务。
3. **协议在收敛**：**MCP 成为工具协议共同语言**（Claude/Mastra/AgentScope/ADK/OpenAI 全支持）、**A2A 成为智能体协议**（Microsoft/ADK/AgentScope）——2026 生态关键词是"互通"而非"封闭"。

## 覆盖率口径

本矩阵覆盖 2026-09-24 前主流出现的 harness/框架：Python 生态 11 家 + TS 生态 5 家 + 应用型 3 形态 + 平台面（Agno/DSPy/MetaGPT/低代码见 C-18）。结合 B 模块（术语/六元组/五维/循环谱系/Context/Tool/State/Safety/编排九讲），可覆盖市面 80% harness 的设计与底层原理。
