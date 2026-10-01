# Chapter 4: 盒尺寸四大家族（content / padding / margin / border）

## Core Idea
四大家族性格迥异：content 决定"是不是替换元素"，padding 温和（几乎无意外），margin 激进（合并、负值、auto 都有特异行为），border 功勋卓越（本职画框，衍生图形构建/布局利器）。

## Frameworks Introduced
- **替换元素判定与本质**：修改某个属性值（如 `img src`）即可替换呈现内容的元素（img/object/video/iframe/textarea/input/select）。
  - 三特性：外观不受页面 CSS 影响（需 `appearance` 或私有伪元素）；有自己的默认尺寸（多为 300×150，`<img>` 为 0，表单随浏览器）；有一套自己的表现规则（`vertical-align:baseline` 在替换元素上被定义为**下边缘**——因为其内容可能不含字符 x）。
  - **距离只有一点**：与非替换元素只隔一个 `src` 属性（`<img>` 无 src 就是普通内联元素；Chrome 下需非空 `alt` 触发）或一个 `content` 属性（`content` 生成的对象就是"匿名替换元素"）。`content` 生成的文本无法选中/复制、读屏读不到、SEO 抓不到——只用于装饰性内容。
- **替换元素尺寸三层结构**（从内到外）：固有尺寸（内容原本尺寸）→ HTML 尺寸（`width/height`、`size`、`cols/rows` 属性）→ CSS 尺寸（width/height 属性，作用于 content box）。
  - 优先级 CSS > HTML > 固有；有固有宽高比时只设一边会等比缩放；全都没有 → 300×150（`<img>` 例外，各浏览器 0×0/0×22/28×30 不等）。
  - **与 display 无关**：inline/block/inline-block 尺寸规则一致——所以 `img { display:block }` 不会铺满容器。
  - 固有尺寸不可改变：图片显示尺寸变化的本质是 `fill` 填充适配（CSS3 才能用 `object-fit` 改）。
- **margin 改变尺寸的条件**（与 padding 恰好互补）：只有元素处于"充分利用可用空间"（fill-available）状态时 margin 才能改变其尺寸；定宽或包裹性状态下无效。垂直方向同理仅限绝对定位拉伸元素。
- **margin 合并**：块级元素（不含浮动/绝对定位）的垂直 margin（严格说：与当前文档流方向相垂直的方向）合并为一个。三种场景：
  1. 相邻兄弟；
  2. **父子**（父和第一个/最后一个子元素）——"父元素没出力，子元素出全力，margin 全合到父元素上"；阻止：父元素成为 BFC / 设 border-top(bottom) / padding-top(bottom) / 中间加内联元素；bottom 还可设 height/min-height/max-height。
  3. 空块级元素自身合并（`margin:1em 0` 的空 div 高度就是 1em）。
- **margin:auto 填充规则**：auto 是为填充"闲置空间"设计的强计算关键字。一侧定值一侧 auto → auto 取剩余空间全部；两侧 auto → 平分剩余空间。前提是元素有"自动填充"能力（块级水平方向、绝对定位对立方位拉伸时）。
- **border-color 默认 = color 计算值**（outline/box-shadow/text-shadow 同理）——用 border 画图形、用 `color` 控制颜色，hover 只需改一处。
- **border-style:double 表现规则**："双线宽度永远相等，中间间隔 ±1"；1px/2px 时与 solid 无异，3px 起才有双线——这就是 border-width 默认值是 medium(3px) 而非 1px 的原因（保证 double 语义成立）。

## Key Concepts
- 尺寸三概念（按 jQuery API 命名）：元素尺寸 `offsetWidth/Height`（border box）、元素内部尺寸 `clientWidth/Height`（padding box）、元素外部尺寸（margin box，无原生 API，**可为负**——理解为"占据的空间尺寸"）。
- 内联元素的垂直 padding **有效**：影响视觉层也影响布局层（父容器 `overflow:auto` 出滚动条即为证），只是不加入行盒高度计算；非替换内联元素的 padding/margin/border 都不参与行高计算。垂直 margin 则彻底无效（石沉大海）。
- padding 百分比与 margin 百分比：**水平垂直都相对宽度**——这是等比例矩形（`padding:50%` 正方形、`padding:10% 50%` 5:1 头图）的实现基础；内联元素 padding 会断行（跟着行框盒子走）并让幽灵空白节点显现（空 span `padding:50%` 高宽不一，`font-size:0` 修复）。
- margin 合并时改值可能"无效"：除非新值更大或为负。
- 图片 margin 永不合并：内联替换元素垂直 margin 有效且无合并行为。
- border 转角等分平滑规则（由 inset/outset 等老样式奠定）：4 色边框 → 梯形（width:10px）→ 三角（width:0）——一切三角/梯形图形皆可 border 模拟。
- `border-color:transparent` IE7+ 就支持（早于 `color:transparent` 的 IE9）。
- 滚动容器底部留白：IE/Firefox 忽略滚动容器的 padding-bottom（触发滚动条的判定基准不同：Chrome 按 content box，IE/FF 按 padding box——属未定义行为），**必须用子元素 margin-bottom 实现**。

