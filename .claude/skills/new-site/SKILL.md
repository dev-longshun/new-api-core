---
name: new-site
description: 从内核模板创建新站点。自动克隆、配置 remote、配置 merge 策略、创建 GitHub 仓库并推送。
user-invocable: true
---

# 新建站点 Skill

## 触发方式

用户说"新建站点 xxx"、"创建站点 xxx"、"拓展一个新站点 xxx"时触发。

## 工作流

### 1. 收集信息

从用户输入中提取：
- 站点名称（如 `mysite`）
- GitHub 仓库名（默认 `new-api-{站点名}`）
- 站点品牌名（用于替换首页的 "New API" 文案）

如果信息不全，向用户确认。

### 2. 克隆内核

```bash
SITE_NAME="{站点名}"
SITE_DIR="/Users/longshun/Desktop/Program/00_use/new-api-${SITE_NAME}"

git clone /Users/longshun/Desktop/Program/00_use/new-api-core "${SITE_DIR}"
cd "${SITE_DIR}"

# 重新配置 remote
git remote rename origin core
git remote add origin git@github.com:dev-longshun/new-api-${SITE_NAME}.git

# 配置 merge=ours 驱动
git config merge.ours.driver true
```

### 3. 站点定制

根据用户提供的品牌信息，修改以下文件：
- `web/src/pages/Home/sections/HeroSection.jsx` — 替换品牌名、域名、终端动画文案
- `web/src/pages/Home/sections/FAQSection.jsx` — 替换品牌名
- `web/src/pages/Home/landing.css` — 替换注释中的品牌名
- `web/index.html` — 替换 `<title>`

### 4. 提交并推送

```bash
git add -A
git commit -m "feat: 🎨 初始化 ${SITE_NAME} 站点定制"
git push -u origin main
```

### 5. 更新站点列表

在 `/sync-site` skill 的站点列表中添加新站点条目。

### 6. 报告结果

告知用户：
- 本地路径
- GitHub 仓库地址
- 下一步：配置 GitHub Actions secrets、部署到服务器

## 注意事项

- 用户需要先在 GitHub 上创建空仓库，或者用 `gh repo create` 自动创建
- `.gitattributes` 已经在内核中配置好了 `merge=ours`，克隆后自动继承
- 新站点创建后立即可以用 `/sync-site` 同步内核改动
