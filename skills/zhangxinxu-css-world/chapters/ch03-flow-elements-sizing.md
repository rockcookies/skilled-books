# Chapter 3: 流、元素与基本尺寸

## Core Idea
元素由"外在盒子+内在盒子（容器盒子）"两层构成，width/height 只作用在容器盒子上；`width:auto` 不是"没有宽度"，而是包含四种截然不同的尺寸表现——掌握它们才掌握流体布局。

## Frameworks Introduced
- **双盒子体系**：每个元素 = 外在盒子（决定一行显示还是换行）+ 内在盒子/容器盒子（决定宽高与内容呈现）。
  - `display:block` 应脑补为 `block-block`（简写），规范的新脑补是 `block flow`；`inline-block` = 外内联、内块级——所以既能与图文一行显示又能设宽高。
  - `width/height`、`box-sizing` 都只作用于容器盒子。
- **width:auto 的四种表现**（判断元素宽度行为的第一决策树）：
  1. **充分利用可用空间**（fill-available）——`<div>`/`<p>` 默认铺满父容器，唯一的外部尺寸（Extrinsic），是"流"的精髓。
  2. **收缩与包裹**（shrink-to-fit，作者称"包裹性"）——浮动、绝对定位、inline-block、table；CSS3 的 `fit-content` 即此表现。
  3. **收缩到最小**（min-content，规范名 preferred minimum width / minimum content width）——文字能断就断，常见于 `table-layout:auto` 的"一柱擎天"。
  4. **超出容器限制**（max-content）——连续英文/数字或 `white-space:nowrap`，"流到断崖也不回头"。
  - 判断内部/外部尺寸：元素没内容时宽度为 0 → 内部尺寸。
- **流动性**：不是"宽度 100%"，而是 margin/border/padding 和 content 自动分配水平空间的机制。
- **鑫三无准则**：无宽度、无图片、无浮动——作者多年的布局总则。
- **宽度分离原则**：width 不与影响宽度的 padding/border（有时含 margin）共存；width 独占一层标签，内层靠流动自适应。改 padding 无需重算宽度。
- **格式化宽度**：非替换元素 `left/right`（或 `top/bottom`）对立方位同时设置时，宽度由外部决定，相对于最近的定位祖先计算，具有完全的流体性（"格式化高度"同理，仅存在于绝对定位模型）。
- **超越 !important，超越最大**：min/max 的覆盖铁律——`max-width` 超级覆盖 `width`（连 `style` + `!important` 都拍沉）；`min-width` 与 `max-width` 冲突时 min 胜出。
- **内联盒模型（简易版）**：内容区域（content area，可用"文本选中背景色"可视化，本质是字符盒子/em-box）→ 内联盒子（有标签，如 `<em>`）/匿名内联盒子（光秃秃文字）→ 行框盒子（line box，每一行）→ 包含盒子（containing block，如 `<p>`）。
- **幽灵空白节点**（规范名 strut，"支柱"）：HTML5 文档声明下，每个行框盒子前面都有一个 0 宽度、具有元素字体和行高属性的假想内联盒。空 `<span>` 撑起 div 高度就是它作祟。

## Key Concepts
- **块级元素 ≠ display:block**：`<li>`（list-item）、`<table>`（table）都是块级元素，共同特征是水平流上单独占一行、换行显示；因此理论上都能配合 `clear` 清除浮动。
- **list-item 的项目符号**来自附加的"标记盒子"（marker box）；IE 伪元素不支持 `display:list-item` 即因无法创建标记盒子——清除浮动只用 `block` 或 `table`。
- **替换元素**（`<img>`/`<textarea>` 等）：尺寸由内部决定，不受 display 水平影响。
- **首选最小宽度**：中文 = 一个汉字宽；英文 = 连续字符单元（终止于空格、短横线、问号等）；图片 = 自身内容宽。`word-break:break-all` 可让英文逐字断行。
- **min-width/min-height 初始值是 auto**（所有浏览器，非文档标注的 0；transition 从初始值变化无动画可证）；max-* 初始值是 `none`（若为 auto，width 将永远无法超过 auto 计算值）。
- **height:100% 失效原因**：父级高度非显式时，规范把子元素百分比高度解析为 `auto`（`auto × 100% = NaN`）；不是"死循环"（渲染一次到位，宽度同场景是未定义行为而非死循环）。
- **绝对定位的百分比基准**：绝对定位元素宽高百分比相对 padding box；普通元素相对 content box。

