# Chapter 3: 从增强已有的 CSS 属性开始（尺寸体系 / 逻辑属性 / border-image / sticky / 字体 / 换行 / background 新特性）

## Core Idea
CSS 新世界把 CSS2.1 中隐晦的概念（shrink-to-fit、最小/最大内容宽度）变成显式关键字；CSS2.1 基于方向（left/right）的属性体系补上了基于"流"（inline/block × start/end）的逻辑属性。

## Frameworks Introduced
- **尺寸体系金字塔**：顶层概念 Intrinsic Sizing（内在尺寸，由内容决定）vs Extrinsic Sizing（外在尺寸，由上下文决定）；四个 width 关键字落地：
  - `fit-content`（紧身裤）：= CSS2.1 的 shrink-to-fit/包裹性。两大优点：① 保护元素原始 display 计算值（`<li>` 设 display:table 会丢项目符号）；② **让尺寸有了确定值**——绝对定位弹框 `width:fit-content; height:fit-content` + 四向 0 + `margin:auto` 居中，不占 transform（transform 居中会与动画关键帧冲突）。兼容性陷阱：width:fit-content 移动端可用（webkit 前缀兜底）；Firefox 语法认 `height:fit-content` 但无效果——勿写 @supports 特征检测。
  - `stretch`（= 旧 fill-available/available，规范新名字）：填满可用空间。声明三连：`-webkit-fill-available` → `-moz-available` → `stretch`。场景少（块级/flex/grid 子项天生自带）；标签受限时替代"宽度分离"（如 button）。Firefox 对 table 元素 `-moz-available` 渲染等同 100%（bug），table 用它别加 -moz-。IE/Edge 不支持，calc(100%-30px)+box-sizing 兜底。
  - `min-content`（首选最小宽度显式化）：替换元素=内容宽；CJK=单字宽（避头尾标点会带上邻字）；非 CJK=字符单元宽（Space 永远可中断；短横线/问号/括号规则各浏览器不同）。元素最终值=所有子元素中的最大值。学它主要为理解 flex/grid 的尺寸不足现象。
  - `max-content`：一行显示的最大宽度；实际都能用 `white-space:nowrap` 替代，价值在概念本身。
  - 总结：带 content 的三个关键字=内在尺寸；stretch=外在尺寸。
- **CSS 逻辑属性**（inline/block × start/end）：CSS2.1 基于方向（margin-right）的设计与"流"的底层理念不符——direction:rtl 后 margin-right 的间隙就错了，`margin-inline-end` 永远正确。
  - 默认映射：left↔inline-start、top↔block-start、right↔inline-end、bottom↔block-end；writing-mode:vertical-rl 下映射换轴。
  - 只在配合 writing-mode/direction/text-orientation 时有意义；flex-direction:row-reverse 与逻辑属性无关。
  - 最佳场景：**对称布局**——用逻辑属性做一侧，另一侧一句 `direction:rtl`（微信对话布局）。
  - 全家桶：margin/padding/border 的 *-inline-start/end 等基础属性（Chrome69+/Safari 12.1+）与缩写（margin-inline 等，Safari 曾不支持→慎用）；`width→inline-size`、`height→block-size`；`text-align:start|end`。
  - **inset 是唯一日常高频的逻辑属性**（逻辑是摆设，缩写太诱人）：`position:absolute; inset:0` ≡ `left:0; top:0; right:0; bottom:0`；支持 1~4 值，方位同 margin。
