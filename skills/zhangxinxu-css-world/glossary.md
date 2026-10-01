# Glossary — 《CSS世界》术语表

按主题分组、拼音排序；括号内为出处章节。

## 布局与流

- **BFC（块级格式化上下文）** — "CSS 世界的结界"：内部子元素翻江倒海不影响外部；不会发生 margin 合并、可清除浮动影响。触发：根元素 / float≠none / overflow:auto|scroll|hidden / display:table-cell|table-caption|inline-block / position≠relative|static (Ch 6)
- **包裹性** — 作者对 shrink-to-fit 的称谓 = "包裹"（尺寸由内容决定）+ "自适应性"（永不超出包含块，如同冥冥中有 max-width:100% 罩着）；浮动、绝对定位、inline-block、table 都有 (Ch 3)
- **包含块（containing block）** — 元素计算尺寸与定位的基准框。普通元素=最近块容器祖先的 content box；absolute=最近 position≠static 祖先的 padding box；fixed=初始包含块（视口）(Ch 3, Ch 6)
- **包含盒子** — 由一行行行框盒子组成的盒子（如 `<p>`）；规范中更准确的称呼是"包含块" (Ch 3)
- **流体布局** — 利用元素"流"特性实现的布局，天然自适应；"div+CSS 布局"实质即此；是"自适应布局"的子集 (Ch 1)
- **流（flow）** — CSS 世界中引导元素排列和定位的一条看不见的"水流"，类比现实水流的物理规则；特殊布局是对流的破坏与保护 (Ch 1)
- **三无准则（鑫三无准则）** — 无宽度、无图片、无浮动——作者的布局总则 (Ch 3, Ch 6)
- **外部尺寸 / 内部尺寸** — Extrinsic（宽度由外部决定，唯一表现 fill-available）vs Intrinsic（由内部决定：包裹性、min-content、max-content）；判据：元素无内容时宽度为 0 即内部尺寸 (Ch 3)
- **无依赖绝对定位** — 不设 left/top/right/bottom 的 absolute：停留在原位置，保持"相对特性"但不占流空间；比 relative+方位值更简洁健壮 (Ch 6)
- **相对特性** — 无方位值的 absolute/fixed 元素的定位表现如同相对定位（不设方位时停在原地）(Ch 6)
- **格式化宽度 / 格式化高度** — absolute/fixed 元素对立方位（left+right / top+bottom）同时定位时，尺寸自适应包含块 padding box，具有完全流体性 (Ch 3, Ch 6)
- **最小化影响原则（relative）** — 尽量不用 relative；必须用时把 relative 套在仅包住目标元素的最小容器上 (Ch 6)

## 盒尺寸

- **盒尺寸四大家族** — content、padding、margin、border；性格比喻：content 深不可测、padding 温和、margin 激进、border 功勋卓越 (Ch 4)
- **宽度分离原则** — width 不与影响宽度的 padding/border（有时含 margin）共存；width 独占一层标签，内层靠流动性自适应，改 padding 无需重算 (Ch 3)
- **内在盒子 / 外在盒子** — 元素双盒子模型：外在盒子决定内联/块级（能否一行显示），内在盒子（容器盒子）承载宽高与内容呈现；width/height、box-sizing 只作用于内在盒子 (Ch 3)
- **替换元素** — 修改某属性值即可替换呈现内容的元素（img/object/video/iframe/input/textarea/select）；与非替换元素只隔一个 src 或一个 content 属性 (Ch 4)
- **固有尺寸 / HTML 尺寸 / CSS 尺寸** — 替换元素三层尺寸，优先级 CSS > HTML > 固有；有固有宽高比时只设一边等比缩放；与 display 水平无关 (Ch 4)
- **匿名替换元素** — content 属性生成的内容；文本不可选中/复制、读屏读不到、SEO 抓不到，只用于装饰性内容 (Ch 4)
- **标记盒子（marker box）** — display:list-item 元素附带的"附加盒子"，专门放项目符号；IE 伪元素不支持 list-item 即因无法创建 (Ch 3)
- **首选最小宽度（min-content）** — width:auto 收缩到的最小宽度：中文=一个汉字宽，英文=连续字符单元（终止于空格/短横线/问号等），图片=自身宽 (Ch 3)
- **最大宽度（max-content）** — 等同于包裹性元素设 white-space:nowrap 后的宽度；最大的连续内联盒子宽度 (Ch 3)
- **超越 !important，超越最大** — min/max 覆盖铁律：max-width 超级覆盖 width（压过 style+!important）；min-width 与 max-width 冲突时 min 胜 (Ch 3)

