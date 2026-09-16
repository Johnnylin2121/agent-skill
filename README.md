# MiMoCode Skills

> ⚠️ **本仓库已冻结（2026-09-16）**：真源已迁移到 **`dsh-agent`**（`git@github.com:Johnnylin2121/dsh-agent.git`，本地 `~/.dsh/skills`）。
> 本仓保留 MiMoCode 时代的旧技能集，**仅作历史对照**：不再更新、不双写。其中 `notion-api`、`cavecrew`、`active-notes`、`goal-drift`、`plan-lock`、`caveman-stats` 因 DSH 无 hook 已弃用；
> `trading-价值投资功法`、`trading-每日复盘` 已被 dsh-agent 的 `trading-value-investing`、`trading-daily-review` 取代。
> 新机器请只克隆 dsh-agent；仓库结构见 dsh-agent 的 `REPO-MAP.md`。


个人 MiMoCode 技能仓库。**所有平台共用 `main` 分支**，平台差异通过运行时检测处理。

---

## 安装

```bash
mimo skill install git@github.com:Johnnylin2121/mimocode-skill.git
```

安装后，激活 `skill-sync` 即可进行同步管理。

---

## ⚠️ Agent 推送规则（必须遵守）

### 规则 1：推送前检查

```bash
git branch --show-current          # 确认在 main 分支
git status --porcelain             # 确认无未提交更改
git log origin/main..HEAD --oneline   # 本地领先远程的提交
git log HEAD..origin/main --oneline   # 远程领先本地的提交
```

### 规则 2：禁止的操作

- ❌ 禁止使用 `git push --force`
- ❌ 禁止在 skill 运行时自动修改仓库文件并推送
- ❌ 禁止将 `__pycache__`、`.DS_Store`、`Thumbs.db` 等缓存文件提交

### 规则 3：平台差异处理

路径占位符说明：

| 占位符 | 含义 | 用户需替换为 |
|--------|------|------------|
| `{VAULT_PATH}` | Obsidian Vault 根目录 | Windows: `D:\OneDrive\ObsidianVault`，macOS: `~/Library/CloudStorage/...` |
| `{TEMP}` | 临时目录 | Windows: `%TEMP%`，macOS: `/tmp` |
| `~/Desktop/` | 桌面目录 | 双平台通用 |

Agent 在激活 skill 时，应通过 `process.platform` 检测当前系统，并告知用户需要配置的路径。

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

## 首次设置

### Windows（当前机器）

```powershell
cd C:\Users\johnn\.mimocode\skills
git checkout main
git branch -D adapt/win adapt/mac master  # 清理旧分支（如有）
```

### macOS

```bash
cd ~/.mimocode/skills
git checkout main
git branch -D adapt/win adapt/mac master  # 清理旧分支（如有）
```

---

## 故障排查

| 问题 | 原因 | 解决 |
|------|------|------|
| `git push` 被拒绝 | 远程有新的提交 | 先 `git pull --rebase` 再推送 |