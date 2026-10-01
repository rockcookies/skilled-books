# Chapter 5: 内联元素与流（line-height / vertical-align）

## Core Idea
内联世界的两把钥匙：字母 x 定义基线（一切垂直对齐的原点），幽灵空白节点（strut）让 line-height 与 vertical-align 在"看不见的地方"共同决定高度——内联现象的诡异，几乎都是这两者联手的结果。

## Frameworks Introduced
- **基线体系（字母 x 中心论）**：基线 = 字母 x 的下边缘；x-height = x 的高度（基线到中线 mean line）；`ex` 单位 = 1 个 x-height。
  - `vertical-align:middle` = 元素垂直中心对齐"基线往上 1/2 x-height 处"（即 x 的交叉点），**不是**容器中分线——所以 middle 永远只是"近似垂直居中"（微软雅黑等下沉字体偏移更明显）。
  - `ex` 的副业价值：图标 `height:1ex` 配合默认基线对齐 → 与文字天然垂直居中，不受字体字号影响。
- **line-height 是内联高度之本**：纯内联非替换元素的可视高度完全由 line-height 决定（padding/border 不参与行盒高度计算）；`line-height:0` 会让容器塌成边框，`font-size:0` 不影响 16px 高度。
  - **行距公式**：行距 = line-height − em-box（= font-size）；CSS 中行距上下等分（各半行距），而传统印刷行距独立、InDesign 加在上方——"上下等分"不是理所当然。
  - **大值特性**：内联元素所处行框盒子的高度由**最大的 line-height** 决定（子 20px、父 96px → 容器 96px）。根因：行框盒子前有幽灵空白节点（strut，0 宽、继承字体与行高）。规避：给内联元素 `display:inline-block` 创建独立行框盒子。
- **幽灵空白节点（strut）**：HTML5 文档声明下每个行框盒子前面的 0 宽度假想内联盒。`<div><img></div>` 底部间隙、容器高度 ≠ line-height、`margin-top` 负值"失效"（内联元素被 strut 的基线限死，非主动位移不可能跑出容器）全是它的杰作。
- **vertical-align 四类属性值**：
  1. 线类：`baseline`（默认，≡ 数值 0）/`top`/`middle`/`bottom`；
  2. 文本类：`text-top`/`text-bottom`（对齐父级内容区域，随 font-family 变化，实用性有限）；
  3. 上标下标类：`sub`/`super`（规范用词"合适"太模糊，只配做上标下标；不会改变字号）；
  4. 数值百分比类：正上负下相对基线偏移，**兼容性最好的定位手段**（IE6/7 亦可靠）；百分比相对 line-height 计算值，实战直接用数值（如 `vertical-align:-5px` 像素级图标居中）。
- **inline-block 的基线规则**（理解对齐错位的钥匙）：里面有内联元素且 overflow 为 visible → 基线 = 里面**最后一行内联元素**的基线；否则（空元素或 overflow 裁剪）→ 基线 = 自己的 **margin 底边缘**。

## Key Concepts
- line-height 属性值继承差异：**数值**（`1.5`）继承的是属性值本身，各元素按自己的 font-size 计算；**百分比/长度值**（`150%`/`1.5em`）继承的是计算值（21px），大字号标题会文字重叠。全局重置务必用数值。
- `line-height:normal` 是随 font-family 变化的变量（雅黑≈1.32、宋体≈1.141），跨浏览器默认值不一致，重置势在必行。
- 替换元素不受 line-height 影响；图文混排行框盒子中 line-height 只决定**最小高度**，"文字 20px 行高容器却是 21/22px"是 vertical-align 作祟。
- 图片底部间隙三大元凶：幽灵空白节点 + line-height + vertical-align（x 下边缘与图片下边缘基线对齐，x 的半行距嫁祸给图片）。四种清除：图片块状化（一锅端）/ 容器 line-height 足够小 / 容器 font-size 足够小（行高需相对值才有效）/ 图片 `vertical-align:top|middle|bottom`。
- table-cell 的 vertical-align 作用于**自身**（对齐的是子元素）；给 cell 里的图片设 middle 无效。
- vertical-align 作用前提：display 计算值为 inline/inline-block/inline-table/table-cell；float、absolute 块状化后失效。
- line-height 数值推荐：图文站 1.6~1.8；精确布局可用 `line-height:20px`；**行高计算向上舍入**（`1.42857` 在 Chrome 得 19px，`1.42858` 才是 20px）；心算友好值首选 1.5。

