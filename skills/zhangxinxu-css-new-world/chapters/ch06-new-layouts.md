# Chapter 6: 全新的布局方式（分栏 / 弹性 / 网格 / Shapes）

## Core Idea
CSS2.1 没有专门的布局属性（float 是被逼出来的布局工具）；新世界给出四种正牌布局：columns（图文分栏）、flex（一维弹性）、grid（二维网格，27 个属性的最重模块）、shapes（文字环绕任意形状）。

## Frameworks Introduced
- **CSS 对齐特性总纲**（flex/grid 共用的命名法）：`justify-*` 管水平（主轴）、`align-*` 管垂直（交叉轴）；`-content` 管整体（所有行/列作为整体）、`-items` 管每个子项、`-self` 管单个子项。这些属性只在 flex/grid 中生效，但语法在所有布局中合法。弹性布局用 `flex-start/flex-end`（逻辑值，随文档流），网格布局用 `start/end`；`justify-content` 语法初始值是 normal，但**弹性布局中 normal 表现为 flex-start、网格中表现为 stretch**——文档讲"默认值"指当前布局的表现。
- **flex 属性 = 分家产模型**（flex-basis 分基础家产 / flex-grow 富余时分配 / flex-shrink 不足时分摊）：
  - **flex 缩写不按默认值补全**（独此一家）：`flex:1` ≡ `1 1 0%`（basis 不是默认 auto！）、`flex:100px` ≡ `1 1 100px`（grow 不是默认 0！）——规范按实用主义优化计算值。
  - flex-grow：剩余空间分配。总和 >1 按比例全分；**总和 <1 只分走对应比例**（flex-grow:.5 只拿一半剩余空间）。
  - flex-shrink：不足时收缩。总和 ≥1 完全收缩；**总和 <1 收缩不完全（内容溢出容器）**。
  - flex-basis：基础尺寸，默认 auto（类似 width；与 width 同时存在时 basis 优先）。
- **flex 单值四件套**（作者强烈推荐：单值语法语义化，勿轻易用 3 值语法）：

| 单值 | 计算值 | 行为 | 场景 |
|---|---|---|---|
| flex:initial | 0 1 auto | 不增大、可收缩、内容自适应 | 按钮等小部件、一侧定宽自适应两栏（只设 display:flex） |
| flex:0 | 0 **1 0%** | basis=0 → **最小内容宽度**（"一柱擎天"） | 内容主体是替换元素（图+说明文字等宽） |
| flex:none | 0 0 auto | 无弹性，最大内容宽度 | **固定尺寸元素免 width**（列表右侧按钮防挤压） |
| flex:1 | 1 1 0% | 弹性、尺寸不足时**优先牺牲自己**（类似 table-layout:fixed） | 等分列表、动态内容栏（最常用） |
| flex:auto | 1 1 auto | 弹性、尺寸不足时**优先保留内容**（类似 table-layout:auto） | 内容优先的弹性栏 |

- **flex 容器侧**：`flex-direction`（row/row-reverse/column/column-reverse）、`flex-wrap`（nowrap 默认/wrap/wrap-reverse）、`flex-flow` 缩写。对齐：`justify-content`（flex-start/flex-end/center/space-between/space-around/space-evenly——between 只分中间、around 两侧半份、evenly 完全均等）；`align-items`（stretch 默认/flex-start/flex-end/center/**baseline**——所有子项的基线拉到一条线，区别于 flex-end 的下边缘对齐）；`align-content` 管**多行整体**（仅 wrap 后生效）；`align-self` 单项覆盖；`order` 控制视觉顺序（不影响 DOM/无障碍）。
- **grid 模板三属性**：
  - `grid-template-columns/rows`：值的个数=列/行数；`<track-size>` 九类值（长度/百分比/min-content/max-content/auto/fr/repeat()/minmax()/fit-content()）。**网格线命名** `[名称] 尺寸 [名称2]`（可中文；中间线可双名），服务 grid-column-start 等区域描述，页面级语义布局才值得用。
  - **auto 关键字**：上限=最大内容尺寸最大值但**受 justify-content 影响**（stretch 会拉伸超 max-content）；下限=最小尺寸最大值（min-width 可抬高）；多列 auto 不是等分而是"max-content 基础上加同样大小"；≈minmax(min-content, max-content)。
  - **fr（fraction）**：纯比例分配剩余空间，与内容无关（对比 auto 随内容伸缩）。**fr 总和 <1 时不铺满**（.25fr 只拿 25% 可分配空间）；有固定列时 fr 分"容器−固定值"；有 auto 列时 fr 分"容器−auto 列的 fit-content 尺寸"。
  - **minmax(min, max)**：尺寸区间；**fr 只能当第二个参数**（`minmax(1fr, 200px)` 非法整列失效）。
  - **fit-content(limit)** = `max(min-content, min(limit, max-content))`——随内容伸缩但封顶 limit（网格多子项共享包含块，fit-content 关键字无意义，故有函数版）；参数只支持长度/百分比（fit-content(1fr) 非法）。
  - **repeat()**：`repeat(次数|auto-fill|auto-fit, 轨道列表)`；轨道列表可含 minmax()/fit-content()/命名线（不可嵌套 repeat()）。**auto-fill vs auto-fit**：都按容器尺寸自动算列数；auto-fill 保留空白匿名轨道，**auto-fit 把空白轨道连同间隙折叠为 0**——配 `repeat(auto-fit, minmax(100px, 1fr))` 是响应式等分卡片布局的终极一行（子项数不足时也铺满容器）。限制：auto-fill/auto-fit 不能与 auto 关键字混用（可与固定值混用）。
  - 其余：`grid-template-areas`（字符串画区域图，配合 grid-area 名字）、`grid-auto-columns/rows`（隐式轨道尺寸）、`grid-auto-flow`（隐式排列方向 row/column + dense 稠密填充）、缩写 `grid-template` / `grid`（`grid: rows / columns`）；gap 统一三布局（column-gap/row-gap/gap）；`grid-column: start / end`、`grid-row`、`grid-area` 区间定位（支持 span N、负数倒数、命名线）；对齐族：`justify-items/align-items/place-items`（子项在格子内）、`justify-content/align-content/place-content`（整体）、`justify-self/align-self/place-self`。
- **CSS Shapes 布局**：`shape-outside`（作用在浮动元素上，让文字按形状环绕）——值=`<shape-box> || <basic-shape> | <image>`；`circle()/ellipse()/inset()/polygon()/path()`；图片作形状源配 `shape-image-threshold`（透明度阈值）；`shape-margin` 外边距。用途：图文环绕任意形状、杂志式排版。

## Code Examples
```css
/* 响应式等分卡片（新世界最著名的一行布局） */
.container {
   display: grid;
   grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
   gap: 10px;
}