- **border-image 九宫格模型**：源图像按 slice 划分成 9 区填进元素边框形成的九宫格。三属性分工：`border-image-slice`（划分源图，1~4 值+可选 fill 关键字让中心区也填充；默认 100% 只剩四角）；`border-image-width`（九宫格边框宽度，支持数值=border-width 的倍数、百分比、auto=slice 尺寸；超出时按"等比例+不超元素"缩放）；`border-image-outset`（中心区外扩，只能正值；扩展区不占布局不响应鼠标）。`border-image-repeat`: stretch(默认)/repeat/round(微缩放不截断)/space(保持原尺寸补空隙)。
  - 缩写语法 `source slice / width / outset repeat`——`54 // 20px` 的双斜杠是省略 width 的合法写法。
  - **现代定位：渐变边框**（`border-image: linear-gradient(...) 20 / 10px`；配 `border-style:solid`，border 声明必须写在 border-image 之前——border 缩写会重置 border-image）；自定义虚线（repeating-linear-gradient）；圆角渐变边框需配 `clip-path:inset(0 round 10px)` 或外层 overflow:hidden（border-radius 管不了 border-image）。
- **position:sticky 黏性定位**（相对定位的延伸，非 relative+fixed 混合）：
  - 与 relative 相同：占位保留、创建绝对定位包含块、可设 z-index。
  - 与 relative 不同：① 偏移计算基准=**层级最近的可滚动元素**（无则视窗）——祖先有 overflow≠visible 时窗体滚动无黏性效果（不是 bug）；② 计算规则=**黏性约束矩形**（包含块矩形 ∩ 流盒按 top/bottom 偏移后的矩形），元素碰到约束矩形边缘开始吸附，超出约束矩形就被"带滚"；**父元素与自身等高 → 约束矩形无空间 → 永无黏性效果**；③ 堆叠规则：同一容器内多个 sticky 会重叠，**不同容器**内依次推开（通讯录字母索引要用后者——每个条目套独立容器）。
  - 应用：层次滚动（标题 sticky top + 评论区 sticky bottom + z-index:-1，每段独立 section）。
- **字体新特性**：
  - 新通用字体族：`system-ui`（跟系统走，免去字体冲突与过时字体；实践：`system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, sans-serif`）、`emoji`、`math`、`fangsong`、ui-serif/ui-sans-serif/ui-monospace/ui-rounded（仅 Safari）。
  - **emoji 最佳实践**：@font-face 用 `src: local("Apple Color Emoji"), local("Segoe UI Emoji"), local("Segoe UI Symbol"), local("Noto Color Emoji")` + `unicode-range: U+1F000-1F644, U+203C-3299` 把 Emoji 字体**前移**到 Helvetica 之前——靠 unicode-range 限定生效范围，避免常规字体抢先渲染了 emoji 码位的字符。
  - `local()` 函数：调用系统已装字体（打包字体列表成短名；自定义字体场景已安装用户免请求）。
  - `unicode-range`：`U+26`（单字符）/`U+0-7F`（区间）/`U+4??`（通配，?=0-F）/逗号多值。中文常用：汉字 U+4E00-9FA5。
  - woff2：比 woff 小 30%+，现代标配；woff2 无需 GZIP；Base64 内联 ≤30KB 字体体验最佳。ttf/otf 无 Web 使用理由。
  - `font-display`（时间线三时段：阻塞/交换/失败）：`auto`≈block（3s 阻塞，适合图标字体）；`swap`（阻塞 ≤100ms+无限交换，重要小文本）；`fallback`（阻塞极短+交换 3s，大段文本）；`optional`（阻塞极短+无交换，**日常 Web 产品首选**——内容优先）。勿用 FOIT/FOUT 概念理解，官方是"字体显示时间线"。
