# Chapter 13: 用户行为与体验增强

## Core Idea
滚动、点击、选择、打印四类行为的 CSS 化（scroll-behavior/overscroll-behavior/Scroll Snap/pointer-events/user-select）+ 性能三件套（will-change/contain/content-visibility）。

## Frameworks Introduced
- **滚动行为**：
  - `scroll-behavior: auto | smooth`——锚点跳转/选项卡横向切换的平滑滚动一行化（渐进增强直接用）。
  - `overscroll-behavior: [contain|none|auto]{1,2}`（x/y 分轴）——**滚动穿透终结者**：弹层滚到边缘不再带动 body 滚动（contain 默认效果留在内部；none 连自身反弹都禁）。
  - `overflow-anchor: auto|none`——滚动锚定（防止上方内容加载导致视口跳动）；布局跳动元凶时可定位关闭。
  - **CSS Scroll Snap**：容器 `scroll-snap-type: x|y|both mandatory|proximity` + 子项 `scroll-snap-align: start|center|end`（可双值 `start end`）；`scroll-snap-stop: normal|always`（强制逐卡停靠——轮播防一划到底）；`scroll-margin`（子项捕获点外扩）。轮播/整屏滚动的纯 CSS 方案。
  - 滚动条自定义双体系：标准 `scrollbar-width: auto|thin|none` + `scrollbar-color: 前景色 背景色`（Firefox）；webkit 私有 `::-webkit-scrollbar` 系（全定制度高）。
- **点击行为**：
  - `pointer-events: none` 三真相：① 不阻止键盘（Tab 仍聚焦、回车仍触发）；② 影响无障碍（title 提示读不到，禁用按钮别只靠它）；③ **继承性**——父 none 子可 `pointer-events: auto` 局部复活（遮罩镂空点击的经典搭配：蒙层 none + 高亮区 auto）。SVG 场景还有 visiblePainted 等细分值。
  - `touch-action`（ch07 已述）：manipulation 消除点击延迟。
- **拉伸/输入/选择**：
  - `resize: none|both|horizontal|vertical|block|inline`——不仅 textarea，**普通元素配 overflow 非 visible 即可拉伸**；拖拽条可用 ::-webkit-resizer 定制。
  - `caret-color`：输入光标颜色（原生控件与 contenteditable 通用）。
  - `user-select: auto|text|none|contain|all`（all=点一下全选）；注意 ::before/::after 内容永远不可选；Safari 至今要 -webkit- 前缀。
  - `::selection`：选中态样式（color/background/caret-color 等），IE9+，微博/B 站都有整站定制。
- **打印**：`color-adjust: exact`（保留精确色打印）；`break-inside: avoid` 等 break 系（旧 page-break 系改名）；`orphans/widows`（页首/页尾最少行数，Firefox 不支持）；`@page` 规则（纸张 margin，配 :first/:blank 伪类）。
- **性能三件套**：
  - `will-change: <custom-ident>`——**提前告知**浏览器将要变化的属性（transform/opacity/scroll-position/contents），使其预先优化（提升层、分配显存）。**慎用三律**：① 别滥用——每个值都消耗内存与合成层；② 隐藏特性：写上即生效对应属性的层叠上下文等副作用（`will-change: transform` 等同 transform≠none）；③ 用完移除（JS 在动画结束移除）或加在即将交互的父层短名单上，禁止全站 `* { will-change: transform }`。
  - `contain: none|strict|content|[size||layout||style||paint]`——**渲染隔离声明**：告诉浏览器子树变化不影响外部（size 布局尺寸固定/layout 内部布局不影响外/paint 不溢出/style 计数器不外泄）。页面区块级性能优化；`contain: content`（layout+paint）是常用起点。
  - `content-visibility: auto`（+ `contain-intrinsic-size`）——**视口外内容跳过渲染**，长列表/长页面的渲染性能大杀器；配合 contain-intrinsic-size 给占位高度防滚动条跳动。

## Code Examples
```css
/* 弹层防滚动穿透 */
.modal { overscroll-behavior: contain; }

/* 横向轮播：整卡停靠 */
.carousel {
   display: flex; overflow-x: auto;
   scroll-snap-type: x mandatory;
   scroll-behavior: smooth;
}
.carousel > li { scroll-snap-align: center; scroll-snap-stop: always; }

/* 遮罩镂空可点击 */
.overlay { pointer-events: none; }
.overlay .spot { pointer-events: auto; }

/* 长列表渲染优化 */
.feed > section {
   content-visibility: auto;
   contain-intrinsic-size: 320px;
}
```

## Reference Tables
| 问题 | CSS 解 |
|---|---|
| 弹层滚动带动页面 | overscroll-behavior: contain |
| 锚点跳转生硬 | scroll-behavior: smooth |
| 轮播一划多屏 | scroll-snap-stop: always |
| 禁用元素还能被 Tab | pointer-events 拦不住键盘，需 disabled |
| 长页滚动卡顿 | content-visibility: auto |
| 动画掉帧 | will-change（慎用）+ 只动 transform/opacity |

## Anti-patterns
- **全站撒 will-change**：合成层爆炸，性能反降；只在即将动画的少量元素上声明并在结束后移除。
- **pointer-events:none 当"完整禁用"**：键盘照常触发；真禁用用 disabled 属性。
- **content-visibility 不配 contain-intrinsic-size**：视口外元素高度为 0，滚动条乱跳。
- **滚动条样式只写 -webkit- 系**：Firefox 用 scrollbar-width/color 标准属性。

## Key Takeaways
1. 滚动体验四件套：smooth（顺）、overscroll-contain（稳）、scroll-snap（准）、overflow-anchor（不跳）。
2. pointer-events 继承性 + 键盘豁免是它的两大"隐藏性格"。
3. 性能优化优先级：content-visibility（渲染量）> contain（隔离）> will-change（提前优化）——前两者几乎零风险。
4. 打印也是"设备适配"：break 系 + @page 让 Web 页面体面地变成纸。

## Connects To
- **Ch 6/7**: scroll-snap 配 flex/grid 轮播；touch-action 配断点适配。
- **Ch 5**: will-change 与"高性能动画三要素"互补。
- **Ch 8**: 主题切换 + ::selection 定制是品牌化标配。
