# Chapter 9: 元素的装饰与美化（color / background）

## Core Idea
CSS2.1 世界的 color 与 background 都很单调——但正因为单调，兼容处理技巧（透明度模拟、多背景 hack、滤镜）才成为这一章的看家本领。

## Frameworks Introduced
- **CSS 世界颜色能力边界**：颜色关键字仅 17 个（CSS2.1；gray/grey 同色）；`color:transparent`、`currentColor`、`rgba()/hsla()` 均 **IE9+**（原生 IE8 不支持，高版本 IE 的兼容模式测试结果不可信）。
  - `background-color:transparent` IE6+、`border-color:transparent` IE7+、`color:transparent` IE9+——透明能力的可用面各不相同。
  - 无须画蛇添足写 `currentColor`：border/outline/text-shadow/box-shadow 缺省即 currentColor 表现（IE7 起）。
- **rgb 颜色规则**：十六进制（`#f03`/`#ff0033`，字符最少、渲染最快——首选格式）、`rgb(255,0,51)`、百分比 `rgb(100%,0%,20%)`；**整数与小数、整数与百分比不可混用**（`rgb(255,0,51.2)` 无效）。
- **hsl 的取色器价值**：h 色相 0~360、s 饱和度、l 亮度——hover 加深只需调低 l，比 rgb 好算。
- **background 是五个子属性的缩写**：image/position/repeat/attachment/color（IE9+ 再加 size/origin/clip）。
- **background-position 百分比特殊公式**：`positionX = (容器宽 − 图片宽) × percentX`——与 `left:100%`（跑出容器）完全不同；`100% 100%` = 右下角对齐；**负百分比在图片大于容器时反而定位在容器内**（差值为负 × 负 % = 正）。
- **background-color 永远垫底**：无论多少层背景图，背景色总在最底 → 全站通用 `:active` 按压反馈：`background-image: linear-gradient(to top, rgba(0,0,0,.05), rgba(0,0,0,.05))` 叠加在原有背景色上，无需逐个按钮写样式（IE8/9 换 base64 PNG）。

## Key Concepts
- 隐藏元素的 background-image 加载行为（未定义行为）：IE 系 display:none 仍发请求；Chrome 看父级是否 none——用背景图做 hover 换图务必合并 sprite，否则 hover 时图片"消失在请求路上"。
- `<position>` 值：IE8 最多 2 个值；IE9+ 支持 4 值语法 `background-position: right 40px bottom 20px`（距右 40px 距底 20px）；缺省偏移关键字按 center。
- **background-repeat 性能**：平铺图别做 1×1（平铺次数太多渲染卡顿），用 100×100 尺寸明显更流畅。
- **background-attachment:fixed 外强中干**：背景相对视区锁定，可做"拖动区域实时模糊"（子元素 `background:inherit` + 同为 fixed，无需 JS 算 background-position）；但页面一滚动就毁（背景不跟元素走）→ 只适合不滚动的窗体背景场景。`local`（IE9+）是"背景随容器滚动"，与 fixed 无关。
- **多背景属性 hack**（IE8 无多背景但有伪元素）：`background: url(icon.png); background: url(icon.svg), none;`——IE8 不认识多背景语法忽略第二行，IE9+ 用 SVG；同理 `linear-gradient(to top, transparent, transparent)` 可区分 IE9 及以下与 IE10+。比 `:root .ie9{}` 权重 hack 更优雅。
- **IE 渐变滤镜**：`filter: progid:DXImageTransform.Microsoft.gradient(startcolorstr=#FFFF0000, endcolorstr=#7F0000FF, gradientType=0)`；8 位十六进制 `#AARRGGBB` 表示透明度（AA=FF 不透明、00 全透明；转换：`Math.round(256*opacity).toString(16)`）。半透明纯色 = 起止色相同的渐变滤镜 + `:root` 下重置 `filter:none`（防 IE9 双重生效）。
- 系统颜色（如 ButtonFace）鸡肋但可当"天然变量"用：即时配色工具里作占位色值，替换不冲突。

## Code Examples
```css
/* 全站 :active 按压反馈（背景色垫底特性） */
a[href]:active, button:active {
   background-image: url(data:image/png;base64,...);            /* IE8/9 */
   background-image: linear-gradient(to top, rgba(0,0,0,.05), rgba(0,0,0,.05));
}

/* 区分 IE8 / IE9+ 的无 hack 图片方案 */
.bg { background: url(icon.png); background: url(icon.svg), none; }

/* IE9- 半透明背景兼容（rgba + 渐变滤镜） */
.bgcolor {
   background: rgba(0,0,0,.5);
   filter: progid:DXImageTransform.Microsoft.gradient(
           startcolorstr=#7F000000, endcolorstr=#7F000000);
}
:root .bgcolor { filter: none; }   /* 防 IE9 双重叠加 */

/* background-attachment:fixed 免 JS 模糊镜 */
.box { background:url(1.jpg); background-attachment:fixed; overflow:hidden; }
.box > .drag { background: inherit; filter: blur(5px); position:absolute; }
```

## Reference Tables
| 需求 | CSS 世界（IE8 约束下）做法 |
|---|---|
| 半透明背景 | PNG/base64 图片 或 起止同色的渐变滤镜（`#7F...`） |
| 多背景 | 伪元素 + `z-index:-1`（父级 `relative;z-index:0` 造结界，最多 3 张） |
| 渐变 | 私有滤镜 gradientType=1 横 / 0 纵 |
| 悬停换图标 | 合并 sprite（防请求延迟"消失"） |
| :active 反馈 | 半透明背景图叠加（背景色天然垫底） |

## Worked Example
**background-position 负百分比纠偏**：直觉上 `-50% -50%` 会把图片定位到容器左上外侧；实际容器 160×160、图片 256×192 时，按公式 (160−256)×(−50%)=+48、(160−192)×(−50%)=+16 → 效果等同 `background-position:48px 16px`，图片在容器**内**。凡 sprite 定位"算不对"，先套这个公式再调参。

## Anti-patterns
- **用高版本 IE 的兼容模式验证 IE8 表现**：颜色/透明度类渲染与原生 IE8 不一致（如 CSS3 颜色关键字、color:transparent）。
- **平铺遮罩用 1×1 图片**：省几 KB 换来明显卡顿；100×100 平铺性能好得多。
- **可滚动页面用 background-attachment:fixed 做视差**：一滚就碎。
- **逐按钮写 :active 背景色**：用垫底背景图一层代码全站解决。

## Key Takeaways
1. 透明能力分层记忆：background IE6 / border IE7 / color IE9——透明边框技巧兼容面最广。
2. background-position 百分比公式 (容器−图片)×% 是 sprite 定位的唯一心算工具。
3. 区分浏览器用"认识/不认识某属性值"的声明覆盖，别用权重 hack。
4. IE 透明度 = 8 位十六进制 AARRGGBB；转换公式 `Math.round(256*opacity).toString(16)`。
5. 背景色永远垫底 → 叠加半透明背景图即可做通用交互反馈。

## Connects To
- **Ch 4**: border-color 缺省 color 的图形变色技巧；z-index:-1 伪元素多背景模拟（Ch 7）。
- **Ch 10**: display:none 与背景图请求行为交叉于"显隐"主题。
- **《CSS 新世界》**: 多背景、background-size/origin/clip、object-fit 等属于新世界。
