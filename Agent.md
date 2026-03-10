# OpenClaw 多 Agent 架构与飞书对接配置指南

本指南基于当前的 `openclaw.json` 配置环境，详细解析如何在 OpenClaw 中实现多 Agent 的配置、独立工作空间的管理，以及如何将它们精准路由到飞书群组。

---

## 1. 核心架构概览与快速初始化

OpenClaw 是一个多租户、多角色的 AI 智能体编排中心，通过统一网关接入飞书，并根据群组来源自动将任务分发给不同的独立 Agent。

### 1.1 您的 Agent 阵容
1. **Main Agent** (默认助手)：负责一对一私聊，使用性价比最高的模型。
2. **Ops Agent** (运维助手)：负责运维支持飞书群，使用推理能力强的模型。
3. **Code Agent** (编程助手)：负责研发交流飞书群，使用专精代码的模型。
4. **Test Agent** (测试助手)：负责测试专用的飞书群。

### 1.2 快速上手：创建隔离的工作空间
可通过 CLI 快速生成独立的 Workspace（以 Ops 和 Code 为例），实现物理级别的记忆和文件隔离：

```bash
# 创建 ops (运维助手)
openclaw agents add ops \
  --workspace ~/.openclaw/workspace-ops \
  --agent-dir ~/.openclaw/agents/ops/agent \
  --non-interactive

# 创建 code (代码助手)
openclaw agents add code \
  --workspace ~/.openclaw/workspace-code \
  --agent-dir ~/.openclaw/agents/code/agent \
  --non-interactive
```
进入对应工作空间创建或修改 `SOUL.md`，即可为 Agent 注入独立人设与规则。

---

## 2. `openclaw.json` 关键配置解析

此文件是核心配置文件（路径：`~/.openclaw/openclaw.json`）。**任何修改必须重启服务生效：**
`systemctl --user restart openclaw-gateway`

### 2.1 模型提供商 (Models)
**避坑警告 ⚠️：** 定义第三方提供商（如 NVIDIA）下的模型时，`id` 字段切勿重复添加提供商前缀，否则会报 404。另外，建议设置 `"reasoning": false` 避免复杂的思考标签导致飞书卡片渲染失败 (HTTP 400)。

```json
"models": {
  "providers": {
    "nvidia": {
      "baseUrl": "https://integrate.api.nvidia.com/v1",
      "apiKey": "nvapi-1-...", 
      "api": "openai-completions",
      "models": [
        // 注意：NVIDIA 提供商下，模型 ID 不需要加 "nvidia/" 前缀
        { "id": "z-ai/glm4.7", "name": "GLM-4.7", "reasoning": false },
        { "id": "minimaxai/minimax-m2.1", "name": "MiniMax-M2.1", "reasoning": false }
      ]
    },
    "zhipu": {
      "baseUrl": "https://open.bigmodel.cn/api/paas/v4",
      "apiKey": "9f49...",
      "api": "openai-completions",
      "models": [
        { "id": "glm-4.7-flash", "name": "GLM-4.7-Flash", "reasoning": false }
      ]
    }
  }
}
```

### 2.2 智能体定义与隔离 (Agents)
通过为每个 Agent 指定不同的 `workspace` 实现记忆隔离，并绑定不同的大模型。

```json
"agents": {
  "defaults": {
    "model": { "primary": "zhipu/glm-4.7-flash" }, // 默认模型
    "workspace": "/home/jack/.openclaw/workspace"
  },
  "list": [
    { "id": "main" }, // 继承 defaults
    {
      "id": "ops",
      "workspace": "/home/jack/.openclaw/workspace-ops", // 独立隔离的记忆库
      "model": "nvidia/minimaxai/minimax-m2.1" // 专属模型
    },
    {
      "id": "code",
      "workspace": "/home/jack/.openclaw/workspace-code",
      "model": "nvidia/z-ai/glm4.7"
    },
    {
      "id": "test",
      "workspace": "/home/jack/.openclaw/workspace-test",
      "model": "nvidia/minimaxai/minimax-m2.1"
    }
  ]
}
```

### 2.3 飞书路由与绑定 (Bindings)
当飞书收到消息时，通过 `bindings` 规则决定由哪个 Agent 接客。规则按自上而下顺序匹配。

```json
"bindings": [
  {
    "agentId": "ops",
    "match": {
      "channel": "feishu",
      "accountId": "main", // 必须与 channels 中的 accountId 一致
      "peer": { "kind": "group", "id": "oc_1645d94ce7beb3d89f81b865164925db" }
    }
  },
  {
    "agentId": "code",
    "match": {
      "channel": "feishu",
      "accountId": "main",
      "peer": { "kind": "group", "id": "oc_96acab002583326c8aa7eb8448be5536" }
    }
  },
  {
    "agentId": "test",
    "match": {
      "channel": "feishu",
      "accountId": "main",
      "peer": { "kind": "group", "id": "oc_de6b7461e1a432cf7663e06cbb4eb506" }
    }
  },
  {
    "agentId": "main", // 兜底规则（Fallback）
    "match": { "channel": "feishu", "accountId": "main" } // 私聊或未绑定群统一交给 Main
  }
]
```

### 2.4 飞书接入层 (Channels)
负责与飞书建立 WebSocket 长连接，实现免公网 IP 的消息收发。

```json
"channels": {
  "feishu": {
    "enabled": true,
    "connectionMode": "websocket",
    "requireMention": false, // 私聊不需 @，群聊默认需 @ 触发
    "accounts": {
      "main": {
        "appId": "cli_a92...",
        "appSecret": "ASVM3..."
      }
    }
  }
}
```

---

## 3. 常见问题排查与运维指南

| 故障现象 | 根本原因 | 解决办法 |
| :--- | :--- | :--- |
| **发送消息毫无反应** | 模型配置错误导致 API 报错，阻塞了队列 | 检查模型 ID 是否带了错误的前缀，使用 `openclaw models list` 验证。 |
| **日志报错 HTTP 400** | 飞书拒绝接收包含复杂 Markdown 标签的卡片 | 在模型配置中设置 `"reasoning": false`，关闭原生思考标签输出。 |
| **所有消息都发给了 main** | `bindings` 中缺少或填错了 `accountId` | 确保 `accountId` 值为 `"main"` 或与配置的账户一致。 |
| **某群组响应极慢** | 绑定的模型（如 Kimi）冷启动慢或接口限速 | 更换为响应更快的模型（如 `minimaxai/minimax-m2.1` 或 `glm-4.7-flash`）。 |

### 终极绝招：清除僵尸记忆 (Zombie Memory)
* **现象：** 使用 `/new` 重置对话后，Agent 依然保留着之前的错误设定或角色认知。
* **原因：** 反复追问触发了系统钩子，错误设定被固化在工作空间的**长期记忆文件**（`.md`）中。
* **解决：** 物理删除专属工作空间下的记忆，并强制刷新：

```bash
# 1. 物理删除特定工作空间下的所有记忆记录（以 test 为例）
rm -rf ~/.openclaw/workspace-test/memory/*

# 2. 强制发送新会话指令刷新短期上下文
openclaw message send --channel feishu --target [群ID] --message "/new"
```