## Code Examples
```css
/* 多行文本/弹框垂直居中：幽灵节点思想 + 双 middle */
.container { position: fixed; top:0; right:0; bottom:0; left:0;
             text-align:center; font-size:0; white-space:nowrap; overflow:auto; }
.container:after { content:''; display:inline-block; height:100%;
                   vertical-align:middle; }        /* 0 宽的"人造 strut" */
.dialog { display:inline-block; vertical-align:middle;
          text-align:left; font-size:14px; white-space:normal; }

/* 20px 图标基线对齐策略：图标内永远有字符 + 不裁剪 */
.icon { display:inline-block; width:20px; height:20px;
        background:url(sprite.png) no-repeat;
        white-space:nowrap; letter-spacing:-1em; text-indent:-999em; }
.icon:before { content:'\3000'; }   /* 全角空格，撑出字符基线 */

/* 两端对齐占位元素消除底部大间隙（三选一） */
.box { text-align:justify; font-size:0; }      /* 或 line-height:0 + 占位 i 塞 &nbsp; */
```

## Reference Tables
| 现象 | 元凶 | 处方 |
|---|---|---|
| 容器高度 > line-height | 大小字号基线位移（strut vs 内联盒） | 统一 font-size 或 `vertical-align:top` |
| 图片底部 5px 间隙 | strut + line-height + baseline 对齐 | 块状化 / line-height:0 / font-size:0 / 改 vertical-align |
| 子元素 line-height 不生效 | 幽灵空白节点行框更高（大值特性） | 子元素 `display:inline-block` |
| inline-block 间基线错位 | 空元素基线 = margin 底边缘 | 内部塞字符 / `vertical-align:top` / 容器 font-size:0 |
| `margin-top` 负值推不出图片 | strut 基线限死内联元素 | 改用 relative/vertical-align 数值 |

## Worked Example
**大小不固定的弹框纯 CSS 水平垂直居中**（对比 JS 定位的优势：无 resize 监听、内容动态变化自适应、`overflow:auto` 超高可滚、伪元素 `height:90%` 可调视觉比例）。原理：`font-size:0` 使 x 中心点=容器上边缘，`height:100%` 的 0 宽伪元素与之对齐后把 x 中心点带到容器垂直中心；弹框 `vertical-align:middle` 再与之对齐。IE7 把伪元素换成真实空元素即可；IE8 蒙层用 base64 半透明 PNG 替代 rgba。

## Anti-patterns
- **`height` + `line-height` 才能单行垂直居中**：只需 line-height；且都是"近似"居中（字形偏下 1px 左右，普通场景可接受）。
- **全局 line-height 用 `150%` 或 `1.5em`**：继承计算值，标题字号一大就重叠——全局只用数值。
- **用 margin/relative 微调内联对齐**：先想基线——多数情况 `vertical-align` 数值一发入魂且无兼容问题。
- **图标元素 `overflow:hidden` 藏字**：会改变基线为底边缘导致对不齐；用 `text-indent:-999em` + `letter-spacing:-1em` 保留基线。

## Key Takeaways
1. 分析任何内联怪象，先假设行首站着一个"幽灵空白节点"，再套 vertical-align 定义逐个分析（各属性值互不冲突）。
2. line-height 决定的是行框盒子高度的基础；内联元素高度听最大的行高（大值特性）。
3. 全局行高只设数值；计算行高永远向上舍入。
4. inline-block 的基线看内容：有字看字，没字（或裁剪）看底边——对齐问题先查基线规则。
5. vertical-align 数值（含负值）是内联垂直微调的最强且最兼容的工具。

## Connects To
- **Ch 3**: 内联盒模型四层结构与幽灵空白节点定义在这里全面投入使用。
- **Ch 6**: 替换元素基线=下边缘、图文混排行盒最小高度在浮动布局中继续发酵。
- **Ch 8**: font-size 与 vertical-align 的隐秘故事（大字号 strut）、font-family 与 normal 行高值在文本处理章深化。
