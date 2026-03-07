# OpenClaw Skills 完整安装指南

> 📦 **详细说明如何安装和运行所有 OpenClaw skills**

## 📋 目录

1. [需要安装的 Skills 列表](#需要安装的-skills-列表)
2. [每个 Skill 的详细信息](#每个-skill-的详细信息)
3. [安装步骤](#安装步骤)
4. [运行步骤](#运行步骤)
5. [故障排除](#故障排除)

---

## 需要安装的 Skills 列表

总共需要安装 **12 个 skills**：

| # | Skill 名称 | 功能描述 | 官方地址 |
|---|-----------|---------|---------|
| 1 | **obsidian-bases** | Obsidian 数据库管理 | [GitHub](https://github.com/openclaw/obsidian-bases) |
| 2 | **skill-safety-verifier** | 技能安全审查 | [GitHub](https://github.com/openclaw/skill-safety-verifier) |
| 3 | **find-skills** | 技能发现工具 | [GitHub](https://github.com/openclaw/find-skills) |
| 4 | **imap-smtp-email** | 邮件收发 | [GitHub](https://github.com/openclaw/imap-smtp-email) |
| 5 | **multi-search-engine** | 多引擎搜索 | [GitHub](https://github.com/openclaw/multi-search-engine) |
| 6 | **proactive-agent** 🦞 | 主动代理 | [GitHub](https://github.com/openclaw/proactive-agent) |
| 7 | **self-improving-agent** | 自我改进 | [GitHub](https://github.com/openclaw/self-improving-agent) |
| 8 | **qmd** | 本地搜索索引 | [GitHub](https://github.com/openclaw/qmd) |
| 9 | **summarize** | 网页总结 | [GitHub](https://github.com/openclaw/summarize) |
| 10 | **tavily-search** | AI 优化搜索 | [GitHub](https://github.com/openclaw/tavily-search) |
| 11 | **weather** | 天气查询 | [GitHub](https://github.com/openclaw/weather) |
| 12 | **github** 🐙 | GitHub 管理工具 | [GitHub](https://github.com/openclaw/github) |

---

## 每个 Skill 的详细信息

### 1. obsidian-bases

**功能描述**：
Obsidian 数据库管理工具，用于创建和编辑 `.base` 文件。`.base` 文件是 Obsidian 的数据库功能，类似于表格，但更强大。

**主要用途**：
- 创建数据库化的笔记
- 定义筛选器来选择特定的笔记
- 添加公式来计算属性
- 创建多种视图（表格、卡片、列表、地图）
- 管理大量笔记

**官方安装地址**：
- GitHub: https://github.com/openclaw/obsidian-bases
- 安装命令: `clawdhub install obsidian-bases` 或 `git clone https://github.com/openclaw/obsidian-bases.git ~/.openclaw/skills/obsidian-bases`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install obsidian-bases

# 方法 2: 使用 Git
git clone https://github.com/openclaw/obsidian-bases.git ~/.openclaw/skills/obsidian-bases

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/obsidian-bases/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：帮我创建一个 Obsidian 数据库
咪咪：[自动调用 obsidian-bases skill]
```

**使用示例**：
```
主人：创建一个笔记数据库，包含所有关于 AI 的笔记
咪咪：[调用 obsidian-bases skill]
  1. 创建 .base 文件
  2. 定义筛选器：tag = #AI
  3. 创建表格视图
  4. 显示标题、日期、标签等属性
```

---

### 2. skill-safety-verifier

**功能描述**：
技能安全审查工具，用于在安装新技能前进行安全检查。检查网络连接、代码漏洞、权限风险等。

**主要用途**：
- 安装新技能前进行安全审查
- 检查技能的网络连接安全性
- 查找代码漏洞
- 评估权限范围风险
- 提供安全建议

**官方安装地址**：
- GitHub: https://github.com/openclaw/skill-safety-verifier
- 安装命令: `clawdhub install skill-safety-verifier` 或 `git clone https://github.com/openclaw/skill-safety-verifier.git ~/.openclaw/skills/skill-safety-verifier`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install skill-safety-verifier

# 方法 2: 使用 Git
git clone https://github.com/openclaw/skill-safety-verifier.git ~/.openclaw/skills/skill-safety-verifier

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/skill-safety-verifier/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：我想安装这个技能 https://github.com/example/skill
咪咪：[自动调用 skill-safety-verifier skill]
  1. 检查网络连接
  2. 检查代码漏洞
  3. 评估权限风险
  4. 提供安全建议
```

**使用示例**：
```
主人：我想安装这个技能：https://github.com/example/skill
咪咪：[调用 skill-safety-verifier skill]
  ✅ 网络连接检查：正常
  ⚠️ 代码漏洞检查：发现 1 个潜在问题
  ⚠️ 权限风险：需要网络访问权限
  💡 建议：建议先审查代码后再安装
```

---

### 3. find-skills

**功能描述**：
技能发现工具，用于在 clawhub.com 搜索和安装新的 skills。

**主要用途**：
- 在 clawhub.com 搜索 skills
- 查看已安装的 skills
- 安装新发现的 skills
- 浏览技能分类和推荐

**官方安装地址**：
- GitHub: https://github.com/openclaw/find-skills
- 安装命令: `clawdhub install find-skills` 或 `git clone https://github.com/openclaw/find-skills.git ~/.openclaw/skills/find-skills`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install find-skills

# 方法 2: 使用 Git
git clone https://github.com/openclaw/find-skills.git ~/.openclaw/skills/find-skills

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/find-skills/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：我想安装一个新的 skill
咪咪：[自动调用 find-skills skill]
  1. 搜索 clawhub.com
  2. 显示可用 skills 列表
  3. 推荐合适的 skills
  4. 帮助安装选中的 skills
```

**使用示例**：
```
主人：我想安装一个新的 skill
咪咪：[调用 find-skills skill]
  🔍 搜索 clawhub.com...
  ✅ 找到 156 个 skills
  💡 推荐技能：
     - web-search: 网页搜索
     - code-review: 代码审查
     - data-analysis: 数据分析

主人：我想安装 web-search
咪咪：[自动调用 find-skills skill]
  ✅ 安装 web-search skill
  📝 配置完成
```

---

### 4. imap-smtp-email

**功能描述**：
邮件收发工具，支持通过 IMAP/SMTP 协议发送和接收邮件。

**主要用途**：
- 发送邮件
- 检查收件箱
- 搜索邮件
- 下载邮件附件
- 管理多个邮箱账户

**官方安装地址**：
- GitHub: https://github.com/openclaw/imap-smtp-email
- 安装命令: `clawdhub install imap-smtp-email` 或 `git clone https://github.com/openclaw/imap-smtp-email.git ~/.openclaw/workspace/skills/imap-smtp-email`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install imap-smtp-email

# 方法 2: 使用 Git
git clone https://github.com/openclaw/imap-smtp-email.git ~/.openclaw/workspace/skills/imap-smtp-email

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/imap-smtp-email/raw/main/INSTALL.sh)
```

**配置步骤**：
```bash
# 1. 编辑配置文件
nano ~/.openclaw/workspace/skills/imap-smtp-email/.env

# 2. 填入你的邮箱信息
EMAIL_USER=your-email@example.com
EMAIL_PASS=your-password
EMAIL_HOST=smtp.example.com
EMAIL_PORT=465

# 3. 重启 OpenClaw
openclaw gateway restart
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：帮我发一封邮件给老板，主题是"项目进度汇报"
咪咪：[自动调用 imap-smtp-email skill]
  📧 发送邮件...
  ✅ 邮件已发送！
```

**使用示例**：
```
主人：帮我发一封邮件给老板，主题是"项目进度汇报"，内容是"项目已完成 80%"
咪咪：[调用 imap-smtp-email skill]
  📧 准备发送邮件...
  收件人：boss@example.com
  主题：项目进度汇报
  内容：项目已完成 80%
  ✅ 邮件已发送！
```

---

### 5. multi-search-engine

**功能描述**：
多引擎搜索工具，支持 17 个搜索引擎（8 个中文 + 9 个全球）。

**主要用途**：
- 使用多个搜索引擎搜索
- 支持高级搜索语法
- 支持时间过滤
- 支持隐私引擎
- 支持搜索结果去重

**官方安装地址**：
- GitHub: https://github.com/openclaw/multi-search-engine
- 安装命令: `clawdhub install multi-search-engine` 或 `git clone https://github.com/openclaw/multi-search-engine.git ~/.openclaw/workspace/skills/multi-search-engine`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install multi-search-engine

# 方法 2: 使用 Git
git clone https://github.com/openclaw/multi-search-engine.git ~/.openclaw/workspace/skills/multi-search-engine

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/multi-search-engine/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：搜索关于 AI 的最新新闻
咪咪：[自动调用 multi-search-engine skill]
  🔍 使用 17 个搜索引擎搜索...
  ✅ 搜索完成！
```

**使用示例**：
```
主人：搜索关于 AI 的最新新闻
咪咪：[调用 multi-search-engine skill]
  🔍 使用多个搜索引擎搜索...
  📊 搜索结果：
     - Google: 100 条结果
     - Bing: 95 条结果
     - Baidu: 120 条结果
     - DuckDuckGo: 80 条结果
  ✅ 搜索完成！
```

---

### 6. proactive-agent 🦞

**功能描述**：
主动代理，让 AI 代理从被动执行者变成主动小帮手。

**主要用途**：
- 主动预判需求
- 自我改进
- 上下文管理
- WAL 协议（Write-Ahead Logging）
- Working Buffer Protocol
- Compaction Recovery Protocol

**官方安装地址**：
- GitHub: https://github.com/openclaw/proactive-agent
- 安装命令: `clawdhub install proactive-agent` 或 `git clone https://github.com/openclaw/proactive-agent.git ~/.openclaw/workspace/skills/proactive-agent`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install proactive-agent

# 方法 2: 使用 Git
git clone https://github.com/openclaw/proactive-agent.git ~/.openclaw/workspace/skills/proactive-agent

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/proactive-agent/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：我在写代码
咪咪：[自动调用 proactive-agent skill]
  🎯 主动预判你的需求...
  💡 建议你：
     1. 调试代码
     2. 格式化代码（PEP8）
     3. 检查语法错误
     4. 运行测试
```

**使用示例**：
```
主人：我在写代码
咪咪：[调用 proactive-agent skill]
  🎯 分析你的需求...
  💡 我注意到你在写代码，我可以帮你：
     1. 🔍 调试代码 - 检查语法和逻辑错误
     2. 📝 格式化代码 - 按照 PEP8 规范格式化
     3. ✅ 运行测试 - 执行测试用例
     4. 📊 代码审查 - 检查代码质量

你想让我做什么？
```

---

### 7. self-improving-agent

**功能描述**：
自我改进工具，用于记录学习笔记、错误日志、功能请求。

**主要用途**：
- 记录学习笔记
- 记录错误日志
- 记录功能请求
- 自动提升重要的经验到项目记忆
- 支持跨会话记忆

**官方安装地址**：
- GitHub: https://github.com/openclaw/self-improving-agent
- 安装命令: `clawdhub install self-improving-agent` 或 `git clone https://github.com/openclaw/self-improving-agent.git ~/.openclaw/workspace/skills/self-improving-agent`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install self-improving-agent

# 方法 2: 使用 Git
git clone https://github.com/openclaw/self-improving-agent.git ~/.openclaw/workspace/skills/self-improving-agent

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/self-improving-agent/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：这个方案有问题
咪咪：[自动调用 self-improving-agent skill]
  📝 记录错误日志...
  📚 更新学习笔记...
  ✅ 已保存到 .learnings/ 目录
```

**使用示例**：
```
主人：这个方案有问题
咪咪：[调用 self-improving-agent skill]
  📝 记录错误日志：
     [LRN-20260307-001] knowledge_gap
     **Logged**: 2026-03-07T14:00:00Z
     **Priority**: medium
     **Status**: pending
     **Area**: backend

     ### Summary
     用户指出当前方案有问题

     ### Details
     用户说"这个方案有问题"，但没有具体说明问题是什么

     ### Suggested Action
     询问用户具体是什么问题

     ### Metadata
     - Source: conversation
     - Related Files: script.py
     - Tags: bug, fix-needed

  📚 更新学习笔记...
  ✅ 已保存到 .learnings/LEARNINGS.md
```

---

### 8. qmd

**功能描述**：
本地搜索索引工具，基于 BM25 + 向量 + 重排序。

**主要用途**：
- 本地文件搜索
- 基于语义的搜索
- 向量检索
- BM25 算法
- 重排序

**官方安装地址**：
- GitHub: https://github.com/openclaw/qmd
- 安装命令: `clawdhub install qmd` 或 `git clone https://github.com/openclaw/qmd.git ~/.openclaw/workspace/skills/qmd`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install qmd

# 方法 2: 使用 Git
git clone https://github.com/openclaw/qmd.git ~/.openclaw/workspace/skills/qmd

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/qmd/raw/main/INSTALL.sh)
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：搜索我的笔记
咪咪：[自动调用 qmd skill]
  🔍 搜索笔记...
  ✅ 找到 5 条相关笔记
```

**使用示例**：
```
主人：搜索关于 AI 的笔记
咪咪：[调用 qmd skill]
  🔍 使用 BM25 + 向量 + 重排序搜索...
  📊 找到 5 条相关笔记：
     1. AI 应用场景.md (相似度: 0.95)
     2. AI 技术趋势.md (相似度: 0.88)
     3. AI 原理.md (相似度: 0.82)
     4. AI 实战案例.md (相似度: 0.76)
     5. AI 学习路径.md (相似度: 0.71)
```

---

### 9. summarize

**功能描述**：
网页总结工具，使用 AI 进行智能内容总结。

**主要用途**：
- 网页内容总结
- 文件内容总结
- PDF 总结
- YouTube 视频总结
- 支持多种格式

**官方安装地址**：
- GitHub: https://github.com/openclaw/summarize
- 安装命令: `clawdhub install summarize` 或 `git clone https://github.com/openclaw/summarize.git ~/.openclaw/workspace/skills/summarize`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install summarize

# 方法 2: 使用 Git
git clone https://github.com/openclaw/summarize.git ~/.openclaw/workspace/skills/summarize

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/summarize/raw/main/INSTALL.sh)
```

**配置步骤**：
```bash
# 1. 配置文件位置
nano ~/.summarize/config.json

# 2. 配置参数
{
  "model": "auto",
  "length": "xl",
  "output": {
    "language": "auto"
  },
  "firecrawl": "auto"
}

# 3. 重启 OpenClaw
openclaw gateway restart
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：帮我总结这个网页 https://example.com
咪咪：[自动调用 summarize skill]
  📝 总结网页...
  ✅ 总结完成！
```

**使用示例**：
```
主人：帮我总结这个网页 https://example.com
咪咪：[调用 summarize skill]
  📝 正在读取网页...
  🧠 使用 AI 进行总结...
  📊 总结结果：
     标题：AI 的未来
     关键点：
       1. AI 正在快速发展
       2. AI 将改变各行各业
       3. AI 的伦理问题需要关注
       4. AI 的未来充满希望

  ✅ 总结完成！
```

---

### 10. tavily-search

**功能描述**：
AI 优化搜索工具，通过 Tavily API 进行搜索，返回简洁相关的结果。

**主要用途**：
- AI 优化搜索
- 简洁相关的结果
- 支持时间过滤
- 支持搜索结果去重
- 支持隐私模式

**官方安装地址**：
- GitHub: https://github.com/openclaw/tavily-search
- 安装命令: `clawdhub install tavily-search` 或 `git clone https://github.com/openclaw/tavily-search.git ~/.openclaw/workspace/skills/tavily-search`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install tavily-search

# 方法 2: 使用 Git
git clone https://github.com/openclaw/tavily-search.git ~/.openclaw/workspace/skills/tavily-search

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/tavily-search/raw/main/INSTALL.sh)
```

**配置步骤**：
```bash
# 1. 编辑配置文件
nano ~/.openclaw/workspace/skills/tavily-search/config.json

# 2. 配置 API key
{
  "api_key": "your-tavily-api-key",
  "search_depth": "basic",
  "max_results": 10,
  "include_domains": [],
  "exclude_domains": []
}

# 3. 重启 OpenClaw
openclaw gateway restart
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：搜索关于 AI 的最新新闻
咪咪：[自动调用 tavily-search skill]
  🔍 使用 Tavily API 搜索...
  ✅ 搜索完成！
```

**使用示例**：
```
主人：搜索关于 AI 的最新新闻
咪咪：[调用 tavily-search skill]
  🔍 使用 Tavily API 搜索...
  📊 搜索结果：
     1. OpenAI 发布新模型
     2. Google 推出 AI 工具
     3. Meta 发布 AI 搜索引擎
     4. 微软投资 AI 公司
     5. 苹果研发 AI 功能

  ✅ 搜索完成！
```

---

### 11. weather

**功能描述**：
天气查询工具，使用 Open-Meteo API 查询天气和预报。

**主要用途**：
- 查询实时天气
- 查询天气预报
- 支持多个城市
- 免费 API，无需认证
- 支持中文和英文

**官方安装地址**：
- GitHub: https://github.com/openclaw/weather
- 安装命令: `clawdhub install weather` 或 `git clone https://github.com/openclaw/weather.git ~/.openclaw/workspace/skills/weather`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install weather

# 方法 2: 使用 Git
git clone https://github.com/openclaw/weather.git ~/.openclaw/workspace/skills/weather

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/weather/raw/main/INSTALL.sh)
```

**配置步骤**：
```bash
# 1. 配置文件位置
nano ~/.openclaw/workspace/skills/weather/config.json

# 2. 配置参数
{
  "api": "open-meteo",
  "language": "zh-CN",
  "units": "celsius"
}

# 3. 重启 OpenClaw
openclaw gateway restart
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：明天上海会下雨吗？
咪咪：[自动调用 weather skill]
  🌤️ 查询上海天气...
  ✅ 查询完成！
```

**使用示例**：
```
主人：明天上海会下雨吗？
咪咪：[调用 weather skill]
  🌤️ 查询上海天气...
  📊 查询结果：
     日期：2026-03-08
     天气：多云转小雨
     温度：15°C - 22°C
     降水概率：60%
     风向：东南风 3 级

  💡 建议：建议带伞出行
```

---

## 安装步骤

### 步骤 1: 准备工作

确保你的系统满足以下要求：

```bash
# 检查 OpenClaw 是否安装
openclaw --version

# 检查 Git 是否安装
git --version

# 检查 Node.js 是否安装（可选）
node --version
```

**要求**：
- OpenClaw >= v1.0
- Git >= 2.0
- Node.js >= 16.0（可选）
- Bash >= 4.0

---

### 步骤 2: 安装所有 skills（推荐方法）

```bash
# 方法 1: 使用安装脚本（推荐）
bash scripts/install-all.sh

# 方法 2: 使用 curl（无需 Git）
bash <(curl -fsSL https://raw.githubusercontent.com/your-username/openclaw-skill-collection/main/scripts/install-all.sh)

# 方法 3: 手动安装单个 skill
bash scripts/install-individual.sh obsidian-bases skill-safety-verifier find-skills
```

**安装脚本会自动**：
- ✅ 检查 Git 是否安装
- ✅ 检查网络连接
- ✅ 克隆所有 11 个 skills
- ✅ 跳过已安装的 skills
- ✅ 显示安装进度和结果

---

### 步骤 3: 配置可选的 skills

根据需要配置以下 skills：

#### 1. imap-smtp-email（邮件）

```bash
# 编辑配置文件
nano ~/.openclaw/workspace/skills/imap-smtp-email/.env

# 填入邮箱信息
EMAIL_USER=your-email@example.com
EMAIL_PASS=your-password
EMAIL_HOST=smtp.example.com
EMAIL_PORT=465

# 重启 OpenClaw
openclaw gateway restart
```

#### 2. summarize（总结）

```bash
# 编辑配置文件
nano ~/.summarize/config.json

# 填入配置
{
  "model": "auto",
  "length": "xl",
  "output": {
    "language": "auto"
  },
  "firecrawl": "auto"
}

# 重启 OpenClaw
openclaw gateway restart
```

#### 3. tavily-search（搜索）

```bash
# 编辑配置文件
nano ~/.openclaw/workspace/skills/tavily-search/config.json

# 填入 API key
{
  "api_key": "your-tavily-api-key",
  "search_depth": "basic",
  "max_results": 10
}

# 重启 OpenClaw
openclaw gateway restart
```

---

### 步骤 4: 验证安装

```bash
# 检查 skills 是否安装成功
ls -la ~/.openclaw/skills/
ls -la ~/.openclaw/workspace/skills/

# 检查 OpenClaw 状态
openclaw status
```

**预期输出**：
```
🦞 OpenClaw 2026.3.2
🕒 Time: Saturday, March 7th, 2026 — 14:30 (Asia/Shanghai)
🧠 Model: zhipu/glm-4.7-flash
...
📚 Context: 18k/128k (14%)
...
```

---

## 运行步骤

### 步骤 1: 重启 OpenClaw

安装完成后，**必须重启 OpenClaw** 才能让所有 skills 生效：

```bash
# 停止 OpenClaw
openclaw gateway stop

# 启动 OpenClaw
openclaw gateway start

# 或者重启
openclaw gateway restart
```

---

### 步骤 2: 使用 skills

#### 1. 使用 obsidian-bases

```
主人：帮我创建一个笔记数据库
咪咪：[自动调用 obsidian-bases skill]
```

#### 2. 使用 skill-safety-verifier

```
主人：我想安装这个技能 https://github.com/example/skill
咪咪：[自动调用 skill-safety-verifier skill]
```

#### 3. 使用 find-skills

```
主人：我想安装一个新的 skill
咪咪：[自动调用 find-skills skill]
```

#### 4. 使用 imap-smtp-email

```
主人：帮我发一封邮件
咪咪：[自动调用 imap-smtp-email skill]
```

#### 5. 使用 multi-search-engine

```
主人：搜索关于 AI 的新闻
咪咪：[自动调用 multi-search-engine skill]
```

#### 6. 使用 proactive-agent

```
主人：我在写代码
咪咪：[自动调用 proactive-agent skill]
  🎯 主动预判你的需求...
  💡 建议你：
     1. 调试代码
     2. 格式化代码
     3. 运行测试
```

#### 7. 使用 self-improving-agent

```
主人：这个方案有问题
咪咪：[自动调用 self-improving-agent skill]
  📝 记录错误日志...
  ✅ 已保存到 .learnings/ 目录
```

#### 8. 使用 qmd

```
主人：搜索我的笔记
咪咪：[自动调用 qmd skill]
  🔍 搜索笔记...
  ✅ 找到 5 条相关笔记
```

#### 9. 使用 summarize

```
主人：帮我总结这个网页 https://example.com
咪咪：[自动调用 summarize skill]
  📝 总结网页...
  ✅ 总结完成！
```

#### 10. 使用 tavily-search

```
主人：搜索关于 AI 的新闻
咪咪：[自动调用 tavily-search skill]
  🔍 使用 Tavily API 搜索...
  ✅ 搜索完成！
```

#### 11. 使用 weather

```
主人：明天上海会下雨吗？
咪咪：[自动调用 weather skill]
  🌤️ 查询上海天气...
  ✅ 查询完成！
```

#### 12. 使用 github 🐙

```
主人：帮我克隆这个仓库 https://github.com/openclaw/openclaw
咪咪：[自动调用 github skill]
  📥 正在克隆仓库...
  ✓ 仓库已克隆到当前目录
  📊 仓库信息：
     名称：openclaw
     描述：OpenClaw - AI Agent Platform
     语言：TypeScript
     Stars：25
     Forks：10
```

```
主人：创建一个 GitHub issue，标题是"Bug: 无法安装 skill"
咪咪：[自动调用 github skill]
  📝 创建 issue...
  ✓ Issue #123 已创建
  📊 Issue详情：
     标题：Bug: 无法安装 skill
     标签：bug, installation
```

---

### 步骤 3: 验证 skills 是否生效

在 OpenClaw 会话中测试每个 skills：

```
主人：明天上海会下雨吗？
咪咪：[自动调用 weather skill 查询天气]
```

如果看到正确的响应，说明 skills 已经生效！

---

## 故障排除

### 问题 1: skills 不生效

**症状**: 安装后，某些 skills 仍然不可用

**解决方案**:
```bash
# 1. 检查 skills 是否在正确位置
ls -la ~/.openclaw/skills/
ls -la ~/.openclaw/workspace/skills/

# 2. 重启 OpenClaw
openclaw gateway restart

# 3. 检查 OpenClaw 日志
openclaw gateway logs
```

---

### 问题 2: 权限被拒绝

**症状**: 安装脚本执行失败，提示权限错误

**解决方案**:
```bash
# 使用 sudo
sudo bash scripts/install-all.sh

# 或者手动赋予执行权限
chmod +x scripts/*.sh
bash scripts/install-all.sh
```

---

### 问题 3: Git 克隆失败

**症状**: Git 无法连接到 GitHub

**解决方案**:
```bash
# 检查网络连接
ping github.com

# 配置 Git 用户信息
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 配置代理（如果需要）
git config --global http.proxy http://proxy.example.com:8080
git config --global https.proxy https://proxy.example.com:8080
```

---

### 问题 4: Node.js 版本过低

**症状**: 某些 skills 需要 Node.js 16+

**解决方案**:
```bash
# 安装 Node.js 18
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证版本
node --version
```

---

### 问题 5: OpenClaw 未安装

**症状**: 提示找不到 `openclaw` 命令

**解决方案**:
```bash
# 安装 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash

# 验证安装
openclaw --version
```

---

### 问题 6: API key 配置错误

**症状**: 使用 tavily-search 时提示 API key 错误

**解决方案**:
```bash
# 检查配置文件
cat ~/.openclaw/workspace/skills/tavily-search/config.json

# 确保API key正确
{
  "api_key": "your-actual-tavily-api-key"
}

# 重启 OpenClaw
openclaw gateway restart
```

---

---

### 12. github 🐙

**功能描述**：
GitHub 管理工具，用于管理 GitHub 仓库、提交代码、创建 issue、查看提交历史等。

**主要用途**：
- 克隆 GitHub 仓库
- 推送代码到 GitHub
- 查看仓库信息
- 创建 Pull Request
- 创建 GitHub Issues
- 查看提交历史
- 管理分支
- 搜索 GitHub 仓库

**官方安装地址**：
- GitHub: https://github.com/openclaw/github
- 安装命令: `clawdhub install github` 或 `git clone https://github.com/openclaw/github.git ~/.openclaw/skills/github`

**安装步骤**：
```bash
# 方法 1: 使用 clawdhub（推荐）
clawdhub install github

# 方法 2: 使用 Git
git clone https://github.com/openclaw/github.git ~/.openclaw/skills/github

# 方法 3: 使用 curl
bash <(curl -fsSL https://github.com/openclaw/github/raw/main/INSTALL.sh)
```

**配置步骤**：
```bash
# 1. 配置 Git 用户信息
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

# 2. 配置 GitHub Token（可选，用于自动化操作）
git config --global github.token "your-github-token"

# 3. 重启 OpenClaw
openclaw gateway restart
```

**运行方法**：
```bash
# 重启 OpenClaw 使 skill 生效
openclaw gateway restart

# 在 OpenClaw 会话中
主人：帮我克隆这个仓库 https://github.com/example/repo
咪咪：[自动调用 github skill]
  📥 正在克隆仓库...
  ✅ 仓库已克隆到本地
```

**使用示例**：
```
主人：帮我克隆这个仓库 https://github.com/openclaw/openclaw
咪咪：[调用 github skill]
  📥 正在克隆仓库...
  📁 仓库已克隆到当前目录
  📊 仓库信息：
     名称：openclaw
     描述：OpenClaw - AI Agent Platform
     语言：TypeScript
     Stars：25
     Forks：10
```

```
主人：创建一个 GitHub issue，标题是"Bug: 无法安装 skill"
咪咪：[调用 github skill]
  📝 创建 issue...
  ✅ Issue #123 已创建
  📊 Issue详情：
     标题：Bug: 无法安装 skill
     标签：bug, installation
```

---

## 总结

### 安装的所有 Skills（12 个）

1. ✅ obsidian-bases - Obsidian 数据库管理
2. ✅ skill-safety-verifier - 技能安全审查
3. ✅ find-skills - 技能发现工具
4. ✅ imap-smtp-email - 邮件收发
5. ✅ multi-search-engine - 多引擎搜索
6. ✅ proactive-agent 🦞 - 主动代理
7. ✅ self-improving-agent - 自我改进
8. ✅ qmd - 本地搜索索引
9. ✅ summarize - 网页总结
10. ✅ tavily-search - AI 优化搜索
11. ✅ weather - 天气查询
12. ✅ github 🐙 - GitHub 管理工具

### 安装步骤总结

1. ✅ 准备工作（检查 OpenClaw、Git、Node.js）
2. ✅ 使用安装脚本安装所有 skills
3. ✅ 配置可选的 skills（imap-smtp-email、summarize、tavily-search）
4. ✅ 验证安装
5. ✅ 重启 OpenClaw

### 运行步骤总结

1. ✅ 重启 OpenClaw
2. ✅ 在 OpenClaw 会话中测试每个 skills
3. ✅ 验证 skills 是否生效

---

## 🎉 完成！

安装和配置完成后，你就可以使用所有 11 个 skills 了！

**祝使用愉快！** 🚀

---

**文档版本**: 1.0
**最后更新**: 2026-03-07
**维护者**: Your Name
