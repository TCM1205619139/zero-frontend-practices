# SVG 使用规则

项目已使用 Vite SVG 加载方案，并提供 `src/components/SvgRenderer.vue` 和 `src/components/svg-renderer/index.vue`。不要为单个功能重新写一套 SVG loader。

## 放置位置

- 可复用 icon：`src/assets/icons`。
- 可复用 icon 子集：`src/assets/icons/<group>`。
- 页面特有、非 icon 语义 SVG：`src/assets/svg/<feature>`。
- 位图资源：`src/assets/images/<feature>`。
- 不在页面或业务组件目录下新建 `assets` 存放资源。

判断标准：如果它表示一个动作、状态、菜单项、控件符号，并且需要按 `color/size` 复用，放 `icons`；如果它是页面插画、商店徽章、品牌图形、下载页专用 SVG，放 `svg/<feature>`。

## SvgRenderer 用法

`SvgRenderer` 会从 `src/assets/icons/**/*.svg` 中动态加载图标。

```vue
<template>
  <SvgRenderer name="download" size="20" />
  <SvgRenderer name="dashboard/history" size="20" color="#111827" />
  <SvgRenderer file="dashboard" name="history" size="20" />
</template>

<script setup>
import SvgRenderer from '@/components/SvgRenderer.vue'
</script>
```

`name` 中的 `.` 会被转成目录分隔符，例如 `dashboard.history` 会读取 `src/assets/icons/dashboard/history.svg`。

## SVG 处理

- 需要跟随文字颜色变化的 SVG，将可变 `fill` 或 `stroke` 改为 `currentColor`。
- 不要移除 `viewBox`。
- 不要把页面插画强行改造成 `SvgRenderer`。
- 不使用 `new URL('../assets/xxx.svg', import.meta.url).href` 引用 SVG。
- 从 Figma 迁移资源后，搜索旧路径，确认没有遗漏引用。

## 非语义 SVG 用法

非语义 SVG 放在 `src/assets/svg/<feature>`，按 Vue 组件 import 后直接渲染：

```vue
<template>
  <AdditionalPersonalInformationSvg class="banner-illustration" aria-hidden="true" />
</template>

<script setup>
import AdditionalPersonalInformationSvg from '@/assets/svg/dashboard/additional-personal-information.svg'
</script>
```

这类资源不要放到 `src/assets/icons`，除非它已经变成跨页面、跨场景的通用 icon。语义化 icon 使用 `SvgRenderer`，非语义插画不要强行接入 `SvgRenderer`。
