# OpenClaw 必备十项核心技能安装与配置指南

本指南将指导您如何使用 `clawhub` 安装并配置精选的 10 个 OpenClaw 核心扩展技能。这套技能组合提供了从底层文档检索、自动化浏览器、到深度总结和自我进化的全面能力。

## 第一步：环境准备与登录

在安装任何技能之前，请确保您的 OpenClaw 已安装并可以通过命令行调用。您需要通过 ClawHub (OpenClaw 的官方技能中心) 来管理这些技能。

1. **登录 ClawHub：**
   ```bash
   npx clawhub login
   ```
   *(根据终端弹出的提示，在浏览器中完成授权登录)*

## 第二步：一键安装核心技能

登录成功后，您可以通过以下命令一次性将这 10 个核心技能安装到您的 OpenClaw 工作空间中：

```bash
npx clawhub install agent-browser clawflows clever-compact find-skills multi-search-engine qmd security-scanner self-improving-agent skill-safety-verifier summarize
```

---

## 第三步：技能详情与配置说明

以下是这 10 个技能的具体作用说明以及对应的配置要求：

### 1. `agent-browser` (Agent Browser)
* **作用：** 提供极速、抗反爬的无头浏览器自动化能力。允许 Agent 自主访问网页、点击按钮、填写表单并抓取动态内容。
* **配置要求：** **无需额外配置**。默认使用 OpenClaw 自带的本地浏览器容器。

### 2. `clawflows` (ClawFlows)
* **作用：** 这是一个多技能编排引擎。它允许您将多个单一技能组合成强大的工作流（例如：搜索资料 -> 抓取网页 -> 总结分析 -> 发送飞书通知）。
* **配置要求：** **无需额外配置**。安装后即可在聊天中触发复合工作流。

### 3. `clever-compact` (Clever Compact)
* **作用：** 核心的记忆保护与断层修复机制。当对话记录过长导致上下文截断（Compaction）时，此技能会智能提取关键信息并写入持久化状态文件，防止 Agent 突然“失忆”或忘记之前的指令。
* **配置要求：** **无需额外配置**。安装后在后台静默运行。

### 4. `find-skills` (Find Skills)
* **作用：** 技能发现助手。当您在聊天中问“你能做XXX吗？”或“有没有处理XXX的技能？”时，此技能会自动在 ClawHub 技能库中检索并推荐合适的技能供您一键安装。
* **配置要求：** **无需额外配置**。

### 5. `multi-search-engine` (Multi Search Engine)
* **作用：** 终极聚合搜索引擎。内置了 17 种国内外主流搜索引擎（如 Google, DuckDuckGo, 百度, Bing, WolframAlpha 等），支持时间过滤、站点限定。
* **配置要求：** **无需额外配置**。且**不需要任何 API Key** 即可直接实现强大的联网搜索。

### 6. `qmd` (QMD)
* **作用：** 本地文档与代码检索神器。通过混合检索（BM25 + 向量 + Rerank）技术，让 Agent 可以瞬间从您的本地工作目录、代码库或大量 Markdown 文档中找到精准的上下文信息。
* **配置要求：** **按需配置**。默认文本搜索开箱即用。如需启用本地向量模型（实现语义理解搜索），将在首次运行时自动拉取相关的小型模型文件，请确保磁盘空间充足。

### 7. `security-scanner` (Security Scanner)
* **作用：** 自动化安全扫描工具。当您要求 Agent 检查某个目标（URL 或 IP）时，它能自动调用底层工具发现漏洞、端口暴露及 SSL 证书问题。
* **配置要求：** **需安装系统依赖**。该技能依赖于 Linux 宿主机的安全工具。请确保您的系统已安装 `nmap`。
  * *安装命令 (Debian/Ubuntu)：* `sudo apt install nmap -y`

### 8. `self-improving-agent` (Self-Improvement)
* **作用：** 让您的机器人拥有“吃一堑长一智”的成长能力。当它执行命令失败或遭到您纠正时，它会自动将错误原因和正确做法记录在特殊的学习档案中，避免在未来的对话中犯同样的错误。
* **配置要求：** **需确认 Hooks 已启用**。默认安装即可生效。如发现未生效，请检查 `openclaw.json`，确保 `"hooks": { "internal": { "enabled": true } }` 处于开启状态。

### 9. `skill-safety-verifier` (Skill Safety Verifier)
* **作用：** 安全卫士。在您通过 ClawHub 安装任何来源未知的新技能前，它会自动审查该技能的代码，检查是否存在恶意系统指令（如 `rm -rf`）、越权网络请求或敏感数据泄露风险。
* **配置要求：** **无需额外配置**。在您执行 `clawhub install` 时它会自动在后台提供保护。

### 10. `summarize` (Summarize)
* **作用：** 万能内容总结器。您可以直接发给它一个极长的新闻链接、PDF 甚至 YouTube 视频链接，它会在后台将其解析并压缩成简明扼要的摘要，极大地节省大模型的 Token 开销。
* **配置要求：** **无需额外配置**。

---

## 第四步：生效与重启

当所有的技能安装和必要的依赖配置完毕后，请务必重启 OpenClaw 的网关服务，以确保所有的技能配置和钩子都能被重新加载到内存中：

```bash
systemctl --user restart openclaw-gateway
# 或者使用 OpenClaw 官方命令: openclaw gateway restart
```

**至此，您的 OpenClaw 已经装备了这 10 项强大且互补的核心技能！您可以立刻在聊天窗口中测试它们的功能。**