# Glossary — 《CSS新世界》术语表

按主题分组；括号内为出处章节。

## 体系与方法论

- **CSS 新世界** — CSS2.1 之后的模块化 CSS 时代；没有 CSS4，"CSS3"已解体为独立模块（Beijing doctrine）(Ch 1)
- **新特性四方向** — 更弹性的布局 / 更丰富的视觉 / 更多设备适配 / 开发者自定义（Houdini）(Ch 1)
- **CSS 数据类型** — 尖括号标记的值类型（`<image>`/`<color>`/`<basic-shape>`…）；属性值=数据类型的组合，学类型即学一片属性 (Ch 2)
- **CSS 属性值定义语法** — 关键字+数据类型+符号（`, /`字面；空格 `&& || |` 组合；`* + ? {A,B} # !` 数量）的形式语法；"斜杠前后类型必须相同或部分相同" (Ch 2)
- **全局关键字** — inherit/initial/unset/revert；unset 配 all 用、revert 回浏览器默认、initial 回规范初始值 (Ch 2)
- **渐进增强五技巧** — 直接用 > 属性值语法差异 > 伪类区分 > @supports > JS 兜底；旧 CSS Hack 已过时 (Ch 2)
- **all 属性** — 重置除 unicode-bidi/direction/CSS 自定义属性外的一切属性 (Ch 2)

## 尺寸与逻辑

- **尺寸体系** — Intrinsic（内在：fit-content/min-content/max-content）vs Extrinsic（外在：stretch/fill-available）(Ch 3)
- **逻辑属性** — inline/block × start/end 命名的属性（margin-inline-end 等），随流方向而变；inset 是唯一日常高频者 (Ch 3)
- **黏性约束矩形** — sticky 元素的包含块矩形 ∩ 流盒（按 top/bottom 偏移后）；黏性效果的空间由它决定 (Ch 3)
- **流盒（flow box）** — sticky 最近可滚动元素的盒子（无则视窗）(Ch 3)
- **边界特性** — opacity/颜色通道等数值超范围按边界钳制；配合变量实现 CSS if/else (Ch 4)

## 布局

- **flex 分家产模型** — flex-basis（基础）/flex-grow（富余分配）/flex-shrink（不足分摊）(Ch 6)
- **flex 非默认补全** — `flex:1`≡`1 1 0%`、`flex:100px`≡`1 1 100px`，缩写值不按默认值补（独此一家）(Ch 6)
- **fr** — 网格剩余空间比例单位，与内容无关；总和 <1 时不铺满 (Ch 6)
- **auto（grid 轨道）** — ≈minmax(min-content,max-content)，上限受 justify-content 影响、下限受 min-width 抬升 (Ch 6)
- **auto-fill / auto-fit** — repeat() 自动列数；auto-fit 折叠空白轨道（卡片布局首选）(Ch 6)
- **CSS 对齐命名法** — justify 水平/align 垂直 × content 整体/items 每项/self 单项 (Ch 6)
- **CSS Shapes** — shape-outside 让文字环绕任意形状（作用在浮动元素上）(Ch 6)

## 视觉

- **CSS 渐变角度** — 0deg 向上、顺时针为正（与 PS 相反）；`to 方位值` 免算角度 (Ch 5)
- **渐变断点回填规则** — 后断点位置小于前断点时按前者渲染（饼图 `gold 0` 技巧）(Ch 5)
- **字体显示时间线** — 字体阻塞/交换/失败三时段；font-display 五值（block/swap/fallback/optional）(Ch 3)
- **可变字体** — 单文件多设计轴（wght/wdth/ital/slnt/opsz）；font-variation-settings 精调 (Ch 9)
- **混合模式边界** — 任何层叠上下文都是 mix-blend-mode 的作用边界；isolation: isolate 显式画界 (Ch 11)
- **遮罩语义** — mask：遮罩图可见处内容可见；mask-composite 的 subtract/exclude 实现挖洞/异或 (Ch 12)
- **填充规则** — nonzero/evenodd 决定路径交叉区域是否填充 (Ch 12, Ch 14)

## 交互与行为

- **滚动穿透** — 弹层滚动带动页面；overscroll-behavior: contain 终结 (Ch 13)
- **Scroll Snap** — scroll-snap-type（容器）+ scroll-snap-align（子项）+ scroll-snap-stop: always（强制逐卡）(Ch 13)
- **pointer-events 继承性** — 父 none 子可 auto 复活；不阻止键盘行为 (Ch 13)
- **渲染隔离（contain）** — size/layout/style/paint 四类隔离声明；content=layout+paint (Ch 13)
- **content-visibility** — 视口外内容跳过渲染；配 contain-intrinsic-size 防跳动 (Ch 13)
- **will-change** — 提前声明将要变化的属性以触发优化；副作用=对应属性层叠上下文立即生效；慎用 (Ch 13)

## Houdini

- **@property** — 注册自定义属性的 syntax/inherits/initial-value；注册后变量可参与过渡动画 (Ch 15)
- **Paint API** — registerPaint + paint(ctx,size,properties) 自绘图像，作为 `<image>` 使用 (Ch 15)
- **Typed OM** — 类型化 CSSOM（CSSUnitValue），替代字符串解析 (Ch 15)
