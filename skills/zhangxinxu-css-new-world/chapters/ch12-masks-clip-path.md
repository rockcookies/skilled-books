# Chapter 12: 更丰富的图形处理（mask / clip-path / 倒影 / offset 路径运动）

## Core Idea
mask 是"让哪里显示"（像素级、支持图像与渐变合成），clip-path 是"裁出什么形状"（矢量、可动画）；两者 + offset 路径 = 纯 CSS 图形交互三剑客。

## Frameworks Introduced
- **CSS 遮罩 mask 家族**（语义：遮罩图**显示的地方内容才显示**——白/不透明=显示，透明=隐藏）：
  - 全套子属性与 background 平行：`mask-image`（`<image>|<mask-source>`，支持渐变/内联 SVG `<mask>`/外链 SVG#id/cross-fade/image-set/element() 全家桶）、`mask-mode`（alpha（默认按透明度）/luminance（按亮度，可用 JPG 做遮罩）/match-source）、`mask-repeat/position/size/origin/clip`（语义同 background 系）、`mask-composite`（多遮罩合成：add 默认/subtract（挖洞）/intersect（交集）/exclude（异或）——CSS 环形/圆环相减的钥匙）、`mask-type`（SVG 内元素的 mask-mode 等价物）。
  - `mask` 缩写类似 background（`mask: url(x.png) no-repeat center / contain`）。
  - 经典应用：图标任意变色（彩色 icon + 纯色背景 + mask）、PNG→JPG 省体积 78%（JPG+轮廓遮罩恢复透明角）、渐变淡出边缘（`mask-image: linear-gradient(transparent, white)`——倒影的 Firefox 替代方案）。
  - `mask-border`（九宫格边框遮罩，子属性与 border-image 平行；-webkit-mask-box-image 为兼容写法）。
- **clip-path 剪裁**：
  - 值类型：`<basic-shape>`（inset()/circle()/ellipse()/polygon()/path()）、`url(#svgClip)`（Chrome 仅内联 SVG）、`<geometry-box>`（border-box 等，Chrome 对纯盒值不支持；可与 basic-shape 组合 `padding-box circle(50%)`）。
  - `inset(上 右 下 左 round 圆角)`——**唯一能带圆角的剪裁函数**（圆角渐变边框的裁圆角法；`inset(0 round 10px)`）；circle/ellipse 半径+at 位置；polygon 任意多边形（`<fill-rule>, 坐标...` 支持 nonzero/evenodd 填充规则）；path() 任意路径（IE 无缘）。
  - 三大行为特性：① 原始占位保留（同 transform 无侵入）；② **被剪掉的区域不响应点击/hover/active**（与 mask 一致——遮住即不可交互）；③ 基本形状函数**支持过渡与动画**（前提：坐标点数量前后一致）——形状变形动画的零 JS 方案。
  - 应用：图片揭示动画（circle 半径 0→200px）、对话气泡尖角（polygon+calc）、倒角卡片。
- **-webkit-box-reflect**（仅 webkit）：`below|above|left|right 偏移 遮罩图`——倒影不占空间、不响应点击；遮罩图一次一张（常用 linear-gradient(transparent, white) 渐隐）；Firefox 替代 = 复制元素 + `transform: scaleY(-1)` + mask 渐隐。
- **offset 路径运动**（不规则运动首选，替代 JS 逐帧算坐标）：
  - 五子属性：`offset-path`（路径：`path("M...")` / `ray(角度 closest-side contain?)`（射线）/ basic-shape / url(#svgPath)）、`offset-distance`（沿路径距离，0~100%，支持负值与 >100%）、`offset-rotate`（`auto`（朝向切线）| `reverse` | `auto 45deg` | 固定角度）、`offset-anchor`（元素上哪个点贴路径，默认 auto=transform-origin）、`offset-position`（路径起点；非 auto 时创建层叠上下文与包含块）。
  - 动画 = `transition/animation offset-distance`——小车沿 SVG 路径行驶的纯 CSS 实现。
  - `offset` 缩写语法复杂、子属性兼容参差——**作者建议用子属性分开写**。

## Code Examples
```css
/* 图标任意变色（遮罩大法） */
.icon-color {
   width: 20px; height: 20px;
   background: var(--icon-color, deepskyblue);
   mask: url(icon.svg) no-repeat center / contain;
}

/* 圆环：两形状相减 */
.ring {
   mask: radial-gradient(circle, transparent 40%, black 41%);
}

/* 形状揭示动画（点数一致即可过渡） */
.reveal { clip-path: circle(0 at 50% 50%); transition: clip-path .4s; }
.reveal.open { clip-path: circle(200px at 50% 50%); }

/* 沿路径运动 */
.car {
   offset-path: path("M10,80 q100,120 120,20 q140,-50 160,0");
   offset-rotate: auto;
   animation: drive 4s linear infinite;
}
@keyframes drive { to { offset-distance: 100%; } }
```

## Reference Tables
| 需求 | 工具 | 一句话 |
|---|---|---|
| 图片局部淡出/圆环 | mask + 渐变/composite | 遮罩决定可见性 |
| 任意形状裁剪+动画 | clip-path polygon/circle | 点数一致可过渡 |
| 渐变边框裁圆角 | clip-path: inset(0 round N) | border-radius 管不了 border-image |
| 倒影 | -webkit-box-reflect | Firefox 用 scaleY(-1)+mask |
| 沿曲线移动 | offset-path + distance | 替代 JS 轨迹计算 |

## Anti-patterns
- **以为剪裁/遮罩区域还能点击**：被隐藏区域交互一并消失（既是特性也是坑）。
- **clip-path 动画前后点数不同**：无法过渡，只跳变；补齐点数。
- **mask 用 JPG 却按 alpha 模式**：JPG 无透明通道，需 `mask-mode: luminance`。
- **offset 缩写一把梭**：兼容参差整条失效；分开写子属性。

## Key Takeaways
1. mask 问"哪里可见"，clip-path 问"形状是什么"；都保留原始占位、都吃 `<image>`/basic-shape。
2. mask-composite 的 subtract/exclude 是纯 CSS 画环、挖洞的公式。
3. clip-path 过渡条件只有一条：变形前后关键点数量相同。
4. offset-path 把"沿轨迹运动"从 JS 逐帧简化为一条 offset-distance 动画。

## Connects To
- **Ch 5**: conic-gradient+mask = loading 圆环；blur+contrast gooey。
- **Ch 11**: mask 与 filter 都作用于视觉层，注意与层叠上下文的交互。
- **Ch 15**: paint() 可直接画遮罩图像。
