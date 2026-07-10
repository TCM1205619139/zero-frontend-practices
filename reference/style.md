# 样式规则

## 先查现有能力

新增组件 class 或私有样式前，先检查：

- `src/assets/style/index.scss`
- `src/assets/style/unilities.scss`
- `src/assets/style/common.scss`
- `src/assets/style/theme.css`
- 当前页面和相邻组件的既有 class 用法

能用项目原子化 CSS 和工具类解决的布局、文字、间距、颜色，不要重复写组件私有样式。组件仍然需要保留语义化 root class 和必要结构 class；原子类负责通用视觉，组件 class 负责组件专属结构和状态。

常见可复用类包括：

- 布局：`flex`、`flex-col`、`flex-1`、`items-center`、`items-start`、`items-end`、`space-bt`、`space-center`
- 尺寸：`w-full`、`h-full`、`size-full`、`w-fit`
- 间距：`gap-4`、`gap-8`、`gap-12`、`gap-16`、`gap-20`、`gap-24` 等
- 字号和字重：`font-12`、`font-14`、`font-16`、`font-20`、`bold`、`semi-bold`、`medium`
- 文本和颜色：`text-center`、`text-left`、`text-right`、`text-ellipsis`、`primary-color`、`content-color`、`title-color`、`error-color`

项目存在一些历史缩写工具类，例如 `space-bt`、`btn-color`。这些类可以在延续现有页面模式时使用，但不要新增同类缩写作为组件私有 class。

## Class 命名

- class 使用连字符分隔单词，例如 `support-content`。
- 组件私有 class 可以使用 BEM 语义，例如 `support-content__empty`、`support-content--mobile`。
- 禁止为了少写字母使用不清晰缩写，例如 `profile` 不写成 `pf`，`container` 不写成 `ctn`。
- BEM 中的 `__` 和 `--` 是完整 class 名的一部分，允许在 template 中使用；禁止的是 SCSS 中写 `&__empty`、`&--mobile` 这种选择器简写。
- 状态 class 使用 `is-` 前缀，例如 `is-active`、`is-expanded`、`is-disabled`。
- 动画 class 使用 `animate-` 前缀，例如 `animate-fade-in`。
- class 命名前先判断职责：原子类处理通用视觉规则，组件 class 处理结构语义和组件专属样式。
- 不要为了通用 flex、gap、字号、颜色新增私有 class；优先组合原子类和项目 token。

```vue
<!-- good: 语义 class + 原子类组合 -->
<div class="support-content flex flex-col gap-16">
  <div class="support-content__body flex-1">
    ...
  </div>
</div>
```

```vue
<!-- bad: 为通用布局额外造私有 class -->
<div class="support-content">
  <div class="support-content__flex-column-gap">
    ...
  </div>
</div>
```

## SCSS 写法

组件私有样式使用 scoped SCSS，并按照 DOM 层级嵌套。禁止把同一组件的 class 平铺成多段，也禁止使用 `&__xxx`、`&--xxx` 这类选择器简写。

```scss
// good
.support-content {
  display: flex;

  .support-content__body {
    flex: 1;

    .support-content__empty {
      color: var(--schemes-background-surface-on-surface-variant);
    }
  }

  &.support-content--mobile {
    .support-content__body {
      padding: var(--Spacing-XL);
    }
  }
}
```

```scss
// bad: 平铺，层级关系不清楚
.support-content {}
.support-content__body {}
.support-content__empty {}
```

```scss
// bad: 使用 class 简写
.support-content {
  &__body {}
  &--mobile {}
}
```

## Token 和数值

- 边距、圆角、颜色优先使用 `src/assets/style/theme.css` 中的变量。
- `var(...)` 不写回退值，例如 `color: var(--schemes-primary-primary);`。
- 不直接写设计系统外的颜色；设计稿出现色盘外颜色时，需要指出并优先映射到项目 token。
- `box-shadow`、渐变、透明色如果已有 token，优先使用 token。
- 字号优先使用全局字体工具类；只有特殊布局确实不能复用时再写私有样式。
- 修改旧组件时，如果 touched scope 内已有裸 `10px`、`16px`、硬编码颜色等历史写法，能安全替换为 token 或工具类就顺手替换；不做无关大范围格式化。
- 不新增静态内联 style；动态样式可以使用 `:style`，但静态布局、字号、颜色、间距应放到 class、工具类或 token 中。

## 布局

- 不允许用 `calc(100vh - xxx)` 实现主体自适应高度；优先使用 flex、`flex: 1`、`height: 0`。
- 固定格式 UI 需要稳定尺寸约束，避免 hover、文字、icon、loading 导致布局跳动。
- 文本不能溢出父元素；优先使用 `text-ellipsis` 或明确换行策略。
- PC/H5 差异优先通过已有工具类、组件 props、少量 modifier class 处理，不把大量条件样式写进组件。

## 交互和覆盖

- hover-only 效果放在 `@media (hover: hover)` 中。
- 需要 hover/active/focus 状态时，优先复用项目组件已有状态。
- 尽可能不要使用样式穿透。能通过插槽包裹、额外标签、本地组件 props 或局部 class 实现的，不使用 `:deep`。
- 不要为了单个页面视觉效果修改全局覆盖样式；确实需要修改 `vuetify-overrides` 时，先确认所有既有用法。

## 动画

- 动画需要符合设计语义，不为了装饰随意添加。
- 尽量不要在单个 `.vue` 文件内新增通用动画；可复用动画放到全局动画样式中。
- 展开、收起、显示、隐藏等交互状态建议配合 `is-` 状态 class 和过渡效果。
