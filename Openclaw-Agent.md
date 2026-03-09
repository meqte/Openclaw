# OpenClaw 多Agent配置完全指南

> 配置日期：2026-03-09
> 作者：主人 + 咪咪
> 状态：✅ 已验证可用

---

## 目录

- [一、概述](#一概述)
- [二、核心概念](#二核心概念)
- [三、准备工作](#三准备工作)
- [四、创建多Agent](#四创建多agent)
- [五、配置文件详解](#五配置文件详解)
- [六、路由配置详解](#六路由配置详解)
- [七、模型分配策略](#七模型分配策略)
- [八、常见问题与解决方案](#八常见问题与解决方案)
- [九、验证方法](#九验证方法)
- [十、实战经验总结](#十实战经验总结)

---

## 一、概述

### 1.1 什么是多Agent

OpenClaw支持在单个Gateway进程中运行多个隔离的Agent，每个Agent拥有：

- **独立工作空间** - 独立的文件、配置、记忆
- **独立认证目录** - 独立的API密钥和认证配置
- **独立会话历史** - 隔离的对话记录
- **独立模型配置** - 不同Agent可使用不同LLM模型

### 1.2 应用场景

| 场景 | Agent | 特点 |
|------|-------|------|
| 日常聊天 | main | 可爱幽默、通用助手 |
| 运维管理 | ops | 严谨专业、快速响应 |
| 代码开发 | code | 技术专业、代码能力强 |
| 测试质保 | test | 详细全面、追求零缺陷 |

### 1.3 核心优势

- 🔒 **安全隔离** - 各Agent配置互不影响
- 🎯 **专精职责** - 不同Agent专注不同领域
- 🛠️ **灵活配置** - 每个Agent可独立模型、工具、权限
- 📊 **成本优化** - 根据任务复杂度选择合适模型

---

## 二、核心概念

### 2.1 Agent组成

```
Agent = Workspace + AgentDir + Model
```

| 组件 | 说明 | 路径示例 |
|------|------|---------|
| **Workspace** | 工作空间（文件、SOUL.md等） | `~/.openclaw/workspace-ops/` |
| **AgentDir** | 认证和配置目录 | `~/.openclaw/agents/ops/agent/` |
| **Model** | 使用的LLM模型 | `nvidia/minimaxai/minimax-m2.1` |

### 2.2 路由机制

OpenClaw通过**绑定规则（bindings）**将入站消息路由到特定Agent：

```
消息 → 路由匹配 → Agent选择 → 执行任务 → 返回回复
```

**路由匹配优先级（从高到低）：**
1. **peer** 精确匹配（群组ID或用户ID）
2. **accountId** 账户匹配
3. **channel** 通道匹配
4. **默认agent**

### 2.3 配置文件结构

```
~/.openclaw/
├── openclaw.json              # 全局配置
├── workspace/                 # main工作空间
├── workspace-ops/             # ops工作空间
├── workspace-code/            # code工作空间
├── workspace-test/            # test工作空间
└── agents/
    ├── main/agent/            # main认证目录
    ├── ops/agent/             # ops认证目录
    ├── code/agent/            # code认证目录
    └── test/agent/            # test认证目录
```

---

## 三、准备工作

### 3.1 环境要求

- ✅ OpenClaw已安装并正常运行
- ✅ 已配置至少一个LLM Provider（NVIDIA、智谱等）
- ✅ 飞书或其他通道已配置（可选）

### 3.2 检查当前状态

```bash
# 查看当前Agent列表
openclaw agents list

# 查看Gateway状态
openclaw gateway status

# 查看已配置模型
openclaw models list
```

### 3.3 规划Agent配置

在开始前，建议填写以下规划表：

| Agent ID | 名称 | 工作空间 | 模型 | 飞书群组 | 称呼风格 |
|----------|------|---------|------|---------|---------|
| main | 咪咪 | ~/.openclaw/workspace | nvidia/z-ai/glm4.7 | 私聊 | 主人 ❤️ |
| ops | 运维小帮手 | ~/.openclaw/workspace-ops | nvidia/minimaxai/minimax-m2.1 | oc_1645d9... | 老板 👷 |
| code | 代码专家 | ~/.openclaw/workspace-code | nvidia/z-ai/glm4.7 | oc_96acab... | 大佬 💻 |
| test | 质量守护者 | ~/.openclaw/workspace-test | nvidia/moonshotai/kimi-k2.5 | oc_de6b74... | QA/Team ✅ |

---

## 四、创建多Agent

### 4.1 创建Agent工作空间

使用 `openclaw agents add` 命令创建独立工作空间：

```bash
# 创建ops Agent
openclaw agents add ops \
  --workspace ~/.openclaw/workspace-ops \
  --agent-dir ~/.openclaw/agents/ops/agent \
  --non-interactive

# 创建code Agent
openclaw agents add code \
  --workspace ~/.openclaw/workspace-code \
  --agent-dir ~/.openclaw/agents/code/agent \
  --non-interactive

# 创建test Agent
openclaw agents add test \
  --workspace ~/.openclaw/workspace-test \
  --agent-dir ~/.openclaw/agents/test/agent \
  --non-interactive
```

**参数说明：**
- `--workspace` - Agent工作空间路径
- `--agent-dir` - Agent认证和配置目录
- `--non-interactive` - 非交互模式（自动确认）

### 4.2 验证创建结果

```bash
# 查看所有Agent
openclaw agents list

# 预期输出：
# Agents:
# - main (default)
# - ops
# - code
# - test
```

### 4.3 初始化工作空间文件

每个工作空间需要以下核心文件：
- `AGENTS.md` - Agent工作规则
- `SOUL.md` - Agent灵魂（性格、风格）
- `IDENTITY.md` - Agent身份标识
- `USER.md` - 用户信息
- `TOOLS.md` - 工具偏好
- `HEARTBEAT.md` - 心跳检查清单（可选）

**示例：ops/SOUL.md（运维小帮手）**

```markdown
# SOUL.md - 运维小帮手

_我是运维小帮手，专业可靠的服务器管家_

## 关于我

**名字：** 运维小帮手 🔧
**风格：** 严谨专业、简洁高效、安全第一
**称呼：** 老板
```

---

## 五、配置文件详解

### 5.1 openclaw.json完整结构

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "nvidia/z-ai/glm4.7"
      },
      "workspace": "/home/jack/.openclaw/workspace"
    },
    "list": [
      {
        "id": "main"
      },
      {
        "id": "ops",
        "name": "ops",
        "workspace": "/home/jack/.openclaw/workspace-ops",
        "agentDir": "/home/jack/.openclaw/agents/ops/agent",
        "model": "nvidia/minimaxai/minimax-m2.1"
      },
      {
        "id": "code",
        "name": "code",
        "workspace": "/home/jack/.openclaw/workspace-code",
        "agentDir": "/home/jack/.openclaw/agents/code/agent",
        "model": "nvidia/z-ai/glm4.7"
      },
      {
        "id": "test",
        "name": "test",
        "workspace": "/home/jack/.openclaw/workspace-test",
        "agentDir": "/home/jack/.openclaw/agents/test/agent",
        "model": "nvidia/moonshotai/kimi-k2.5"
      }
    ]
  },
  "bindings": [
    {
      "agentId": "ops",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {
          "kind": "group",
          "id": "oc_1645d94ce7beb3d89f81b865164925db"
        }
      }
    },
    {
      "agentId": "code",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {
          "kind": "group",
          "id": "oc_96acab002583326c8aa7eb8448be5536"
        }
      }
    },
    {
      "agentId": "test",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {
          "kind": "group",
          "id": "oc_de6b7461e1a432cf7663e06cbb4eb506"
        }
      }
    },
    {
      "agentId": "main",
      "match": {
        "channel": "feishu",
        "accountId": "main"
      }
    }
  ],
  "channels": {
    "feishu": {
      "enabled": true,
      "domain": "feishu",
      "connectionMode": "websocket",
      "requireMention": false,
      "accounts": {
        "main": {
          "appId": "cli_xxxxxxxxxxxxxxxxxx",
          "appSecret": "your_app_secret_here"
        },
        "default": {
          "dmPolicy": "open",
          "groupPolicy": "allowlist",
          "groupAllowFrom": [
            "oc_1645d9...",
            "oc_96acab...",
            "oc_de6b74..."
          ],
          "allowFrom": ["*"]
        }
      }
    }
  },
  "models": {
    "providers": {
      "nvidia": {
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "apiKey": "YOUR_NVIDIA_API_KEY",
        "api": "openai-completions",
        "models": [
          {
            "id": "nvidia/z-ai/glm4.7",
            "name": "GLM-4.7",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "nvidia/minimaxai/minimax-m2.1",
            "name": "MiniMax-M2.1",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "nvidia/moonshotai/kimi-k2.5",
            "name": "Kimi-K2.5",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

### 5.2 agents.list字段详解

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `id` | string | ✅ | Agent唯一标识符 |
| `name` | string | ❌ | Agent显示名称 |
| `workspace` | string | ❌ | 工作空间路径（非mainAgent必填） |
| `agentDir` | string | ❌ | 认证目录路径（非mainAgent必填） |
| `model` | string | ❌ | 使用的模型ID（默认使用全局default） |
| `default` | boolean | ❌ | 是否为默认Agent |

**注意：** `main` Agent通常只需要`id`字段，其他字段使用默认值。

---

## 六、路由配置详解

### 6.1 bindings配置结构

```json
{
  "bindings": [
    {
      "agentId": "ops",           // 要路由到哪个Agent
      "match": {                  // 匹配规则
        "channel": "feishu",     // 通道类型
        "accountId": "main",     // 账户ID（关键！）
        "peer": {                // 对方信息
          "kind": "group",       // 类型：direct|group
          "id": "oc_16..."       // 群组或用户ID
        }
      }
    }
  ]
}
```

### 6.2 关键字段说明

#### agentId
- 要路由到的Agent的`id`
- 必须是`agents.list`中已配置的Agent

#### match.channel
- 通道类型：`feishu`、`telegram`、`discord`、`webchat`等
- 区分不同消息来源

#### match.accountId ⚠️ 关键字段
- 如果通道配置了多个accounts，**必须指定**
- 对应`channels.<channel>.accounts`中的key
- **缺少此字段会导致路由失败！**

#### match.peer
- 精确匹配特定的群组或用户
- `kind`: `direct`（私聊）| `group`（群聊）
- `id`: 群组或用户的唯一标识

### 6.3 路由匹配优先级

**从高到低：**

1. **peer + accountId + channel**（最精确）
   ```json
   {
     "agentId": "ops",
     "match": {
       "channel": "feishu",
       "accountId": "main",
       "peer": {"kind": "group", "id": "oc_xxx"}
     }
   }
   ```

2. **accountId + channel**
   ```json
   {
     "agentId": "main",
     "match": {
       "channel": "feishu",
       "accountId": "main"
     }
   }
   ```

3. **channel**
   ```json
   {
     "agentId": "default",
     "match": {"channel": "webchat"}
   }
   ```

4. **默认Agent**（`agents.list[].default = true`）

### 6.4 常见路由配置示例

#### 场景1：按群组路由（推荐）
```json
{
  "bindings": [
    {
      "agentId": "ops",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {"kind": "group", "id": "ops-group-id"}
      }
    },
    {
      "agentId": "code",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {"kind": "group", "id": "code-group-id"}
      }
    }
  ]
}
```

#### 场景2：按通道路由
```json
{
  "bindings": [
    {
      "agentId": "ops",
      "match": {"channel": "telegram"}
    },
    {
      "agentId": "code",
      "match": {"channel": "disc ord"}
    }
  ]
}
```

#### 场景3：混合路由
```json
{
  "bindings": [
    // 特定群组 → ops
    {
      "agentId": "ops",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {"kind": "group", "id": "ops-group-id"}
      }
    },
    // 特定用户 → code
    {
      "agentId": "code",
      "match": {
        "channel": "feishu",
        "accountId": "main",
        "peer": {"kind": "direct", "id": "user-open-id"}
      }
    },
    // 其他私信 → main
    {
      "agentId": "main",
      "match": {"channel": "feishu", "accountId": "main"}
    }
  ]
}
```

---

## 七、模型分配策略

### 7.1 常见模型特点对比

| 模型 | 优势 | 适用场景 | 成本 |
|------|------|---------|------|
| **GLM-4.7** | 代码能力强、中文处理好 | 代码生成、复杂推理 | 中等 |
| **MiniMax-M2.1** | 响应快速、轻量级 | 简单任务、高频操作 | 低 |
| **Kimi-K2.5** | 长文本理解强 | 文档分析、代码审查 | 中等 |

### 7.2 推荐分配方案（根据实践经验）

#### 方案A：专业匹配（推荐）

| Agent | 模型 | 理由 |
|-------|------|------|
| main | nvidia/z-ai/glm4.7 | 日常助手需要通用能力强 |
| ops | nvidia/minimaxai/minimax-m2.1 | 运维任务简单，快速响应优先 |
| code | nvidia/z-ai/glm4.7 | 代码生成需要强推理能力 |
| test | nvidia/moonshotai/kimi-k2.5 | 测试需要阅读大量代码和日志 |

#### 方案B：成本优化

| Agent | 模型 | 理由 |
|-------|------|------|
| main | nvidia/moonshotai/kimi-k2.5 | 日常使用，综合能力好 |
| ops | nvidia/minimaxai/minimax-m2.1 | 最经济，简单任务够用 |
| code | nvidia/z-ai/glm4.7 | 代码不能省，需要质量 |
| test | nvidia/minimaxai/minimax-m2.1 | 共享ops模型，节省成本 |

### 7.3 模型配置示例

```json
{
  "agents": {
    "list": [
      {
        "id": "ops",
        "model": "nvidia/minimaxai/minimax-m2.1"
      },
      {
        "id": "code",
        "model": "nvidia/z-ai/glm4.7"
      },
      {
        "id": "test",
        "model": "nvidia/moonshotai/kimi-k2.5"
      }
    ]
  }
}
```

---

## 八、常见问题与解决方案

### 8.1 路由失败问题

**症状：** 所有消息都路由到main Agent，其他Agent不工作

**原因：** bindings配置中缺少`accountId`字段

**解决方案：**

```json
// ❌ 错误配置（缺少accountId）
{
  "agentId": "ops",
  "match": {
    "channel": "feishu",
    "peer": {"kind": "group", "id": "oc_xxx"}
  }
}

// ✅ 正确配置（添加accountId）
{
  "agentId": "ops",
  "match": {
    "channel": "feishu",
    "accountId": "main",
    "peer": {"kind": "group", "id": "oc_xxx"}
  }
}
```

### 8.2 Agent不回复问题

**症状：** 消息路由正确，但Agent不回复

**检查步骤：**

1. **检查模型是否可用**
```bash
openclaw agent --agent <agent-id> --message "测试消息"
```

2. **查看错误日志**
```bash
tail -100 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log | grep -i error
```

3. **检查飞书权限**
- 确认机器人有在群组中发消息的权限
- 检查飞书开放平台的应用权限配置

### 8.3 工作空间文件丢失

**症状：** Agent使用默认性格，个性化配置未生效

**解决方案：**

手动创建必要文件：

```bash
# 切换到工作空间
cd ~/.openclaw/workspace-ops

# 创建SOUL.md
cat > SOUL.md << 'EOF'
# SOUL.md - 运维小帮手

名字：运维小帮手 🔧
风格：严谨专业、简洁高效
称呼：老板
EOF

# 创建IDENTITY.md
cat > IDENTITY.md << 'EOF'
# IDENTITY.md

姓名：运维小帮手
角色：服务器运维专家
Emoji：🔧
EOF
```

### 8.4 模型不可用

**症状：** Agent响应时报错模型不存在

**原因：** 模型ID错误或未在`models.providers`中配置

**解决方案：**

1. 检查已配置的模型：
```bash
openclaw models list
```

2. 确认模型ID与配置一致：
```json
// 检查模型ID格式
{
  "id": "nvidia/z-ai/glm4.7",  // ✅ 正确（带provider前缀）
  "model": "glm-4.7-flash"     // ✅ 正确（别名）
}
```

### 8.5 会话历史混乱

**症状：** 不同Agent共享会话历史

**原因：** 多个Agent使用了相同的`agentDir`

**解决方案：**

确保每个Agent有独立的`agentDir`：
```json
{
  "agents": {
    "list": [
      {
        "id": "ops",
        "agentDir": "/home/jack/.openclaw/agents/ops/agent"
      },
      {
        "id": "code",
        "agentDir": "/home/jack/.openclaw/agents/code/agent"
      }
    ]
  }
}
```

---

## 九、验证方法

### 9.1 验证Agent配置

```bash
# 查看所有Agent
openclaw agents list

# 查看路由绑定
openclaw agents list --bindings

# 预期输出应显示每个Agent的配置和路由规则
```

### 9.2 验证模型配置

```bash
# 列出所有可用模型
openclaw models list

# 测试特定Agent的模型
openclaw agent --agent ops --message "你使用的什么模型？"

# 验证Agent使用的模型是否符合预期
```

### 9.3 验证路由配置

```bash
# 查看bindings配置
cat ~/.openclaw/openclaw.json | jq '.bindings'

# 预期输出应显示所有路由规则，包含agentId、accountId、peer
```

### 9.4 验证实际路由

**方法1：通过CLI测试**
```bash
# 在不同群组发送测试消息
openclaw agent --agent ops --message "你是谁？"
openclaw agent --agent code --message "你是谁？"
openclaw agent --agent test --message "你是谁？"
```

**方法2：在飞书等通道测试**
- 在不同群组发送相同消息
- 观察回复风格是否符合预期Agent的性格

### 9.5 验证日志

```bash
# 查看路由日志
tail -100 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log | grep "dispatching"

# 预期输出示例：
# dispatching to agent (session=agent:ops:feishu:group:oc_16...)
# dispatching to agent (session=agent:code:feishu:group:oc_96...)
# dispatching to agent (session=agent:test:feishu:group:oc_de...)
```

### 9.6 验证会话隔离

```bash
# 查看每个Agent的会话历史
ls -lh ~/.openclaw/agents/*/sessions/

# 检查会话是否隔离（不同Agent应有独立会话文件）
```

---

## 十、实战经验总结

### 10.1 关键经验

#### ✅ 成功的关键点

1. **accountId是必填项**
   - 如果通道配置了多个accounts，bindings必须包含`accountId`
   - 缺少会导致所有消息路由到默认Agent

2. **性格差异化很重要**
   - 每个Agent的`SOUL.md`必须个性化
   - 相同的性格会让多Agent失去意义

3. **模型选择要匹配任务**
   - 简单任务用轻量模型（MiniMax）
   - 复杂任务用强模型（GLM-4.7）
   - 长文本任务用Kimi

4. **工作空间必须隔离**
   - 不要共享同一个`workspace`或`agentDir`
   - 会导致会话历史混乱

5. **路由规则要全面**
   - 精确匹配的规则放在前面
   - 通用规则放在后面作为fallback

#### ❌ 常见错误

1. **忘记添加accountId**
   - **症状：** 所有消息路由到main
   - **解决：** 在bindings中添加`accountId`

2. **所有Agent用同一性格**
   - **症状：** Agent回复风格相同
   - **解决：** 为每个Agent编辑独立的`SOUL.md`

3. **peer ID错误**
   - **症状：** 路由不生效
   - **解决：** 从飞书日志中复制正确的群组ID

4. **模型ID拼写错误**
   - **症状：** Agent提示模型不存在
   - **解决：** 使用`openclaw models list`确认正确ID

### 10.2 配置流程总结

**标准配置流程：**

```
1. 规划Agent
   ↓
2. 创建工作空间 (openclaw agents add)
   ↓
3. 编辑SOUL.md和IDENTITY.md
   ↓
4. 配置agents.list (openclaw.json)
   ↓
5. 配置bindings (openclaw.json)
   ↓
6. 配置models (openclaw.json)
   ↓
7. 重启Gateway (openclaw gateway restart)
   ↓
8. 验证路由 (openclaw agents list --bindings)
   ↓
9. 测试Agent (openclaw agent --agent xxx --message "...")
   ↓
10. 飞书群组验证
```

### 10.3 快速检查清单

配置完成后，检查以下项目：

- [ ] 所有Agent已创建（`openclaw agents list`）
- [ ] 每个Agent有独立的工作空间和agentDir
- [ ] 每个Agent有个性化的SOUL.md和IDENTITY.md
- [ ] agents.list中配置了正确的model字段
- [ ] bindings中配置了accountId（如有多账户）
- [ ] bindings中的peer ID与飞书群组ID匹配
- [ ] Gateway已重启（`openclaw gateway restart`）
- [ ] 各Agent可通过CLI测试（`openclaw agent --agent xxx`）
- [ ] 飞书群组中路由正确（@机器人观察回复风格）

### 10.4 故障排查流程

```
Agent不回复？
    ↓
检查模型可用性 → openclaw agent --agent xxx --message "测试"
    ↓
    如果模型正常
    ↓
检查路由 → openclaw agents list --bindings
    ↓
    查看日志 → tail -100 /tmp/openclaw/openclaw-*.log | grep dispatching
    ↓
    如果路由到正确agent
    ↓
检查飞书权限 → 确认机器人有发消息权限
```

---

## 十一、参考资源

### 官方文档

- OpenClaw多Agent路由：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/multi-agent.md`
- Agent运行时：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/agent.md`
- Agent工作空间：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/agent-workspace.md`
- 沙盒配置：`~/.npm-global/lib/node_modules/openclaw/docs/gateway/sandboxing.md`

### 实用命令

```bash
# Agent管理
openclaw agents list                    # 列出所有Agent
openclaw agents list --bindings         # 列出Agent和绑定关系

# Agent测试
openclaw agent --agent <id> --message "<text>"  # 测试特定Agent

# Gateway管理
openclaw gateway status                 # 查看Gateway状态
openclaw gateway restart                # 重启Gateway（应用配置更改）

# 模型管理
openclaw models list                    # 列出所有可用模型

# 通道状态
openclaw channels status                # 查看通道状态
openclaw channels status --probe        # 实时探针测试

# 日志查看
tail -100 /tmp/openclaw/openclaw-$(date +%Y-%m-%d).log  # 查看当日日志

# 配置验证
cat ~/.openclaw/openclaw.json | jq .   # 验证JSON语法
openclaw status                        # 完整状态检查
```

---

## 十二、附录：完整配置示例

### 12.1 minimal配置（仅CLI使用）

```json
{
  "agents": {
    "defaults": {
      "model": {"primary": "nvidia/z-ai/glm4.7"},
      "workspace": "/home/jack/.openclaw/workspace"
    },
    "list": [
      {"id": "main"},
      {
        "id": "ops",
        "workspace": "/home/jack/.openclaw/workspace-ops",
        "agentDir": "/home/jack/.openclaw/agents/ops/agent",
        "model": "nvidia/minimaxai/minimax-m2.1"
      }
    ]
  }
}
```

### 12.2 完整配置（飞书集成）

见本文档[5.1 openclaw.json完整结构](#51-openclawjson完整结构)

### 12.3 个性化SOUL.md示例

**ops/SOUL.md**

```markdown
# SOUL.md - 运维小帮手

名字：运维小帮手 🔧
风格：严谨专业、简洁高效、安全第一
称呼：老板

工作方式：
- 直奔主题，不废话
- 危险操作前必须确认
- 执行前先解释命令作用
```

**code/SOUL.md**

```markdown
# SOUL.md - 代码专家

名字：代码专家 💻
风格：技术强、追求质量、注重规范
称呼：大佬

代码原则：
- 遵守PEP8规范
- 优先考虑可维护性
- 写测试用例
```

**test/SOUL.md**

```markdown
# SOUL.md - 质量守护者

名字：质量守护者 ✅
风格：细致全面、严谨客观、不放过bug
称呼：QA或Team

测试理念：
- 测试覆盖率>80%
- 边界条件不能漏
- 质量第一，速度第二
```

---

## 结语

通过本文档，你应该能够：

✅ 理解OpenClaw多Agent的核心概念
✅ 成功创建和配置多个独立Agent
✅ 配置正确的路由规则
✅ 选择合适的LLM模型
✅ 排查和解决常见问题

**记住：**
- **accountId是必填项**（多账户场景）
- **性格差异化很重要**
- **工作空间必须隔离**
- **路由规则由具体到一般**

祝使用愉快！🎉

---

_文档版本：1.0_
_最后更新：2026-03-09_
_作者：主人 + 咪咪 ❤️_
