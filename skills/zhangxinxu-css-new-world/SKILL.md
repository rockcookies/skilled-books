---
name: zhangxinxu-css-new-world
description: "Knowledge base from \"CSS新世界\" by 张鑫旭. Use when applying 张鑫旭's frameworks for CSS3+ 新特性（flex/grid 布局、CSS 渐变与动画、CSS 变量 var()、滤镜混合遮罩、clip-path、响应式适配、Houdini @property），studying the book, or referencing its concepts."
---

<!-- argument-hint: [topic, framework name, or chapter number] -->

# CSS新世界
**Author**: 张鑫旭 | **Pages**: ~808 | **Chapters**: 15 | **Generated**: 2026-10-01

## How to Use This Skill

- **Without arguments** — load core frameworks for reference
- **With a topic** — ask about `flex`、`grid`、`CSS 变量`、`渐变`、`遮罩` or another indexed topic; I find and read the relevant chapter
- **With chapter** — ask for `ch06`; I load that specific chapter
- **Browse** — ask "what chapters do you have?" to see the full index

When you ask about a topic not covered in Core Frameworks below, I will read
the relevant chapter file before answering.

---

## Core Frameworks & Mental Models

**数据类型思维**（新世界学习总纲）：属性值 = `<尖括号数据类型>` 的组合。`<image>` = url/渐变/cross-fade/element/image-set/paint——凡支持该类型的属性自动获得全部成员；学一次、处处生效。属性值定义语法（`||`/`#`/`?`…）是"重要性堪比正则"的阅读工具，斜杠规则一句话：**斜杠前后类型必须相同或部分相同**。

**渐进增强五技巧**：直接用 > 属性值语法差异分流（旧声明在前、新语法声明在后）> 伪类/伪元素区分 > @supports（复杂条件靠括号递归嵌套）> JS `CSS.supports()` 兜底。全局关键字四件套：inherit / initial（规范初始值）/ unset（配 all）/ revert（浏览器默认）——`ol{padding:revert}` 才能找回序号。

**尺寸体系**：fit-content（紧身裤=包裹性，核心价值是"尺寸确定值"→ 弹框 `width:fit-content + inset:0 + margin:auto` 居中且不占用 transform）｜min-content/max-content（首选最小/最大内容宽度的显式化）｜stretch（=fill-available，外在尺寸）。逻辑属性 = inline/block × start/end 随流而变（对称布局配 `direction:rtl` 一句完成镜像）；**inset 是唯一日常高频的逻辑属性**（`inset:0` ≡ 四向 0）。

**flex 分家产模型**：flex-basis 分基础、flex-grow 富余分配、flex-shrink 不足分摊。**flex 缩写不按默认值补全**：`flex:1` ≡ `1 1 0%`（basis 不是 auto）、`flex:100px` ≡ `1 1 100px`。单值四件套：`flex:initial`(0 1 auto 小部件)、`flex:0`(最小内容宽)、`flex:none`(固定尺寸免 width、防挤压)、`flex:1`(等分首选、牺牲自己)、`flex:auto`(保内容)。最后一行对齐是 flex 死角——**列数不定就换 grid**。

**grid 轨道尺寸学**：`fr`=纯比例（总和 <1 不铺满）；auto ≈ minmax(min-content,max-content) 且受 justify-content/min-width 影响；minmax 的 **fr 只能当第二参**；fit-content(limit)=max(min-content, min(limit, max-content))。响应式卡片万能起手式：**`repeat(auto-fit, minmax(220px, 1fr))`**（auto-fit 折叠空白轨道；auto-fill 保留）。对齐命名法：justify 水平/align 垂直 × content/items/self。

**sticky 三问**：祖先有无 overflow≠visible（黏性只在最近滚动容器内生效）？父容器是否与元素等高（黏性约束矩形无空间）？多个 sticky 是否分属独立容器（同容器重叠、分容器依次推开——层次滚动的基础）？

**CSS 角度与渐变**：CSS 角度与 PS 相反——**0deg 向上、顺时针为正**；`to 方位值` 免算角度。断点回填规则：后断点位置小于前者按前者渲染（饼图 `gold 0 75%` 免计算）。conic 一行饼图、取色盘、棋盘；渐变是 `<image>` 不可直接过渡（解法：@property）。重复渐变须显式起止（条纹标配）。

