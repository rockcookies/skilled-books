# Chapter 5: 更强的视觉表现（渐变 / 3D 变换 / 过渡 / 动画）

## Core Idea
CSS 渐变是第一种纯 CSS 图像（必考内容）；CSS 角度体系与设计软件相反（0deg 向上、顺时针为正）；动画关键帧优先级高于一切（连 !important 都压制，Firefox 除外）。

## Frameworks Introduced
- **linear-gradient()**：
  - 方向两法：`to <side-or-corner>`（语义清晰、不受尺寸限制、免算角度——对角线占位图只有它适配任意尺寸）与角度值。**CSS 角度与 PS 相反**：0deg 向上（PS 向右）、正角度顺时针（PS 逆时针）；45deg = 从左下来往右上去。`linear-gradient(0, ...)` 的 0 别省单位（IE/Edge 不认）。
  - 断点十大细节：至少 2 色；位置必须在颜色后（`skyblue 50%`）；无位置自动等分；首尾同色可省（`linear-gradient(white 25%, skyblue 75%)` ≡ 25%~75% 渐变）；位置可负可 >100%；多类型位置混用合法；**后面断点位置小于前面时按前面较大值渲染**（饼图技巧：`gold 0deg 75%` 的 0deg 自动取前一个 40%）；双位置语法 `skyblue 40% 60%` = 区间纯色（IE 不支持，生产用四断点写法）；转换点语法 `linear-gradient(white, 70%, skyblue)` 控制过渡中点（IE 不支持）；同位置双色在 1 倍屏 Chrome 有锯齿 → `calc(50% + 1px)` 留过渡。
  - **渐变是 `<image>`，不能 transition/animation**（间接方案见 Houdini 章）。
- **radial-gradient()**：语法三要素=半径、中心点（`at <position>`，支持 4 值语法）、终止位置（`<extent-keyword>`：closest-side / closest-corner / farthest-side / **farthest-corner(默认)**）。circle+单半径只能长度值（`circle 50%` 非法）；ellipse 是默认形状无需写；at 位置固定在半径后、断点前。
  - 应用模式：按钮高光（`radial-gradient(160% 100% at 50% 0%, ...)`）、多色融合、点击扩散（background-size 0%→250% + closest-side circle）、优惠券波浪边。
- **conic-gradient()**（IE/Edge 不支持）：`conic-gradient([from <angle>]? [at <position>]?, 角断点)`；断点用角度或百分比（45deg ≡ 12.5%）；角度断点是相对值（与 from 累加）。应用：**饼图一行**（`conic-gradient(yellowgreen 40%, gold 0 75%, deepskyblue 0)`——0 起点自动继承前色位置）、取色盘（conic 色环 + radial 灰度叠加）、棋盘（一行替代多背景双渐变错位法）、loading 圆环（conic + mask 挖孔）。
- **重复渐变**：repeating-* 前缀；与普通渐变语法相同，区别是**起止位置必须显式定义**（否则重复单元无法确定）。条纹边框/斑马线标配。
- **3D 变换**：
  - rotateX/rotateY/rotateZ、translate3d/scale3d/rotate3d(x,y,z,angle)、matrix3d。rotateX/Y 本质仍是 3D（需要透视才有视觉意义）。
  - **perspective 属性决定画面是 2D 还是 3D**；值是"眼睛到舞台的距离"，越小透视越强。两种写法：父元素 `perspective: 600px`（**所有子元素共用一个透视点**，符合现实——远处的人只见侧脸）vs 子元素 transform 中的 `perspective()` 函数（各自独立透视）。translateZ 正值=靠近眼睛放大，接近 perspective 值时铺满甚至穿越。
  - `perspective-origin`：眼睛相对舞台的位置（默认 50% 50% 中心）。
  - `transform-style: preserve-3d`：让子元素处于同一三维空间（默认 flat 各自压扁）；3D 嵌套动画必配。
  - `backface-visibility: hidden`：翻转时隐藏背面（卡片翻转、转木马）。
  - GPU 加速：3D 变换天然走合成层；`translateZ(0)`/`translate3d(0,0,0)` 是传统 hack（现代用 will-change，见 ch13）。
- **transition 细节**：
  - 缩写 = property duration timing-function delay（两时间值**先时长后延时**；但含负值时可乱序——duration 不能为负，-1s 必是 delay）。
  - **负延时 = 省略动画前段**：delay -0.5s + duration 1s → 过渡从 50% 进程开始，总时长 0.5s。
  - 延时交互：`transition-delay: .2s` 让 hover 浮层"快速路过不出现"（配合 visibility）。
  - property 初始值就是 all——`transition: all .2s` 的 all 纯属多余，写 `transition: .2s` 即可。
  - 列表长度不齐："有缺则补（循环取值），多之则除"。
  - **display:none 会中断过渡且不触发 transitionend**——显隐一律用 visibility。
  - timing-function 三类：linear；cubic-bezier 系（ease=0.25,.1,.25,1 先加速再减速；ease-in 出剑慢；ease-out 拔剑快；ease-in-out 对称钟摆；cubic-bezier(x1,y1,x2,y2) 起终点固定 (0,0)(1,1)；**纵坐标可 >1/<0 → 回弹效果** `cubic-bezier(.16,.67,.28,1.46)`）；steps 系（动画章）。作者实战：transition 基本只用 ease 默认值。
  - **visibility 过渡特性**：插值 0~1 间一律按 visible 渲染 → "显示立即出现、隐藏延时消失"天然匹配显隐场景；但 Chrome/Firefox 类名触发时有"结束时才显示"怪癖 → 在激活态重置 `transition-property: opacity`（visibility 不参与过渡即可立即显示）。