- **换行控制决策**：动态内容 `word-break:break-all`（主力）+ `word-wrap:break-word`（IE/Edge 连续破折号兜底）；要中文标点也断 → `line-break:anywhere`（能断则断，牺牲避头尾）。静态内容：英文单词用软连字符 `&shy;`（换行处显形），非英文（URL/API 名）用 `<wbr>`（= 零宽空格 U+200B 的语义化标签，IE 兼容 `wbr:after{content:'\00200B'}`）；`<wbr>`=有机会才断，`<br>`=必断。`overflow-wrap:anywhere` ≈ break-word+断点考虑进 min-content 计算（适合 flex；兼容性欠佳）。
- **text-decoration 升级**：现在是缩写（line/style/color/thickness 四子属性）；`wavy underline 3px red` 兼容差建议 `text-decoration: wavy underline red` + `text-decoration-thickness:3px` 分开写；装饰线可**累加**（多个声明叠加）；`text-underline-position:under` 解决下划线穿过中文降部；`text-underline-offset` 任意偏移（支持负值，只对 underline 生效，多行时每行同偏移）；`text-decoration-skip-ink`（默认 auto 跳过字形）。竞不过 border 模拟（兼容+偏移灵活），wavy 波浪线是独门场景。
- **color 新语法**：rgb()/rgba() 语法互通、hsl()/hsla() 互通；逗号可省（`rgb(255 0 0 / 50%)`）；数值可小数/科学计数；透明度可百分比；hsl 色调可用 deg/rad/turn。148 个颜色关键字（rebeccapurple 唯一非规范色名；darkgray 比 gray 浅）。transparent 有限的实用点：渐变中 transparent ≡ rgba(0,0,0,0)（会往黑色渐变）。
- **background 新特性**（IE9+）：
  - `background-size`：cover（覆盖裁剪）/contain（包含留白）保比例；**auto 渲染规则**——有内在尺寸按原尺寸（多倍图必须设 size）、无尺寸无比例铺满=100%、无尺寸有比例=contain、单侧尺寸有比例等比拉伸；百分比相对**背景定位区域**（由 background-origin 决定，默认 padding-box）。缩写中 size 只能跟在 position 后用斜杠分隔（`center / 100%`）。
  - **多背景**：逗号分隔；渐变（起止同色=纯色）+ 多背景 + size = 任意图形（加减号按钮：两条 currentColor 渐变十字交叉）；`background: linear-gradient(...), deepskyblue` 合法而 `background: deepskyblue linear-gradient(...)` 非法（纯色必须放最后当"层"）。
  - `background-clip`：border-box(默认)/padding-box/content-box/**text**（渐变文字三件套：`background: linear-gradient(...); -webkit-background-clip:text; background-clip:text; color:transparent`；配 @supports 保 IE 文字可见；emoji 颜色会丢）。
  - `background-origin`：定位原点 border-box/padding-box(默认)/content-box——渐变在粗边框处"断开"就是 origin 在作怪，设 border-box 即愈。
  - `background-repeat: space`（不裁剪，两端固定均分空隙；图像大于区域时 IE/Edge 空白）/`round`（压缩平铺不裁剪；Firefox 会忽略 position→要用边缘定位）；双值语法 `round space` 分轴控制。
  - `background-position` 4 值语法：`right 20px bottom 20px`（IE9+）——替代 calc(100% - 20px)（性能差且 IE9 会崩）。
- 其他：`outline-offset`（轮廓间隙，IE/Edge15+ 支持）；多边框 border-right-colors 已死（用 box-shadow/border-image）；cursor 新增 zoom-in/out、grab/grabbing。

## Code Examples
```css
/* fit-content 弹框居中（不占用 transform，动画无冲突） */
.cw-dialog { width: fit-content; height: fit-content;
             position: absolute; inset: 0; margin: auto; }

/* 逻辑属性 + direction 实现微信对话对称布局 */
.bubble { margin-inline-start: 10px; }
[data-self] { direction: rtl; }

/* 渐变边框 + 圆角 */
.fancy {
   border: 10px solid;
   border-image: linear-gradient(deepskyblue, deeppink) 20 / 10px;
   clip-path: inset(0 round 10px);
}

/* sticky 层次滚动 */
h4 { position: sticky; top: 0; }
footer { position: sticky; bottom: 50vh; z-index: -1; }

/* emoji 与系统字体的平衡 */
@font-face {
   font-family: Emoji;
   src: local("Apple Color Emoji"), local("Segoe UI Emoji"),
        local("Segoe UI Symbol"), local("Noto Color Emoji");
   unicode-range: U+1F000-1F644, U+203C-3299;
}
body { font-family: system-ui, -apple-system, Segoe UI, Roboto,
       Emoji, Helvetica, Arial, sans-serif; }

