# figma 实现规则

读取此文档前，先使用 implement-design skill，以便更好地理解设计系统的相关概念和规范。
此文档只做一些补充说明。

1. 使用 figma-desktop MCP 获取设计上下文和截图。尽量将设计图中的资源（如图标、图片）下载到项目内使用，避免直接引用 Figma 资源链接。
2. 对于可复用图标资源，优先下载 SVG，并按项目图标体系处理（例如将可变颜色改成 `currentColor`）。
3. 对于页面特有、非 icon 语义的 SVG，放到 `src/assets/svg/<feature>`；可复用 icon 才放到 `src/assets/icons`。
4. Figma 中的 button、tabs、select、input 等基础组件，优先使用项目已有封装（如 `SeButton`、`SeTabs`）或 Vuetify 组件二次封装。
5. 如果组件库可以满足设计，尽量不要改组件内部主题，只处理布局、间距、状态和局部样式。
6. 如果组件库无法满足设计，需要先判断是否适合局部 DOM/CSS 实现；涉及全局组件行为变化时再询问开发者。
7. 边距，字号，颜色等视觉细节，优先使用项目 token 和工具类实现，避免写死数值。
