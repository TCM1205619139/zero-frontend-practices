# API 请求封装规则

本项目已经在 `src/utils/request.js` 中封装了 `RequestFactory`，业务 API 模块不要重新创建 axios 实例，也不要直接使用 `fetch`、裸 `axios` 或 `XMLHttpRequest`。

## 使用规则

- 从 `@/utils/request.js` 导入 `RequestFactory`。
- 在 API 模块内创建 feature request 实例，并复用 `request.instance`。
- baseURL 使用环境变量，不要硬编码域名。
- 新 API 模块需要从 `src/api` 统一导出。
- 默认响应拦截会处理业务错误、登录失效、权限错误和系统错误；业务侧不要重复弹通用错误。
- 需要静默错误提示时传 `notify: false`。
- 需要业务错误也返回给调用方处理时传 `allowBusinessError: true`。
- 接口默认都需要鉴权，不需要鉴权的情况，需要在请求配置里传 `authorization: false`。
- 下载或二进制接口使用 `responseType: 'blob'`。

## 接口模块样例

```javascript
// src/api/subscription.js
import RequestFactory from '@/utils/request.js'

const subscriptionRequest = RequestFactory.make({
  baseURL: import.meta.env.VITE_APP_SERVER_URL,
})

const request = subscriptionRequest.instance

export const getSubscriptionPlansHTTP = () => {
  return request({
    method: 'get',
    url: '/subscription/plans',
  })
}

export const subscribePlanHTTP = data => {
  return request({
    method: 'post',
    url: '/subscription/subscribe',
    data,
    notify: false,
  })
}
```

## 竞态请求

需要“只保留最后一次请求结果”的场景（例如搜索、筛选、快速切换 tab）优先使用 `RequestFactory.createLatestRequest()`，避免旧请求覆盖新状态。

## 无感刷新

项目内部已经使用响应拦截器处理了登录失效和权限错误，在定义 API 时，可使用 makeNoneInductiveRequest 创建无感刷新请求，避免登录失效时弹出登录过期提示。

```javascript
import RequestFactory from '@/utils/request.js'

export const cancelWithdrawal = makeNoneInductiveRequest((data) => {
  return instance.request({
    method: 'put',
    url: '/api/withdrawal/application/cancel',
    data,
  })
})
```

如需要结合竞态请求使用，可以先创建无感刷新实例，再基于该实例创建竞态请求：

```javascript
// example
const createDepositOrder = () => {
  return createOrderRequest.run(({ signal, isLatest }) => {
    loading.value = true
    const payload = {
      requestId: query.value.requestId,
      accountNo: accountId.value,
      gatewayId: Number(query.value.gatewayId),
      sourceCurrency: query.value.source,
      targetCurrency: query.value.target,
      sourceAmount: Number(query.value.amount),
    }

    return requestDepositOrder(payload, { signal })
      .then(request => {
        if (isLatest()) {
          orderDetail.value = request.data
        }
        return request
      })
      .finally(() => {
        if (isLatest()) {
          loading.value = false
        }
      })
  })
}
```