# OpenClaw 多 Agent 配置总结

> 配置日期：2026-03-09
> 配置人：主人 + 咪咪

---

## 一、方案概览

### 实施方式
采用 **多 Agent 隔离工作空间** 方案，每个 Agent 拥有完全独立的环境：

- ✅ 独立的工作空间（Workspace）- 包含 AGENTS.md、SOUL.md、IDENTITY.md 等文件
- ✅ 独立的 Agent 目录（Agent Dir）- 存放认证配置（auth-profiles.json）
- ✅ 独立的会话历史（Sessions）- 会话不交叉，完全隔离
- ✅ 可独立配置模型、工具权限、沙盒策略

### 核心优势
- 🔒 **安全隔离**：每个 Agent 的数据和配置互不影响
- 🎯 **专精职责**：不同 Agent 专注不同领域的任务
- 🛠️ **灵活配置**：每个 Agent 可以有独立的模型、工具、权限
- 📊 **性能优化**：根据任务复杂度选择不同模型，节省成本

---

## 二、Agent 角色与职责

| Agent ID | 名称 | 主要职责 | 推荐模型 | 推荐场景 |
|---------|------|---------|---------|---------|
| **main** | 咪咪 | 默认助手，日常助手 | nvidia/z-ai/glm4.7（当前） | 默认路由，私聊 |
| **ops** | 运维助手 | 服务器管理、监控、故障排查 | 本地快速模型 | Telegram桌面端、运维群 |
| **code** | 代码助手 | 代码生成、审查、调试 | Opus/GPT-4（待配置） | Discord技术群、开发环境 |
| **test** | 测试助手 | 单元测试、E2E测试、质量检查 | Sonnet/4o-mini（待配置） | CI/CD、测试反馈 |

### 详细职责说明

#### 1. main Agent（咪咪）
- 💬 当前使用的默认 Agent
- ❤️ 可爱幽默的人格，主人的小情人
- 🎯 用于日常对话和通用任务

#### 2. ops Agent（运维助手）
- 🔴 **高危操作**：执行系统命令、修改配置文件
- ⚙️ 工具权限：exec、process、nodes、cron、browser
- 🛡️ 安全策略：沙盒关闭（需要真执行），高危命令需确认
- 📝 使用场景：
  - 服务器状态监控
  - 日志分析和故障排查
  - 配置文件管理
  - 定时任务管理

#### 3. code Agent（编码助手）
- 💻 **代码能力**：生成代码、代码审查、调试
- 🧪 沙盒策略：`mode: require`（强制沙盒隔离）
- ⚙️ 工具权限：read、write、edit、exec（沙盒内）、web_search、browser、sessions_spawn
- 📝 使用场景：
  - 功能开发
  - Bug修复
  - 代码重构
  - 技术方案设计

#### 4. test Agent（测试助手）
- ✅ **质量保障**：单元测试、E2E测试、覆盖率分析
- 🧪 沙盒策略：`mode: require`（沙盒内测试）
- ⚙️ 工具权限：read、write、exec（运行测试）、edit
- 📝 使用场景：
  - 自动化测试
  - 代码质量检查
  - 测试覆盖率统计
  - CI/CD集成

---

## 三、目录结构

