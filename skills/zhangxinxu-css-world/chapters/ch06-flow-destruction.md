# Chapter 6: 流的破坏与保护（float / clear / BFC / overflow / position）

## Core Idea
float、absolute 都是为"文字环绕"设计的流破坏者——高度塌陷不是 bug 是标准；理解"行框盒子与浮动元素不可重叠"和"无依赖定位"，就能用最小的 CSS 获得最健壮的布局。

## Frameworks Introduced
- **float 四特性**：包裹性（包裹+自适应，上限受首选最小宽度约束）；块状化（display 计算值变 block，inline-table 例外变 table——所以 `float` 后写 `display:block`/`vertical-align` 都是多余的）；破坏文档流（万恶之源+立命之本）；无 margin 合并。
- **float 作用机制双术语**：
  - **浮动参考（float reference）**：float 元素对齐的参考是**行框盒子**（不是包含块）——标题换行后，行内末尾的 `float:right` 链接会对齐最后一行的行框盒子右侧。
  - **浮动锚点（float anchor）**：float 所在流中的一个点，表现如空的内联元素，作用是"产生"行框盒子，使纯块级元素上下文也有对齐参考。
  - **行框盒子与浮动元素不可重叠性**：块盒与浮动元素完全重叠，但其中的行框盒子被限死在浮动元素一侧（margin 负值也推不动）——这是"文字环绕"的实现本质。
- **clear 的真相**：官方定义"元素盒子的边**不能和前面的浮动元素相邻**"——是自身抗浮动，不是清除浮动。`clear:left/right` 毫无使用价值（起效时必等价于 `both`，且只对"前面的"浮动生效）。`clear:both` 的局限：前面是浮动元素时 margin-top 负值无效；后面元素仍可能被环绕。
- **BFC = CSS 世界的结界**：内部子元素再怎么翻江倒海都不影响外部（所以 BFC 元素不会发生 margin 合并、天然清除浮动影响）。触发条件：根元素 / float≠none / overflow:auto|scroll|hidden / display:table-cell|table-caption|inline-block / position≠relative|static。
- **BFC 自适应布局**（BFC 最重要的用途，胜过 margin 避让）：浮动元素旁边的 BFC 块盒自动收缩避让且**宽度不限**——一侧定宽用 float+margin 避让（保流动性），宽度完全不定则给自适应栏触发 BFC。
- **absolute 与 float 是兄弟**（块状化、包裹性、破坏性、BFC 四大共性），但 absolute 更霸道：二者同时存在时 float 无效。absolute 天然包裹性，无需 `display:inline-block`。
- **absolute 的包含块**：最近 position≠static 祖先的 **padding box**（普通元素是父块容器的 content box）；祖先为纯内联元素时 = 前后生成的 0 宽内联盒的 padding box 包围盒（范围稳固，不受 line-height 影响，受 ::first-line 不受 ::first-letter；跨行未定义）；无定位祖先 → 初始包含块（视口）。
- **无依赖绝对定位**：不设 left/top/right/bottom 的 absolute **停留在原位置**（保持"相对特性"，只是不占流空间）。错误认知"会跑到窗口左上角"导致滥用 `relative + top/left + z-index` 三件套。实践：图标定位只需 `position:absolute + margin 微调`，原样式零改动，文字长度变化也自适应。
- **absolute 流体特性**：对立方向同时定位（`left:0;right:0` / `top:0;bottom:0`）→ 格式化宽度/高度，自适应包含块 padding box，**水平垂直双向流动**（比普通块级更强）——覆盖全屏用四向定位而非 `width/height:100%`（后者加 margin 直接跑出窗体，加 padding 溢出）。
- **relative 两大定位特性**：相对自身偏移；**无侵入**（后续元素纹丝不动，与 margin 定位的本质差异）。
- **relative 的最小化影响原则**（作者自创实践准则）：① 尽量不用 relative，优先"无依赖绝对定位"；② 必须用时，把 relative 套在仅包住目标元素的最小容器上，不污染整个模块。

## Key Concepts
- float 让父元素高度塌陷是**标准行为**（为环绕而生）；塌陷 + 行框盒子限缩共同成就环绕效果。
- overflow 剪裁/滚动界线 = **border box 内边缘**（想"剪裁+四周留白"用透明边框，padding 无能为力）；Chrome 把 padding-bottom 计入滚动尺寸而 IE/Firefox 忽略（作者认为忽略才符合规则）——滚动容器底部留白一律用子元素 margin。
- overflow-x/y 组合规则：一方 visible 另一方 auto/scroll/hidden 时，visible 按 auto 解析——不可能"一向滚动、一向溢出显示"。
- PC 端滚动条来自 `<html>`（去滚动条只需 `html{overflow:hidden}`，移动端不适用）；Windows 滚动栏精确占宽 **17px**，会造成居中布局加载晃动与双 table 表头错位（预留 17px 或末列自适应）。
- 单行文字打点三件套缺一不可：`text-overflow:ellipsis + white-space:nowrap + overflow:hidden`；多行打点 `-webkit-line-clamp` 无需 overflow:hidden。
- **锚点定位的本质 = 改变容器滚动高度**（scrollTop），由内而外触发所有可滚动容器；两种触发：URL 锚链交互、可 focus 元素获得焦点。`<a href="#">` 返回顶部优于 `javascript:`（无 JS 依赖）；这也是 margin 负值等高布局锚点错乱的根源。
- fixed 的包含块是根元素，relative 无法限制；要"被限制的固定效果"用 **absolute 模拟**（页面滚动交给内部 `overflow:auto` 容器）或无依赖固定定位（`text-align` + 0 高辅助层 + margin 偏移）。
- 弹窗蒙层背景锁定：根元素 `overflow:hidden` + 透明 `border-right:17px` 补偿滚动条消失的宽度跳动。
- relative 的百分比相对包含块（包含块高度 auto 时 top/bottom 百分比计算值 0，偏移无效）；对立方向同用时"你死我活"按文档流顺序定胜负（top 压 bottom、left 压 right）——与 absolute 的拉伸完全不同。