**CSS 变量 = 自定义属性 + var()**：作用域即继承（全局放 :root；Shadow DOM 唯一穿透通道）。**fallback 只救"一定无效"**（未定义/空值/全局关键字）；`--x:20px` 配 background-color 会回落初始值而非 fallback。空格开关技巧：`--open: ;`（可能有效=关闭态）↔ `--open: inherit`（一定无效=启用全部 fallback）——一个变量切换多个属性。变量+calc+counter = 数据驱动样式；`@property` 注册类型后变量可过渡（渐变动画的官方解）。

**视觉化学五分工**：filter（自己：十函数可累加，drop-shadow 跟随透明轮廓）、backdrop-filter（身后：毛玻璃正解）、mix-blend-mode（与下方：difference 反色适配深色模式）、background-blend-mode（自身多层背景）、isolation:isolate（混合边界=层叠上下文）。mask 问"哪里可见"（mask-composite subtract/exclude 挖洞画环）、clip-path 问"什么形状"（inset 唯一带圆角；前后点数一致才可过渡；剪掉的区域不可交互）。offset-path + offset-distance 把路径运动一行化。

**文本与字体新世界**：text-emphasis 中文着重号（under right）、text-fill-color 保护 color、font-synthesis:none 禁伪粗体、tab-size 制表宽；font-variant 家族与 font-feature-settings 依赖字体内含 OpenType 特征；可变字体（wght/wdth/ital/slnt/opsz 轴）一个文件精调任意字重。SVG stroke 的 `dasharray=L + dashoffset L→0` 是描边动画万能公式；`paint-order:stroke` 外描边；`vector-effect:non-scaling-stroke` 缩放稳定。

**设备适配**：@media 只记三类型（screen/print/all）+ 主力特性（width/height、prefers-color-scheme、prefers-reduced-motion、hover/pointer）。流式数值黄金公式 **`clamp(MIN, calc(基准 + 斜率*(100vw − 设计宽)/区间宽), MAX)`**；安全区 = env(safe-area-inset-*) + viewport-fit=cover（区分大小写）。touch-action:manipulation 消除点击延迟。滚动体验四件套：scroll-behavior:smooth（顺）、overscroll-behavior:contain（稳）、Scroll Snap（准）、overflow-anchor（不跳）。

**性能三件套**：content-visibility:auto + contain-intrinsic-size（视口外跳过渲染，长列表大杀器）> contain:content（渲染隔离）> will-change（提前优化；**声明即生效对应属性层叠上下文**，少量元素+用完移除）。动画只碰 transform/opacity。

**Houdini 押注两点**：paint()（JS 自绘图像，`<image>` 家族新成员）与 @property（变量类型注册→可过渡/可动画）。其余模块（Layout/Parser/Typed OM/Worklet/Font Metrics）演进中。

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-overview.md) | 概述 | 模块化、新特性四方向、语法/理解/记忆三角 |
| [ch02](chapters/ch02-prerequisites.md) | 需要提前了解的知识 | 数据类型、值定义语法、全局关键字、渐进增强五技巧 |
| [ch03](chapters/ch03-enhanced-properties.md) | 从增强已有的 CSS 属性开始 | 尺寸体系、逻辑属性/inset、border-image 九宫格、sticky、字体新特性、background 新特性 |
| [ch04](chapters/ch04-fine-styles.md) | 更细致的样式表现 | opacity 边界特性、border-radius 重叠算法、box-shadow spread、2D 变换、calc/min/max/clamp |
| [ch05](chapters/ch05-visual-effects.md) | 更强的视觉表现 | 渐变角度体系、断点回填、3D 透视、transition/animation 细节、负延时 |
| [ch06](chapters/ch06-new-layouts.md) | 全新的布局方式 | flex 分家产模型、grid 轨道尺寸、auto-fit 公式、对齐命名法、Shapes |
| [ch07](chapters/ch07-responsive.md) | 不同设备的适配与响应 | @media 矩阵、env()、rem+vw 黄金公式、touch-action、image-set |
| [ch08](chapters/ch08-css-variables.md) | CSS 变量函数 var() 与自定义属性 | fallback 语义、空格开关、作用域穿透、数据驱动样式 |
| [ch09](chapters/ch09-text-upgrade.md) | 文本字符处理能力的升级 | text-emphasis、text-combine-upright、font-variant 家族、可变字体 |
| [ch10](chapters/ch10-media.md) | 图片等多媒体的处理 | object-fit 五值、object-position、cross-fade/element |
| [ch11](chapters/ch11-filters-blending.md) | 更绚丽的视觉表现 | filter 十函数、backdrop-filter、混合模式分工、isolation |
| [ch12](chapters/ch12-masks-clip-path.md) | 更丰富的图形处理 | mask 全家族与 composite、clip-path 形状动画、box-reflect、offset 路径运动 |
| [ch13](chapters/ch13-user-behavior.md) | 用户行为与体验增强 | 滚动四件套、Scroll Snap、pointer-events、性能三件套 |
| [ch14](chapters/ch14-svg-css.md) | SVG 元素的 CSS 控制 | fill/stroke CSS 化、dashoffset 描边动画、paint-order、vector-effect |
| [ch15](chapters/ch15-houdini.md) | Houdini 是 CSS 新的未来 | Paint API、@property、Layout API、Typed OM |

