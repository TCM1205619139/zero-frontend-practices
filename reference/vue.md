# Vue 规则

## 组件结构

- 新增或编辑 Vue SFC 时，默认使用 Vue 3 Composition API 和 `<script setup>`。
- 路由级页面保持轻量，复杂 UI 拆到当前业务目录的 `components`。
- 同一页面存在 `pc.vue` 和 `mobile.vue` 时，先检查两端是否都需要改。
- PC/H5 共享数据、请求、状态和业务动作提取到靠近业务的 hook/composable。
- PC/H5 布局差异保留在各自组件内，不强行合并设备差异模板。
- 内容组件默认宽度 `100%` 并继承容器宽度；容器组件负责布局和外部间距，内容组件负责展示和交互。
- 内容组件最外层 DOM 尽量不要写外部 `margin`，避免破坏父级布局。

## Template

- 模板保持清晰，复杂判断优先放到 computed 或小函数。
- 面向渲染的数据命名建议使用 `renderXxx`。
- 面向状态的 computed 命名建议表达状态语义，例如 `isXxx`、`showXxx`、`disabledXxx`。
- 动态组件可以返回稳定空对象，避免为了模板安全写大量 `v-if`；但不要让空对象掩盖真实异常。
- 组件事件命名表达业务意图，例如 `submit`、`close`、`renew`、`complete`。
- 接口返回 HTML 需要渲染时，不能直接把原始内容传给 `v-html`；先使用 `DOMPurify` 或项目已有净化工具生成安全 HTML。
- 需要从 HTML 字符串提取图片、文件或文本时，优先使用 DOMParser / DOMPurify 这类结构化方式，不用脆弱正则硬拆。

## Script

- store 响应式数据优先使用 `storeToRefs` 解构。
- 异步逻辑优先使用 `async/await`。
- 不要在 `catch` 中吞掉错误；可以做状态回滚或提示，但需要继续抛出或明确交给调用方。
- 常量、枚举、事件名优先放到 `src/constants` 或业务就近常量。
- 页面 hook 返回给组件的内容保持必要，不返回未使用的变量和方法。

## 样式协作

- Vue 文件新增样式前，读取 `reference/style.md`。
- 优先把通用布局、字体、颜色交给项目原子化 CSS。
- 组件私有样式只处理该组件结构语义和局部视觉差异。
- template 中允许完整 BEM class；scoped SCSS 按 DOM 层级嵌套，禁止 `&__xxx`、`&--xxx` 选择器简写。

## 多语言

- 用户可见文案需要国际化。
- 纯文本使用 `t('key')`。
- 带变量文本使用 `t('key', { value })`。
- 需要插入 DOM 或组件的翻译，优先使用项目已有 `I18nT` / `i18n-t` 模式。
- 不要为了拼接样式把完整句子拆成多个无法正确翻译的片段。

## 工作流

当用户明确说“开启工作流”或要求多 agent 协作时，再进入工作流模式：

- 主 agent 负责拆分任务、整合结果和最终交付。
- 开发 agent 负责实现。
- review agent 负责检查 bug、遗漏逻辑和测试风险。
- 可维护性 agent 负责检查命名、结构、样式规范和项目一致性。
- 如果当前环境没有可用 subagent 工具，说明限制后用单 agent 模拟同样的检查清单。
