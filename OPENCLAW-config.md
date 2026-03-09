# OpenClaw 完整配置指南

本指南帮助用户在 Windows 和 Debian 13 系统上部署和配置 OpenClaw，支持国内 LLM API (NVIDIA)、本地模型 (Ollama) 和飞书集成。

> **当前配置版本**: 2026.3.1  
> **更新时间**: 2026-03-03

## 目录

- [1. 简介](#1-简介)
- [2. 系统要求](#2-系统要求)
- [3. 安装](#3-安装)
  - [3.1 Windows 安装](#31-windows-安装)
  - [3.2 Debian 13 安装](#32-debian-13-安装)
- [4. 配置向导](#4-配置向导)
- [5. 模型配置](#5-模型配置)
  - [5.1 NVIDIA API (推荐)](#51-nvidia-api-推荐)
  - [5.2 Ollama 本地模型](#52-ollama-本地模型)
  - [5.3 Qwen Portal](#53-qwen-portal)
- [6. 局域网访问配置](#6-局域网访问配置)
- [7. 飞书集成](#7-飞书集成)
- [8. 验证与测试](#8-验证与测试)
- [9. 常见问题](#9-常见问题)
- [10. 技能扩展 (Skills)](#10-技能扩展-skills)

---

## 1. 简介

OpenClaw 是一个 AI 代理工具，支持多种 LLM 提供商和本地模型部署。本指南涵盖：

- ✅ Windows 10/11 原生安装
- ✅ Debian 13 服务器部署
- ✅ NVIDIA API 配置 (免费额度)
- ✅ Ollama 本地模型集成
- ✅ 飞书聊天集成
- ✅ 局域网访问配置
- ✅ 核心技能 (Skills) 生态扩展

---

## 2. 系统要求

| 组件 | 要求 |
|------|------|
| Node.js | ≥ 22.0.0 |
| 操作系统 | Windows 10+ / Debian 13 |
| 网络 | 可访问互联网 (API 调用) |
| NVIDIA GPU | 可选 (本地模型加速) |

---

## 3. 安装

### 3.1 Windows 安装

#### 3.1.1 安装 Node.js

下载并安装 Node.js 22.x:

```bash
# 使用 winget (推荐)
winget install OpenJS.NodeJS.LTS

# 或从官网下载: https://nodejs.org/
```

验证安装:

```bash
node --version  # 应显示 v22.x.x
npm --version
```

#### 3.1.2 安装 OpenClaw

```bash
npm install -g openclaw
```

验证安装:

```bash
openclaw --version
```

#### 3.1.3 启动 Gateway

```bash
# 启动 Gateway 服务
openclaw gateway start

# 设置开机自启 (Windows 任务计划)
openclaw gateway install-task
```

---

### 3.2 Debian 13 安装

#### 3.2.1 安装 Node.js

```bash
# 使用 nvm 安装 Node.js 22
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22

# 验证
node --version
```

#### 3.2.2 安装 OpenClaw

```bash
npm install -g openclaw
```

#### 3.2.3 安装系统服务 (可选)

```bash
# 安装为系统服务
sudo openclaw gateway install-systemd

# 启动服务
sudo openclaw gateway start
```

---

## 4. 配置向导

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

## 5. 模型配置

以下是基于用户实际配置的模型设置。

### 5.1 NVIDIA API (推荐)

NVIDIA 提供免费 API 额度，支持 GLM-4.7、MiniMax-M2.1、Kimi-K2.5 等模型。

#### 5.1.1 获取 API Key

1. 访问 https://build.nvidia.com/
2. 注册账户
3. 选择模型，点击 "Get API Key"
4. 复制 API Key

#### 5.1.2 配置模型

```bash
openclaw models add nvidia \
  --api-key YOUR_NVIDIA_API_KEY \
  --base-url https://integrate.api.nvidia.com/v1 \
  --models "nvidia/z-ai/glm4.7,nvidia/minimaxai/minimax-m2.1,nvidia/moonshotai/kimi-k2.5"
```

#### 5.1.3 手动配置文件

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

#### 5.1.4 重启 Gateway

```bash
openclaw gateway restart
```

---

### 5.2 Ollama 本地模型

Ollama 允许在本地运行大语言模型，保护隐私，无需联网。

#### 5.2.1 安装 Ollama

**Windows**:

```bash
# 下载安装: https://ollama.com/download/windows
winget install Ollama.Ollama
```

**Debian**:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

#### 5.2.2 拉取模型

```bash
ollama pull qwen3:8b
ollama pull qwen2.5:14b
ollama pull llama3.1:8b
```

查看可用模型:

```bash
ollama list
```

#### 5.2.3 启动 Ollama 服务

```bash
# 默认端口 11434
ollama serve
```

#### 5.2.4 配置 OpenClaw

```bash
openclaw models add ollama \
  --base-url http://localhost:11434 \
  --models "qwen3:8b,qwen2.5:14b"
```

#### 5.2.5 手动配置文件

```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://localhost:11434",
        "apiKey": "ollama",
        "api": "ollama",
        "models": [
          {
            "id": "qwen3:8b",
            "name": "Qwen3 8B (Local)",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 32786,
            "maxTokens": 8192
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "models": {
        "ollama/qwen3:8b": {
          "alias": "ollama"
        }
      }
    }
  }
}
```

---

### 5.3 Qwen Portal

阿里云 Qwen 门户，提供免费 coder 和 vision 模型。

#### 5.3.1 配置 Qwen Portal

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

#### 5.3.2 OAuth 认证

Qwen Portal 使用 OAuth 认证，首次使用时会打开浏览器进行授权。

---

## 6. 局域网访问配置

允许在同一网络内的其他设备访问 OpenClaw Web UI。

### 6.1 启用局域网访问

```bash
openclaw onboard
```

选择 `Enable LAN Access` 选项。

### 6.2 手动配置

编辑 `~/.openclaw/openclaw.json`:

```json
{
  "gateway": {
    "port": 80,
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

### 6.3 配置说明

| 配置项 | 说明 | 推荐值 |
|--------|------|--------|
| `gateway.port` | Gateway 端口 | `80` (HTTP 默认) 或 `18789` |
| `gateway.bind` | 绑定模式 | `lan` (监听所有网卡) |
| `gateway.auth.token` | 访问令牌 | 使用强随机字符串 |

### 6.4 获取访问信息

**Windows**:

```cmd
ipconfig
```

**Debian**:

```bash
hostname -I | awk '{print $1}'
```

### 6.5 访问 Web UI

```
http://<你的局域网IP>:80/
```

例如: `http://192.168.16.2:80/`

### 6.6 防火墙配置

**Windows**:

```powershell
# 允许端口通过防火墙
netsh advfirewall firewall add rule name="OpenClaw" dir=in action=allow protocol=tcp localport=80
```

**Debian**:

```bash
sudo ufw allow 80/tcp
sudo ufw reload
```

---

## 7. 飞书集成

OpenClaw 支持飞书机器人，可以在飞书群聊中直接使用 AI 助手。

### 7.1 创建飞书应用

1. 访问 https://open.feishu.cn/
2. 创建企业自建应用
3. 获取 `App ID` 和 `App Secret`

### 7.2 配置应用权限

在飞书开放平台的应用管理中，添加以下权限:

- `im:chat:readonly` - 读取群信息
- `im:chat:send_as_bot` - 发送消息
- `im:message:send_as_bot` - 以机器人身份发消息
- `im:message:receive` - 接收消息

### 7.3 配置 OpenClaw

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

### 7.4 发布应用

1. 在飞书开放平台创建应用版本
2. 发布应用到企业全员或指定群聊
3. 获取应用 `App ID` 和 `App Secret`

### 7.5 重启 Gateway

```bash
openclaw gateway restart
```

### 7.6 验证飞书集成

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

### 7.7 使用飞书对话

在飞书群聊中 @机器人 即可开始对话:

```
@AI助手 帮我写一个 Python 脚本
```

---

## 8. 验证与测试

### 8.1 检查 Gateway 状态

```bash
openclaw gateway status
```

预期输出:

```
Gateway: bind=lan (0.0.0.0), port=80
Dashboard: http://192.168.16.2:80/
```

### 8.2 查看模型列表

```bash
openclaw models list
```

预期输出:

```
Model                         Input      Ctx      Local Auth  Tags
nvidia/z-ai/glm4.7            text       128k     no    yes   default,configured,alias:glm
ollama/qwen3:8b               text       32k      yes   yes   configured,alias:ollama
```

### 8.3 测试模型

```bash
# 测试默认模型
openclaw agent --message "你好，请介绍一下自己" --session-id test

# 指定模型
openclaw agent --message "你好" --model ollama/qwen3:8b --session-id test
```

### 8.4 完整状态检查

```bash
openclaw status
```

---

## 9. 常见问题

### Q1: Gateway 启动失败

**问题**: `openclaw gateway start` 失败

**解决方案**:

1. 检查端口占用:
   ```bash
   # Windows
   netstat -ano | findstr :80
   
   # Debian
   sudo netstat -tlnp | grep :80
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
   # NVIDIA API
   curl https://integrate.api.nvidia.com/v1/models
   
   # Ollama
   curl http://localhost:11434/api/tags
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
   # Windows
   netsh advfirewall firewall add rule name="OpenClaw" dir=in action=allow protocol=tcp localport=80
   
   # Debian
   sudo ufw allow 80/tcp
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

### Q5: Ollama 模型未显示

**问题**: `openclaw models list` 没有显示 Ollama 模型

**解决方案**:

1. 确认 Ollama 正在运行:
   ```bash
   curl http://localhost:11434/api/tags
   ```

2. 重新添加模型:
   ```bash
   openclaw models add ollama --models qwen3:8b
   ```

3. 重启 Gateway:
   ```bash
   openclaw gateway restart
   ```

### Q6: 如何更新 OpenClaw

```bash
# 检查更新
openclaw update

# 或手动更新
npm install -g openclaw@latest
```

### Q7: 配置文件位置

| 操作系统 | 配置文件路径 |
|----------|--------------|
| Windows | `%USERPROFILE%\.openclaw\openclaw.json` |
| Linux | `~/.openclaw/openclaw.json` |

### Q8: 安全建议

⚠️ 启用局域网访问后，请注意:

- 仅在受信任的网络中使用
- 使用强随机 Token
- 定期更新 OpenClaw
- 不要在公共 WiFi 下使用

---

## 10. 技能扩展 (Skills)

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
- [Ollama 官网](https://ollama.com)
- [飞书开放平台](https://open.feishu.cn/)

---

## 版本历史

| 日期 | 版本 | 变更 |
|------|------|------|
| 2026-03-03 | 1.0.0 | 初始版本，整合 Windows/Debian 安装、NVIDIA/Ollama 模型、飞书集成 |
