# Chapter 11: 更绚丽的视觉表现（filter / backdrop-filter / 混合模式 / isolation）

## Core Idea
滤镜管"自己"、backdrop-filter 管"身后的背景"、mix-blend-mode 管"与下方元素"、background-blend-mode 管"自己的多层背景"、isolation 画混合的边界——五者构成视觉化学全套装备。

## Frameworks Introduced
- **filter 十滤镜函数**（2013+ 全支持，可任意累加）：
  `blur(5px)`（高斯标准差；**不支持百分比**）、`brightness(2.4)`（1 为原值）、`contrast(2)`、`drop-shadow(4px 4px 8px blue)`、`grayscale(70%)`、`hue-rotate(90deg)`、`invert(75%)`、`opacity(25%)`、`saturate(230%)`、`sepia(60%)`。
  - **drop-shadow vs box-shadow**：前者跟随**实际渲染轮廓**（透明 PNG 轮廓、border 三角的形状投影），但无 inset、不支持多重投影叠加；后者盒模型投影。
  - 累加化合：`invert(11%) sepia(81%) saturate(7450%) contrast(114%)` 可把任意图染成指定色；`blur(10px) contrast(5)` = gooey 黏连效果。
  - 支持引用 SVG 滤镜：`filter: url(#goo)` / `url(filters.svg#filter)`。
  - filter≠none 创建层叠上下文（ch07 的老朋友）；滤镜动画开销大——慎用于大面积。
- **backdrop-filter**：语法与 filter 完全同构，作用对象是**元素身后的背景区域**——毛玻璃效果的官方解（`backdrop-filter: blur(5px)` + 半透明背景色）；元素自身内容不受影响。已可大规模使用。
- **混合模式三属性**：
  - `mix-blend-mode`：元素与**下方内容**混合。常用值：multiply（正片叠底，白底消失）、screen（滤色，黑底消失）、overlay（叠加）、lighten/darken、difference（差值——反色，"深色模式图片自适应"经典）、hue/saturation/color/luminosity。
  - `background-blend-mode`：元素**自身多层背景之间**混合（背景图×渐变×背景色）。细节：默认与背景色也混（需 `background-color: transparent` 隔离）；自身带隔离性；多值按 background-image 列表对应（不足循环、多余忽略）。
  - `isolation: isolate`：创建**层叠上下文**从而限制 mix-blend-mode 的混合范围——不与"组件外"内容混合（混合模式的分组边界）。
  - 经典组合：暗色文字图 mix-blend-mode:screen 深色模式自动变亮；`filter + mix-blend-mode` 实现图标任意变色（`img { filter: brightness(3) invert(1) grayscale(1); }` 之类链）。
  - **注意混合陷阱**：父级任何创建层叠上下文的属性（transform/opacity/filter/isolation…）都会成为混合边界——元素"突然不与页面背景混合了"先查祖先。

## Code Examples
```css
/* 毛玻璃卡片 */
.glass {
   background: rgba(255,255,255,.2);
   backdrop-filter: blur(8px);
}

/* 透明 PNG 轮廓投影（box-shadow 做不到） */
.sticker { filter: drop-shadow(0 6px 8px rgba(0,0,0,.3)); }

/* 差值模式：深色模式 Logo 自动反色 */
.logo { mix-blend-mode: difference; }

/* 限制混合范围：弹层内部自己玩 */
.modal { isolation: isolate; }
```

## Reference Tables
| 想做什么 | 用谁 |
|---|---|
| 元素自身模糊/变色 | filter |
| 磨砂玻璃（身后模糊） | backdrop-filter |
| 跟随透明轮廓的投影 | filter:drop-shadow |
| 白底图标融入背景 | mix-blend-mode: multiply |
| 多背景纹理融合 | background-blend-mode |
| 组件内混合不外泄 | isolation: isolate |

## Anti-patterns
- **drop-shadow 想要内阴影或多层**：能力只有 box-shadow 有。
- **毛玻璃只写 backdrop-filter 不给半透明底色**：视觉上"没效果"。
- **mix-blend-mode 混到了意想不到的地方**：祖先层叠上下文边界 + isolation 控制。

## Key Takeaways
1. 滤镜函数可累加，累加顺序即处理管线；SVG 滤镜可接入。
2. backdrop-filter 是毛玻璃正解，语法与 filter 相同。
3. 混合模式四件套各管一层：自身/身后/与下/内部，isolation 定边界。
4. filter/opacity/backdrop-filter 都创建层叠上下文——与 ch07 层叠知识联动。

## Connects To
- **Ch 5**: 滤镜+渐变可做纹理；渐变动画缺口的 Houdini 解法在 ch15。
- **Ch 4**: box-shadow vs drop-shadow 的取舍表。
- **《CSS世界》Ch 7**: 层叠上下文创建清单在本章继续扩编（filter/backdrop-filter）。
