# GitHub Security Audit

一个 Claude Code 插件，用于自动化审计 GitHub 仓库或本地项目的安全性，检测恶意代码、后门和供应链攻击。

## 安装

### 通过 Marketplace 安装（推荐）

```bash
# 添加 marketplace
claude plugin marketplace add alondai/claude-plugin-marketplace

# 安装插件
claude plugin install github-security-audit@alondai
```

### 手动安装

```bash
git clone https://github.com/alondai/github-security-audit.git
ln -s $(pwd)/github-security-audit ~/.claude/skills/github-security-audit
```

## 使用

### 审计 GitHub 仓库

```
审计下 https://github.com/owner/repo
```

### 审计本地项目

```
审计当前目录
```

### 支持的触发词

- "审计下"、"审计一下"
- "安全审计"
- "检查下这个仓库"
- "审计当前目录"、"审计本地项目"
- "audit"、"check repo"

## 特性

- 🔍 **零信任审计**: 按照区块链安全专家标准进行地毯式扫描
- 🚀 **自然语言触发**: 直接说"审计下 URL"或"审计当前目录"即可
- 📁 **双模式支持**: GitHub URL 或本地目录
- 📊 **五步分析法**: 网络指纹、数据窃取、代码混淆、供应链、结构化报告
- 📄 **结构化报告**: 自动生成 Markdown 报告保存到 Obsidian

## 审计示例

```
审计下 https://github.com/suspicious/wallet-stealer

📊 审计完成！

【高危实体清单】
- 🚨 可疑域名: http://api-metamask.xyz
- 🚨 硬编码钱包: 0x1234...
- 🚨 Telegram Bot 回传

【结论】
⛔ 极度危险 - 该项目为恶意软件
```

## 项目结构

```
github-security-audit/
├── .claude-plugin/
│   ├── plugin.json           # 插件元数据
│   └── marketplace.json      # Marketplace 条目
├── skills/
│   └── github-security-audit/
│       └── SKILL.md          # Skill 指令（核心文件）
├── .tools/
│   ├── clone_repo.py         # 浅克隆工具
│   └── cleanup.py            # 临时文件清理
├── audit-standard.md         # 审计标准
└── README.md
```

## 安全说明

⚠️ **重要**:
- 该工具**不会执行任何代码**
- **不会运行** `npm install` 或 `pip install`
- 仅进行静态分析（Read/grep/find 等只读操作）
- 本地审计不会删除或修改任何文件

## License

MIT