/* 渐变文字三件套 */
.text-gradient {
   background: linear-gradient(deepskyblue, deeppink);
   -webkit-background-clip: text; background-clip: text;
   color: transparent;
}

/* 多背景画加减号 */
.btn-add {
   background:
      linear-gradient(currentColor, currentColor) no-repeat center / .875em 2px,
      linear-gradient(currentColor, currentColor) no-repeat center / 2px .875em;
}

/* 动态内容换行组合拳 */
p { word-break: break-all; word-wrap: break-word; }
```

## Reference Tables
| width 关键字 | 尺寸归属 | 一句话 |
|---|---|---|
| fit-content | 内在 | 紧身裤=包裹性，且有确定值（可配 margin:auto 居中） |
| min-content | 内在 | 首选最小宽度（单字/字符单元/替换元素自身） |
| max-content | 内在 | nowrap 等价物，概念价值>实用价值 |
| stretch | 外在 | 填满可用空间（-webkit-fill-available / -moz-available） |

| 场景 | 处方 |
|---|---|
| 绝对定位元素 4 向定位 | `inset: 0` |
| 渐变边框 | border-image + 渐变（border 缩写要在其之前） |
| 中文词组不断行 | `word-break: keep-all` |
| 字体加载策略 | 文本用 optional；图标字体用默认/auto |
| 右下角背景定位 | `background-position: right 20px bottom 20px` |

## Worked Example
**黏性约束矩形推演**：`div{height:100px; margin-top:50px}` 包 `nav{position:sticky; top:20px}`。流盒=视口下移 20px 的矩形；约束矩形=div 矩形 ∩ 流盒。初始 nav 距约束顶 33px（无效果）→ 滚动使距离归 0，nav 吸附 → 滚到 nav 底部触及约束矩形底（div 底边）→ 黏性失效随父滚走。推论：父元素高度=nav 高度时约束矩形没有活动空间，sticky 永不生效；多个 sticky 想依次推开就各自套容器。

## Anti-patterns
- **transform 居中 + transform 动画**：关键帧优先级高会打架——用 fit-content + margin:auto 居中，把 transform 留给动画。
- **`@supports (height: fit-content)` 特征检测**：Firefox 语法通过但无效果，检测不到真相。
- **table 上用 `-moz-available`**：渲染等同 100%，margin 会溢出。
- **全局 `*{word-break:break-all}`**：牺牲大多数阅读体验换少数场景整齐；动态内容才需要。
- **border 声明写在 border-image 后**：border 缩写重置 border-image 系属性，效果丢失。
- **用 calc(100% - 20px) 做右下背景定位**：性能差、IE9 会崩；用 4 值语法。
- **white-space 溢出用 break-all 硬断 URL**：URL 里插软连字符会改变语义——用 `<wbr>`。

## Key Takeaways
1. 四个尺寸关键字给 CSS2.1 的隐晦表现命名；fit-content 的核心价值是"尺寸确定值"。
2. 逻辑属性只需记住两个映射（inline-start/end、block-start/end）和一个高频属性（inset）。
3. sticky 三问：最近滚动祖先是谁？约束矩形还有空间吗？多个 sticky 是否分属不同容器？
4. 字体三件套：system-ui 打底、local() 打包、unicode-range 精修；加载策略文本选 optional。
5. background 新特性是 IE9+ 免费午餐：size/多背景/clip:text/origin/4 值 position 全部放心用。

## Connects To
- **Ch 4**: min()/max()/clamp() 是本章 stretch/calc 的下一代替代；background 多背景在滤镜章继续升级。
- **Ch 6**: min-content/max-content 是 flex/grid 尺寸不足问题的钥匙；gap 统一三大布局间隙。
- **《CSS世界》**: 包裹性、首选最小宽度、流、基线等概念在此显式化。
