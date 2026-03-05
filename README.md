# GitHub Security Audit Skill

一个 Claude Code Skill，用于自动化审计 GitHub 仓库或本地项目的安全性，检测恶意代码、后门和供应链攻击。

## 特性

- 🔍 **零信任审计**: 按照区块链安全专家标准进行地毯式扫描
- 🚀 **自然语言触发**: 直接说"审计下 URL"或"审计当前目录"即可
- 📁 **双模式支持**: GitHub URL 或本地目录
- 📊 **五步分析法**: 网络指纹、数据窃取、代码混淆、供应链、结构化报告
- 📄 **结构化报告**: 自动生成 Markdown 报告
- 🧹 **自动清理**: 审计 GitHub 仓库后自动删除临时文件
- ⚡ **快速克隆**: 使用浅克隆（`--depth 1`）提升速度

## 安装

```bash
# 创建软链接
ln -s $(pwd) ~/.claude/skills/github-security-audit

# 验证
ls -l ~/.claude/skills/github-security-audit
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

或

```
审计本地项目
```

### 支持的触发词

- "审计下"、"审计一下"
- "安全审计"
- "检查下这个仓库"
- "审计当前目录"、"审计本地项目"
- "audit"、"check repo"

### 命令方式

```
/github-security-audit https://github.com/owner/repo
```

## 审计示例

### 检测恶意项目

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

### 审计本地项目

```
审计当前目录

📊 审计完成！

目录: ~/my-project

【高危实体清单】
无

【结论】
✅ 安全 - 未发现恶意代码
```

## 项目结构

```
github-security-audit/
├── SKILL.md              # Skill 配置和工作流（核心文件）
├── audit-standard.md     # 审计标准
├── README.md             # 本文档
└── .tools/               # Python 工具集
    ├── clone_repo.py     # 浅克隆工具
    └── cleanup.py        # 临时文件清理
```

## 工作原理

### GitHub 仓库审计
1. **浅克隆**: 使用 `git clone --depth 1` 只下载最新代码
2. **静态扫描**: 不执行任何代码，纯文本分析
3. **五步审计**: URL/IP/钱包地址扫描、私钥窃取检测、混淆代码、依赖投毒、结构化报告
4. **报告生成**: 保存为 Markdown
5. **自动清理**: 删除临时目录

### 本地目录审计
1. **直接扫描**: 使用当前工作目录
2. **静态扫描**: 同上
3. **五步审计**: 同上
4. **报告生成**: 同上
5. **不清理**: 不会删除任何本地文件

## 依赖

- Python 3.6+
- Git

## 安全说明

⚠️ **重要**:
- 该工具**不会执行任何代码**
- **不会运行** `npm install` 或 `pip install`
- 仅进行静态分析（Read/grep/find 等只读操作）
- 本地审计不会删除或修改任何文件

## License

MIT
