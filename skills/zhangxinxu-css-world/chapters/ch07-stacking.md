# Chapter 7: CSS 世界的层叠规则

## Core Idea
z-index 只是层叠规则中的一叶小舟：层叠由"层叠上下文（结界）+ 层叠顺序（7 阶规则）+ 两条黄金准则"共同决定——多数 z-index 问题源于无意中创建了层叠上下文。

## Frameworks Introduced
- **三个概念分层**：
  - **层叠上下文（stacking context）**：z 轴上的"层叠结界"，自成小世界、可嵌套、与兄弟独立；内部元素无论 z-index 多大都出不了结界。
  - **层叠水平（stacking level）**：同一结界内元素的论资排辈；**所有元素都有**，z-index 只是影响它的手段之一（仅限定位元素）。
  - **层叠顺序（stacking order）**：规则本身——CSS2.1 的 7 阶图（从底到顶）：
    1. 层叠上下文元素的 background/border
    2. **z-index 负值**子元素
    3. block 块级盒子
    4. float 浮动盒子
    5. **inline 水平盒子**（inline/inline-block/inline-table）
    6. z-index:auto 或 z-index:0 的定位元素/层叠上下文元素
    7. **z-index 正值**元素
- **两条黄金准则**：① 谁大谁上——同一结界内层叠水平大者覆盖小者；② 后来居上——层叠水平一致时，DOM 流后面的覆盖前面的。
- **层叠顺序设计逻辑**：装饰（background/border）最底、布局（block/float）居中、**内容（inline）最高**——CSS 为图文展示而生，内容必须最优先呈现（文字永远压在浮动图上方的原因）。
- **层叠上下文创建三流派**：
  1. **天生派**：根元素 `<html>`（根层叠上下文）；
  2. **正统派**：z-index 为数值的定位元素（relative/absolute；fixed 在 IE/Firefox 亦需数值）；
  3. **扩招派**（CSS3）：flex 子元素且 z-index≠auto / opacity≠1 / transform≠none / mix-blend-mode≠normal / filter≠none / isolation:isolate / will-change 为上述值 / -webkit-overflow-scrolling:touch。
- **z-index:0 与 z-index:auto 的根本差异**（本章最重要考点）：层叠水平上二者等价，但**数值 0 会创建层叠上下文**——子元素的 z-index 从此只在结界内比，结界之间按父级"谁大谁上/后来居上"裁决。
- **z-index 负值渲染规则**：渲染过程 = **向上寻找第一个层叠上下文元素并止步于其上**——位于该结界背景之上、block 元素之下。所以"z-index 负值隐藏元素"时灵时不灵：父级有无背景色/结界决定结果。
- **z-index"不犯二"准则**（作者经验准则）：非浮层元素的 z-index 不需要超过 2。理由：设值即变结界、层叠关系根本改变；防止"9→99→999999"的军备竞赛。"absolute 必配 z-index"是最愚蠢的习惯。浮层组件例外，用"层级计数器"（JS 取页面最大 z-index，超出则 +1，默认 9 足够安全）。

## Key Concepts
- 定位元素（position≠static）z-index 自动生效为 auto（0 级别）→ 天然压过 block/float/inline；CSS3 层叠上下文元素（无 z-index）同为 auto 级别，与定位元素相遇按"后来居上"。
- IE6/7 bug：z-index:auto 的定位元素也创建层叠上下文（旧项目 z-index 折腾死人的根源）。
- Chrome 的 position:fixed 天然创建层叠上下文（无需数值 z-index）；IE/Firefox 仍需数值。
- fadeIn 动画把文字"压到图片后"之谜：动画期间 opacity≠1 → 图片成结界（auto 级）→ DOM 在后的图片按"后来居上"覆盖文字。修复：调 DOM 顺序或文字 z-index:1。
- z-index 负值的三个正当用途：① 可访问性隐藏（比 clip 隐藏好：relative 即可、不影响布局与焦点；缺点是需要父级有背景配合）；② IE8 多背景模拟（父 `relative+z-index:0` 造结界，伪元素 `absolute+z-index:-1` 垫底，最多 3 张）；③ 装饰元素精确定位在"背景之上、内容之下"（纸张卷边阴影）。

## Code Examples
```css
/* IE8 多背景模拟：伪元素背景永远在内容之下 */
.box { position: relative; z-index: 0;          /* 创建层叠上下文 */
       background-image: url(1.png); }
.box:before, .box:after { content:''; position:absolute; z-index:-1; }
.box:before { background-image: url(2.png); }
.box:after  { background-image: url(3.png); }

/* 纸张卷边阴影：藏在结界背景之上、纸张之下 */
.container { position: relative; z-index: 0; background:#666; }
.page { position: relative; background:#f4f39e; }
.page:before, .page:after { content:""; position:absolute; z-index:-1;
       box-shadow: 0 8px 16px rgba(0,0,0,.3); }
```

## Reference Tables
| 场景 | 判定 | 准则应用 |
|---|---|---|
| 两个定位元素比大小 | 看生效的 z-index 数值 | 谁大谁上 |
| 层叠水平相同（含 auto/结界元素） | 看 DOM 顺序 | 后来居上 |
| z-index 大却压不过别人 | 父级结界封顶 | 提升或统一父级层级 |
| 想让元素垫在背景上 | 父级造结界 + 子 z-index:-1 | 负值止步于第一个结界 |
| 文字/图片内容压浮动图 | 无需任何设置 | inline 天然高于 float |

## Worked Example
**父级 z-index:auto → 0 引发的覆盖反转**：两个兄弟 `<div style="position:relative">` 各含一张图（z-index:2 与 1）。父级为 auto 时是普通定位元素，两张图直接比较 → 2 压 1；父级改为 `z-index:0` 后各自成为结界，子图 z-index 被封印，改为父级之间比较——都是 0，按"后来居上"，DOM 在后的容器整体覆盖在前。结论：调试"z-index 失灵"先查祖先链上谁成了结界。

## Anti-patterns
- **absolute 一来就 z-index:999**：定位元素 auto 已是 0 级、天然高于普通流；巨大数值只会埋雷。
- **z-index 军备竞赛（9→99→999999）**：多人协作层级失控的典型路径；用"不犯二"准则 + 层级计数器管理浮层。
- **给容器随手加 transform/opacity（如 scale(1)、opacity:0.99）**：无意创建结界，子孙 z-index 全部被封锁——排查层叠 bug 必查这些属性。
- **以为 z-index 负值能让元素"藏到页面背后"**：它最多藏到第一个结界的背景处；隐不掉时先找结界。

## Key Takeaways
1. 背 7 阶顺序图：负 z → block → float → inline → auto/0 → 正 z（结界背景/边框垫底）。
2. z-index:0 与 auto 的差别在"是否创建结界"，不在层叠水平——这是 90% z-index 诡异行为的答案。
3. 覆盖冲突永远两条准则裁决：先比层叠水平（谁大谁上），平手看 DOM（后来居上）。
4. 页面主体 z-index 不超过 2；浮层组件用 JS 层级计数器（默认 9）。
5. z-index 负值 = 垫底艺术（多背景模拟、装饰性阴影），隐元素首选还是 clip/display 方案。

## Connects To
- **Ch 6**: BFC 结界 vs 层叠结界——overflow 触发前者，z-index/transform 等触发后者；无依赖定位的 z-index 慎用观与此呼应。
- **Ch 5**: 行内盒（inline 水平）在 7 阶图中高于 float，解释了图文混排时文字永远在图上。
- **Ch 10**: clip 隐藏与 z-index 负值隐藏的取舍在"元素显隐"中对比。
