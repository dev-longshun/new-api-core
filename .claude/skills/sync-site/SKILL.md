---
name: sync-site
description: 将内核仓库 (new-api-core) 的改动同步到站点仓库。自动执行 fetch + merge，首页 UI 文件通过 .gitattributes merge=ours 策略保留站点版本。
user-invocable: true
---

# 站点同步 Skill

## 触发方式

用户说"同步 xxx"、"sync xxx"、"同步到 xxx"、"同步所有站点"时触发。

## 架构

```
new-api-core (内核模板)          ← 通用内核，所有公共改动在这里
    ↓ git fetch core + merge
new-api (= rabbitcode 站点)      ← rabbitcode 定制，remote "core" 指向内核
    ↓ git fetch core + merge
new-api-xxx (未来站点)           ← 各自定制，remote "core" 指向内核
```

本地路径：
- 内核：`/Users/longshun/Desktop/Program/00_use/new-api-core/`
- rabbitcode：`/Users/longshun/Desktop/Program/00_use/new-api/`

## 工作流

### 1. 识别目标站点

从用户输入中提取站点名称，查找对应的本地文件夹。

如果用户说"同步所有站点"，则逐个同步所有站点。

### 2. 执行同步

```bash
cd {站点文件夹路径}

# 确保 merge=ours 驱动已配置
git config merge.ours.driver true

# 拉取内核最新改动
git fetch core

# 合并内核改动（首页文件自动保留站点版本）
git merge core/main

# 如果有冲突，报告给用户，不要自动解决
# 如果无冲突，推送
git push origin main
```

### 3. 报告结果

同步完成后，简要报告：
- 同步了哪些站点
- 是否有冲突需要手动处理
- 受影响的文件列表（简要）

## 注意事项

- `.gitattributes` 中配置了 `merge=ours` 的文件在 merge 时自动保留站点版本：
  - `web/src/pages/Home/**`
  - `web/index.html`
  - `web/public/logo.png`
  - `web/public/favicon.ico`
- 如果遇到冲突，不要自动解决，报告给用户
- 同步前确认工作区干净（无未提交的改动）
- 每个站点仓库的 remote "core" 指向 `https://github.com/dev-longshun/new-api-core.git`

## 当前站点列表

| 站点 | 本地路径 | GitHub 仓库 | 说明 |
|------|----------|-------------|------|
| rabbitcode | `/Users/longshun/Desktop/Program/00_use/new-api/` | `dev-longshun/new-api` | RabbitCode AI 中转站 |
