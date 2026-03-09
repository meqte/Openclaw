# OpenClaw Skills 安装与配置指南

本指南提供 OpenClaw 核心技能的安装与配置参考。所有技能统一安装至 `~/.openclaw/skills/` 目录。

## 📦 技能列表 (分类浏览)

### 🛡️ 安全与核心进化 (Safety & Evolution)
| Skill 名称 | 功能描述 | 备注 |
|-----------|---------|------|
| **skill-safety-verifier** | 安全审查，防止恶意脚本 | - |
| **security-scanner** | 运行环境实时监控 | - |
| **self-improving-agent** | 自我优化与反思机制 | - |
| **find-skills** | 技能发现与匹配 | - |

### 🌐 搜索与浏览器 (Search & Browsing)
| Skill 名称 | 功能描述 | 备注 |
|-----------|---------|------|
| **agent-browser** | 自动化网页操作 | - |
| **tavily-search** | LLM 优化搜索 | 需 API Key |
| **multi-search-engine** | 聚合搜索引擎 | - |

### ✍️ 信息处理与生产力 (Productivity & Tools)
| Skill 名称 | 功能描述 | 备注 |
|-----------|---------|------|
| **summarize** | 长文本/网页摘要 | 需配置 |
| **obsidian-bases** | 第二大脑知识库 | - |
| **github** 🐙 | 代码库与版本管理集成 | 需配置 Token |
| **qmd** | 文档排版与处理 | - |

### 🤖 自动化与通信 (Automation & Communication)
| Skill 名称 | 功能描述 | 备注 |
|-----------|---------|------|
| **clawflows** | 自动化工作流编排 | - |
| **proactive-agent** 🦞 | 主动执行与提醒机制 | - |
| **imap-smtp-email** | 邮件收发处理 | 需配置 |

---

## 🚀 安装方法

### 步骤 1: 安装技能管理器 (ClawHub)
在使用技能安装命令前，请确保已安装官方管理器：
```bash
npm i -g clawhub
```

### 步骤 2: 批量安装推荐列表
使用 **`clawhub`** 命令进行快速部署。

```bash
# 核心、安全与进化
clawhub install skill-safety-verifier security-scanner self-improving-agent find-skills

# 搜索与信息处理
clawhub install agent-browser tavily-search multi-search-engine summarize obsidian-bases github qmd

# 自动化与通信
clawhub install clawflows proactive-agent imap-smtp-email
```

---

## ⚙️ 技能配置 (仅需 API / 认证的技能)

### 1. imap-smtp-email（邮件）
```bash
nano ~/.openclaw/skills/imap-smtp-email/.env
```

### 2. summarize（总结）
```bash
nano ~/.openclaw/skills/summarize/config.json
```

### 3. tavily-search（搜索 API）
```bash
nano ~/.openclaw/skills/tavily-search/config.json
```

### 4. github 🐙（GitHub 认证）
```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
git config --global github.token "your-github-token"
```

---

## 🔄 生效设置

安装和配置完成后，重启 OpenClaw 即可使用：

```bash
openclaw gateway restart
```

---

## 🔍 验证安装
查看当前已安装的技能状态：
```bash
openclaw skills
```