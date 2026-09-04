# jerryzhao.com 切换与回滚清单

当前任务不会执行以下任何线上动作。

## 变更前留档

- [ ] 记录旧基线 `cf31accdb382d1c15e2f08d990d1801b4ec8d4c2`。
- [ ] 留存 GitHub Pages 发布来源、Custom domain、Enforce HTTPS 状态。
- [ ] 导出腾讯云 DNS 全部记录及 TTL，不误删邮件等无关记录。
- [ ] 记录 apex、`www` 与 GitHub Pages 默认地址的 DNS/HTTP 响应。
- [ ] 至少提前一个 TTL 周期降低相关记录 TTL。

## 本地与代码验收

- [ ] Node 24 LTS 下运行 `npm ci`、`npm run build`、`npm run preview`。
- [ ] 检查首页、文章列表、关于页、Markdown/MDX、公式、代码块和移动端。
- [ ] 确认 `dist/CNAME` 仅含 `jerryzhao.com`。
- [ ] 确认旧文章没有被误迁移；迁移需另开内容任务。

## 提交但不部署

- [ ] 获得批准后推送任务分支；非 `master` 分支不会触发部署。
- [ ] 创建 PR，复核删除项、锁文件、Actions 权限与本地构建结果。
- [ ] 合并前建立回滚标签或再次记录旧 SHA；禁止强推或改写历史。
- [ ] 决定正式简介、视觉、首批文章及旧 URL 重定向策略。

## Pages 与域名切换

- [ ] 按 GitHub 官方文档将 Pages Source 设为 **GitHub Actions**。
- [ ] 按 GitHub 官方自定义域名文档核对当时有效的 DNS 值，不照搬旧 IP。
- [ ] 分别验证 apex 与可选 `www`，避免 CNAME 循环。
- [ ] DNS 生效后再批准合并到 `master`；合并会触发部署工作流。
- [ ] 检查 Actions、Pages 环境 URL、Custom domain、首页、文章、404 与 HTTPS。
- [ ] 稳定一个 TTL 周期后恢复常规 TTL，并在证书就绪后启用 Enforce HTTPS。

官方参考：<https://docs.astro.build/en/guides/deploy/github/> 与 <https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site>。

## 回滚

1. 代码异常：revert Astro 合并提交并重新部署，不 force-push。
2. Actions 异常：恢复旧基线，并把 Pages Source 恢复为留档的分支/目录方式。
3. DNS 异常：只恢复留档 DNS，不同时改代码与 DNS。
4. 证书未签发：暂不强制 HTTPS；保留正确 DNS，按 GitHub 官方窗口排查。
5. 回滚后复验默认地址和自定义域名，并记录故障时间线。
