---
name: github-security-audit
description: 对 GitHub 仓库或本地目录进行全栈安全审计，检测恶意代码、后门和供应链攻击，生成报告保存到 Obsidian。当用户说"审计下"、"审计一下"、"安全审计"、"检查下这个仓库"、"审计当前目录"、"审计本地项目"、"check repo"、"audit" 时立即触发。支持 GitHub URL 或本地目录。
---

# 代码安全审计 Skill

对 GitHub 仓库或本地目录进行全面安全审计。

---

## 执行流程

### 第一步：确定审计目标

**判断用户输入：**
- 如果用户提供了 **GitHub URL** → 克隆仓库到临时目录
- 如果用户说 **"当前目录"、"本地"** 或没有提供 URL → 使用当前工作目录

#### 情况 A：GitHub URL（需要克隆）

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/.tools/clone_repo.py" "<用户提供的GitHub URL>"
```

工具会返回克隆后的临时目录路径（格式：`/tmp/github_audit_<repo>_<id>`）。

**注意**：只下载最新代码，不安装任何依赖！

#### 情况 B：本地目录（无需克隆）

直接使用当前工作目录（`pwd`）作为审计目标。

**注意**：
- 不需要克隆步骤
- 不需要清理步骤（不要删除用户的代码！）
- 报告中的"仓库地址"改为本地路径

---

### 第二步：执行安全审计（核心步骤）

**审计标准**：严格按照 `${CLAUDE_PLUGIN_ROOT}/audit-standard.md` 中定义的"全栈安全审计专家 Prompt"执行五步分析法。

**重要**：
- 你是**区块链安全专家**和**恶意软件逆向工程师**
- 采用**零信任原则** - 假设代码中一定有后门
- 必须覆盖：代码逻辑、配置文件、静态资源、依赖定义

**五步分析**：
1. 网络指纹与硬编码审计
2. 敏感数据窃取行为分析
3. 代码混淆与隐藏执行
4. 供应链与安装脚本
5. 最终判决

**输出要求**：
- 【高危实体清单】
- 【逻辑风险点】
- 【结论】（明确"安全"或"极度危险"）

---

### 第三步：生成审计报告

根据审计结果，确定结论并生成报告。

#### 3.1 确定审计结论

根据审计发现，选择以下结论之一：

| 结论 | 含义 | 判定标准 |
|------|------|----------|
| `Safe` | 安全 | 未发现恶意代码、后门或供应链攻击 |
| `Risky` | 有风险 | 存在可疑代码但无法确定是否恶意 |
| `Dangerous` | 极度危险 | 确认存在恶意代码、后门或数据窃取行为 |

#### 3.2 生成报告

直接将审计报告写入文件：

**输出路径**：`~/obsidian-vault/Security-Audit/`（用户可自定义）
**文件命名**：`YYYYMMDD-<对象名>-SecurityAudit-<结论>.md`

报告格式：
```markdown
---
date: YYYY-MM-DD
target: <对象名>
source: <GitHub URL 或本地路径>
result: <Safe/Risky/Dangerous>
tags:
  - security-audit
---

# 代码安全审计报告

<审计内容>
```

---

### 第四步：清理临时文件

**仅当审计 GitHub URL 时执行此步骤！**

如果是本地目录审计，**跳过此步骤**（不要删除用户的代码）。

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/.tools/cleanup.py" <临时目录路径>
```

**安全检查**：工具只会删除 `/tmp/github_audit_*` 目录，防止误删。

---

## 最终输出格式

向用户报告以下内容：

```
📊 审计完成！

🎯 审计对象: <GitHub URL 或本地路径>

【高危实体清单】
<列出所有可疑项，无则显示"无">

【逻辑风险点】
<解释危险行为，无则显示"无">

【结论】
<Safe / Risky / Dangerous> - <简短说明>

📁 报告已保存: ~/obsidian-vault/Security-Audit/YYYYMMDD-<对象>-SecurityAudit-<结论>.md
```

---

## 安全边界（重要！）

### ✅ 允许的操作（只读，安全）

| 操作 | 说明 | 示例 |
|------|------|------|
| `Read(xxx.sh)` | 读取文件内容查看源码 | `Read(install.sh)` - 看代码，不执行 |
| `grep` 搜索 | 在文件中搜索关键词 | `grep "curl" *.sh` - 搜索文本 |
| `find` 查找 | 列出文件路径 | `find . -name "*.sh"` - 找文件 |
| `cat/head/tail` | 显示文件内容 | `cat package.json` - 看内容 |

### ❌ 禁止的操作（绝对不做）

| 操作 | 为什么危险 | 示例 |
|------|-----------|------|
| `bash xxx.sh` | 会执行脚本里的命令 | `bash install.sh` ❌ |
| `./xxx.sh` | 直接运行脚本 | `./bin/clean.sh` ❌ |
| `source xxx.sh` | 加载并执行脚本 | `source lib/common.sh` ❌ |
| `npm install` | 会执行 postinstall 钩子 | `npm install` ❌ |
| `pip install` | 可能执行 setup.py | `pip install -e .` ❌ |
| `node xxx.js` | 执行 JS 代码 | `node index.js` ❌ |
| `python xxx.py` | 执行 Python 代码 | `python main.py` ❌ |

### 核心原则

**只进行静态分析** = 只看代码内容，绝不运行任何目标仓库的代码。

就像法医检查证物：**只看不碰，不触发任何机关。**
