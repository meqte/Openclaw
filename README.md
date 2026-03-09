# OpenClaw 完整配置指南

本指南帮助用户在 Debian 13 系统上部署和配置 OpenClaw，支持国内 LLM API (NVIDIA)、Qwen Portal 和飞书集成。

> **当前配置版本**: 2026.3.1  
> **更新时间**: 2026-03-09

## 目录

- [1. 简介](#1-简介)
- [2. 系统要求](#2-系统要求)
- [3. 安装 (Debian 13)](#3-安装-debian-13)
- [4. 免密码使用 sudo 配置](#4-免密码使用-sudo-配置)
- [5. 配置向导](#5-配置向导)
- [6. 模型配置](#6-模型配置)
  - [6.1 NVIDIA API (推荐)](#61-nvidia-api-推荐)
  - [6.2 Qwen Portal](#62-qwen-portal)
- [7. 局域网访问配置](#7-局域网访问配置)
- [8. 飞书集成](#8-飞书集成)
- [9. 验证与测试](#9-验证与测试)
- [10. 常见问题](#10-常见问题)
- [11. 技能扩展 (Skills)](#11-技能扩展-skills)

---

## 1. 简介

OpenClaw 是一个 AI 代理工具，支持多种 LLM 提供商。本指南涵盖：

- ✅ Debian 13 服务器部署
- ✅ 免密码 sudo 配置
- ✅ NVIDIA API 配置 (免费额度)
- ✅ 阿里云 Qwen Portal 配置
- ✅ 飞书聊天集成
- ✅ 局域网访问配置
- ✅ 核心技能 (Skills) 生态扩展

---

## 2. 系统要求

| 组件 | 要求 |
|------|------|
| Node.js | ≥ 22.0.0 |
| 操作系统 | Debian 13 |
| 网络 | 可访问互联网 (API 调用) |

---

## 3. 安装 (Debian 13)

### 3.1 安装 Node.js

```bash
# 使用 nvm 安装 Node.js 22
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22

# 验证
node --version
```

### 3.2 安装 OpenClaw

```bash
npm install -g openclaw
```

### 3.3 安装系统服务

```bash
# 安装为系统服务
sudo openclaw gateway install-systemd

# 启动服务
sudo openclaw gateway start
```

---

## 4. 免密码使用 sudo 配置

为了让 OpenClaw 或当前用户在执行需要提升权限的操作时免去输入密码的烦恼，可以通过 `visudo` 配置无密码执行。

1. **以 root 身份编辑 sudoers 文件**：
   ```bash
   sudo visudo
   ```
2. **在文件末尾添加规则**（假设当前用户名为 `your_username`，请将其替换为实际的用户名）：
   ```text
   your_username ALL=(ALL) NOPASSWD: ALL
   ```
   *(如果是在特定用户组，比如 `sudo` 组，也可以修改 `%sudo` 这一行变为 `%sudo ALL=(ALL:ALL) NOPASSWD: ALL`)*

3. **保存并退出**（如果是 nano 编辑器，按 `Ctrl+O` 回车保存，`Ctrl+X` 退出）。配置立即生效。

---

## 5. 配置向导

首次安装后，推荐使用配置向导完成基础设置:

```bash
openclaw onboard
```

### 向导配置选项

1. **选择安全选项** - 理解风险
2. **配置工作区目录** - 设置工作文件存放位置
3. **配置模型** - 选择 API 提供商
4. **配置局域网访问** - 开启 LAN Access
5. **设置认证方式** - Token 认证

### 手动启动向导 (指定参数)

```bash
# 完整安装模式
openclaw onboard --install-daemon

# 仅配置模型
openclaw onboard --provider nvidia
```

---

## 6. 模型配置

以下是基于用户实际配置的模型设置。

### 6.1 NVIDIA API (推荐)

NVIDIA 提供免费 API 额度，支持 GLM-4.7、MiniMax-M2.1、Kimi-K2.5 等模型。

#### 6.1.1 获取 API Key

1. 访问 https://build.nvidia.com/
2. 注册账户
3. 选择模型，点击 "Get API Key"
4. 复制 API Key

#### 6.1.2 配置模型

```bash
openclaw models add nvidia \
  --api-key YOUR_NVIDIA_API_KEY \
  --base-url https://integrate.api.nvidia.com/v1 \
  --models "nvidia/z-ai/glm4.7,nvidia/minimaxai/minimax-m2.1,nvidia/moonshotai/kimi-k2.5"
```

#### 6.1.3 手动配置文件

编辑 `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "providers": {
      "nvidia": {
        "baseUrl": "https://integrate.api.nvidia.com/v1",
        "apiKey": "YOUR_NVIDIA_API_KEY",
        "api": "openai-completions",
        "models": [
          {
            "id": "z-ai/glm4.7",
            "name": "GLM-4.7",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "minimaxai/minimax-m2.1",
            "name": "MiniMax-M2.1",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "moonshotai/kimi-k2.5",
            "name": "Kimi-K2.5",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "nvidia/z-ai/glm4.7"
      },
      "models": {
        "nvidia/z-ai/glm4.7": {
          "alias": "glm"
        }
      }
    }
  }
}
```

#### 6.1.4 重启 Gateway

```bash
openclaw gateway restart
```

---

### 6.2 Qwen Portal

阿里云 Qwen 门户，提供免费 coder 和 vision 模型。

#### 6.2.1 配置 Qwen Portal

```json
{
  "models": {
    "providers": {
      "qwen-portal": {
        "baseUrl": "https://portal.qwen.ai/v1",
        "apiKey": "qwen-oauth",
        "api": "openai-completions",
        "models": [
          {
            "id": "coder-model",
            "name": "Qwen Coder",
            "reasoning": false,
            "input": ["text"],
            "contextWindow": 128000,
            "maxTokens": 8192
          },
          {
            "id": "vision-model",
            "name": "Qwen Vision",
            "reasoning": false,
            "input": ["text", "image"],
            "contextWindow": 128000,
            "maxTokens": 8192
          }
        ]
      }
    }
  }
}
```

#### 6.2.2 OAuth 认证

Qwen Portal 使用 OAuth 认证，首次使用时会打开浏览器进行授权。

---

## 7. 局域网访问配置

允许在同一网络内的其他设备访问 OpenClaw Web UI。

### 7.1 启用局域网访问

```bash
openclaw onboard
```

选择 `Enable LAN Access` 选项。

### 7.2 手动配置

编辑 `~/.openclaw/openclaw.json`:

```json
{
  "gateway": {
    "port": 8000,
    "mode": "local",
    "bind": "lan",
    "auth": {
      "mode": "token",
      "token": "123456"
    },
    "controlUi": {
      "dangerouslyAllowHostHeaderOriginFallback": true,
      "allowInsecureAuth": true,
      "dangerouslyDisableDeviceAuth": true
    }
  }
}
```

### 7.3 配置说明

| 配置项 | 说明 | 推荐值 |
|--------|------|--------|
| `gateway.port` | Gateway 端口 | `8000` |
| `gateway.bind` | 绑定模式 | `lan` (监听所有网卡) |
| `gateway.auth.token` | 访问令牌 | 使用强随机字符串 |

### 7.4 获取访问信息

```bash
hostname -I | awk '{print $1}'
```

### 7.5 访问 Web UI

```
http://<你的局域网IP>:8000/
```

例如: `http://192.168.16.2:8000/`

### 7.6 防火墙配置

```bash
sudo ufw allow 8000/tcp
sudo ufw reload
```

---

## 8. 飞书集成

OpenClaw 支持飞书机器人，可以在飞书群聊中直接使用 AI 助手。

### 8.1 创建飞书应用

1. 访问 https://open.feishu.cn/
2. 创建企业自建应用
3. 获取 `App ID` 和 `App Secret`

### 8.2 配置应用权限

在飞书开放平台的应用管理中，添加以下权限:

- `im:chat:readonly` - 读取群信息
- `im:chat:send_as_bot` - 发送消息
- `im:message:send_as_bot` - 以机器人身份发消息
- `im:message:receive` - 接收消息

### 8.3 配置 OpenClaw

编辑 `~/.openclaw/openclaw.json`:

```json
{
  "channels": {
    "feishu": {
      "enabled": true,
      "appId": "cli_xxxxxxxxxxxxxxxxxx",
      "appSecret": "your_app_secret_here",
      "domain": "feishu",
      "connectionMode": "websocket",
      "dmPolicy": "open",
      "groupPolicy": "allowlist",
      "allowFrom": ["*"],
      "requireMention": true
    }
  }
}
```

### 8.4 发布应用

1. 在飞书开放平台创建应用版本
2. 发布应用到企业全员或指定群聊
3. 获取应用 `App ID` 和 `App Secret`

### 8.5 重启 Gateway

```bash
openclaw gateway restart
```

### 8.6 验证飞书集成

```bash
openclaw status
```

查看 Channels 部分应显示:

```
┌──────────┬─────────┬────────┬─────────────┐
│ Channel  │ Enabled │ State  │ Detail      │
├──────────┼─────────┼────────┼─────────────┤
│ Feishu   │ ON      │ OK     │ configured  │
└──────────┴─────────┴────────┴─────────────┘
```

### 8.7 使用飞书对话

在飞书群聊中 @机器人 即可开始对话:

```
@AI助手 帮我写一个 Python 脚本
```

---

## 9. 验证与测试

### 9.1 检查 Gateway 状态

```bash
openclaw gateway status
```

预期输出:

```
Gateway: bind=lan (0.0.0.0), port=8000
Dashboard: http://192.168.16.2:8000/
```

### 9.2 查看模型列表

```bash
openclaw models list
```

预期输出:

```
Model                         Input      Ctx      Local Auth  Tags
nvidia/z-ai/glm4.7            text       128k     no    yes   default,configured,alias:glm
```

### 9.3 测试模型

```bash
# 测试默认模型
openclaw agent --message "你好，请介绍一下自己" --session-id test
```

### 9.4 完整状态检查

```bash
openclaw status
```

---

## 10. 常见问题

### Q1: Gateway 启动失败

**问题**: `openclaw gateway start` 失败

**解决方案**:

1. 检查端口占用:
   ```bash
   sudo netstat -tlnp | grep :8000
   ```

2. 使用其他端口:
   ```bash
   openclaw gateway set-port 18789
   ```

### Q2: 模型无法连接

**问题**: 调用模型时显示连接错误

**解决方案**:

1. 检查网络连接:
   ```bash
   curl https://integrate.api.nvidia.com/v1/models
   ```

2. 检查 API Key 是否正确
3. 重启 Gateway: `openclaw gateway restart`

### Q3: 局域网无法访问

**问题**: 其他设备无法访问 Web UI

**解决方案**:

1. 确认 `bind` 设置为 `lan`:
   ```bash
   openclaw gateway status
   ```

2. 检查防火墙:
   ```bash
   sudo ufw allow 8000/tcp
   ```

3. 重启 Gateway: `openclaw gateway restart`

### Q4: 飞书消息收不到

**问题**: 飞书群聊中 @机器人 没有反应

**解决方案**:

1. 检查飞书配置:
   ```bash
   openclaw status
   ```

2. 确认应用已发布:
   - 登录飞书开放平台
   - 检查应用版本状态
   - 确认已发布到对应群聊

3. 检查权限配置:
   - 确认已添加所有必要权限
   - 重新发布应用版本

4. 查看日志:
   ```bash
   openclaw logs --follow
   ```

### Q5: 如何更新 OpenClaw

```bash
# 检查更新
openclaw update

# 或手动更新
npm install -g openclaw@latest
```

### Q6: 配置文件位置

| 操作系统 | 配置文件路径 |
|----------|--------------|
| Linux | `~/.openclaw/openclaw.json` |

### Q7: 安全建议

⚠️ 启用局域网访问后，请注意:

- 仅在受信任的网络中使用
- 使用强随机 Token
- 定期更新 OpenClaw
- 不要在公共 WiFi 下使用

---

## 11. 技能扩展 (Skills)

OpenClaw 的核心理念是“极简核心 + 插件化扩展”。安装核心网关和服务后，你可以通过安装各种技能 (Skills) 来赋予 AI 代理联网搜索、自动执行、邮件收发、处理本地文件等能力。

关于核心官方技能的**批量安装指令、详细分类及 API 配置说明**，请直接参阅专门的技能手册：

👉 **[Skills.md 完整安装与配置指南](./Skills.md)**

---

## 附录

### 常用命令

| 命令 | 说明 |
|------|------|
| `openclaw --version` | 查看版本 |
| `openclaw gateway start` | 启动 Gateway |
| `openclaw gateway restart` | 重启 Gateway |
| `openclaw gateway status` | 查看状态 |
| `openclaw models list` | 列出模型 |
| `openclaw status` | 完整状态 |
| `openclaw logs --follow` | 查看日志 |

### 相关链接

- [OpenClaw 官方文档](https://docs.openclaw.ai)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [NVIDIA AI API](https://build.nvidia.com/)
- [飞书开放平台](https://open.feishu.cn/)

---

## 版本历史

| 日期 | 版本 | 变更 |
|------|------|------|
| 2026-03-09 | 1.1.0 | 专注 Debian 服务器部署，移除 Windows 与 Ollama 配置，更新端口为 8000，新增 sudo 免密配置 |
