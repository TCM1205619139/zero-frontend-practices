# 项目适配配置

这个文件记录当前仓库的项目专属规则。迁移到其他项目时，优先调整本文件和 `SKILL.md` frontmatter `description`，尽量不要改通用规范。

## 当前项目

- 仓库：`se-br-client-portal`
- 框架：Vue 3
- 构建：Vite
- 状态：Pinia
- 路由：Vue Router，通过 `addRoute` 动态注册路由
- UI：Vuetify + 项目本地封装组件
- 样式：SCSS + `src/assets/style` token 和全局工具类
- API：`RequestFactory` 封装 axios
- 包管理：pnpm

## 路径职责

- `src/pages`：路由页面。PC/H5 通常拆为 `pc.vue` 和 `mobile.vue`。
- `src/pages/**/hook.js`：页面或业务模块共享逻辑。
- `src/pages/**/components`：当前页面或业务模块私有组件。
- `src/components`：跨业务共享组件。新增前先查找是否已有本地封装。
- `src/api`：API 模块。新 API 需要从 `src/api/index.js` 统一导出。
- `src/store/src`：Pinia store。
- `src/router`：路由配置和动态路由注册。
- `src/utils`：共享工具函数。
- `src/constants`：跨模块常量、枚举、事件名。
- `src/assets/style`：主题变量、原子化 CSS、全局样式、Vuetify 覆盖。
- `src/assets/icons`：可复用 icon，供 `SvgIcon` / `SvgRenderer` 使用。
- `src/assets/svg/<feature>`：非 icon 语义 SVG 插画。
- `src/assets/images/<feature>`：位图资源。
- `src/assets/fonts`：字体资源。

## 命名

- 目录使用 kebab-case 或项目既有小写语义命名，例如 `table-column`、`download-app`、`additional-personal-information-banner`。
- 组件文件使用 PascalCase，例如 `FirstDepositBanner.vue`。
- 设备差异入口使用 `pc.vue` / `mobile.vue`。
- 不要把 PascalCase 组件名直接作为目录名。

## 本地组件优先级

- 按钮优先使用 `SeButton`。
- tabs 优先使用 `SeTabs`。
- 图标优先使用 `SvgRenderer` 或项目现有 icon 组件。
- 图片加载、预览、上传、弹窗等能力，先搜索 `src/components`、`src/utils` 和已有页面用法。
- 只有本地封装不满足需求时，才直接使用 Vuetify 组件。
- 不要为了单个页面视觉效果修改共享组件的全局行为；确实需要改共享组件时，先确认所有已有用法是否安全。

## 资源选择

- 新增可复用 icon 时放 `src/assets/icons`，并通过 `SvgRenderer` / `SvgIcon` 使用。
- 新增非 icon 语义 SVG 时放 `src/assets/svg/<feature>`，通过普通 `import` 使用。
- 新增位图资源时放 `src/assets/images/<feature>`。
- 资源只能放在 `src/assets` 下，不放在 `src/pages/**/assets` 或业务组件目录内。
- 不使用 `new URL('../assets/xxx', import.meta.url).href` 引用资源；SVG 插画使用 `import XxxSvg from '@/assets/svg/<feature>/xxx.svg'` 后按 Vue 组件渲染，图片使用 `@/assets/images/<feature>/xxx.png` 或项目既有 import 模式。
- 移动资源前必须搜索旧路径；删除资源前确认没有其他页面引用。

## Dialog Adaptor

- 业务流程中需要临时弹窗时，优先使用 `src/components/dialog-adaptor/adaptor.js`。
- 普通内容弹窗使用 `makeDialogAdaptor(ContentComponent, wrapperOptions)`。
- 带状态图标的成功、警告、邮件类弹窗使用 `makeStateIconDialogAdaptor(ContentComponent, wrapperOptions)`。
- 弹窗外壳能力放在 `wrapperOptions`，例如 `props.width`、`props.title`、`props.fullscreen`、`props.state`、`mobile.props.sheet`、`style`、生命周期 `on`。
- 内容组件入参、事件和插槽放在 `dialog.show({ props, on, slots, style })`。
- 内容组件只负责展示和业务事件，不直接关心 PC dialog 或 H5 bottom sheet 的容器形态。
- `close` 事件由 adaptor 自动关闭；其他业务事件是否调用 `dialog.hide()` 由调用方显式决定。
- PC/H5 差异优先放在 adaptor wrapper 内处理，不要把弹窗壳结构散落到业务页面模板。

## 埋点

- 事件名称放到 `src/constants/pixel-event.js`。
- 页面和组件级埋点在业务 vue/js 同级新增 `use-pixel-data.js`。
- `usePixelData` 内部封装纯方法调用 `gtmTrack` 或 `makeGTMTrackFn`。
- 公共组件不直接放埋点；公共组件通过 emit 抛业务事件，由页面 hook 接入埋点。
