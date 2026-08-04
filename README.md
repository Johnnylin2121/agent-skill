# MiMoCode Skills

个人 MiMoCode 技能仓库，按运行平台分离分支管理。任何访问此仓库的 agent 必须**先阅读此文档**，再执行安装或推送操作。

---

## 分支结构

| 分支 | 用途 | 说明 |
|------|------|------|
| `main` | 平台无关模板 | 路径使用 `{VAULT_PATH}` / `~/Desktop/` 等占位符，不包含任何平台具体路径 |
| `adapt/mac` | macOS 适配版 | 所有路径、shell 命令、工具引用均已适配 macOS |
| `adapt/win` | Windows 适配版 | 所有路径、shell 命令、工具引用均已适配 Windows |

**核心原则**：`main` 是单一事实来源（SSOT），`adapt/*` 是平台专属副本。

---

## 安装指南

### 自动安装（推荐）

使用 `skill-sync` skill 自动检测当前系统并安装对应分支：

```bash
# 激活 skill-sync 后，它会自动完成以下操作：
# 1. 检测 OS 类型（Windows → adapt/win，macOS → adapt/mac）
# 2. 使用正确的 --branch 参数安装
# 3. 配置本地仓库指向正确的远程分支
```

### 手动安装

```bash
# Windows
mimo skill install git@github.com:Johnnylin2121/mimocode-skill.git --branch adapt/win

# macOS
mimo skill install git@github.com:Johnnylin2121/mimocode-skill.git --branch adapt/mac
```

---

## ⚠️ Agent 推送规则（必须遵守）

任何 agent 在向此仓库推送更改时，必须依次执行以下检查：

### 规则 1：确认当前分支

```bash
git branch --show-current
```

| 当前系统 | 允许推送的分支 | 禁止推送的分支 |
|---------|--------------|--------------|
| Windows | `adapt/win` | `main`, `adapt/mac` |
| macOS | `adapt/mac` | `main`, `adapt/win` |

**禁止向 `main` 分支直接推送**。`main` 只接受从 `adapt/*` cherry-pick 过来的通用功能更新。

### 规则 2：推送前检查

```bash
# 检查是否有未提交的更改
git status --porcelain

# 检查本地是否领先/落后于远程
git log origin/{branch}..HEAD --oneline
git log HEAD..origin/{branch} --oneline
```

### 规则 3：禁止的操作

- ❌ 禁止向非当前平台的分支推送
- ❌ 禁止向 `main` 直接推送（除非是初始化或紧急修复，且需人工确认）
- ❌ 禁止使用 `git push --force`（除非人工确认）
- ❌ 禁止在 skill 运行时自动修改仓库文件并推送
- ❌ 禁止将 `__pycache__`、`.DS_Store`、`Thumbs.db` 等缓存文件提交

### 规则 4：跨平台更新流程

当需要在多个平台间同步更新时：

```
1. 在 adapt/win 或 adapt/mac 上修改并推送
2. 将通用功能（非平台特定部分）cherry-pick 到 main
3. 切换到另一个平台，将 main 的更新 cherry-pick 到对应的 adapt/ 分支
```

---

## 仓库结构

```
mimocode-skill/
├── .gitignore              # 排除 __pycache__ / .DS_Store / Thumbs.db
├── README.md               # 本文件
├── SKILLS-INDEX.md         # 完整 skill 索引
├── active-notes/           # 多步骤任务主动记录
├── amazon-ad-analysis/     # 亚马逊广告分析
├── amazon-listing/         # Listing 优化
├── amazon-product-selection/ # 选品分析
├── cavecrew/               # 子代理委派决策
├── caveman/                # 超压缩通信模式
├── caveman-commit/         # 压缩提交信息
├── caveman-compress/       # 记忆文件压缩
├── caveman-help/           # 快速参考卡
├── caveman-review/         # 压缩代码审查
├── caveman-stats/          # Token 用量统计
├── domain-memory/          # 领域记忆管理
├── goal-drift/             # 长任务目标漂移防护
├── grill-me/               # 追问面试
├── notion-api/             # Notion API 集成
├── obsidian-reconcile/     # Obsidian 矛盾信息检测
├── obsidian-vault-sync/    # 自动同步文件到 Obsidian
├── plan-lock/              # 计划完整性校验
├── skill-sync/             # Skill 同步工具（本仓库的管家）
├── trading-价值投资功法/    # 价值投资完整体系
├── trading-每日复盘/        # A股每日复盘
├── trading-contradiction-check/ # 复盘矛盾自动检测
├── trading-policy-impact/  # 政策/事件影响链路分析
└── trading-stock-scan/     # 个股深度扫描
```

---

## 首次设置（Windows 本地）

当前 Windows 机器上的 `.mimocode/skills/` 目录是通过 `git clone` 部署的，分支为 `master`（已废弃）。迁移步骤：

```powershell
# 1. 进入技能目录
cd C:\Users\johnn\.mimocode\skills

# 2. 切换到 adapt/win 分支
git checkout adapt/win

# 3. 确认状态
git log --oneline -3
```

---

## 故障排查

| 问题 | 原因 | 解决 |
|------|------|------|
| `git push` 被拒绝 | 远程有新的提交 | 先 `git pull --rebase` 再推送 |
| 推送到了错误的分支 | 未检查当前分支 | 用 `git reset HEAD~1` 撤回，切到正确分支重推 |
| Mac 上的修改被推到了 Windows 分支 | 未做系统检测 | 使用 `skill-sync` 自动管理分支选择 |
| 路径不匹配（`C:\` vs `~`） | 使用了错误的分支 | 确认当前分支与系统匹配 |