- **animation 细节**：
  - 缩写 8 项顺序：name duration timing delay count direction fill-mode play-state。
  - **多动画分开声明**（`animation: fadeIn .2s, slideInRight .2s`）而非合成一个大 keyframes——动画规则可复用。
  - @keyframes 特性：from/to ≡ 0%/100%；起止帧可省（省略则取元素当前值）；同帧样式可合并（`0%,50%,100%`）；**帧选择器无序**；重复定义同帧——不同属性累加、相同属性后者覆盖；各帧属性无需一致（各属性独立走自己的帧序列）；**帧内 !important 无效；动画执行时帧样式优先级最高**（>style 内联；Firefox 中低于 !important，其余浏览器压过一切）——可用来重置第三方内联样式（但 display 等不可动画属性无效）。
  - 动画名 = `<custom-ident>`（区分大小写；不能是 none/全局关键字；不能数字开头；- 开头后不能跟数字；标点需转义 `example\.png`；Unicode 转义后加空格 `\233 haha`）。
  - **animation-delay 负值 = 即时从动画中段播放**：`fadeIn 1s linear -.25s` 的可见过程是 **0.25→1**（不是 0.75→1！）——"0.25s 之前就已从 0% 开始播"。波形/交错动画各元素负 delay 是标配（正 delay 有"初始全部同尺寸"的问题）。
  - 无限循环动画的 delay 不参与循环——要"每圈都停顿"就把停顿做进关键帧（`0%,30%{...}100%{...}`）。
  - steps() 步进（时间函数）、reverse/alternate（方向控制）、forwards/backwards（填充模式，见原书 5.4.6-5.4.9）、小数播放次数均支持。

## Code Examples
```css
/* 锥形渐变饼图（0 起点自动继承前色位置） */
.pie { border-radius: 50%;
       background: conic-gradient(yellowgreen 40%, gold 0 75%, deepskyblue 0); }

/* 加载圆环：锥形渐变 + 遮罩挖孔 */
.loading { border-radius: 50%;
   background: conic-gradient(deepskyblue, 30%, white);
   -webkit-mask-image: radial-gradient(closest-side, transparent 75%, black 76%);
   mask-image: radial-gradient(closest-side, transparent 75%, black 76%);
   animation: spin 1s linear infinite reverse; }

/* 底部 Popup：opacity + visibility + transform 组合 */
.popup { opacity:0; visibility:hidden; transition: opacity .2s, visibility .2s; }
.popup.active { transition-property: opacity; opacity:1; visibility:visible; }
.popup > .content { transform: translateY(100%); transition: transform .2s; }
.popup.active > .content { transform: translateY(0); }

/* 交错波形：负延时立即播放 */
.loading i { animation: scaleUp 4s linear infinite alternate; }
.loading i:nth-child(2) { animation-delay: -1s; }

/* 回弹缓动 */
.target { transition: 1s cubic-bezier(.16, .67, .28, 1.46); }
```

## Reference Tables
| 渐变函数 | 断点单位 | 专属能力 |
|---|---|---|
| linear-gradient | 长度/百分比 | to 方位（尺寸无关） |
| radial-gradient | 长度/百分比 | at 中心 + closest/farthest-side/corner |
| conic-gradient | 角度/百分比 | from 起始角；饼图/色环/棋盘 |
| repeating-* | 同上 | 显式起止；条纹/斑马线 |

| transition vs animation | transition | animation |
|---|---|---|
| 触发 | 状态变化 | 立即/循环 |
| 帧控制 | 只有首尾 | 多帧 + 无序定义 |
| 优先级 | 正常 | 帧样式最高（>style） |
| 负延时 | 省略前段 | 从中段即时播放 |

## Anti-patterns
- **用 PS 的角度直觉写 CSS 渐变**：0deg 方向差 90°、正负方向相反；用 to 方位值可完全绕开。
- **渐变想 transition**：渐变是 image，不可过渡；动态渐变要靠 @property（ch15）或伪元素 opacity。
- **radial 单值半径写百分比**（`circle 50%`）：非法；百分比必须双值。
- **display:none 挂 transition**：过渡中断且 transitionend 不触发；用 visibility。
- **`transition: all .2s`**：all 是默认值，多写无益还可能拖累无关属性。
- **正延时做交错动画**：初始帧全同；改负延时。

## Key Takeaways
1. 渐变断点"后者小于前者按前者渲染"是饼图/区间纯色的免费语法糖。
2. perspective 是 3D 的开关：写父元素共享透视点，写 transform 里各自透视。
3. visibility 过渡 = 立即显示 + 延时隐藏；显隐动画的三件套是 opacity+visibility+transition。
4. @keyframes 帧样式优先级最高——既是特性也是雷（动画会覆盖内联样式）。
5. animation-delay 负值语义 = "早已开始播"，可见区间从 |delay| 处到结束。

## Connects To
- **Ch 4**: transform/opacity 是动画高性能二要素；渐变不可动画在本章验证。
- **Ch 12**: mask 与 conic 渐变组合出 loading 圆环。
- **Ch 15**: @property 让渐变角度/颜色可过渡，补上本章缺憾。
