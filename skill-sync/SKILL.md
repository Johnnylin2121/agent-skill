---
name: skill-sync
description: >
  管理本地 skill 与 GitHub 远程仓库的同步。
  所有平台共用 `main` 分支，平台差异在 skill 内部通过运行时检测处理。
  当用户说"同步 skill"、"推送到 GitHub"、"skill 更新了吗"、"检查 skill 版本"、
  "skill-sync"时使用。
---

# Skill Sync

管理 `.mimocode/skills/` 目录与 GitHub 远程仓库 `Johnnylin2121/mimocode-skill` 的同步。
**所有平台共用 `main` 分支。**

**远程仓库**：`git@github.com:Johnnylin2121/mimocode-skill.git`

---

## 前置检查（每次执行前必须执行）

### Step 0：确认当前分支

```bash
cd <repoDir>
git branch --show-current
```

**规则**：必须为 `main` 分支。如果不是，切换到 `main` 再继续。

---

## 流程

### Step 1：检测版本差异

```bash
git status
git log origin/main..HEAD --oneline   # 本地有但远程没有的提交
git log HEAD..origin/main --oneline   # 远程有但本地没有的提交
```

向用户报告：
- 本地未提交的更改（modified / untracked）
- 本地领先远程的提交数
- 远程领先本地的提交数

### Step 2：处理分歧（如有）

如果远程领先本地（`git pull` 会产生合并）：
- 先执行 `git pull origin main`
- 如有冲突，列出冲突文件并提醒用户手动解决
- 如无冲突，自动完成合并

**冲突处理建议**：

| 冲突类型 | 建议 |
|---------|------|
| SKILL.md 冲突 | 保留双方内容，手动合并后 `git add` + `git commit` |
| 新增文件冲突 | 通常保留双方，不会真正冲突 |
| 删除文件冲突 | 确认哪个版本正确，手动处理 |

### Step 3：提交本地更改

如果有未提交的更改：

**自动生成提交信息**：根据变更文件自动判断提交类型和描述。

```bash
# 获取变更文件列表
git status --porcelain
```

**提交信息生成规则**：

| 变更情况 | 提交信息格式 | 示例 |
|---------|-------------|------|
| 新增 skill 目录 | `feat: add <skill-name>` | `feat: add amazon-listing` |
| 更新单个 skill | `feat: update <skill-name> - <简述>` | `feat: update amazon-ad-analysis - add scripts/analysis.py` |
| 更新多个 skill | `feat: update skills - <简述>` | `feat: update skills - optimize 4 skills` |
| 删除 skill | `chore: remove <skill-name>` | `chore: remove last30days` |
| 新增脚本/参考文件 | `feat: add <skill-name> scripts/references` | `feat: add amazon-listing scripts` |
| 修改配置文件 | `chore: update config` | `chore: update .gitignore` |

**自动判断逻辑**：
1. 检查是否有新增目录（`git status --porcelain | grep "^??"`）→ 新增 skill
2. 检查变更文件所属的 skill 目录 → 更新对应 skill
3. 检查是否有删除的文件 → 删除 skill
4. 混合变更 → 使用通用格式

```bash
git add -A
git commit -m "<自动生成的提交信息>"
```

### Step 4：推送到远程

```bash
git push origin main
```

若 push 被拒绝：
- 执行 `git pull origin main` 合并远程更改
- 再次 `git push origin main`
- 若仍有冲突，提示用户手动解决

### Step 5：确认结果

执行 `git status` 和 `git log --oneline -3` 确认同步成功，向用户报告最终状态。

---

## 选择性同步

用户可以指定只同步特定 skill，而非全部。

**用法**：
- "同步 amazon-listing"
- "只推送到 amazon-ad-analysis"
- "skill-sync amazon-product-selection"

**执行流程**：
1. 只 `git add` 指定 skill 目录下的文件
2. 提交信息使用该 skill 名称
3. 推送到远程

```bash
# 示例：只同步 amazon-listing
git add amazon-listing/
git commit -m "feat: update amazon-listing - <简述>"
git push origin main
```

---

## 同步状态仪表盘

用户说"检查 skill 版本"或"skill 状态"时，输出同步状态仪表盘。

**输出格式**：

```markdown
## Skill 同步状态

| Skill | 本地状态 | 远程状态 | 同步状态 |
|-------|---------|---------|---------|
| amazon-ad-analysis | ✅ 已提交 | ✅ 已同步 | 🟢 同步 |
| amazon-listing | ⚠️ 有变更 | - | 🟡 待同步 |
| trading-每日复盘 | ✅ 已提交 | ❌ 落后 | 🔴 需推送 |
| ... | ... | ... | ... |

**汇总**：
- 已同步：X 个
- 待同步：Y 个（有本地变更）
- 需推送：Z 个（本地领先远程）
- 需拉取：W 个（远程领先本地）
- 当前分支：`main`
```

**判断逻辑**：
- 有本地变更（`git status` 非空）→ 🟡 待同步
- 本地领先远程（`git log origin/main..HEAD` 有提交）→ 🔴 需推送
- 远程领先本地（`git log HEAD..origin/main` 有提交）→ 🔵 需拉取
- 都为空 → 🟢 同步

---

## 快捷模式

用户说"同步 skill"且无其他上下文时：
1. 执行 Step 0（确认分支）
2. 执行 Step 1（检测差异）
3. 若有远程分歧 → Step 2
4. 若有本地更改 → Step 3
5. 若有需要推送 → Step 4
6. 执行 Step 5（确认结果）

---

## 禁止的操作

- ❌ 禁止使用 `git push --force`
- ❌ 禁止在 skill 运行时自动提交并推送
- ❌ 禁止将 `__pycache__`、`.DS_Store`、`Thumbs.db` 等缓存文件提交

---

## 注意事项

- 不主动删除远程分支或强制推送
- push 被拒绝时先 pull 再 push，不使用 `--force`
- 每次操作前先确认当前在 `main` 分支
- 选择性同步时，只提交指定目录的文件
- 自动生成提交信息时，优先使用具体 skill 名称而非通用格式