## 内联世界

- **基线（baseline）** — 字母 x 的下边缘；内联垂直对齐的原点；替换元素的基线=元素下边缘 (Ch 5)
- **x-height** — 小写字母 x 的高度（基线到中线 mean line 的距离）；vertical-align:middle = 元素中心对齐"基线往上 1/2 x-height 处" (Ch 5)
- **ex** — CSS 单位，= 1 个 x-height；副业价值：图标 height:1ex 与文字天然垂直居中，不受字体字号影响 (Ch 5)
- **幽灵空白节点（strut，支柱）** — HTML5 文档声明下每个行框盒子前面的 0 宽度、具备元素字体和行高属性的假想内联盒；图片底部间隙、容器高度≠行高、内联 margin-top 负值失效皆因它 (Ch 3, Ch 5)
- **内联盒模型（简易版）** — 内容区域（可近似为文本选中背景）→ 内联盒子（有标签）/ 匿名内联盒子（裸文字）→ 行框盒子（每行一个）→ 包含盒子（如 `<p>`）(Ch 3)
- **内容区域（content area）** — 围绕文字看不见的盒子，受 font-family 和 font-size 双重影响；可把文本选中背景色当作它（Chrome 的 ::selection 不总准确）；宋体下与 em-box 等同 (Ch 3, Ch 5)
- **行框盒子（line box）** — 每一行内联元素外层的盒子，由内联盒子组成；行框盒子与浮动元素不可重叠 (Ch 3, Ch 5, Ch 6)
- **行距 / 半行距** — 行距 = line-height − font-size（em-box）；CSS 中行距上下等分（各一半行距）；传统印刷行距是独立区域 (Ch 5)
- **大值特性** — 内联元素 line-height 的大值特性：父级高度由数值大的 line-height 决定（幽灵空白节点参与竞争）(Ch 5)
- **近似垂直居中** — line-height（单行）或 vertical-align:middle（行内）实现的居中都不是几何意义的居中——字形垂直中线普遍偏下（微软雅黑明显）(Ch 5)

## 浮动与定位

- **浮动参考（float reference）** — float 元素对齐的参考实体 = 行框盒子（不是包含块）；标题换行后行尾 float:right 元素对齐最后一行 (Ch 6)
- **浮动锚点（float anchor）** — float 元素所在流中的一个点，表现如没有 margin/border/padding 的空内联元素；作用是产生行框盒子 (Ch 6)
- **行框盒子与浮动元素不可重叠性** — 块盒与浮动元素完全重叠，但其中的行框盒子被限死在浮动元素一侧（margin 负值推不动）——文字环绕的实现本质 (Ch 6)
- **margin 合并** — 块级元素垂直 margin 合一，三种场景：相邻兄弟、父子（父与第一个/最后一个子元素）、空块级元素自身；严格说发生在与文档流方向相垂直的方向 (Ch 4, Ch 12)
- **锚点定位** — 通过改变容器滚动高度实现定位；两种触发：URL 锚链交互、focus 锚点定位；由内而外触发所有可滚动容器 (Ch 6)
- **剪裁界线** — overflow 剪裁/滚动的边界是 border box 内边缘（非 padding box）；想"剪裁+留白"用透明边框 (Ch 6)

## 层叠

