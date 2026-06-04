# 字体使用规则

项目字体统一在 `src/assets/style/font.scss` 中注册，再通过全局样式继承使用。不要在普通组件里重复 `@font-face` 或直接引入字体文件。

## 当前模式

- 非 Google 字体在 `src/assets/style/font.scss` 的 `$fonts` map 中维护。
- 字体文件放在 `src/assets/fonts`。
- `@font-face` 由 `register-fonts` mixin 统一生成。
- 字体加载使用 `font-display: swap`。
- Google 字体由 Vite/项目配置处理，不在 `font.scss` 重复注册。

## 新增字体

1. 将字体文件放入 `src/assets/fonts`。
2. 在 `src/assets/style/font.scss` 的 `$fonts` 中增加 family、base、format、weights。
3. 优先全局继承字体；只有设计明确要求局部不同字体时，才在组件样式中覆盖 `font-family`。

示例：

```scss
$fonts: (
  "D-DIN-PRO": (
    base: "D-DIN-PRO",
    format: "opentype",
    weights: (
      400: "-400-Regular.otf",
      500: "-500-Medium.otf",
      700: "-700-Bold.otf",
    )
  )
);
```

组件中如需局部字体，直接使用已注册字体名，不要再次导入字体资源：

```scss
.amount {
  font-family: "D-DIN-PRO", sans-serif;
}
```