## Topic Index

- **@media / 媒体查询** → ch07
- **@property** → ch15
- **all / inherit / initial / unset / revert** → ch02
- **animation / @keyframes** → ch05
- **auto-fit / auto-fill / repeat()** → ch06
- **backdrop-filter（毛玻璃）** → ch11
- **background 新特性（size/clip:text/origin/多背景）** → ch03
- **border-image / 渐变边框** → ch03
- **calc() / min() / max() / clamp()** → ch04
- **clip-path** → ch12
- **contain / content-visibility / will-change** → ch13
- **cross-fade() / element() / image-set()** → ch07, ch10
- **CSS 变量 / var() / 自定义属性** → ch08, ch15
- **CSS Shapes** → ch06
- **direction 配合逻辑属性** → ch03
- **env() 安全区** → ch07
- **filter / 混合模式 / isolation** → ch11
- **fit-content / min-content / max-content / stretch** → ch03, ch06
- **flex 属性 / flex:0/1/none/auto** → ch06
- **fr / minmax() / grid-template** → ch06
- **gradient 渐变（linear/radial/conic/重复）** → ch05
- **inset 逻辑属性** → ch03
- **mask 遮罩 / mask-composite** → ch12
- **object-fit / object-position** → ch10
- **offset-path 路径运动** → ch12
- **Paint API** → ch15
- **pointer-events / user-select / ::selection** → ch13
- **prefers-color-scheme / prefers-reduced-motion** → ch07
- **scroll-behavior / overscroll-behavior / Scroll Snap** → ch13
- **sticky 黏性定位** → ch03
- **stroke-dasharray / dashoffset 描边动画** → ch14
- **text-emphasis / text-shadow / text-stroke** → ch09
- **touch-action** → ch07, ch13
- **transition / 负延时 / cubic-bezier** → ch05
- **vw/rem 移动适配** → ch07
- **渐变角度 / 断点回填** → ch05
- **可变字体 / font-variation-settings** → ch09
- **逻辑属性 / inline-size / block-size** → ch03
- **数据类型 / 值定义语法** → ch02
- **响应式卡片布局** → ch06, ch07
- **性能优化（contain 三件套）** → ch13
- **遮罩应用（图标变色/环形）** → ch12
- **渐变过渡（@property）** → ch15

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions
- [patterns.md](patterns.md) — all techniques and design patterns
- [cheatsheet.md](cheatsheet.md) — quick reference tables and decision guides

---

## Scope & Limits

This skill covers the book content only（CSS2.1 之后的"新世界"特性，兼容性以书中 2021 年前后的数据为基线，实际项目请以 caniuse 现状为准）。For CSS2.1 基础（流/盒模型/内联世界/层叠），use the companion skill `zhangxinxu-css-world`（《CSS世界》）。
两技能 + 《CSS选择器世界》构成作者笔下 CSS 语言的完整面貌。