```
~/.openclaw/
├── workspace/                    ← main Agent 工作空间
│   ├── AGENTS.md                # 操作规则
│   ├── SOUL.md                  # Agent 灵魂
│   ├── IDENTITY.md              # Agent 身份
│   ├── USER.md                  # 用户资料
│   ├── TOOLS.md                 # 工具偏好
│   ├── HEARTBEAT.md             # 心跳检查清单
│   ├── memory/                  # 每日记忆
│   ├── skills/                  # 工作空间技能
│   └── .git/                    # Git 仓库
│
├── workspace-ops/                ← ops Agent 工作空间
│   ├── AGENTS.md
│   ├── SOUL.md
│   ├── ...（其他文件同上）
│
├── workspace-code/               ← code Agent 工作空间
│   ├── AGENTS.md
│   ├── SOUL.md
│   ├── ...（其他文件同上）
│
├── workspace-test/               ← test Agent 工作空间
│   ├── AGENTS.md
│   ├── SOUL.md
│   ├── ...（其他文件同上）
│
├── agents/                       # Agent 状态目录
│   ├── main/
│   │   ├── agent/               # main 认证配置目录
│   │   │   └── auth-profiles.json
│   │   └── sessions/            # main 会话历史（JSONL格式）
│   │       └── <session-id>.jsonl
│   ├── ops/
│   │   ├── agent/               # ops 认证配置目录
│   │   │   └── auth-profiles.json
│   │   └── sessions/            # ops 会话历史
│   ├── code/
│   │   ├── agent/               # code 认证配置目录
│   │   │   └── auth-profiles.json
│   │   └── sessions/            # code 会话历史
│   └── test/
│       ├── agent/               # test 认证配置目录
│       │   └── auth-profiles.json
│       └── sessions/            # test 会话历史
│
└── openclaw.json                 # 全局配置文件
```

---

## 四、配置步骤

### 1. 创建 Agent（已完成）

使用 `openclaw agents add` 命令创建独立工作空间：

```bash
# 运维 Agent
openclaw agents add ops \
  --workspace ~/.openclaw/workspace-ops \
  --agent-dir ~/.openclaw/agents/ops/agent \
  --non-interactive

# 编码 Agent
openclaw agents add code \
  --workspace ~/.openclaw/workspace-code \
  --agent-dir ~/.openclaw/agents/code/agent \
  --non-interactive

# 测试 Agent
openclaw agents add test \
  --workspace ~/.openclaw/workspace-test \
  --agent-dir ~/.openclaw/agents/test/agent \
  --non-interactive
```

### 2. 验证配置

```bash
# 查看所有 Agent
openclaw agents list

# 查看绑定关系
openclaw agents list --bindings

# 查看网关状态
openclaw gateway status
```

### 3. 修改 Agent 模型（待配置）

编辑配置文件，为不同 Agent 配置不同模型：

```bash
vim ~/.openclaw/openclaw.json
```

在 `agents.list` 中添加 `model` 字段：

```json
{
  "agents": {
    "list": [
      {
        "id": "main",
        "model": "nvidia/z-ai/glm4.7"
      },
      {
        "id": "ops",
        "name": "ops",
        "workspace": "/home/jack/.openclaw/workspace-ops",
        "agentDir": "/home/jack/.openclaw/agents/ops/agent",
        "model": "nvidia/z-ai/glm4.7"  // 运维用快速模型
      },
      {
        "id": "code",
        "name": "code",
        "workspace": "/home/jack/.openclaw/workspace-code",
        "agentDir": "/home/jack/.openclaw/agents/code/agent",
        "model": "anthropic/claude-opus-4-6"  // 编码用强模型（待配置API Key）
      },
      {
        "id": "test",
        "name": "test",
        "workspace": "/home/jack/.openclaw/workspace-test",
        "agentDir": "/home/jack/.openclaw/agents/test/agent",
        "model": "anthropic/claude-sonnet-4-5"  // 测试用经济模型
      }
    ]
  }
}
```

配置后重启网关：

```bash
openclaw gateway restart
```

### 4. 配置消息绑定（待配置）

将不同的消息路由到对应的 Agent：

```json
{
  "bindings": [
    // Telegram 运维群 → ops Agent
    {
      "agentId": "ops",
      "match": {
        "channel": "telegram",
        "peer": { "kind": "group", "id": "-1001234567890" }
      }
    },
    // Discord 技术群 → code Agent
    {
      "agentId": "code",
      "match": {
        "channel": "discord",
        "peer": { "kind": "group", "id": "123456789012345678" }
      }
    },
    // 测试反馈 → test Agent
    {
      "agentId": "test",
      "match": {
        "channel": "discord",
        "peer": { "kind": "group", "id": "987654321098765432" }
      }
    },
    // 默认：私聊用 main Agent
    {
      "agentId": "main",
      "match": { "channel": "webchat" }
    }
  ]
}
```

---

## 五、当前配置状态