/* 分家产布局：3 个固定 100px + 保底 20px 的弹性兄弟 */
.zhang { flex: 0 2 100px; }  /* 不争富余，不足时双倍分摊 */
.shuai { flex: 3 0 20px; }   /* 争富余 3 份，不足时受保护 */

/* flex:none 保住按钮不被挤压 */
.container { display: flex; }
.container > button { flex: none; }

/* 基线对齐：所有子项文字基线一条线 */
.row { display: flex; align-items: baseline; }

/* Shapes：文字环绕圆形头像 */
.avatar { float: left; shape-outside: circle(50%); shape-margin: 10px; }
```

## Reference Tables
| 布局 | 一维/二维 | 关键属性 | 适用 |
|---|---|---|---|
| columns | 分栏图文 | columns/column-gap/break-inside | 长文报纸式分栏 |
| flex | 一维 | flex 系列 + justify/align | 单行/单列弹性分配 |
| grid | 二维 | template + repeat/fr/minmax + area | 页面骨架、卡片矩阵 |
| shapes | 环绕 | shape-outside | 文字绕图/绕形 |

## Worked Example
**flex 最后一行左对齐**（6.2.13 的四套方案）：① 列数固定：去掉 space-between 改 `margin-right: calc(间隙/(列数-1))` + 负 margin 容器补偿；② 列数固定+space-between：给最后一行"占位补位"（n 个缺位用等尺寸空元素或 ::after）；③ 子项宽度不固定：末尾塞 `margin-right:auto` 的占位元素；④ **列数不固定且 HTML 不可改 → 直接换 grid**（`display:grid; grid-template-columns: repeat(auto-fit, minmax(尺寸, 1fr))` 天然左对齐）——grid 是 flex 布局死角的标准逃生门。

## Anti-patterns
- **以为 flex:1 等于 flex:1 1 auto**：实际是 `1 1 0%`（basis=0）——这个"友善的非默认值"是 flex 大部分尺寸诡异行为的根源。
- **`minmax(1fr, 200px)`**：fr 只能当第二个参数，写反整列失效。
- **fr 之和小于 1 期待铺满**：.25fr 就是只拿 25%，不会等比放大。
- **auto-fill 用于卡片列表**：末行留空白轨道；常规需求用 auto-fit。
- **align-items:baseline 以为是对齐底边**：是基线对齐（x 下边缘），要用底边对齐请用 flex-end。
- **用 order 调顺序当作 DOM 顺序**：读屏/tab 顺序仍按 DOM，无障碍场景慎用。

## Key Takeaways
1. flex 缩写的非默认补全（flex:1→basis 0%）先背下来，再看任何 flex 尺寸问题都不神秘。
2. flex:0/none/1/auto 四单值覆盖 95% 场景；复杂分配才写 3 值（分家产模型：basis-grow-shrink）。
3. grid 尺寸关键字：auto≈minmax(min-content,max-content) 且受对齐属性影响；fr 与内容无关。
4. `repeat(auto-fit, minmax(min, 1fr))` 是响应式网格的万能起手式。
5. flex 最后一行对齐是 flex 死角，列数不固定就换 grid。

## Connects To
- **Ch 3**: min-content/max-content/fit-content 在 grid 轨道尺寸中全面应用。
- **Ch 7**: 响应式断点配合 auto-fit 通常不再需要媒体查询。
- **Ch 13**: contain/content-visibility 配合 grid 大列表做性能优化。
