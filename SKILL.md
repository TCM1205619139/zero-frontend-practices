---
name: zero-frontend-practices
description: 在 se-br-client-portal 仓库进行任何前端工作时必须使用，包括 Vue 页面和组件、CSS/SCSS、Figma 设计稿实现、资源和 SVG/icon 处理、API 接入、路由、Pinia/store、代码 review、重构、构建和 lint 修复。用户询问 ZERO 前端规范、项目结构、命名、PC/H5 响应式实现或团队实践时也必须使用。
---

# ZERO 前端项目规范

这个 skill 记录 `se-br-client-portal` 仓库的前端专用规则。

只要任务涉及本仓库前端代码，就加载这个 skill。如果另一个 skill 也适用，先使用本 skill 对齐项目规范，再使用对应领域 skill，例如 `vue-best-practices`、`figma-implement-design`、`pinia`、`vant-vue3` 或 `vueuse-functions`。

## 必须遵守

- 回复用户时，开头或自然位置必须包含 `老爷～`。
- 用户要求改代码时，优先直接、务实地实现，避免只给长篇方案。
- 修改前先阅读现有代码，优先沿用本地模式，不套用泛化示例。
- 不要回退无关的用户改动。
- 做 Vue 相关任务时，同时使用 `vue-best-practices` skill。
- 根据 Figma 实现页面或组件时，同时使用 `figma-implement-design` skill，然后补充阅读 `reference/figma.md` 中的本项目规则。

## 项目技术栈

- 框架：Vue 3。
- 路由：Vue Router，通过 `addRoute` 动态注册路由。
- 状态：Pinia。
- UI：Vuetify 加本地封装组件和覆盖样式。
- 构建：Vite。
- 包管理：pnpm。
- 样式：SCSS，项目 token 来自 `src/assets/style`。
- API：axios 统一由 `RequestFactory` 封装；业务代码不要直接使用 `fetch`、裸 `axios` 或 `XMLHttpRequest`。

## 当前目录结构

- `src/pages`：路由页面。PC/H5 通常使用 `pc.vue` 和 `mobile.vue`。
- `src/components`：共享组件。创建新组件前优先查找已有组件。
- `src/api`：API 模块。`src/api/index.js` 是统一导出入口。
- `src/assets/images`：位图资源，按功能或页面分目录。
- `src/assets/svg`：非 icon 语义的 SVG 插画或页面专属 SVG，按功能或页面分目录，例如 `src/assets/svg/download`。
- `src/assets/icons`：可复用 icon SVG，供 `SvgIcon` / `svg-renderer` 使用。
- `src/assets/fonts`：字体资源。
- `src/store/src`：Pinia store。
- `src/router`：路由配置和动态路由注册。
- `src/utils`：共享工具函数。

## 实现规则

### Vue 和组件结构

- 新增或编辑 Vue SFC 时，默认使用 Vue 3 Composition API 和 `<script setup>`，除非周边代码明确要求其他写法。
- 路由级页面保持轻量。较大的 UI 区块拆到当前页面的 `components` 目录。
- 同一页面同时存在 PC 和 H5 版本时，把共享变量和业务逻辑抽到靠近业务的 hook/composable，避免两边重复维护。
- 功能组件文件名使用 PascalCase；路由入口或设备差异页面按现有模式使用 `pc.vue` / `mobile.vue`。
- 不要为了“看起来通用”创建抽象。只有真实减少重复或符合现有模式时才抽象。用户明确要求在组件内写纯 DOM 时，不要再包一层中间组件。
- 用户要求改页面时，先检查是否同时存在 PC 和 H5。
- 如果用户只要求一端，变更范围保持在这一端，除非确实需要移动共享逻辑或资源。
- 两端需要同一份数据或动作时，创建共享 hook。
- 设备差异布局保留在各自组件内，不要强行合并。
- vue 文件补充规则见 `reference/vue.md`。

### 样式

- 优先使用项目 CSS 变量和已有工具类。
- 组件私有样式使用 scoped SCSS。
- hover-only 效果必须放在 `@media (hover: hover)` 中。
- 避免新增和产品 UI 冲突的一次性装饰背景。
- 复杂背景上的渐变边框，需要把背景层和边框层分开。优先使用伪元素 border mask 或同等可靠方案，避免背景盖住边框。
- outlined 按钮的 hover/active 背景需要裁剪到 padding box，保证边框仍然可见。
- PC/H5 样式差异优先使用工具类和 CSS 变量覆盖，避免在组件内写过多条件样式。

### Vuetify 和本地组件

- 优先使用本地封装，例如 `SeButton`、`SeTabs`、`SvgIcon`、`ProgressiveImage`、dialog 组件和现有页面组件。
- 只有本地封装不满足需求时，才直接使用 Vuetify 组件。
- 不要为了单个页面视觉效果全局修改共享组件，除非该修改对所有已有用法都安全。

### 资源、SVG 和图标

- 可复用 icon 放到 `src/assets/icons`。
- 页面特有、非 icon 语义的 SVG 放到 `src/assets/svg/<feature>`。
- 位图资源放到 `src/assets/images/<feature>`。
- 移动资源时，必须搜索旧路径，确认没有影响其他页面或模块。
- SVG 细节规则见 `reference/svg-usage.md`。

### API

- API 函数必须使用项目请求工厂模式。
- 新 API 模块需要从 `src/api` 统一导出。
- baseURL 使用环境配置，不要硬编码。
- 请求相关细则见 `reference/axios-factory.md`。

### 字体

- 优先沿用现有全局字体配置。
- 不要在组件内重复导入或注册字体，除非确实必要。
- 字体配置细节见 `reference/font-usage.md`。

### Figma

- 从 Figma URL 实现页面或组件时，如果工具可用，必须先获取 Figma 上下文或截图。
- 如果 `get_design_context` 被 Figma Dev Mode allowed directories 限制阻塞，简要说明原因；在截图和元数据足够时继续实现。
- 匹配 Figma 布局和视觉层级时，优先使用项目组件和项目 token。
- Figma 补充规则见 `reference/figma.md`。

## Review 清单

完成或 review 前端改动时，检查：

- 实现符合现有本地模式。
- PC/H5 共享逻辑没有不必要的重复。
- 没有回退无关文件或用户改动。
- 资源路径正确，旧路径没有残留引用。
- hover 样式按需放进 `@media (hover: hover)`。
- SVG 根据语义放在 `icons` 或 `svg/<feature>`。
- touched files 的 `eslint` 通过。
- touched files 的 `git diff --check` 通过。
- 变更可能影响运行时时，已执行 build 或针对性验证。

## 回复风格

- 以 `老爷～` 开头。
- 简洁、具体。
- 说明实际改了哪些文件，以及通过了哪些检查。
- 如果工具、Figma 上下文、dev server 或测试无法运行，明确说明阻塞原因。
