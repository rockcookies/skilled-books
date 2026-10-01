# Chapter 14: SVG 元素的 CSS 控制

## Core Idea
SVG 的外观属性（fill/stroke/marker 系）已全面 CSS 化——继承行为、currentColor 联动、描边动画（stroke-dasharray/offset）是前端应用的核心抓手。

## Frameworks Introduced
- **CSS 直接绘制 SVG 图形**：`<svg>` 内用 `<circle>`、`<rect>` 等标签 + CSS 属性上色（`circle { fill: deepskyblue; }`）。对比 HTML+border-radius 画圆：SVG 圆形必须在 `<svg>` 画布里、且是矢量个体——两者互补而非替代。
- **填充系**：
  - `fill`：**有类似继承的表现**——`<svg> { fill: currentColor; }` 让整个图标树继承文字颜色（图标变色的标准姿势）；也可 `fill: url(#gradient)` 引用渐变。
  - `fill-opacity`（0~1 透明度；尽量别用百分比）、`fill-rule: nonzero|evenodd`（路径交叉区域的填充规则——五角星镂空等效果的开关）。
- **描边系**（图形与文字通用）：
  - `stroke` / `stroke-width`：**全兼容文字描边**的正确姿势（`text { fill: white; stroke: deepskyblue; }`）——比 -webkit-text-stroke 兼容面广。
  - `stroke-linecap`（butt/round/square 端点）、`stroke-linejoin`（miter/round/bevel 转角）、`stroke-miterlimit`（默认 4，锐角处理）、`stroke-opacity`。
  - `stroke-dasharray: 14 4 4 4`——虚实段循环（多值依次循环）。**描边动画公式**：`stroke-dasharray: L; stroke-dashoffset: L → 0`（L=路径总长）即"画出来"效果——环形进度条（SVG 圆周长 1069px 的 dashoffset 渐变）、签名动画、下划线动效的地基。
  - 标记系：`marker-start/marker-mid/marker-end: url(#marker)`（路径起止/转折点挂箭头圆点，IE9+）。
- **实用杂项**：
  - `paint-order: fill || stroke || markers`——绘制顺序；`paint-order: stroke` 让描边垫底 = **外描边效果**（描边不侵蚀字形，解决 -webkit-text-stroke 的内侵问题）。
  - `vector-effect: non-scaling-stroke`——SVG 缩放时描边宽度**不跟随缩放**（波形图自适应宽度时保持 2px 描边的标配）。
  - `text-anchor: start|middle|end`——SVG 文字水平锚点（middle=坐标居中）。
  - `dominant-baseline: middle`——SVG 文字垂直居中（配合 text-anchor:middle 即"圆内居中文字"）；alignment-baseline 是相对行内其他元素的基线对齐（两者别混）。

## Code Examples
```css
/* 图标随文字变色（继承 + currentColor） */
svg.icon { fill: currentColor; }
.btn:hover svg.icon { fill: #fff; }

/* 环形进度条：dashoffset 驱动 */
circle.progress {
   fill: none;
   stroke: url(#gradient);
   stroke-width: 50px;
   stroke-dasharray: 1069px;
   stroke-dashoffset: 1069px;      /* 0% */
   transition: stroke-dashoffset .6s;
}
circle.progress.done { stroke-dashoffset: 0; }  /* 100% */

/* 外描边文字（描边不侵字） */
text { fill: crimson; stroke: white; stroke-width: 6px; paint-order: stroke; }

/* 自适应波形保持描边宽度 */
path.wave { vector-effect: non-scaling-stroke; }
```

## Reference Tables
| 效果 | SVG CSS 组合 |
|---|---|
| 图标变色 | svg{fill:currentColor} |
| 描边"画出来"动画 | dasharray=长度 + dashoffset L→0 |
| 外描边文字 | paint-order: stroke |
| 缩放不变形描边 | vector-effect: non-scaling-stroke |
| 圆内文字居中 | text-anchor:middle + dominant-baseline:middle |

## Anti-patterns
- **用 -webkit-text-stroke 做粗描边**：向内侵蚀字形；paint-order:stroke（SVG）或双层 text 兜底。
- **直接改 SVG 属性写死颜色**：丢掉 fill:currentColor 的继承变色能力。
- **环形进度硬算 dashoffset 却不设 dasharray**：dasharray 决定"一圈=多长"，两者配套。

## Key Takeaways
1. SVG 外观即 CSS：fill/stroke/marker 全部可写样式表，且吃继承与 currentColor。
2. dasharray/dashoffset 是 CSS 描边动画的唯一公式（L = 路径总长）。
3. paint-order:stroke = 外描边；vector-effect:non-scaling-stroke = 缩放稳定描边。
4. SVG 文字居中两件套：text-anchor:middle（水平）+ dominant-baseline:middle（垂直）。

## Connects To
- **Ch 12**: clip-path url(#id) 引用 SVG 剪裁；mask-source 同理。
- **Ch 11**: filter: url(#svgFilter) 引用 SVG 滤镜（gooey）。
- **Ch 9**: SVG stroke 是全兼容文字描边的最终答案。