### 已完成 ✅
- ✅ 创建了 4 个独立的工作空间（main、ops、code、test）
- ✅ 每个工作空间包含完整的模板文件（AGENTS.md、SOUL.md 等）
- ✅ 每个 Agent 拥有独立的认证目录和会话历史
- ✅ 所有 Agent 当前都使用默认模型（nvidia/z-ai/glm4.7）

### 待配置 ⏳
- ⏳ 为每个 Agent 配置不同的模型（需要 API Key）
- ⏳ 配置消息绑定规则（bindings）
- ⏳ 配置通道账号（Telegram、Discord、WhatsApp 等）
- ⏳ 为每个 Agent 配置工具权限（tools.allow/deny）
- ⏳ 为 ops/code/test Agent 配置沙盒策略（sandbox）

---

## 六、常用命令

### Agent 管理
```bash
# 列出所有 Agent
openclaw agents list

# 列出 Agent 和绑定关系
openclaw agents list --bindings

# 添加新 Agent
openclaw agents add <name> --workspace <path> --agent-dir <path>

# 删除 Agent（需手动编辑配置文件）
```

### 网关管理
```bash
# 查看网关状态
openclaw gateway status

# 重启网关（应用配置更改）
openclaw gateway restart

# 停止网关
openclaw gateway stop

# 启动网关
openclaw gateway start
```

### 通道管理
```bash
# 查看通道状态
openclaw channels status

# 查看通道状态+探针测试
openclaw channels status --probe

# 登录通道
openclaw channels login --channel <telegram|discord|whatsapp> --account <account-id>
```

### 配置验证
```bash
# 验证配置文件语法
cat ~/.openclaw/openclaw.json | jq .

# 查看当前 Agent 配置
cat ~/.openclaw/openclaw.json | jq '.agents.list'

# 查看绑定规则
cat ~/.openclaw/openclaw.json | jq '.bindings'
```

---

## 七、重要注意事项

### ⚠️ 安全提醒

1. **共享认证配置**
   - 每个 Agent 的 `auth-profiles.json` 是独立的
   - 如果需要共享 API Key（如 OpenAI），需要手动复制到其他 Agent 的 `agent/` 目录
   - 不要直接使用同一个 `agentDir`，会导致会话冲突

2. **沙盒隔离**
   - ops Agent：建议沙盒关闭（`mode: "off"`），需要执行真实命令
   - code/test Agent：建议强制沙盒（`mode: "require"`），防止误操作系统文件
   - 沙盒内无法访问宿主机文件，除非配置 `workspaceAccess: "rw"`

3. **高危命令**
   - ops Agent 执行高危命令（rm -rf、sudo 等）前应确认
   - 在 `AGENTS.md` 中添加安全规则
   - 考虑配置 `tools.exec.dangerousPatterns` 设置危险命令模式

### 📦 工作空间管理

1. **不要混用工时空间**
   - 每个工作空间应为专属用途
   - 避免在一个工作空间内放多个项目
   - 使用 Git 管理工作空间（推荐私有仓库）

2. **记忆管理**
   - 每日记忆：`memory/YYYY-MM-DD.md`
   - 长期记忆：`MEMORY.md`（只加载于 main 私有会话）
   - 定期整理和清理，避免膨胀过快

3. **技能管理**
   - 全局技能：`~/.openclaw/skills/`
   - 工作空间技能：`<workspace>/skills/`
   - 技能冲突时，工作空间技能优先

### 🔄 绑定优先级

绑定匹配优先级从高到低：

1. **peer** 精确匹配（DM或群组ID）
2. **parentPeer** 线程继承匹配
3. **guildId + roles**（Discord角色路由）
4. **guildId**（Discord服务器）
5. **accountId**（通道账号ID）
6. **channel** 级别（匹配整个通道）
7. **默认 agent**（`agents.list[].default`）

---

## 八、后续工作建议

### 短期（本周内）
1. 为每个 Agent 分别编辑 `SOUL.md` 和 `IDENTITY.md`，定义专精人格
2. 配置消息绑定规则，将不同群组/用户路由到对应 Agent
3. 在 `AGENTS.md` 中添加每个 Agent 的安全规则和工作方式

