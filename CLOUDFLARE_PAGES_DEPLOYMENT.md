# Cloudflare Pages 部署指南

生产站点由 Cloudflare Pages 托管，仓库为 `yueguobin/gns3-docs`（fork），生产分支为 `deploy/cloudflare-pages`，推送即自动构建部署。

## 构建设置

| 设置项 | 值 |
|--------|-----|
| **Framework preset** | `None` |
| **Build command** | `yarn build` |
| **Build output directory** | `build` |
| **Root directory** | `/` |
| **Production branch** | `deploy/cloudflare-pages` |

## 环境变量与运行时版本

- **Dashboard 环境变量**：`YARN_VERSION=1.22.22`（yarn.lock 为 v1 格式，必须锁定 Classic 版本，否则构建报 `The lockfile would have been modified by this install`）
- **Node 版本**：仓库根目录 `.node-version` 锁定为 `20`（Docusaurus 3 要求 Node 18+）

两者都不在代码里生效：前者在 Cloudflare Dashboard → Workers & Pages → gns3-docs → Settings → Environment variables。

## 分支规则

- 推送到 `deploy/cloudflare-pages` → 自动部署到生产环境
- 推送到其他分支 → 创建预览部署（Preview）

## 日常部署流程

`deploy/cloudflare-pages` 应始终保持「master 内容 + 部署配置」的状态，不要直接在上面写文档：

```bash
# 1. 从 master 创建功能分支，更新文档
git switch -c fix/xxx master
# ... 修改、提交、推到 fork、向上游发 PR ...

# 2. PR 合入 master 后，同步到部署分支
git switch deploy/cloudflare-pages
git merge master
git push myfork deploy/cloudflare-pages   # 自动触发生产部署
```

部署分支相对 master 只保留这些差异：

- `docusaurus.config.js`：`url: 'https://gns3-docs.pages.dev'`、`organizationName: 'yueguobin'`
- `.node-version`、`CLOUDFLARE_PAGES_DEPLOYMENT.md`（本文件）

## 本地验证构建

推送前建议本地确认：

```bash
yarn build
yarn serve   # 本地预览生产构建
```

## 自定义域名（可选）

1. Cloudflare Pages 项目设置 → **Custom domains** → **Set up a custom domain**
2. 同步修改 `docusaurus.config.js` 的 `url` 为自定义域名（影响 sitemap/canonical 链接）

## 故障排除

- **构建失败**：查看 Build logs；先确认 `YARN_VERSION` 和 `.node-version` 是否仍然生效
- **404 错误**：确认 `baseUrl: '/'`（不是 `/gns3-docs/`）
- **图片加载失败**：确认图片使用 `useBaseUrl()` 引用

## 访问地址

- 默认地址：<https://gns3-docs.pages.dev>
