---
name: zero-frontend-practices
description: 必须用于 se-br-client-portal 仓库的前端任务，以及任何提到 ZERO 前端规范、项目结构、路径职责、Vue 页面/组件、CSS/SCSS、class 命名、原子化 CSS、Figma 实现、资源/SVG/icon、API 接入、路由、Pinia/store、代码 review、重构、构建或 lint 修复的请求。触发后必须先读取本 skill，并按任务类型读取对应 reference。
---

# ZERO 前端规范

这个 skill 记录 ZERO 前端项目的协作方式、代码风格和当前仓库落地规则。只要任务涉及本仓库前端代码，先使用本 skill，再按需使用 `vue-best-practices`、`figma-implement-design`、`pinia`、`vant-vue3` 或 `vueuse-functions`。

## 必须遵守

### 协作边界

- 回复用户时，开头或自然位置必须包含 `老爷～`。
- 修改前先阅读现有代码，优先沿用本地模式，不套用泛化示例。
- 用户要求改代码时，直接实现；除非用户明确要求方案、解释或 review。
- 不要回退无关的用户改动。
- 只改任务相关代码；不要做无关格式化、重排 import、重命名、迁移或大范围风格治理。

### 技能和参考

- Vue 相关任务同时使用 `vue-best-practices`。
- Figma 实现同时使用 `figma-implement-design`，并读取 `reference/figma.md`。
- 新增或修改样式时，必须读取 `reference/style.md`。
- 当前项目路径、组件体系、Dialog adaptor、资源目录等项目专属规则读取 `reference/project.md`。

### 项目硬规则

- 本项目默认 JavaScript，禁止新增 TypeScript 文件；新增 Vue SFC 不使用 `<script setup lang="ts">`。该规则优先于通用 `vue-best-practices` 中的 TypeScript 默认建议。
- 复杂业务状态优先抽成语义化 getter、computed 或 helper，不在页面模板和事件里散落状态码、planCode、magic string 判断。
- 异步动作必须有明确 loading 复位；轮询、定时器、SSE、事件监听等副作用必须在同一业务范围内清理。
- 组件 props、emits、方法命名表达业务语义；公共组件不直接写业务埋点。
- 不要为了润色随意修改已有 i18n key；改用户可见文案前确认翻译资源和业务语义。

## 读取清单

按任务类型加载最少必要 reference，避免漏读关键规则：

- `.vue`、页面、组件、hook/composable：读取 `reference/vue.md` 和 `reference/style.md`。
- CSS/SCSS、class 命名、布局、响应式、原子化 CSS：读取 `reference/style.md`。
- Figma URL 或设计稿实现：读取 `reference/figma.md`、`reference/style.md`、`reference/project.md`。
- API、请求、代理、接口文件：读取 `reference/axios-factory.md`。
- SVG、icon、图片、字体：读取 `reference/svg-usage.md` 或 `reference/font-usage.md`。
- 项目结构、路径职责、本地组件、弹窗：读取 `reference/project.md`。
- JavaScript 代码风格、异步、常量、错误处理：读取 `reference/javascript.md`。
- JS/TS 边界或异步副作用：读取 `reference/javascript.md`。
- 组件契约、i18n key 或 Vue 模板表达：读取 `reference/vue.md`。
- 历史债治理、跨 feature 复用或项目边界：读取 `reference/project.md`。

## 通用实现规则

- 路由级页面保持轻量，较大 UI 区块拆到靠近业务的 `components` 目录。
- PC/H5 同时存在时，先检查两端；共享状态和动作提取到靠近业务的 hook/composable，设备差异留在各自组件。
- 功能组件文件名使用 PascalCase；路由入口或设备差异页面按项目约定使用 `pc.vue` / `mobile.vue`。
- 不要为了“看起来通用”创建抽象；只有真实减少重复或符合现有模式时才抽象。
- 优先使用项目已有封装组件、工具函数、CSS 变量和原子化 class；组件语义 class 只承担结构命名和组件专属样式。
- 业务代码不要直接使用裸 `fetch`、裸 `axios` 或 `XMLHttpRequest`。
- i18n 文案必须使用项目翻译方式，不把用户可见文案硬编码为单语言。
- 新增业务 hook、store、api、constants 使用 `.js`；现有 `.ts` 文件只在任务明确触达时做最小维护，不作为新增代码范式。
- 页面私有组件默认只在当前 feature 内使用；跨 feature 复用前先抽到公共层或明确说明依赖边界。

## 样式硬规则

- 禁止 SCSS 选择器简写，例如 `&__content`、`&--active`。模板中允许完整 BEM class，但样式里必须写完整 class，例如 `.component-name__content`、`.component-name--active`。
- 禁止新增不清晰缩写 class。项目历史工具类如 `space-bt`、`btn-color` 可以沿用，但不要作为新命名范式扩散。
- 组件私有样式使用 scoped SCSS，并按照 DOM 层级嵌套。
- 写组件 class 前，先查项目原子化 CSS 和已有工具类；能用 `flex`、`items-center`、`gap-16`、`font-14`、`text-ellipsis` 等工具类解决的，不新增只服务这些声明的私有 class。
- 边距、圆角、颜色优先使用项目 token；`var(...)` 不写回退值。
- 尽可能避免样式穿透；能通过插槽、额外包裹标签或本地组件 props 实现的，不使用 `:deep`。
- hover-only 效果放进 `@media (hover: hover)`。

## Review 清单

完成或 review 前端改动时，至少检查：

- 是否读取了任务对应 reference。
- 是否符合现有本地模式，没有引入不必要抽象。
- 是否避免了无关格式化、import 重排、跨模块治理或 JS 到 TS 迁移。
- PC/H5 是否需要同步处理。
- 复杂业务状态是否抽成了清晰语义，而不是散落原始状态值判断。
- 异步 loading、轮询、定时器、SSE 或事件监听是否有复位和清理。
- class 命名没有简写，样式嵌套符合 DOM 层级。
- 是否优先使用了项目原子化 CSS、token 和封装组件。
- 是否避免把历史缩写工具类扩散成新的组件命名。
- 是否没有新增 TypeScript 文件或 `<script setup lang="ts">`。
- 是否没有随意修改现有 i18n key。
- 没有回退无关文件或用户改动。
- 资源路径正确，旧路径没有残留引用。
- touched files 的 `git diff --check` 通过。
- 变更影响运行时时，已执行 build、lint 或针对性验证；无法执行时说明原因。
- 修改后检查 touched files 中是否存在废弃方法、组件、import 或资源引用；能安全删除就删除，不确定就询问。

## 回复风格

- 以 `老爷～` 开头或自然包含。
- 简洁、具体。
- 说明实际改了哪些文件，以及通过了哪些检查。
- 如果工具、Figma 上下文、dev server 或测试无法运行，明确说明阻塞原因。
