# AI/自动化项目发包前需求规约 Checklist

> 💡 **定位说明与读者指南：**  
> * **直接读者**：使用 AI 助手 (AI Assistants)、编程代理 (Coding Agents)、GitHub 以及对技术有基本了解的技术顾问、项目协作者、技术 PM 与半技术发包方。
> * **使用目的**：帮助技术中间人与半技术方将模糊口语化的 AI/自动化外包需求，快速转化为可验收、可报价、可交付、可防范灰产红线的标准化规格说明书（PRD/SOW）。
> * **服务对象**：最终受益者为非技术发包方（虽然他们通常不直接阅读本 GitHub 仓库，但可通过其聘请的技术顾问或 AI 助手来部署并对齐该规约）。

---

## 📌 发包前需求规约 CheckList 简版

在向开发人员发包前，请自测能否清晰回答以下 10 个问题（完整详细解析请参见本仓库的 [checklist.md](checklist.md)）：

1.  **输入边界**：输入数据的格式、产生频次和单次体积是怎样的？
2.  **输出边界**：期望系统自动完成并交付的具体最终状态是什么？
3.  **网关API**：大模型及第三方服务 API 账号是否由你自行拥有，且知晓限流上限？
4.  **托管环境**：系统需要运行在哪里，是否已计入服务器配置及年费预算？
5.  **验收标准**：判定交付成功与否的明确“Pass/Fail”规则与测试集是什么？
6.  **异常边界**：API 超时、失败或数据丢包时，系统如何重试或报警？
7.  **数据安全**：用户的 API Key、敏感手机号和交易额度是否需要加密存储？
8.  **运行费率**：系统单次执行消耗的大模型 Token 费率是否在合理的客单价内？
9.  **交互 UI**：系统是纯后端自动化工作流，还是需要特定的前端交互界面？
10. **交付物归属**：是否要求在你的私有 GitHub 仓库中托管源码并配置 CI/CD 自动部署？

---

## 📂 Example Templates / 真实场景模板

为了让发包方和开发人员（包括 AI Coding Agents）更直观地理解如何编写高规格的需求书，本仓库在 `examples/` 目录下提供了针对高频外包场景的**需求规约模板样本**。每个样本都规定了背景、非目标范围、输入/输出规约、验收标准、风险边界与交付物：

*   **[跨境订单飞书同步规约](examples/cross_border_order_feishu_sync.md)**：针对 Amazon/Shopify/Temu 订单向飞书多维表格的同步，规范 API 频次与限流应对。
*   **[RPA 网页账单导出合规规约](examples/rpa_invoice_export_compliance.md)**：针对拼多多/淘宝等商户后台 RPA 自动导出，规范 UI 变化自适应、免责边界和质保期。
*   **[大模型高并发 API 熔断去重规约](examples/llm_high_concurrency_rate_limiting.md)**：针对调用 DeepSeek/OpenAI 批量文本处理，规范并发 Rate Limit、错误熔断及 Token 计费上限。
*   **[Stripe 支付回调多维表格发卡规约](examples/stripe_webhook_feishu_card_delivery.md)**：针对海外虚拟发卡小工具，规范 Webhook 幂等性处理（防重复回调发卡资损）与网络代理费用。
*   **[GitHub 动态 Telegram/微信群监控规约](examples/github_tg_notification_monitor.md)**：针对特定数据源的高频监控，规范网络代理成本、推送频次限制及异常退出的日志告警。

⚠️ **重要合规声明：**
为了保障项目开发与运行的合规安全，建议在所有外包技术合同中明确双方的安全与风控责任边界。本仓库在 `docs/` 目录下提供了一份通用的合规对齐模板：
*   **[平台合规性与账号风控边界指南](docs/platform_compliance_and_account_risk_boundary.md)**：声明不承接突破平台风控/ToS、批量垃圾群发、人机验证码绕过等对抗性黑产逻辑，明确 API 账户与代理运维成本由发包方自行负担。

---

## 🛠️ 后续模板包整理计划与兴趣反馈

本 Checklist 的目的是提供基本的规约大纲。如果您觉得从零起草对应的规格书合同过于低效，我们后续计划根据用户的实际反馈，将以下资产整理脱敏为一套 **《外包/发包方需求规格化与防坑诊断工具包》**：

*   **《标准化飞书需求排期模板》**：支持直接创建副本的需求规约与里程碑定义多维表格模型。
*   **《AI 需求规格化拆解提示词集》**：辅助使用大模型生成系统功能树的 Prompt 集合。
*   **《开发复杂度与预算自测 Excel 矩阵》**：估算模块排期人天与报价的计算公式模型。
*   **《SOW 工作说明书与里程碑验收模板》**：防烂尾的项目里程碑规范技术协议。

### 📨 早期兴趣登记 (Waitlist)
本工具包目前正处于规划与内容校对中。**当前我们不收取任何费用，亦不制作完整付费交付包。**

我们希望首先验证该 Checklist 对有真实开发发包痛点的用户是否有实际价值。如果您对此项目感兴趣，欢迎通过以下 GitHub 原生方式参与：

1.  🌟 **Star 本仓库**：我们会根据 Star 的增长趋势来评估工具包的开发优先级。
2.  💬 **提交 Issue 反馈您的项目场景**：如果您目前正准备发包，欢迎使用我们的 [项目场景 Issue 模板](https://github.com/a42189399-ux/ai-project-requirement-checklist/issues/new?template=project_scenario.yml) 提交您的业务诉求。我们会根据大家在 Issue 中提到的高频痛点和开发场景，对后续模板包进行针对性优化和免费脱敏分享。

---

## 🤖 For AI Agents & Machine-Readable Entry

This repository is optimized for retrieval-augmented generation (RAG) and LLM-based coding assistants:

*   **Agent Directory**: Read [llms.txt](llms.txt) at the root for a structured index of files.
*   **System Prompts**: Refer to [docs/for-agents.md](docs/for-agents.md) for instructions on how to parse and utilize these checklists in chat.
*   **Machine-Readable Schema**: Refer to [examples/ai_project_brief_manifest.json](examples/ai_project_brief_manifest.json) for a JSON schema defining a specification brief.
