---
name: zhangxinxu-css-world
description: "Knowledge base from \"CSS世界\" by 张鑫旭. Use when applying 张鑫旭's frameworks for CSS 流布局、内联世界（line-height/vertical-align）、盒尺寸、浮动与定位、层叠规则、文本处理，studying the book, or referencing its concepts."
---

<!-- argument-hint: [topic, framework name, or chapter number] -->

# CSS世界
**Author**: 张鑫旭 | **Pages**: ~344 | **Chapters**: 12 | **Generated**: 2026-10-01

## How to Use This Skill

- **Without arguments** — load core frameworks for reference
- **With a topic** — ask about `垂直居中`、`层叠顺序`、`margin 合并`、`隐藏元素` or another indexed topic; I find and read the relevant chapter
- **With chapter** — ask for `ch05`; I load that specific chapter
- **Browse** — ask "what chapters do you have?" to see the full index

When you ask about a topic not covered in Core Frameworks below, I will read
the relevant chapter file before answering.

---

## Core Frameworks & Mental Models

**造物主视角**：CSS 世界为图文展示而生——遇到费解表现，问"如果我为图文展示设计这个世界，为何如此设计"。一切答案的母题。

**流（flow）**：引导元素排列定位的看不见的"水流"。块级负责结构、内联负责内容；特殊布局（float/absolute）= 对流的破坏与保护。布局第一准则是**鑫三无**：无宽度、无图片、无浮动——设了具体宽度，流动性就死。

**双盒子体系**：每个元素 = 外在盒子（决定能否一行显示）+ 内在盒子/容器盒子（承载宽高）。`width/height`、`box-sizing` 只作用于容器盒子。`display:block` 应脑补为 block-block。

**width:auto 四表现**：fill-available（铺满，唯一外部尺寸）｜包裹性 shrink-to-fit（浮动/absolute/inline-block）｜min-content（收缩到最小）｜max-content（nowrap 溢出）。判据：无内容宽度为 0 → 内部尺寸。

**幽灵空白节点（strut）**：每个行框盒子行首的 0 宽假想内联盒（继承字体与行高）。图片底部间隙、容器高度≠行高、内联 margin-top 负值失效——一切内联怪象先找它。内联盒模型四层：内容区域 → 内联盒子 → 行框盒子 → 包含盒子。

**基线体系**：基线 = 字母 x 下边缘；`middle` = 基线上 1/2 x-height（所以只是"近似垂直居中"）；`ex` = 1 个 x-height（图标 `height:1ex` 天然对齐）。**大值特性**：行框盒子高度听最大的 line-height。

**margin 与 padding 的镜像法则**：margin 只在元素"充分利用可用空间"时改变尺寸；margin:auto = 剩余空间分配器（一侧 auto 全拿、两侧 auto 平分）；父子 margin 合并是布局位移头号元凶（BFC/border/padding/内联分隔/height 五选一阻断）；滚动容器底部留白只能用子元素 margin-bottom。

**定位体系**：absolute 与 float 是兄弟（块状化/包裹性/破坏性/BFC），但 absolute 天然包裹、更霸道（同场 float 失效）。**无依赖绝对定位**——不设方位值就停在原地，免 relative 免 z-index；**对立方位定位 = 流体开关**（四向 0 后 padding/margin/auto 全部可用，居中一行搞定）。relative 定位相对自身且无侵入；**最小化影响原则**：能用无依赖就不用 relative，用则套最小容器。fixed 的包含块是根元素，relative 管不了它。

**BFC 结界**：内部翻江倒海不影响外部。触发：根元素 / float / overflow:auto|scroll|hidden / display:table-cell|inline-block / position≠static。用途排序：自适应布局 > 清浮动 > 阻断 margin 合并。clear:both 只是"自身避让"，不是清除。

**层叠七阶图**（底→顶）：结界背景/边框 → z-index 负值 → block → float → **inline** → z-index:auto/0 → 正值。两条黄金准则：谁大谁上；后来居上。**z-index:0 与 auto 的差别是"是否创建结界"**（opacity≠1、transform≠none 等也创建）——90% 的 z-index 诡异行为源于此。z-index 负值 = 垫底艺术（止步于第一个结界的背景之上）。**不犯二准则**：页面主体 z-index ≤ 2，浮层用层级计数器（默认 9）。

**替换元素**：与非替换元素只隔一个 src 或 content 属性。三层尺寸（固有 < HTML < CSS），与 display 无关。content 生成对象 = 匿名替换元素（不可选中、读屏不可达——只做装饰）。

**文本联动链**：font-size 是乘数——line-height 数值 × font-size，vertical-align 百分比 × line-height。全局行高只设**数值**（150% 继承计算值会压扁大标题）；行高计算向上舍入；Chrome 12px 限制唯一例外是 0。white-space 三问：合并空白？保留换行？允许环绕？

