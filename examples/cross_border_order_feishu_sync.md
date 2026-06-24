# 需求规约模板：跨境电商订单自动同步至飞书多维表格 (Shopify/Amazon/Temu -> Feishu Sync)

## 1. 需求背景 (Background)
发包方拥有在海外运行的跨境电商独立站或平台店铺（如 Shopify、Amazon、Temu），希望在客户支付成功生成新订单时，将订单的关键信息（订单号、商品明细、实付金额、支付时间、物流地址等）实时或定时同步到国内团队使用的飞书多维表格（Lark Base）中，以便国内的仓储、运营和客服人员及时跟进发货及售后。

---

## 2. 非目标 / 不承接范围 (Out of Scope)
*   **多平台库存逆向同步**：本期仅支持订单数据从电商平台向飞书单向同步，不支持从飞书反向修改或同步库存/商品状态至 Shopify 或 Amazon。
*   **账号注册与审核**：服务商不代为申请 Shopify Developer App、AWS Partner Network、Amazon Selling Partner API (SP-API) 权限或飞书自建应用凭证。
*   **物流代打标与支付链审计**：不承接与第三方物流系统的直接对接，亦不进行支付渠道账单合规对账。

---

## 3. 输入与输出规范 (Inputs & Outputs)
*   **输入端数据源**：
    *   Shopify Webhook (`orders/create`, JSON 格式) 或 Amazon SP-API 定时轮询接口（每一小时拉取一次 `GET /orders/v0/orders`）。
    *   输入包含字段：`id`, `order_number`, `created_at`, `total_price`, `currency`, `line_items` (数组), `shipping_address` (解密明细)。
*   **输出端目标**：
    *   飞书多维表格指定数据表（Table）。
    *   写入字段映射：系统必须将输入字段正确转换为多维表格中的对应列名，且支持对多商品订单（`line_items`）进行“一单多行”拆分或以换行文本形式聚合存储。

---

## 4. 验收标准 (Acceptance Criteria)
*   **数据一致性**：电商平台产生的订单在 5 分钟内成功呈现在飞书多维表格中，且金额、数量等关键业务字段 100% 准确。
*   **异常重试成功率**：模拟网络波动或飞书 API 限流（Rate Limit）时，系统具备自动重试机制（指数退避，最大重试 5 次），且在重试失败后自动记录错误日志。
*   **幂等性校验**：同一订单号重复发送 Webhook 时，飞书多维表格中不得产生重复行（即通过订单号作为唯一主键进行 Upsert）。

---

## 5. 风险边界与费用约定 (Risk Boundaries & Cost Agreements)
*   **API 限流风险**：飞书 API 对单个应用自建应用的请求频次有限制。若高并发期请求量超限，系统将自动延迟排队写入，数据丢包率需为零。
*   **平台费用归属**：Amazon AWS 托管网关服务费、Shopify 平台订阅年费、国内飞书商业版 API 调用额度费等第三方服务账单费用完全由发包方承担，不包含在外包开发费中。
*   **解密合规限制**：根据 Amazon 数据保护政策（DPP），敏感客户数据（如姓名、详细地址）在同步及本地存储中必须严格遵守合规性，服务商不提供任何规避 SP-API 安全审计的方案。

---

## 6. 可交付物列表 (Deliverables)
*   `[ ]` 自动化中间件部署源码（支持 Docker 或云函数部署）
*   `[ ]` 飞书多维表格字段映射及测试数据集
*   `[ ]` 生产环境部署说明书（包含环境变量配置指南）
*   `[ ]` 自动重试与错误告警组件说明

---

## 7. 适合 AI Agent 执行的任务切片 (Agent Task Slices)
1.  **数据映射器编写**：提供 Shopify `orders/create` 的 JSON schema 与飞书 Base Record 的 JSON 结构，让 Agent 自动编写数据清洗与转换逻辑。
2.  **API 客户端封装**：让 Agent 基于 Feishu OpenAPI SDK 编写一个支持自动获取/刷新 Tenant Access Token 且包含指数退避重试逻辑的 API 客户端。
3.  **单元测试套件生成**：让 Agent 生成高并发 Mock 订单 Webhook 的测试脚本，验证系统的幂等性与去重控制。
