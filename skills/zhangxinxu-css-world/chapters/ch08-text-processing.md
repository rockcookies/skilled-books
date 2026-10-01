# Chapter 8: 强大的文本处理能力

## Core Idea
font-size 是内联世界的隐形推手（line-height、vertical-align 百分比都随它联动）；文本控制属性的诡异表现几乎都源于"它们作用在内联盒子而非块容器"。

## Frameworks Introduced
- **font-size 联动链**：`line-height` 数值 × font-size；`vertical-align` 百分比 × line-height 计算值 × → `font-size:16px; line-height:1.5` 时 `vertical-align:-25%` ≡ `-6px`。百分比写法随字号自适应——"永远垂直居中的图标"：`vertical-align:25% + position:relative; top:8px`。
- **Chrome 12px 最小字号限制**：font-size 计算值 <12px 一律按 12px 渲染（**唯一例外是 0**，`0.0000001px` 都不行）。因此 `html{font-size:62.5%}` 的 rem 方案在 Chrome 失效（10px→12px）；作者不推荐 `625%`（100px 根字号会放大一切基线/间隙问题）——建议基于 16px（medium）或用 Sass 计算。
- **font-size:0 隐藏文本**：logo 图标字隐藏的又一解（另一解 text-indent 缩进）。
- **font-weight 临界点体系**：只接受 100~900 的**整百数**（写 550 无效），400=normal、700=bold；`bolder`/`lighter` 不是 ±100，而是跳到相邻临界点：继承 100~300 → bolder 400 / lighter 100；继承 400~500 → bolder 700 / lighter 100；继承 600~700 → bolder 900 / lighter 400；继承 800~900 → bolder 900 / lighter 700。数值没变化是因为系统缺对应字重字体（Windows 中文只有一档；思源黑体/苹方有多字重）。
- **font 缩写语法**：`[style||variant||weight]? size[/line-height]? family`——**size 与 family 必需**（`font:14px '☺'` 有效，`font:normal 700 14px/20px` 无效）。缩写会把 line-height 重置为 normal（跨浏览器不一致）→ 不带行高别用缩写。省略字体列表两招：① 占位字体 + `font-family:inherit`；② @font-face 把长字体列表打包成一个短名。
- **@font-face 本质是变量**：核心可玩属性 `font-family`（变量名随意）、`src`、`font-style`、`font-weight`、`unicode-range`——可做字体重命名、字体列表打包、按 unicode-range 拆分中英文字体。

## Key Concepts
- **text-indent**：仅对第一行内联盒子有效；inline 计算值的非替换元素无效（inline-block/table 生效，子元素需 `text-indent:0` 重置）；input 无效、button 有效（百分比基准各浏览器不同）。
  - **百分比相对"包含块"而非自身**——`text-indent:100% + overflow:hidden` 的流行隐藏法在包含块宽为 0 时（如双层 absolute）彻底失效；隐藏文字建议定长负值（`-120px` 优于 `-9999em`：滚屏性能 + VoiceOver 不读屏外内容），常配 `overflow:hidden` 防 outline 扩散。
  - 实用：悬挂缩进（`text-indent:-3em; padding-left:3em` 实现"提问："后内容对齐）；隐藏无 src `<img>` 的 alt 灰框。
- **word-break:break-all vs word-wrap:break-word**：前者"毫不留情"任意断（无空白区域但参差）；后者"有换行点（空格/CJK）就不动英文单词"，易出大片空白。`overflow-wrap` 是 word-wrap 的新名字（仅 WebKit 系支持），兼容起见继续用 `word-wrap`。
- **white-space 三维度**（合并空白？保留换行？允许环绕？）：

| 值 | 空格/制表 | 换行 | 环绕 |
|---|---|---|---|
| normal | 合并 | 合并 | 环绕 |
| nowrap | 合并 | 合并 | 不环绕 |
| pre | 保留 | 保留 | 不环绕 |
| pre-wrap | 保留 | 保留 | 环绕 |
| pre-line | 合并 | 保留 | 环绕 |

  - `nowrap` 让元素宽度表现为"最大可用宽度"（配合 overflow 实现横向滚动、修"一柱擎天"）；代码块如今应使用 `pre-wrap`（pre+横向滚动条是 IE6/7 遗留）。
