# MiMoCode Skills

个人 MiMoCode 技能仓库，按平台分离分支管理。

## 分支结构

| 分支 | 用途 | 平台 |
|------|------|------|
| `main` | 模板（平台无关，路径使用占位符） | 通用 |
| `adapt/mac` | macOS 适配版 | 🍎 Mac |
| `adapt/win` | Windows 适配版 | 🪟 Windows |

## 使用方式

**Windows 安装**：
```bash
mimo skill install git@github.com:Johnnylin2121/mimocode-skill.git --branch adapt/win
```

**macOS 安装**：
```bash
mimo skill install git@github.com:Johnnylin2121/mimocode-skill.git --branch adapt/mac
```

## 维护规则

1. 平台适配在 `adapt/` 分支上修改，永不推送到 `main`
2. 通用功能更新在 `main` 上修改，然后 cherry-pick 到 `adapt/` 分支
3. Skill 运行时禁止自动修改仓库文件并推送