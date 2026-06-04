# SVG 使用规则

项目已使用 Vite SVG 加载方案，并提供 `src/components/SvgRenderer.vue` 和 `src/components/svg-renderer/index.vue`。不要为单个功能重新写一套 SVG loader。

## 放置位置

- 可复用 icon：`src/assets/icons`。
- 可复用 icon 子集：`src/assets/icons/<group>`。
- 页面特有、非 icon 语义 SVG：`src/assets/svg/<feature>`。
- 位图资源：`src/assets/images/<feature>`。

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
- 从 Figma 迁移资源后，搜索旧路径，确认没有遗漏引用。

## 页面专用 SVG 用法

页面专用 SVG 可以按普通资源 import：

```vue
<template>
  <AppStoreSvg class="store-badge" />
</template>

<script setup>
import AppStoreSvg from '@/assets/svg/download/app-store.svg'
</script>
```

这类资源不要放到 `src/assets/icons`，除非它已经变成跨页面、跨场景的通用 icon。