- **text-align:justify 的中文坑**：IE（至 IE11）只有 word-spacing 算法，中文无法两端对齐 → 全兼容组合：`text-align:justify; text-justify:inter-ideograph;`（IE 私有值，不怕未来冲突）。两端对齐**布局**：inline-block 列表 + justify + 宽度占位标签。
- **下划线重叠根治**：`text-decoration:none; border-bottom:1px solid;` + `padding-bottom` 调距——纯内联元素的垂直 border 不影响布局，且 border-color 缺省跟随 color（hover 自动同色），还支持 dashed/dotted。
- **text-transform 一本万利**：身份证输入框、验证码输入框加 `text-transform:uppercase`，用户敲小写也显示大写，消除"要不要按 Shift"的心智负担（只改视觉不改值）。

## Code Examples
```css
/* 随字号自适应的垂直居中图标 */
p { font-size: 16px; line-height: 1.5; }
p > img { width:16px; height:16px; vertical-align:25%; position:relative; top:8px; }

/* 全兼容中文两端对齐 */
.justify { text-align: justify; text-justify: inter-ideograph; }

/* 不与文字粘连的下划线 */
a { text-decoration:none; border-bottom:1px solid; padding-bottom:5px; }

/* logo 文字隐藏（定长负值，勿用 -9999em） */
.logo { width:120px; background:url(logo.png); text-indent:-120px; overflow:hidden; }

/* font 缩写省略字体列表 */
.font { font: 30px/30px '☺'; font-family: inherit; }
```

## Reference Tables
| 需求 | 处方 | 备注 |
|---|---|---|
| 强制长英文单词断行 | `word-break:break-all` | 参差但无空白 |
| 仅在无路可走时断词 | `word-wrap:break-word` | 可能大片空白 |
| 保留源码格式且自动换行 | `white-space:pre-wrap` | IE8+ |
| 列表两端对齐（任意个数） | inline-block + justify + 占位 | 需处理基线间隙（ch5） |
| 输入框视觉大写 | `text-transform:uppercase` | 实际值不变 |

## Worked Example
**提问/回答悬挂缩进**：`<p>提问：……</p>` 多段排版，要"提问："后的内容换行对齐——`p { text-indent:-3em; padding-left:3em; }`：第一行左移 3em 露出标签，整体右移 3em，后续行自然对齐。这利用了 text-indent 只作用第一行的特性，标签受限场景下无替代。

## Anti-patterns
- **`html{font-size:62.5%}` rem 方案**：Chrome 12px 限制下根字号实为 12px，全站 rem 偏大；且 100px 根字号方案会放大基线/间隙问题。
- **text-indent 用 `-9999em` 或 `100%`**：前者滚屏卡顿+读屏不读；后者依赖包含块宽度，包含块为 0 时失效。
- **font 缩写不写行高**：line-height 被重置为 normal，跨浏览器行高漂移。
- **代码块 `white-space:pre` + 横向滚动条**：体验差，pre-wrap 即可。
- **下划线用 text-decoration 硬调**：与中文下边缘粘连；border-bottom 模拟是正解。

## Key Takeaways
1. font-size 是 line-height 数值与 vertical-align 百分比的乘数——改字号前想想这两处联动。
2. bolder/lighter 按临界点跳变（100/400/700/900），不是 ±100。
3. font 缩写必须 size+family 成对出现，且会把行高重置为 normal。
4. text-indent 百分比看包含块、只作用第一行；white-space 用三维度表格记忆。
5. 中文两端对齐 = text-align:justify + text-justify:inter-ideograph；精致下划线 = border-bottom。

## Connects To
- **Ch 5**: strut/基线被 font-size 放大的一切现象（大字号容器变高）在本章有了字号视角。
- **Ch 4**: border-color 跟随 color 在"下划线模拟"中再次变现。
- **Ch 10**: font-size:0 与 visibility/display 共同构成显隐工具箱。
