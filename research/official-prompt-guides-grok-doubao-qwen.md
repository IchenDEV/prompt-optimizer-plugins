# Grok、豆包、Qwen 官方 Prompt 资料调查

- 检索日期：2026-08-10
- 检索范围：只采用厂商官方文档、官方站点索引和官方 GitHub；不收录开发者社区投稿、媒体文章或第三方教程。
- 分类口径：
  - **系统化 Prompt 手册**：覆盖提示词设计、消息角色、示例、评测迭代、上下文管理等多个环节，可作为完整工作方法使用。
  - **Prompt engineering 指南**：给出成体系的写法和优化技巧，但范围较窄、通常是单篇教程，或并非针对某个具体模型版本。
  - **仅示例/API 文档**：展示请求结构、原始提示词或具体功能用法，不构成通用提示工程教程。

## 三家结论

| 厂商 / 模型 | 当前最高等级的官方资料 | 主链接 | 关键边界 |
| --- | --- | --- | --- |
| xAI / Grok | **没有找到通用 Prompt 手册**；最接近的是 `Multi Agent` 页面中的专项 `Prompting Guide` | [Multi Agent — Prompting Guide](https://docs.x.ai/developers/model-capabilities/text/multi-agent#prompting-guide) | 只针对多智能体深度研究，不应外推为所有 Grok 模型的通用提示规范。官方 `grok-prompts` 仓库是系统提示词样本，不是教程。 |
| 火山引擎 / 豆包 | **系统化 Prompt 手册**：《提示词工程》 | [提示词工程](https://www.volcengine.com/docs/82379/1221660?lang=zh) | 这是火山方舟平台级指南，覆盖豆包等方舟模型，并非豆包 App 的用户手册，也不是某个豆包版本专属指南。 |
| 阿里云 / Qwen | **Prompt engineering 指南**：《文生文 Prompt 指南》 | [文生文 Prompt 指南](https://help.aliyun.com/zh/model-studio/prompt-engineering-guide) | 这是阿里云百炼的通用文生文教程，包含 `qwen-turbo` 案例，但不是某个 Qwen 版本专属手册。 |

## Grok（xAI）

### 结论与主链接

截至检索日期，在 xAI 的公开文档索引和官方 GitHub 中，未发现面向所有 Grok 文本模型的通用 Prompt engineering 手册。这个结论的证据边界是 xAI 当前公开的[文档站点索引](https://docs.x.ai/llms.txt)和官方组织公开仓库，而不是对未公开资料的绝对断言。

最接近通用指南的页面是：

- 页面标题：`Multi Agent`
- 具体章节：`Prompting Guide`
- 直达 URL：[https://docs.x.ai/developers/model-capabilities/text/multi-agent#prompting-guide](https://docs.x.ai/developers/model-capabilities/text/multi-agent#prompting-guide)
- 官方性：xAI 官方开发者文档 `docs.x.ai`。
- 分类：**专项 Prompt engineering 指南**，不是通用手册。
- 适用边界：仅面向 `grok-4.20-multi-agent` 一类多智能体研究任务；不能据此声称普通 Grok 对话、代码、语音或图像模型都采用相同提示策略。
- 页面核心建议：
  1. 明确研究范围与深度，列出需要比较或覆盖的维度。
  2. 要求结构化输出，例如按指定类别生成对照表。
  3. 指定期望的证据类型或观点来源。
  4. 对复杂研究先宽后窄，通过多轮追问逐步收敛，而不是把所有要求塞入一次请求。
  5. 提供与任务相关的业务背景和约束。
- 登录 / 地区限制：文档页可公开读取，无需登录；未观察到地区限制。真正运行页面中的 API 示例需要 xAI API Key 和相应模型权限。
- 页面标示更新时间：2026-07-02。

### 补充官方资料

#### `Grok prompts`

- 直达 URL：[https://github.com/xai-org/grok-prompts](https://github.com/xai-org/grok-prompts)
- 官方性：xAI 官方 GitHub 组织 `xai-org`。
- 分类：**原始提示词样本**，不是 Prompt engineering 教程。
- 核心内容：公开 Grok 聊天助手、X 上的 `@grok`、`Grok Explain`，以及部分 API 模型所使用的系统提示词或安全提示词文件。
- 适用边界：这些文件说明 xAI 自己如何配置部分产品功能，不能直接推导出“用户提示词的最佳写法”，也不保证覆盖所有当前线上指令。
- 登录 / 地区限制：公开 GitHub 仓库，无需登录即可阅读；未观察到地区限制。

#### `Generate Text` 与 `xAI Cookbooks`

- [Generate Text](https://docs.x.ai/developers/model-capabilities/text/generate-text)：展示 `system` / `user` 消息及 Responses API、多轮会话等调用方式，属于 **API 文档与示例**。
- [xAI Cookbooks](https://github.com/xai-org/xai-cookbook)：提供函数调用、多轮对话、多模态等可运行示例，属于 **示例集合**。
- 适用边界：两者适合确认请求格式和功能用法，不应被标注为官方通用 Prompt 手册。
- 登录 / 地区限制：文档和仓库可公开阅读；执行示例需要 API Key。

## 豆包（火山引擎 / 火山方舟）

### 主链接：《提示词工程》

- 页面标题：`提示词工程`
- 直达 URL：[https://www.volcengine.com/docs/82379/1221660?lang=zh](https://www.volcengine.com/docs/82379/1221660?lang=zh)
- 官方性：火山引擎官方火山方舟文档，文档库编号 `82379`。
- 分类：**系统化 Prompt 手册**。虽然集中在一个页面中，但已覆盖从设计、测试到管理的完整流程。
- 适用边界：平台级指南，面向火山方舟中的文本生成模型，豆包是其中的核心模型系列；它不是豆包 App 的交互手册，也不是某个豆包模型版本的专属规范。
- 核心建议：
  1. 先按任务复杂度选择深度思考或非思考模型：思考模型重点给目标和约束，让模型规划与校验；非思考模型应给更明确的步骤、格式和样例。
  2. 评测先行，采用“更新 → 测试 → 调试 → 评估”的迭代流程，并版本化管理差异；评测集需覆盖正例、负例、边界条件和多样语料。
  3. 区分 `system`、`user`、`assistant`、`tool` 角色；稳定复用的信息放入 `system` 或 `instructions`，并靠前传入。
  4. 用 Markdown 分区和轻量 XML 标签划分 Identity、Instructions、Examples、Context 等不同内容。
  5. 通过 Few-shot 示例约束行为与格式；复杂任务可指定执行步骤和完成标准。
  6. 需要专有知识时，将检索结果放入消息或使用知识库、文件检索、网页解析、联网搜索等工具，同时注意上下文裁剪和摘要。
  7. 针对通用任务明确角色、上下文、具体任务、执行规则、示例与输出格式；针对编程和 Agent 场景再补充工具使用、测试验证、任务跟踪与透明度要求。
  8. 对稳定前缀和长对话使用上下文管理与缓存，以降低成本并保持一致性。
- 登录 / 地区限制：文档页可公开读取，无需登录；未观察到文档页地区限制。文中 PromptPilot 控制台链接指向北京地域，实际使用工具或调用模型需要登录、开通服务并配置 API Key。
- 官方文档数据标示更新时间：2026-07-20。

### 补充官方指南：《角色扮演场景提示词指南》

- 直达 URL：[https://www.volcengine.com/docs/82379/1256348](https://www.volcengine.com/docs/82379/1256348)
- 官方性：火山方舟官方文档。
- 分类：**场景专项 Prompt engineering 指南**。
- 核心内容：角色名称、世界观、性格、语言风格、与用户关系和经历等人设维度；System Prompt 框架；人称视角、长度、口头禅与矛盾设定的控制；推理参数建议；角色生成器及主动消息、群聊等示例。
- 适用边界：只针对角色扮演及社交娱乐智能体，不是通用文本任务规范。
- 登录 / 地区限制：文档可公开阅读；其中的模型广场、应用广场与 Prompt 优化工具需要火山引擎账号和相应地域服务。
- 官方文档数据标示更新时间：2026-07-31。

### 补充官方工具文档：PromptPilot

- [PromptPilot 概述](https://www.volcengine.com/docs/82379/1399495)：从任务生成初始 Prompt，构建评测集，生成回答并评分，再形成优化版本。
- [Prompt 生成](https://www.volcengine.com/docs/82379/1399496)：按单轮、文本理解、视觉理解或多轮任务生成初始 Prompt，并进入后续调优。
- [Prompt 调优](https://www.volcengine.com/docs/82379/1399497)：覆盖样本调试、批量回答与评分、评测集导出、版本对比和智能优化。
- 分类：**提示工程工具的产品文档**，不是独立的方法论手册。
- 登录 / 地区限制：文档可公开阅读；实际 PromptPilot 产品需要账号、服务权限和支持地域。

## Qwen（阿里云 / 通义千问）

### 主链接：《文生文 Prompt 指南》

- 中文页面标题：`文生文Prompt指南`
- 中文直达 URL：[https://help.aliyun.com/zh/model-studio/prompt-engineering-guide](https://help.aliyun.com/zh/model-studio/prompt-engineering-guide)
- 英文镜像标题：`Text-to-text prompt guide`
- 英文直达 URL：[https://www.alibabacloud.com/help/en/model-studio/prompt-engineering-guide](https://www.alibabacloud.com/help/en/model-studio/prompt-engineering-guide)
- 官方性：阿里云 / Alibaba Cloud 官方 Model Studio（百炼）帮助文档。
- 分类：**Prompt engineering 指南**。内容较完整，但仍是一篇通用文生文教程，而不是某个 Qwen 版本的专属系统手册。
- 适用边界：适用于百炼中的通用 LLM 文生文任务。页面含 `qwen-turbo` 的多语言稳定输出案例，因此与 Qwen 直接相关；但原则以 LLM 通用方法表述，不能据此声称 Qwen3.x 每个版本都有相同的专属行为。
- 核心建议：
  1. 把任务写得清晰、具体、无歧义，补足目的、执行策略和必要细节。
  2. 使用“背景、目的、风格、语气、受众、输出”六要素框架；可按任务增删，不是每条提示都必须机械套用。
  3. 提供期望输出样例，以稳定格式、概念、语法和语气。
  4. 对复杂任务明确完成步骤，并使用少见的分隔符划分不同内容单元。
  5. 对推理任务可拆解步骤或使用 Prompt Chaining；页面也介绍了 CoT 的做法。
  6. 持续测试和迭代 Prompt，结合线上反馈修正。
  7. 结构化输出时明确任务指令、JSON 模板、限制条件和输出示例，以降低格式漂移和无依据内容。
- 登录 / 地区限制：中英文文档页均可公开读取，无需登录；未观察到文档页地区限制。中文页提到的百炼 Prompt 自动优化工具位于控制台，使用时需要阿里云账号并会产生模型 Token 费用。
- 页面元数据标示更新时间：2026-05-12。

### 补充官方资料

- [Qwen 官方文档](https://qwen.readthedocs.io/en/latest/)：当前公开导航主要覆盖 Quickstart、推理、部署、Chat Template、思考模式、工具调用等，属于 **模型使用与 API 文档**，不是通用 Prompt engineering 手册。
- [Qwen3.6 官方仓库](https://github.com/QwenLM/Qwen3.6)：模型说明、权重和运行方式等一手资料，属于 **模型卡与使用示例**，不是 Prompt 手册。
- 登录 / 地区限制：上述文档和 GitHub 仓库均可公开阅读；下载模型或调用云 API 的实际条件另行适用。

## 使用这些资料时的证据边界

1. Grok 的专项 `Prompting Guide` 只能支撑多智能体研究提示写法；不能把官方系统提示词仓库改写成面向用户的“最佳实践”。
2. 火山方舟《提示词工程》可以作为豆包优化器的主要官方依据，但成文时应标为“方舟平台级”，避免伪装成豆包 App 或某个模型版本的专属手册。
3. 百炼《文生文 Prompt 指南》可以作为 Qwen 优化器的主要官方方法来源，但应保留“通用指南、含 Qwen 案例、非版本专属”的限定。
4. 模型名、参数、权限、地域、计费和上下文上限变化较快；这些运行时信息应以链接中的实时页面为准，不固化为永久 Prompt 原则。
