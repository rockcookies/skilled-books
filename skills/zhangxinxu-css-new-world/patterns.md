# Patterns — 《CSS新世界》实战模式集

## 布局类

### 响应式等分卡片（auto-fit 公式）
**When to use**: 卡片/图片列表，列数随容器宽度自适应。
**How**: `display:grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: ...`。
**Trade-offs**: 一行解决断点问题；min 值即最小卡片宽；子项不足时也铺满（auto-fill 会留白）。

### flex 最后一行左对齐
**When to use**: space-between 列表末行元素不齐。
**How**: 列数固定→负 margin 补偿；末行缺位→等尺寸占位元素；**列数不固定→换 grid**（天然左对齐）。
**Trade-offs**: grid 方案零 hack，是 flex 死角的标准逃生门。

### sticky 层次滚动
**When to use**: 标题依次置顶、评论区从内容后浮现的层次感滚动。
**How**: 标题 `position:sticky; top:0`；评论 `sticky; bottom:50vh; z-index:-1`；**每段内容独立容器包裹**（多个 sticky 依次推开的关键）。
**Trade-offs**: 祖先不能有 overflow≠visible；父容器高度需大于 sticky 元素。

### fit-content 弹框居中
**When to use**: 大小不定的绝对定位弹框居中，且要保留 transform 做入场动画。
**How**: `width/height: fit-content; position:absolute; inset:0; margin:auto`。
**Trade-offs**: IE/Edge 不支持 fit-content → transform:translate(-50%,-50%) 兜底（放弃动画）。

### 逻辑属性对称布局
**When to use**: 聊天对话等左右镜像布局。
**How**: 一侧用 margin-inline-start 等逻辑属性写好，另一侧容器一句 `direction: rtl`。
**Trade-offs**: 只在配合 direction/writing-mode 时有意义；flex row-reverse 与逻辑属性无关。

## 视觉类

### 渐变边框
**When to use**: 醒目模块/警示条/自定义虚线。
**How**: `border: 10px solid; border-image: linear-gradient(...) 20 / 10px;`；圆角加 `clip-path: inset(0 round 10px)`；border 声明必须**在** border-image 之前。
**Trade-offs**: border-image 怪异机制学习成本高；简单渐变边框也可用背景+内层元素。

### 多背景绘制图形
**When to use**: 加减号、十字、刻度线等简单图标，免伪元素免图片。
**How**: `background: linear-gradient(currentColor,currentColor) no-repeat center/.875em 2px, linear-gradient(...) center/2px .875em;`（起止同色=纯色条）。
**Trade-offs**: 颜色跟随 currentColor（继承文字色）；复杂图形交给 SVG。

### 阴影动画性能优化
**When to use**: hover 阴影过渡在复杂页面上掉帧。
**How**: 双伪元素各持一层阴影，`transition: opacity` 切换（::before 常态 / ::after hover 态）。
**Trade-offs**: 多两个合成层；把样式重计算降为纯合成。

### 毛玻璃
**When to use**: 浮层、侧边栏、顶栏的磨砂背景。
**How**: `background: rgba(255,255,255,.2); backdrop-filter: blur(8px);`
**Trade-offs**: 背后必须有内容才看得见效果；大面积 blur 有性能成本。

### 图标任意变色（mask 大法）
**When to use**: 彩色 SVG/iconfont 无法用 color 控制的多色图标需要换色。
**How**: `background: var(--color); mask: url(icon.svg) no-repeat center / contain;`
**Trade-offs**: 图标变成单色（遮罩只认形状）；-webkit-mask 兜底旧 webkit。

### 形状揭示动画
**When to use**: 图片/卡片圆形展开、气泡尖角变形。
**How**: `clip-path: circle(0 at 50% 50%) → circle(200px at 50% 50%)` + transition；polygon 需前后点数一致。
**Trade-offs**: 被裁区域不可点击；动画只限 basic-shape 函数。

### 沿路径运动
**When to use**: 小车沿曲线行驶、沿轨迹巡游的元素。
**How**: `offset-path: path("M..."); animation { to { offset-distance: 100% } }`；`offset-rotate: auto` 朝向切线。
**Trade-offs**: IE 全系不支持；缩写 offset 兼容参差，分开写子属性。

## 交互类

### 弹层滚动穿透终结
**When to use**: 弹层/抽屉内容滚动到边缘时带动 body 滚动。
**How**: 弹层滚动容器 `overscroll-behavior: contain`。
**Trade-offs**: 老浏览器忽略即回退原状（渐进增强零风险）。

### hover 延迟触发
**When to use**: 浮层防误触（鼠标快速路过不弹出）。
**How**: 显示方向加 `transition-delay: .2s`（或 transition 写在 hover 态配合 visibility 0s 延时）。
**Trade-offs**: 与《CSS世界》visibility 方案同源；新世界直接用 transition-delay 亦可。

### 遮罩镂空可点击
**When to use**: 全屏蒙层上特定区域可交互。
**How**: 蒙层 `pointer-events:none`，镂空区 `pointer-events:auto`（利用继承性复活）。
**Trade-offs**: 键盘行为不被 none 拦截，禁用语义仍需 disabled。

### 长列表渲染优化
**When to use**: 信息流/长表格首屏渲染慢。
**How**: 列表项 `content-visibility: auto; contain-intrinsic-size: 估计高度;`。
**Trade-offs**: 高度估计不准会有轻微滚动条跳动；浏览器外区域不可被 Ctrl+F 命中（视实现）。

## 工程类

### CSS 变量主题系统
**When to use**: 换肤/深色模式/组件定制。
**How**: `:root { --primary: ... }` + 组件读变量；深色模式 `@media (prefers-color-scheme: dark) { :root { --primary: ... } }`；Web 组件穿透靠变量继承。
**Trade-offs**: 变量不可过渡（除非 @property 注册类型）；命名约束宽松但要立规范。

### @property 渐变过渡
**When to use**: 渐变颜色/角度需要 hover 平滑变化。
**How**: `@property --c { syntax:'<color>'; inherits:false; initial-value:#09f; }` + `transition: --c .3s`。
**Trade-offs**: 仅现代浏览器；需 fallback（未注册时是瞬时切换，可接受）。

### 数据驱动样式（变量+calc+counter）
**When to use**: 进度条/评分/饼图等由数据决定的表现。
**How**: HTML `style="--percent: 40"`；CSS `width: calc(1% * var(--percent))`；数值显示用 `counter-reset: p var(--percent); content: counter(p)`。
**Trade-offs**: JS 只改一个变量；counter 只能整数。

### 属性值语法差异分流
**When to use**: 新旧浏览器加载不同资源/样式。
**How**: 旧声明在前、需新语法解析的声明在后（`background: url(a.gif); background: url(a.png), linear-gradient(transparent,transparent);`）。
**Trade-offs**: 依赖"解析失败整条忽略"规则，稳定可靠；比 :root hack 优雅。

## 兼容类

### stretch 三连声明
**When to use**: 标签受限的替换元素自适应宽度（button/table）。
**How**: `width: -webkit-fill-available; width: -moz-available; width: stretch;`（calc 兜底 IE）。
**Trade-offs**: Firefox 的 table 不认 -moz-available（等同 100%）。

### 安全区适配
**When to use**: 刘海屏/底部横条设备。
**How**: viewport meta 加 `viewport-fit=cover` + `padding-bottom: env(safe-area-inset-bottom, 20px)`。
**Trade-offs**: env 名区分大小写；默认值兜底未支持设备。