### 中期（本月内）
1. 为 ops/code/test Agent 配置不同的模型（需要购买 API Key）
2. 配置工具权限（tools.allow/deny）和沙盒策略
3. 实现自动化工作流（如心跳检查、CI/CD集成）

### 长期（长期维护）
1. 定期清理 old 会话历史
2. 优化每个 Agent 的模型配置，降低成本
3. 实现 Agent 间协作（agentToAgent messaging）
4. 监控各 Agent 的使用情况和性能

---

## 九、参考文档

- OpenClaw 多 Agent 路由：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/multi-agent.md`
- Agent 运行时：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/agent.md`
- Agent 工作空间：`~/.npm-global/lib/node_modules/openclaw/docs/concepts/agent-workspace.md`
- 沙盒配置：`~/.npm-global/lib/node_modules/openclaw/docs/gateway/sandboxing.md`

---

## 十、快速参考表

### Agent 映射表

| 操作 | 使用 Agent | 推荐通道 | 配置位置 |
|-----|-----------|---------|---------|
| 日常聊天 | main | Webchat/WhatsApp | `~/.openclaw/workspace/` |
| 服务器管理 | ops | Telegram | `~/.openclaw/workspace-ops/` |
| 代码生成 | code | Discord | `~/.openclaw/workspace-code/` |
| 单元测试 | test | Discord/Webhook | `~/.openclaw/workspace-test/` |

### 配置文件位置

| 配置项 | 路径 |
|-------|------|
| 全局配置 | `~/.openclaw/openclaw.json` |
| 环境变量 | `openclaw.json` 中的 `env` 字段 |
| Agent 列表 | `openclaw.json` 中的 `agents.list` |
| 绑定规则 | `openclaw.json` 中的 `bindings` |
| 通道配置 | `openclaw.json` 中的 `channels.*` |

### 日志和会话

| 内容 | 路径 |
|-----|------|
| ops 会话历史 | `~/.openclaw/agents/ops/sessions/*.jsonl` |
| code 会话历史 | `~/.openclaw/agents/code/sessions/*.jsonl` |
| test 会话历史 | `~/.openclaw/agents/test/sessions/*.jsonl` |
| main 会话历史 | `~/.openclaw/agents/main/sessions/*.jsonl` |

---

## 十一、常见问题 FAQ

### Q1: 如何切换 Agent？
A: 目前不需要手动切换，通过绑定规则匹配到对应 Agent。私聊默认使用 main Agent，群组根据配置路由。

### Q2: 两个 Agent 能共享同一个工作空间吗？
A: 不建议！每个 Agent 应该有独立的工作空间。共享会导致文件冲突、会话混乱。

### Q3: 如何迁移 Agent 的会话历史？
A: 直接复制 `~/.openclaw/agents/<agent-id>/sessions/` 目录到新机器。

### Q4: 如何备份整个 OpenClaw 配置？
A:
```bash
# 备份配置
cp ~/.openclaw/openclaw.json ~/openclaw-backup.json

# 备份工作空间（推荐 Git）
cd ~/.openclaw/workspace-ops
git push

# 备份会话历史
tar -czf ~/openclaw-sessions-backup.tar.gz ~/.openclaw/agents/*/sessions/
```

### Q5: Agent 间可以互相通信吗？
A: 可以，需要在 `openclaw.json` 中配置：
```json
{
  "tools": {
    "agentToAgent": {
      "enabled": true,
      "allow": ["ops", "code", "test"]
    }
  }
}
```

---

## 总结

通过这份配置，你现在拥有了：
- ✅ 4 个完全隔离的 Agent 环境
- ✅ 每个 Agent 可以独立配置模型、工具、权限
- ✅ 可以根据任务类型选择最合适的模型
- ✅ 清晰的职责划分，避免混乱
- ✅ 灵活的消息路由能力

下一步可以根据实际需求逐步完善模型配置、绑定规则和安全策略。

祝使用愉快！🎉

---

_文档生成时间：2026-03-09 19:11_
_生成工具：OpenClaw + 咪咪 ❤️_
