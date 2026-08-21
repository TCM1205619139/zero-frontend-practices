# SSE 与 WebSocket 生命周期

修改实时连接前，先 review `src/api/sse.js`、`src/api/websocket.js`、对应 API 工厂及全部调用方；不能只查看当前页面。重点搜索 `createPIXMethodSSE`、`createWebsocket`、`emitter.on/off`、`socket.close` 和 `destroy`。

## SSE

- `src/api/sse.js` 按 URL 缓存连接，使用 cookie 中的 access token 发送 Bearer 鉴权；当前重连延迟常量为 3 秒。
- 返回对象是 `{ socket, emitter }`；关闭入口是 `socket.close()`，关闭时会 abort 请求、清理重连 timer 并从缓存中删除该 URL。
- Deposit 和 Withdrawal PIX 页面当前在卸载时移除自己的 routing listener，并关闭 socket。
- 因为同 URL 共享实例，新增并发消费者时不能假设连接独占。关闭连接前先搜索所有调用方；每个消费者必须用同一个函数引用 `off` 自己注册的 listener。

## WebSocket

- `src/api/websocket.js` 按 URL 缓存连接，打开后发送 Bearer token 鉴权消息；当前重连间隔常量为 5 秒。
- `close()` 会停止当前连接和自动重连，但保留 emitter listeners；再次通过同 URL 获取实例时可能重新连接。
- `destroy()` 会关闭连接、移除该共享 emitter 的全部 listeners，并从缓存删除。普通页面或单一消费者不要对共享连接调用 `destroy()`。
- 页面级 listener 在卸载时使用 `emitter.off(event, sameHandler)` 清理；不要用匿名回调注册后失去清理引用。
- header hook 当前注册的是长期匿名 listener，且 setup 方法没有返回 cleanup。触达 PC/H5 header 挂载、布局切换或该 hook 时，必须检查重复注册和生命周期，不继续扩散这种模式。

## Review 清单

- 连接是否按 URL 共享，当前调用方是否拥有关闭权。
- listener、重连 timer、AbortController 是否在正确的生命周期结束时清理。
- 页面重复进入、PC/H5 切换、断网恢复和登出后是否产生重复事件或旧连接。
- 实时事件刷新 store 后，是否还需要动态路由更新；涉及 verify 状态时同时读取 `runtime-routing.md`。
- 新增错误处理不能形成同步重连循环，也不能吞掉导致状态永远 loading 的错误。
