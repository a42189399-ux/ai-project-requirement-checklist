# 需求规约模板：支付回调与多维表格自动虚拟发货/发卡系统规约 (Stripe Webhook -> Feishu Card Delivery)

## 1. 需求背景 (Background)
发包方在海外运营个人独立站或 SaaS 服务，使用 Stripe 等支付网关进行收款。希望在用户成功付款（即 Stripe Webhook 触发 `checkout.session.completed` 事件）时，系统能够自动读取飞书多维表格（Lark Base）中预存的卡密库（或激活码列表），提取一个未使用的卡密通过邮件或 IM 发送给买家，并将该卡密的状态标记为“已使用（Used）”及绑定买家邮箱，实现全自动的虚拟发货流程。

---

## 2. 非目标 / 不承接范围 (Out of Scope)
*   **Stripe 账号主体与审核**：服务商不代为申请 Stripe 商业主体、进行 KYC 身份认证或申请境外银行卡绑定。
*   **支付纠纷与退款逻辑**：本自动化流不承接自动退款、虚假欺诈订单拒付判定等复杂的支付风控业务，仅进行正向交易订单的发货处理。
*   **多币种复杂税率计算**：不承接关于不同国家增值税（VAT）自动申报与开票的财务接口对接。

---

## 3. 输入与输出规范 (Inputs & Outputs)
*   **输入端数据源**：
    *   Stripe 官方发送的 Webhook POST 请求，Payload 包含：`type: \"checkout.session.completed\"`, `data.object.id` (Session ID), `data.object.customer_details.email` (买家邮箱), `data.object.amount_total` (付款金额)。
    *   飞书多维表格中的“卡密存储表”（包含字段：`卡密内容`, `使用状态: Yes/No`, `绑定买家邮箱`, `使用时间`）。
*   **输出端目标**：
    *   调用 SMTP 邮件接口（如 SendGrid/Resend 或自建邮件网关）向买家自动发送包含卡密内容的邮件。
    *   更新飞书多维表格中该卡密行的使用状态及元数据。

---

## 4. 验收标准 (Acceptance Criteria)
*   **幂等性机制（防重复发货）**：系统在写入飞书或发送卡密前，必须以 Stripe 的 `checkout.session.completed` 中的 Session ID 建立一个本地轻量分布式锁（或在多维表格中进行唯一键 Upsert 记录）。如果同一 Session ID Webhook 被 Stripe 重复推送，系统仅应执行一次发货，绝不能向同一笔支付订单下发两个卡密密匙。
*   **低延时要求**：从买家支付成功到买家收到包含卡密的邮件，整体链路自动化耗时需控制在 1 分钟以内（不包含邮件网关的排队延迟）。
*   **库存熔断警报**：当飞书多维表格中的可用卡密（“使用状态”为 No 的行数）低于预设阈值（例如 10 个）时，系统需自动向管理员的飞书群发送“卡密库存不足，请及时补货”的报警通知。

---

## 5. 风险边界与费用约定 (Risk Boundaries & Cost Agreements)
*   **Stripe 签名校验合规**：系统在接收 Webhook 时必须强行验证 Stripe Signature（即 `stripe-signature` 头信息），防范外部攻击者构造伪造的支付成功 Webhook 骗取卡密资产。
*   **服务器与网络开销**：由于 Stripe Webhook 回调服务器需运行在公网，且必须能够稳定访问飞书 API 与 Stripe API，发包方需承担相应的公网服务器租用费（如阿里云香港、AWS Serverless）以及网络代理费用。
*   **退款状态资产回收**：若发生用户退款，飞书表格中的卡密状态需要人工或二期流作废处理，一期不承接自动回收，需提前写入合作合同。

---

## 6. 可交付物列表 (Deliverables)
*   `[ ]` 基于云函数或 Node.js/Python FastAPi 编写的 Webhook 接收网关源码
*   `[ ]` Stripe 签名安全校验与幂等去重组件
*   `[ ]` 飞书多维表格库存自检与库存低水位报警脚本
*   `[ ]` 邮件发送对接模块（SMTP / Resend API 集成）

---

## 7. 适合 AI Agent 执行的任务切片 (Agent Task Slices)
1.  **Stripe 签名验证器**：让 Agent 自动编写带 `stripe.webhooks.constructEvent` 签名校验的安全接收函数。
2.  **飞书 Upsert 幂等写入器**：提供飞书 Base 读写 API，让 Agent 编写带行级锁保护的“提取首个空闲卡密并原子更新状态”的多表操作函数。
3.  **库存自检定时器**：编写基于 Cron 定时任务的脚本，统计飞书表格中未分配卡密的剩余量并实现 Webhook 通知的包装逻辑。
