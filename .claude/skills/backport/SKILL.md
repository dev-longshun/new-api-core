---
name: backport
description: 将站点仓库的某个功能 commit 反哺到内核仓库 (new-api-core)。通过 cherry-pick 只挑选功能代码，不带入站点品牌内容。
user-invocable: true
---

# 反哺内核 Skill

## 触发方式

用户说"反哺 xxx"、"backport xxx"、"把 xxx 的功能同步回内核"、"从 xxx 站点反哺"时触发。

## 工作流

### 1. 识别来源

从用户输入中提取：
- 来源站点名称
- 要反哺的内容（commit hash、功能描述、或 "最近的 N 个 commit"）

### 2. 查找目标 commit

```bash
cd /Users/longshun/Desktop/Program/00_use/new-api-core

# 添加站点 remote（如果还没有）
git remote add site-{站点名} https://github.com/dev-longshun/new-api-{站点名}.git 2>/dev/null || true

# 拉取站点最新代码
git fetch site-{站点名}

# 列出站点的最近 commit 供用户选择
git log --oneline site-{站点名}/main -10
```

如果用户没指定具体 commit，列出最近的 commit 让用户选择。

### 3. 检查 commit 内容

在 cherry-pick 之前，先检查 commit 涉及的文件：

```bash
git show --stat {commit-hash}
```

如果涉及首页文件（`web/src/pages/Home/**`、`web/index.html`、`web/public/logo.png`），
警告用户这些文件包含站点品牌内容，确认是否继续。

### 4. 执行 cherry-pick

```bash
git cherry-pick {commit-hash}

# 如果有冲突，报告给用户，不要自动解决
# 如果无冲突，推送
git push origin main
```

### 5. 报告结果

告知用户：
- 反哺了哪个 commit
- 涉及的文件
- 提示：可以用 `/sync-site` 将这个改动同步到其他站点

## 注意事项

- 只用 cherry-pick，不用 merge — 避免把站点的所有历史都带进内核
- 如果一个功能跨多个 commit，按顺序逐个 cherry-pick
- 涉及首页文件的 commit 需要特别注意，可能包含站点品牌内容
- cherry-pick 后如果需要调整（比如把品牌名改回通用名），在内核里额外提交一个修正

## 当前站点列表

| 站点 | remote 名 | GitHub 仓库 |
|------|-----------|-------------|
| rabbitcode | `site-rabbitcode` | `dev-longshun/new-api` |
