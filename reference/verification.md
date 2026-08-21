# 本地验证与排障

执行命令前先 review 当前 `package.json`、`vite.config.mjs` 和相关 `.env.*` 文件；这里不固化 Node、pnpm 或依赖的精确版本。

## 当前命令语义

- `pnpm dev`：启动 Vite。本仓库当前监听 `0.0.0.0:8010` 并自动打开浏览器；代理配置被注释，本地请求使用当前 mode 的环境地址。
- `pnpm build:test`：使用 test mode 构建。
- `pnpm build:online`：使用 online mode 构建。
- `pnpm preview`：预览构建产物。
- `pnpm lint`：实际执行 `eslint . --fix`，会修改工作区，不能作为只读检查直接运行。运行前先确认用户改动，运行后检查 diff，避免带入无关格式化。

验证范围与风险成比例：纯 skill/reference 修改不需要构建业务应用；运行时代码变更优先执行针对性检查，并按影响决定是否补充 test/online build 和浏览器路径验收。

## 环境规则

- 仓库使用 `.env.development`、`.env.test`、`.env.online` 提供构建模式配置；新增或修改变量前先搜索所有使用点。
- `VITE_*` 会打包进浏览器代码，禁止存放 secret、私钥或仅服务端可见的凭据。
- API、跳转、WebSocket、GTM、租户等地址和标识从环境变量读取，不在业务代码中硬编码环境域名。
- 不根据仓库当前缺少某种 CI、部署或监控配置推导团队流程；这些时间点事实不写成实现规则。

## 常见排查入口

| 现象 | 优先检查 |
| --- | --- |
| 页面、菜单或 header 入口缺失 | `auth.js`、`route-config.js`、account/subscription/verify 状态与 `authStore.update()` |
| 状态已更新但入口未刷新 | 状态更新调用链、header 实时事件、是否显式刷新动态路由 |
| 未知或直接 URL 被送回 Dashboard | 目标 route name 是否已经动态注册、全局 router guard |
| API 失败或没有预期提示 | 对应 API 域、RequestFactory 配置、浏览器 Network、token 与拦截器 |
| PIX routing 异常 | Funding SSE、Deposit/Withdrawal listener、共享连接关闭与 gateway/account 匹配 |
| 通知或审核实时状态不更新 | WebSocket URL、鉴权、重连、listener 重复/清理和对应 store 更新 |
| Support 页面空白 | Gaia URL、登录态、iframe 跨域与 CSP |

## 交付前

- 检查 `git diff --check`。
- review touched files 的业务调用链和 PC/H5 对应入口。
- 对会修改文件的命令复查 diff，确认没有覆盖或混入用户无关改动。
- 说明实际执行的检查；未运行构建、浏览器验收或接口联调时，明确其范围和原因。
