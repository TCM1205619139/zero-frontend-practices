# 启动、认证与动态路由

本文件记录会影响启动时序和路由可见性的当前实现。修改相关规则前，先 review：

- `src/main.js`
- `src/router/index.js`
- `src/router/route-config.js`
- `src/store/src/auth.js`
- `src/store/src/account.js`
- `src/store/src/subscription.js`
- `src/store/src/verify.js`
- `src/components/header/headerHook.js`

## 启动依赖

`src/main.js` 当前按以下依赖推进，不能把步骤视为可任意重排的普通初始化：

1. 创建 app、Pinia、Vuetify、i18n，注册全局组件和指令。
2. `getAccessTokenAdaptor()` 取得 token，写入 app store，再初始化登录认证 adaptor；失败会执行 `logout()`。
3. 触发 translate、scrap、app、deposit 初始化。
4. 等待 account、subscription、verify 初始化完成。
5. `authStore.init(router)` 注册动态路由，然后安装 router 并 mount。

translate、scrap、app、deposit 的 `init()` 当前没有被启动链等待；account 的注册来源加载、subscription 的套餐价格加载也在各自 `init()` 中被触发但未被返回等待。修改首屏数据、语言、认证、账户、订阅或路由初始化时，需要明确判断该任务是否应阻塞 mount，并回归首屏竞态和失败路径。

## 动态路由契约

- router 初始 `routes` 为空；`authStore.init()` 先注册 `BaseRoutes`、`NotEVRoutes`，再调用 `authStore.update()`。
- `authStore.update()` 当前把 `DepositedRoutes` 注册到 `Root`，并按设备端的 `MenuRoutes.meta.available()` 动态添加或移除菜单路由。
- `accountStore.update()` 和 `subscriptionStore.update()` 会在请求结束后调用 `authStore.update()`；设置订阅入口计划成功后也会刷新路由。
- `verifyStore.update()` 只刷新审核状态，不会自行刷新动态路由。需要改变菜单或入口可见性时，由业务调用方显式调用 `authStore.update()`；header 的实时事件处理当前采用这种方式。
- 未注册的直接 URL 没有 route name，会由全局守卫跳转到 `RootRedirect`，再进入 `/dashboard`。排查深链跳转时先确认目标路由是否已注册。

## 改动检查

触达 account、subscription、verify、入口计划、菜单 `available()`、启动链或 header 实时事件时，至少检查：

- PC/H5 的菜单和 header 入口是否一致符合预期。
- 可用入口、不可用入口、直接 URL 和刷新后的兜底跳转。
- 状态更新后是否需要且只触发一次 `authStore.update()`。
- 初始化请求失败、部分完成或延迟完成时，router mount 和当前页面是否稳定。
- `DepositedRoutes` 的实际注册条件以当前 `authStore.update()` 为准，不根据路由分组名称或历史注释推断业务权限。
