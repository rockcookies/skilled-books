# Cheatsheet — 《CSS世界》决策速查

## 判断规则（When X → do Y）

- 容器高度和预期不符 → 先假设行首有个**幽灵空白节点**（strut），再逐个套 vertical-align 定义。
- 元素宽度行为怪 → 先归类 width:auto 四表现：铺满=fill-available / 收缩=包裹性 / 断字=min-content / 溢出=max-content。
- margin 不生效 → 按序排查：inline 非替换元素的垂直 margin？table-cell/row？合并中被大值压住？absolute 非定位方位？容器/自身尺寸定死？
- z-index 压不住 → 沿祖先链找层叠上下文（z-index 数值、opacity≠1、transform≠none、filter、will-change…），结界封顶，子值无用。
- 覆盖冲突 → 先比层叠水平（谁大谁上），平手看 DOM 顺序（后来居上）。
- 浮动元素"跑错行" → 它对齐的是所在**行框盒子**，不是容器；把它移到目标行内。
- 元素设置 width 后布局变脆 → 流动性丢了：删宽度 / 宽度分离 / box-sizing（仅替换元素刚需 border-box）。
- margin:auto 不居中 → 前提是元素能"自动填充"（块级水平方向 / absolute 对立方位拉伸）；两侧 auto 才平分。
- height:100% 无效 → 父级无显式高度，规范解析为 auto；出路：高度链 or absolute（相对 padding box）。
- transition 不生效 → 属性在过渡列表里吗（visibility 在、display 不在）？起始值是 auto 类关键字吗（height:auto 不行，用 max-height）？
- 浏览器表现不一 → 查规范：覆盖且违反=bug；空白=未定义行为，适配而非修复。

## 决策树

**隐藏元素怎么选？**
不渲染无请求 → `<script type="text/html">`
├ 键盘/读屏要可达 → clip / z-index:-1 / text-indent / 屏幕外
├ 要 transition → visibility:hidden（+absolute）或 max-height
├ 要占空间 → visibility:hidden
├ 要可点击 → opacity:0
└ 都不要 → display:none
（需 JS 测尺寸 → 一律 visibility）

**清除浮动用哪个？**
容器能加 overflow/BFC 属性 → BFC 结界（首选）
伪元素 → `:after{content:'';display:table;clear:both}`
clear:both → 只能"自身避让"，影响仍在（margin 失效、后续仍被环绕）

**等高布局？**
无 IE6/7 → table-cell｜有锚点定位顾虑 → border 法｜栏多 → margin 负值 + overflow:hidden

**内联垂直微调？**
先 vertical-align 数值（正上负下，兼容最好）→ 不行再 relative → 永不先 margin

**居中方案？**
水平：margin:auto（需定宽）/ text-align:center（内联级）
垂直：行内内容 → line-height（单行）/ 行高+middle+伪元素（多行/弹框）
绝对定位 → 四向 0 + margin:auto
writing-mode 垂直流 → margin:auto / text-align:center 直接垂直居中

## 阈值与默认值

| 项 | 值 |
|---|---|
| Windows 滚动栏宽 | **17px**（精确） |
| border-width 默认 medium | 3px（double ≥3px 才有双线） |
| 替换元素默认尺寸 | 300×150（img 例外，浏览器各异） |
| line-height 推荐值 | 数值 1.5（心算友好）；图文站 1.6~1.8 |
| 行高计算舍入 | **向上**舍入（1.42857→19px，1.42858→20px） |
| Chrome 最小字号 | 12px，唯一例外 0 |
| z-index 页面主体 | ≤2（不犯二）；浮层默认 9 + 层级计数器 |
| margin:auto 规则 | 一侧 auto 全拿剩余；两侧 auto 平分 |
| 滚动容器底部留白 | 只能子元素 margin-bottom（padding-bottom 跨浏览器不一致） |
| IE 渐变滤镜透明度 | #AARRGGBB；AA=Math.round(256*op).toString(16) |
| font-weight | 仅 100~900 整百；400=normal，700=bold；bolder/lighter 按临界点跳变 |

## 征兆速诊（Tells & Smells）

| 征兆 | 病根 |
|---|---|
| 图片下方多 5px | strut+line-height+baseline 三连 |
| 容器高度 > line-height 几像素 | 大小字号基线位移（font-size 放 span 上了） |
| inline-block 之间基线错位 | 空元素基线=margin 底边缘 |
| 弹出层 z-index 巨大仍被盖 | 父级层叠上下文封顶 |
| 弹窗背景还能滚 / 蒙层盖不住滚动条 | fixed 的包含块是根元素 |
| margin-top:-9999px 推不动图片 | strut 基线限死内联元素 |
| 标题一换行"更多"掉到下行 | float 参考是行框盒子 |
| 中文下划线粘字 | 用 border-bottom 模拟 |
| 页面加载时水平晃动 | 滚动条出现改变可用宽 → html{overflow-y:scroll} 或预留 17px |
| IE 中文 justify 右侧留白大 | 补 text-justify:inter-ideograph |
| rem 布局整体偏大 | Chrome 12px 限制吃了 62.5% 根字号 |
| 改 padding 页面错位 | width 与 padding 共存 → 宽度分离 |

## 覆盖关系速记

- max-width > width（连 !important 都压）> min-width 不敌 max？→ min-width 压 max-width；四者可用性排序：min > max > width。
- 层叠 7 阶（底→顶）：结界背景/边框 → z:-1 → block → float → **inline** → auto/0 → z:+1。
- 内联元素 padding 垂直方向：影响视觉与布局（滚动条会出），但不进行盒高度计算；margin 垂直则彻底无效。
- float/absolute 一旦出现：display 计算值必为 block（inline-table 除外）；vertical-align/text-align 失效；absolute 在场时 float 失效。