- **层叠上下文（stacking context）** — z 轴上的"层叠结界"：自成体系、可嵌套、与兄弟独立；内部元素出不了结界 (Ch 7)
- **层叠水平（stacking level）** — 同一结界内元素的论资排辈；所有元素都有，z-index 只是影响因素之一 (Ch 7)
- **层叠顺序（stacking order）** — 7 阶规则（底→顶）：结界 background/border → 负 z-index → block → float → inline 水平盒子 → z-index:auto/0 → 正 z-index (Ch 7)
- **层叠黄金准则** — ① 谁大谁上（层叠水平大者覆盖小者）；② 后来居上（水平相同时 DOM 靠后者覆盖）(Ch 7)
- **三派创建** — 层叠上下文的创建：天生派（根元素）、正统派（z-index 为数值的定位元素）、扩招派（CSS3：opacity/transform/mix-blend-mode/filter/isolation/will-change、flex 子元素+z-index 等）(Ch 7)
- **z-index:0 与 auto 的差异** — 层叠水平等价，但数值 0 创建层叠上下文——子元素 z-index 被封锁在结界内 (Ch 7)
- **不犯二准则** — 非浮层元素 z-index 不需要超过 2；浮层组件用层级计数器管理（默认 9）(Ch 7)

## 文本与装饰

- **12px 限制** — Chrome 最小字号限制：font-size 计算值 <12px 按 12px 渲染，唯一例外是 0（0.0000001px 都不行）(Ch 8)
- **normal（line-height）** — 随 font-family 变化的变量（雅黑≈1.32、宋体≈1.141），跨浏览器不一致，必须重置 (Ch 5, Ch 8)
- **字体属性家族** — 以 font- 开头的属性统称；font-family 是大家长 (Ch 8)
- **衬线 / 无衬线字体** — serif 笔画有装饰（宋体、Times New Roman）；sans-serif 无装饰（雅黑、Arial）；移动端 body 用 sans-serif 即可 (Ch 8)
- **双向性（bidi）** — 混合方向文字同现的现象；unicode-bidi 定义其表现：normal/embed（独立嵌入层）/bidi-override（强制按 direction 反排）(Ch 12)
- **未定义行为（undefined behavior）** — 规范空白处浏览器各自实现导致的表现差异（:active+preventDefault、隐藏元素背景图加载、padding-bottom 滚动差异等）——不是 bug (Ch 2)
- **系统颜色** — 跟随操作系统主题的颜色关键字；鸡肋但可当"天然变量"用于即时配色 (Ch 9)
- **占位曲线** — background-position 百分比公式：positionX=(容器宽−图片宽)×percentX；图片大于容器时负百分比定位在容器内 (Ch 9)
- **转角等分规则** — border 转角平滑等分（源自 inset/outset 3D 时代），solid 边框沿袭之——三角/梯形图形构建的基石 (Ch 4)
- **double 表现规则** — "双线宽度永远相等，中间间隔 ±1"；≥3px 才有双线，故 border-width 默认值为 medium(3px) (Ch 4)

## 显隐与交互

- **隐藏实践矩阵** — 按空间/点击/键盘/读屏/加载/动画维度选隐藏方案：script 模板、display:none、visibility:hidden、clip、z-index 负值、opacity:0、text-indent、max-height (Ch 10)
- **焦点元素** — 可被键盘 Tab 聚焦的元素（a/button/input 或设 tabindex 的元素）(Ch 2, Ch 11)
- **focus 锚点定位** — 可聚焦元素获得焦点时浏览器自动重定位到屏幕内，无需 JS (Ch 6)
- **块状化** — float≠none 或 position:absolute/fixed 使 display 计算值变为 block（inline-table 例外为 table）(Ch 6)
- **三剑客** — direction/unicode-bidi（内联方向，为阿拉伯文设计；不受 all 属性影响）与 writing-mode（纵横规则，为东亚文字设计）；一横一纵无交集 (Ch 12)

## 世界观

- **CSS 世界观** — HTML=魔法石、选择器=法器、属性=魔法师、属性值=技能、浏览器=王国、操作系统=平行世界 (Ch 1)
- **造物主视角** — 遇到费解表现，问"如果我为图文展示设计这个世界，为何如此设计"——CSS 世界的诞生就是为图文信息展示服务的 (Ch 1)
- **世界边界** — 本书"世界"=CSS2.1（IE8+ 基线）；CSS3=新世界；CSS4=新新世界 (Ch 1)
- **层叠（cascade）** — 样式层层累加的策略；CSS 当年胜出其他样式表语言靠的就是它 (Ch 1)
