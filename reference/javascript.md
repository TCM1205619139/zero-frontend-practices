# JavaScript 规则

- 本项目默认使用 JavaScript，不允许新增 TypeScript 文件。
- 不要把 `.js` / `.vue` 主动迁移成 `.ts` / `<script setup lang="ts">`。
- 现有 `.ts` 文件是历史遗留，只在任务明确触达时做最小维护，不作为新增代码范式。
- 新增业务 hook、store、api、constants 默认使用 `.js`。
- 异步调用优先使用 `async/await`，避免 `.then()` 链式嵌套。
- 尽可能使用箭头函数，不使用 `function`，除非项目已有 API 明确依赖 `this`。
- `catch` 中不能吞掉错误；可以做状态回滚、提示或日志处理，但需要继续 `throw`，或明确把错误交给调用方处理。
- 用户主动触发的异步动作必须有明确 loading / disabled 边界，并使用 `finally` 或等价方式复位。
- 轮询、倒计时、SSE、websocket listener、DOM/window 事件监听等副作用，必须在同一业务范围内注册和清理。
- 搜索、筛选、tab 切换、分页等可能并发的请求，要考虑旧请求覆盖新状态；优先使用项目已有 latest request 能力或显式竞态保护。
- 数学运算涉及金额、精度、比例时，使用 Decimal，不直接使用 `+`、`-`、`*`、`/`。
- 正则、枚举、事件名、跨模块 magic string 放到 `src/constants` 或业务就近常量。
- 复杂业务状态优先抽成语义化 getter、computed 或 helper，不在页面事件和模板中散落状态码、planCode、magic string 判断。
- 文件内只使用一次、且不会跨模块复用的展示配置，可以就近定义，避免为了形式抽到全局常量。
- 业务 hook 返回值保持必要；删除不再使用的变量、方法、import。
- 只改任务相关代码，不做无关格式化、重排 import、重命名或大范围风格治理；看到历史债只在 touched scope 内处理。