**隐藏决策矩阵**：不渲染 → script 模板｜要动画 → visibility 或 max-height｜要占位 → visibility:hidden｜键盘可达 → clip / z-index:-1 / text-indent｜可点击 → opacity:0｜干净隐藏 → display:none（注意：背景图加载行为跨浏览器不同；display:none 不支持 transition、不参与计数）。

**未定义行为判定**：浏览器差异先查规范——覆盖且违反 = bug；规范空白 = 未定义行为（如 :active+preventDefault、隐藏元素背景图加载、滚动容器 padding-bottom），适配而非修复。

**流向三剑客**：direction 改内联顺序（弹窗按钮反转一行搞定）；unicode-bidi 管字符双向；writing-mode 颠覆纵横规则（垂直流下 margin 合并换轴、margin:auto/text-align 直接垂直居中、text-indent 变垂直位移、字符"躺倒"= 免 transform 旋转）。

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-overview.md) | 概述 | 流、世界观、流体布局 |
| [ch02](chapters/ch02-terminology.md) | 需提前了解的术语和概念 | 术语层级链、未定义行为 |
| [ch03](chapters/ch03-flow-elements-sizing.md) | 流、元素与基本尺寸 | 双盒子、width:auto 四表现、宽度分离、内联盒模型、幽灵空白节点 |
| [ch04](chapters/ch04-box-dimensions.md) | 盒尺寸四大家族 | 替换元素三层尺寸、margin 合并、margin:auto、border 图形 |
| [ch05](chapters/ch05-inline-flow.md) | 内联元素与流 | 基线体系、line-height 大值特性、vertical-align 四类、近似垂直居中 |
| [ch06](chapters/ch06-flow-destruction.md) | 流的破坏与保护 | float 机制、BFC 结界、overflow、无依赖 absolute、relative 最小化 |
| [ch07](chapters/ch07-stacking.md) | CSS 世界的层叠规则 | 七阶图、黄金准则、层叠上下文三派、z-index 负值、不犯二准则 |
| [ch08](chapters/ch08-text-processing.md) | 强大的文本处理能力 | font-size 联动链、white-space 三维度、两端对齐、border 下划线 |
| [ch09](chapters/ch09-decoration.md) | 元素的装饰与美化 | 颜色能力边界、background-position 公式、多背景 hack、IE 滤镜 |
| [ch10](chapters/ch10-visibility.md) | 元素的显示与隐藏 | 隐藏决策矩阵、visibility 继承性、transition 友好性 |
| [ch11](chapters/ch11-ui-styles.md) | 用户界面样式 | outline 双原则、cursor 语义分类、label 代言按钮 |
| [ch12](chapters/ch12-flow-direction.md) | 流向的改变 | direction、unicode-bidi、writing-mode 换轴规则 |

## Topic Index

- **BFC** → ch06
- **background** → ch09
- **border（图形/等高）** → ch04
- **clear** → ch06
- **clip** → ch06, ch10, ch11
- **content 生成** → ch04
- **cursor** → ch11
- **direction / unicode-bidi** → ch12
- **display:none** → ch10
- **float** → ch06
- **font-family / font-weight / font 缩写** → ch08
- **height:100%** → ch03
- **line-height** → ch05, ch08
- **margin 合并 / margin:auto / margin 无效** → ch04
- **min-width / max-width** → ch03
- **outline** → ch11
- **overflow** → ch06
- **padding（百分比/内联行为）** → ch04
- **position:absolute（无依赖/流体）** → ch06
- **relative / fixed** → ch06
- **text-align / text-indent / white-space** → ch08
- **vertical-align** → ch05
- **visibility** → ch10
- **width:auto / 宽度分离 / box-sizing** → ch03
- **writing-mode** → ch12
- **z-index / 层叠** → ch07
- **层叠上下文** → ch07
- **垂直居中** → ch05, ch06
- **等高布局** → ch04, ch06
- **滚动条** → ch06
- **基线 / x-height / ex** → ch05
- **居中（弹框）** → ch05
- **两端对齐** → ch04, ch05, ch08
- **清除浮动** → ch06
- **透明/透明度** → ch09
- **未定义行为** → ch02
- **显隐/隐藏** → ch10
- **替换元素** → ch04
- **下划线** → ch08
- **显隐动画** → ch03, ch10
- **幽灵空白节点** → ch03, ch05
- **锚点定位** → ch06
- **隐藏元素资源加载** → ch09, ch10

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions
- [patterns.md](patterns.md) — all techniques and design patterns
- [cheatsheet.md](cheatsheet.md) — quick reference tables and decision guides

---

## Scope & Limits

This skill covers the book content only (CSS2.1 世界，IE8+ 基线视角)。For hands-on implementation in your codebase,
combine with project-specific tools. For topics beyond this book (CSS3 flex/grid/transform 细节、CSS 变量、object-fit 等"新世界"内容)，
check related skills or ask the agent directly——作者另著有《CSS新世界》覆盖这些主题。