## Code Examples
```css
/* 两端对齐 + 底对齐柱状图（纯 CSS，全兼容） */
.box { text-align: justify; }
.box:before { content:""; display:inline-block; height:100%; }  /* 底对齐 */
.box:after  { content:""; display:inline-block; width:100%;  }  /* 两端对齐 */

/* margin 负值等高布局 */
.column-box { overflow: hidden; }
.column-left, .column-right { margin-bottom: -9999px; padding-bottom: 9999px; }

/* border 等高布局（父级不能 overflow:hidden 清浮动） */
.box { border-left: 150px solid #333; }
.box > nav { width:150px; margin-left:-150px; float:left; }

/* 朝下的三角形 */
div { width: 0; border: 10px solid; border-color: #f30 transparent transparent; }

/* img 未加载时用 alt 显示信息（利用"无 src 即非替换元素"） */
img { display: inline-block; }   /* 建议加入 CSS reset，修复 Firefox */
img::after { content: attr(alt); position:absolute; ... }

/* 透明边框实现右下角 background 定位（背景默认相对 padding box） */
.box { border-right: 50px solid transparent; background-position: 100% 50%; }
```

## Reference Tables
| margin 无效/"失效"情形 | 原因与对策 |
|---|---|
| inline 非替换元素垂直 margin | 规范说渲染但浏览器无踪迹；改用垂直 padding |
| table-cell / table-row 的 margin | 无效；table/inline-table/table-caption 有效 |
| margin 合并中改小值 | 被合并值掩盖；改更大值或负值 |
| 绝对定位非定位方位的 margin | 其实一直有效（改变外部尺寸），只是独立渲染不影响兄弟 |
| 定高容器子元素 margin-bottom / 定宽子元素 margin-right | 超出无处体现；"失效"是视觉假象 |

| border-style | 要点 |
|---|---|
| none（默认值） | `border:10px`、`border:red` 都不显示边框；`border:solid` 出 3px |
| dashed | Chrome/FF 比例 3:1、间隔 1:1；IE 为 2:1——只能当虚框用 |
| dotted | Chrome/FF 是方点，IE 是圆点 → IE8 用 `border:149px dotted` + `overflow:hidden` 造圆角 |
| double | ≥3px 才有双线；"双线等宽，间隔±1"；可做等宽三道杠图标 |
| inset/outset/groove/ridge | 无实用价值，但奠定了 solid 转角等分规则（三角绘制的基础） |

## Worked Example
**两端对齐列表（一行 3 个，间隙 20px，兼容 IE8）**：浮动列表最后总会多出右侧 20px 间隙，`nth-of-type(3n)` 或加类名都需要 HTML 配合。解法是给父容器负 margin"增加可用宽度"：
```css
ul { margin-right: -20px; }          /* 容器可用宽度变为 100% + 20px */
ul > li { float: left; width: 100px; margin-right: 20px; }
```
第 3n 个 li 的 margin-right 恰好落回容器 100% 位置，纯 CSS 两端对齐。

## Anti-patterns
- **`*{box-sizing:border-box}` 全局重置**（再次警示）：padding 过大时 width 依然会失效（表现为首选最小宽度）；局部使用 + 无宽度/宽度分离才是正道。
- **以为内联元素垂直 padding 无效**：它影响视觉与布局，只是不影响行高——"加 padding 扩点击区"正是利用此特性。
- **滚动容器用 padding-bottom 留白**：IE/Firefox 忽略之；用子元素 margin-bottom。
- **把重要文本放进 content**：无法选中、无法被读屏/SEO 感知；content 只做装饰。
- **`<img src="">` 做占位**：空 src 仍会发起请求（请求当前页面）；直接省略 src 属性才零请求。
- **依赖 dashed/dotted 的精确渲染**：比例与点形各浏览器不同，只能当虚框用。

## Key Takeaways
1. 判定替换元素只看"内容可否被属性替换"；其尺寸规则与 display 无关，三层优先级 CSS > HTML > 固有。
2. margin 只在 fill-available 状态下改变尺寸；margin:auto 是剩余空间分配器（一侧 auto 全拿，两侧 auto 平分）。
3. 父子 margin 合并是布局"莫名位移"的头号元凶：BFC、border、padding、内联分隔、height 五选一阻断。
4. `border-color` 缺省跟随 `color`：图形用 border 画、颜色用 color 控，hover 一处生效。
5. 等高布局选型：无 IE6/7 用 table-cell；有锚点定位风险时用 border 法；margin 负值法注意 overflow 与锚点坑。
6. margin 负值 + 容器加宽是纯 CSS 两端对齐列表的标准解。

## Connects To
- **Ch 3**: 容器盒子四层结构、fill-available/包裹性/首选最小宽度在本章全面应用。
- **Ch 5**: 内联元素 padding/margin 的行为要用内联盒模型与幽灵空白节点解释。
- **Ch 6**: BFC（overflow:hidden）既是 margin 合并的阻断手段也是等高布局的裁剪工具；锚点定位问题在 6.4 展开。
