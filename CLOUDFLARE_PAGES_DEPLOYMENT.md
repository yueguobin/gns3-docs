# Cloudflare Pages 部署指南

## 快速开始

### 1. 在 Cloudflare Dashboard 创建项目

1. 访问：https://dash.cloudflare.com/
2. 进入 **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**
3. 选择仓库：`yueguobin/gns3-docs`
4. 选择分支：`deploy/cloudflare-pages`

### 2. 配置构建设置

在 **Build settings** 中填写：

```
Framework preset: None
Build command: yarn build
Build output directory: build
Root directory: / (默认)
```

### 3. 环境变量（无需配置）

此项目不需要额外的环境变量。

### 4. 点击部署

**Save and Deploy** → Cloudflare Pages 将自动构建和部署

---

## 完整配置说明

### 构建设置

| 设置项 | 值 |
|--------|-----|
| **Framework preset** | `None` |
| **Build command** | `yarn build` |
| **Build output directory** | `build` |
| **Root directory** | `/` |
| **Branch** | `deploy/cloudflare-pages` |

### 分支规则

- **Production branch**: `deploy/cloudflare-pages`
- **Preview deployments**: 启用（所有 PR 都会创建预览）
- **自动部署**: 推送到 `deploy/cloudflare-pages` 分支时自动触发

---

## 自定义域名（可选）

### 1. 在 Cloudflare Pages 项目设置中

1. 进入 **Custom domains**
2. 点击 **Set up a custom domain**
3. 输入你的域名（如：`docs.yourdomain.com`）

### 2. 更新 Docusaurus 配置

如果使用自定义域名，修改 `docusaurus.config.js`：

```js
module.exports = {
  url: 'https://docs.yourdomain.com',  // 你的自定义域名
  baseUrl: '/',
  // ...
}
```

### 3. DNS 配置

Cloudflare 会自动添加 DNS 记录。

---

## 本地测试构建

在推送前测试构建：

```bash
yarn build
yarn serve  # 本地预览生产构建
```

---

## 部署流程

### 首次部署

```bash
# 切换到 Cloudflare Pages 分支
git checkout deploy/cloudflare-pages

# 推送到远程
git push -u myfork deploy/cloudflare-pages

# 在 Cloudflare Dashboard 触发部署或等待自动部署
```

### 更新内容后部署

```bash
# 在 deploy/cloudflare-pages 分支上
git add .
git commit -m "更新内容"
git push myfork deploy/cloudflare-pages

# Cloudflare Pages 会自动检测并部署
```

---

## 访问地址

部署成功后，你会获得：

- **默认地址**: `https://your-project.pages.dev`
- **自定义域名**: （如果配置）`https://docs.yourdomain.com`

---

## 优势对比

| 特性 | Cloudflare Pages | GitHub Pages |
|------|-----------------|--------------|
| **全球 CDN** | ✅ Cloudflare 网络 | ✅ GitHub 网络 |
| **自定义域名** | ✅ 免费无限 | ✅ 免费 |
| **构建时间** | 快速 | 较慢 |
| **预览部署** | ✅ 每个分支/PR | ❌ 仅 gh-pages |
| **带宽限制** | 无限制 | 100GB/月 |
| **构建次数** | 无限制 | 无限制 |

---

## 故障排除

### 构建失败

1. 检查 **Build command** 是否为 `yarn build`
2. 检查 **Build output directory** 是否为 `build`
3. 查看 **Build logs** 获取详细错误信息

### 404 错误

1. 确认 `baseUrl: '/'` （不是 `/gns3-docs/`）
2. 检查自定义域名 DNS 配置

### 图片加载失败

确保所有图片使用 `useBaseUrl()`:

```jsx
import useBaseUrl from '@docusaurus/useBaseUrl';
<img src={useBaseUrl('img/xxx.jpeg')} />
```

---

## 多环境部署建议

### GitHub Pages + Cloudflare Pages 同时部署

可以使用不同的分支：

- `deploy/github-pages` → GitHub Pages (`baseUrl: '/gns3-docs/'`)
- `deploy/cloudflare-pages` → Cloudflare Pages (`baseUrl: '/'`)

这样可以同时使用两个平台的优势。