## Code Examples
```css
/* 一侧定宽自适应：浮动破坏流 + 兄弟流动性 */
.father { overflow: hidden; }      /* BFC 结界包裹 */
.father > img { float: left; }
.animal  { margin-left: 70px; }    /* 保持流动，自适应 */

/* 宽度不定的自适应栏：直接 BFC（无需计算避让宽度） */
.animal { display: table-cell; width: 9999px; }  /* 或 overflow:hidden 等 */

/* 全屏覆盖（流体写法，padding/margin 自动分配） */
.box { position: absolute; left:0; right:0; top:0; bottom:0; }

/* 绝对定位元素水平垂直居中（流体态 + margin:auto） */
.box { position:absolute; left:0; right:0; top:0; bottom:0;
       width: 200px; height: 100px; margin: auto; }

/* 无依赖定位的图标：跟随文字，原样式零污染 */
.icon-hot { position: absolute; margin: -6px 0 0 2px; }
```

## Reference Tables
| 需求 | 正确姿势 | 反面写法 |
|---|---|---|
| 清除浮动影响 | 容器触发 BFC（如 overflow:hidden） | 见 div 就加 clearfix |
| 自适应两栏 | float + 兄弟 margin 避让 / BFC 栏 | 双 float + 定宽（砖头式） |
| absolute 覆盖全屏 | 四向 0 定位 | width/height:100% |
| absolute 居中 | 四向 0 + margin:auto | top:50%+margin-top 负一半 |
| 小图标角标 | 无依赖 absolute + margin | 父 relative + top/right 定值 |
| fixed 受限/被裁剪 | absolute 模拟（内部滚动容器） | 嵌套 transform hack |

## Worked Example
**"标题 + 更多"链接**：`<h3>标题<a style="float:right">更多</a></h3>` 在 IE8+ 直接可用。原理：`<a>` 的浮动参考是它所在**行框盒子**——标题一行时对齐第一行右侧；标题两行时"更多"落在第二行右侧；标题正好占满两行时"更多"孤零零落到第三行（容器高度仍为两行）。同理可推：要让"更多"永远在第一行右侧，把它放在标题文字**前面**（第一行行框盒子内）。IE6/7 因不支持"浮动与内联同行"会换行。

## Anti-patterns
- **`float` + `display:block`/`vertical-align`/`text-align` 组合**：float 已块状化；text-align 对块级盒无效。
- **absolute 必配 relative + left/top + z-index 三件套**：无依赖定位大多一行搞定且免维护。
- **`relative` 直接挂在模块容器上**：子元素 absolute 的包含块被整个模块霸占；按最小化影响原则包一层小容器。
- **滚动容器 padding-bottom 做底部留白 / 依赖其 scrollHeight**：跨浏览器不一致。
- **`clear:both` 当万能清浮**：它只让"自身"换行；后面元素仍会被环绕，且 margin 负值失效——要"彻底"用 BFC。
- **`overflow-y:auto` 跟在 `overflow-x:hidden` 后面**：visible 已被按 auto 解析，纯冗余。

## Key Takeaways
1. 高度塌陷是标准：想清除的是"影响"不是"浮动"；BFC 结界是正解，clear 只是自身避让。
2. 浮动定位参考是行框盒子（由浮动锚点产生）——一切"float 元素跑错行"问题由此解释。
3. 无依赖绝对定位 = 原地悬浮：能不用 relative 就不用，用则最小化。
4. 对立定位方向 = 流体开关：absolute 四向 0 后与普通流元素一样吃 padding/margin/auto，还能双向流动。
5. 锚点定位 = 容器 scrollTop 变化（由内而外），是滚动容器类布局错乱的通用病根。

## Connects To
- **Ch 3**: 包裹性、格式化宽度、首选最小宽度在 float/absolute 上全面复现。
- **Ch 4**: BFC 阻断 margin 合并、透明边框剪裁留白、17px 滚动条与 border 补偿。
- **Ch 7**: BFC/定位与层叠上下文的创建规则相接（z-index 的舞台由本章搭建）。
