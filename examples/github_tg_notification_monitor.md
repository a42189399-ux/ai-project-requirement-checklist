# 需求规约模板：数据源监控与即时消息群自动推送机器人规约 (GitHub API -> Telegram Notification Monitor)

## 1. 需求背景 (Background)
发包方需要实时跟踪特定开源技术仓库、竞品软件库的动态（如 GitHub 产生了新的 Star、发布了新 Release、新增了特定关键词的 Issue），希望搭建一个轻量级数据监控脚本，定时轮询或通过 Webhook 接收变更，并将关键信息转换格式后，自动推送到国内外的团队沟通群（如 Telegram 群、Discord 频道或企业微信群机器人）中，以便技术团队和市场人员快速响应。

---

## 2. 非目标 / 不承接范围 (Out of Scope)
*   **私有网络绕过与爬虫对抗**：不承接针对有强防爬机制或需付费登录的第三方私有数据源的逆向破解；本需求仅针对具有公开公开 API（如 GitHub API、RSS 订阅源）的数据渠道。
*   **代理服务器与科学上网搭建**：服务商不代为提供境外网络节点代理、不翻墙、不提供非法虚拟专用网（VPN）的搭建与售卖服务。
*   **微信个人号协议群发**：因微信官方限制个人号 API，为保障账号安全，本自动化不承接基于 Web 微信协议的“微信个人号自动群发”，仅支持企业微信机器人或 Telegram 等官方开放 BOT 机制。

---

## 3. 输入与输出规范 (Inputs & Outputs)
*   **输入端数据源**：
    *   GitHub OpenAPI 提供的公开接口（如 `GET /repos/{owner}/{repo}/releases` 或配置 GitHub Repo Webhooks）。
    *   输入参数：目标仓库名称、监控频次控制。
    *   秘钥管理：发包方的 GitHub Personal Access Token (PAT) 及 Telegram Bot Token，均需通过部署环境变量进行隔离配置。
*   **输出端目标**：
    *   发送至 Telegram Bot API （`POST https://api.telegram.org/bot<token>/sendMessage`）。
    *   消息体格式化：输出内容需包含 `仓库名`、`事件类型`、`Release标题`、`提交内容摘要`及 `直达链接`。

---

## 4. 验收标准 (Acceptance Criteria)
*   **推送实时性与去重**：若采用轮询机制（例如每 10 分钟执行一次），系统需将每次拉取的数据的 `id` / `updated_at` 与本地轻量级数据库（如 SQLite 或 JSON 文件）进行比对去重。确保每次更新仅推送一次，无历史老数据重复推送。
*   **鉴权异常自动告警**：若由于 Token 过期、GitHub API 超过 Rate Limit 限制（未鉴权限制为 60次/小时，鉴权限制为 5000次/小时）导致请求失败，脚本自身需能捕获异常，并在连续失败 3 次后发出本地健康自检错误日志。
*   **网络连接自适应**：在网络偶发不通（尤其是国内服务器访问 Telegram 接口时）情况下，网络层需具备超时控制（Timeout <= 10s），防范请求无限挂起挂死进程。

---

## 5. 风险边界与费用约定 (Risk Boundaries & Cost Agreements)
*   **网络代理与拓扑成本**：Telegram Bot 接口在国内网络环境下无法直接连接。运行此监控系统的服务器或代理网络节点年费与部署费用完全由发包方采购和承担。
*   **API 额度熔断规则**：发包方需自知 GitHub API 频次额度。如由于监控仓库过多导致触发 IP 封禁或 Rate Limit，服务商不承担连带责任。系统需默认配置符合 GitHub API 要求的 `User-Agent` 标识以及合理的轮询延迟间隔。

---

## 6. 可交付物列表 (Deliverables)
*   `[ ]` 基于 Python 或 Golang 编写的监控与推送服务源码
*   `[ ]` 用于去重记录的 SQLite 数据库初始化脚本
*   `[ ]` 代理网络配置与系统环境变量部署指南
*   `[ ]` 进程守护配置文件（如 Systemd 服务配置或 PM2 配置）

---

## 7. 适合 AI Agent 执行的任务切片 (Agent Task Slices)
1.  **轮询与去重机制编写**：让 Agent 自动编写一个“轮询-比对-记录”的主循环逻辑，使用 SQLite 对已处理的 GitHub Release ID 进行持久化存储过滤。
2.  **消息排版格式化工具**：使用 Agent 编写 Markdown/HTML 格式转换函数，将 GitHub API 的 Release Notes 长文本自动截断并排版为适合 Telegram 气泡阅读的精炼富文本。
3.  **Docker 容器化部署脚本**：让 Agent 生成标准的 Dockerfile 及 docker-compose 配置文件，支持一键配置网络代理环境变量并以非 root 安全权限在后台稳定运行。