## Mental Models
- 判断宽度行为："无内容宽度为 0 → 内部尺寸；铺满父级 → 外部尺寸/流体；想让它别超容器 → 包裹性自带 `max-width:100%` 罩着"。
- 造物主视角：CSS 为图文而生，图片和文字的权重远大于布局——所以 width:auto 时图文宽度永远不会是 0（首选最小宽度存在的理由）。
- 用 box-sizing 的视角想问题：它只改变 width 作用的盒子，`border-box` 让 content 从宽度值中"释放"，形成局部流动性。

## Code Examples
```css
/* 清除浮动：用 block 或 table，不用 list-item（IE 伪元素不支持） */
.clear:after {
   content: '';
   display: table;
   clear: both;
}

/* 宽度分离：外层定宽，内层流动自适应，改 padding 不用重算 */
.father { width: 102px; }
.son    { border: 1px solid; padding: 20px; }

/* height:100% 生效的两条路 */
html, body { height: 100%; }        /* 显式高度链 */
div { height: 100%; position: absolute; }  /* 绝对定位，相对 padding box */

/* max-height 展开收起动画（值要"足够安全的最小值"，太大收起有延迟感） */
.element { max-height: 0; overflow: hidden; transition: max-height .25s; }
.element.active { max-height: 666px; }
```

## Reference Tables
| width:auto 表现 | 规范/新名字 | 典型元素 | 尺寸归属 |
|---|---|---|---|
| 铺满可用空间 | fill-available | div/p | 外部尺寸 |
| 包裹性 | shrink-to-fit / fit-content | inline-block、浮动、绝对定位 | 内部尺寸 |
| 收缩到最小 | min-content / 首选最小宽度 | table 单元格断字 | 内部尺寸 |
| 超出容器 | max-content | nowrap 连续文本 | 内部尺寸 |

| 属性 | 初始值 | 覆盖规则 |
|---|---|---|
| width/height | auto | 被 max-* 超级覆盖（>!important） |
| max-width/height | none | 被 min-* 覆盖 |
| min-width/height | **auto**（非 0） | 最大 |

## Worked Example
"文字少居中、多行居左"的一层标签实现——利用 inline-block 包裹性：
```css
.box    { text-align: center; }
.content{ display: inline-block; text-align: left; }
```
文字少时 `.content` 被内容包裹（窄），随 `text-align:center` 居中；文字多时 `.content` 撞到容器宽度上限自动换行（包裹性的自适应性），外层 center 不影响整块，内层 left 生效。同一原理可做"图片左右半区点击上一张/下一张"：外层 `display:inline-block; position:relative`，覆盖两个 `height:100%` 的绝对定位半区，任意图片高度都自动适配。

## Anti-patterns
- **`a { display:block; width:100% }`**：块级默认已铺满，`width:100%` 纯属多余；加了 margin/padding 反而溢出（流动性丢失）。
- **"砌砖头"式计算宽度**：`width = 容器 − padding − margin` 手工计算，模块一变全盘重算——用流动或宽度分离替代。
- **全局 `*{box-sizing:border-box}`**：对内联元素和默认即 border-box 的 search 输入框是无谓消耗，且解决不了含水平 margin 的场景；box-sizing 的真正初衷是替换元素（textarea/input）自适应，更合理的重置是 `input, textarea, img, video, object { box-sizing: border-box; }`。
- **展开动画 max-height 给一个巨大值**：收起时出现可见的"延迟"（前段动画看不到变化）。
- **以为浮动元素是内联级别**：它可与文字同行只是"恰好站在一起"，实际已生成块盒子、脱离文档流。

## Key Takeaways
1. 遇到宽度问题先问：这是 width:auto 的哪种表现？四种表现的机制完全不同，对症才能下药。
2. "无宽度"是流体布局第一准则：设了具体宽度，流动性就死；全站理论上只需要最外层一个 width。
3. `min-width/min-height` 初始值是 auto 而非 0——这是 min/max 系列与 width/height 最大的暗差异。
4. `height:100%` 无效不是 bug：父级无显式高度时规范解析为 auto；两条出路是显式高度链与绝对定位。
5. 内联盒模型四层（内容区域→内联盒子→行框盒子→包含盒子）+ 幽灵空白节点，是读懂第 5 章内联世界的前置知识。

## Connects To
- **Ch 4**: 容器盒子的四层结构（content/padding/border/margin box）在盒尺寸四大家族中展开。
- **Ch 5**: 内联盒模型与幽灵空白节点是 line-height/vertical-align 一切诡异行为的地基。
- **Ch 6**: 格式化宽度、包裹性在 float/absolute 上的具体化；min/max-width 的"超越"规则与层叠无关，是纯计算规则。
