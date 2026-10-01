# Patterns — 《CSS世界》实战模式集

## 布局类

### 流体两栏（一侧定宽）
**When to use**: 图文混排、侧栏+内容的经典自适应两栏。
**How**: 定宽侧 `float:left`；内容侧保持块级+流动，用 `margin-left` 避让（作者推荐）；宽度完全不定时给内容侧触发 BFC（如 `overflow:hidden` 或 `display:table-cell`），自动收缩避让。
**Trade-offs**: margin 避让需知道定宽值；BFC 栏零计算但有 overflow 裁剪/锚点定位隐患（table-cell 无此虑但 IE8+）。

### 等高布局（三选型）
**When to use**: 分栏背景色需与容器等高。
**How**: ① table-cell 天然等高（无 IE6/7 时首选）；② margin 负值：`margin-bottom:-9999px; padding-bottom:9999px` + 父 `overflow:hidden`；③ border：父 `border-left:150px solid` + 栏 `margin-left:-150px; float:left`。
**Trade-offs**: margin 负值法有锚点定位/overflow 限制；border 法稳健但栏数 ≤2~3 且不支持百分比宽；table-cell 最干净。

### 纯 CSS 两端对齐列表
**When to use**: 一行 N 个、中间定间隙、末行左对齐、需兼容 IE8。
**How**: 机制 A（margin 负值）：`ul{margin-right:-20px}` + `li{float:left;margin-right:20px}`；机制 B（inline-block）：`ul{text-align:justify}` + `li{display:inline-block}` + 行尾补等宽占位标签（处理基线间隙：容器 `font-size:0` 或占位元素塞 `&nbsp;`）。
**Trade-offs**: A 需定宽 li；B 对 HTML 空格/换行敏感，需注释"此处不能换行"。

### 展开收起动画（任意高度）
**When to use**: 高度不定的面板展开/收起需要 transition。
**How**: `max-height:0; overflow:hidden; transition:max-height .25s` ↔ 激活态给"足够安全的最小值"（如 666px）。
**Trade-offs**: max-height 远大于实际高度时收起有可见延迟；值要贴近内容上限。

### absolute 全屏/居中（流体写法）
**When to use**: 遮罩层、全屏容器、绝对定位元素居中。
**How**: 覆盖：`position:absolute; left:0; right:0; top:0; bottom:0;`；居中：同上 + 定宽高 + `margin:auto`。
**Trade-offs**: 流体写法吃 padding/margin 自动分配，远优于 `width/height:100%`（后者加 margin 溢出）。

### 弹框纯 CSS 水平垂直居中
**When to use**: 大小不固定的弹框，免 JS 免 resize 监听。
**How**: 见 ch05/ch10：fixed 容器 `text-align:center; font-size:0` + `:after{content:''; display:inline-block; height:100%; vertical-align:middle}` + 弹框 `display:inline-block; vertical-align:middle`；容器 `overflow:auto` 支持超高滚动。
**Trade-offs**: 是"近似垂直居中"（font-size:0 后偏差消失）；IE7 需把伪元素换成真实元素。

## 内联类

### 图片底部间隙清除
**When to use**: 块容器里图片下方多出几像素。
**How**: 四选一：图片 `display:block`（一锅端）；容器 `line-height:0`；容器 `font-size:0`（行高需相对值）；图片 `vertical-align:top|middle|bottom`。
**Trade-offs**: 块状化影响图文混排场景；font-size:0 会影响容器内其他文字。

### 20px 图标基线对齐体系
**When to use**: 全站背景图标与文字对齐，免逐个 vertical-align 调参。
**How**: 图标统一 20×20 + `display:inline-block`；标签内永远有字符（`:before{content:'\3000'}` 全角空格）；不用 `overflow:hidden`，用 `text-indent:-999em; letter-spacing:-1em` 藏字 → 基线=内部字符基线，天然对齐。
**Trade-offs**: 需统一图标规格；20px 是经验值，可按站点字号调整（如 24px）。

### hover 延时显示下拉
**When to use**: 相邻多个 hover 触发点，防误触路过的面板闪现。
**How**: `.list{visibility:hidden}` + `:hover .list{visibility:visible; transition:visibility 0s .2s}`——transition 写在 hover 态，进入延时 0.2s、移出立即消失。
**Trade-offs**: 依赖 transition 支持 visibility（不支持 display）。

### 需要测量尺寸的隐藏
**When to use**: 隐藏元素要先取 clientWidth/getBoundingClientRect 再定位。
**How**: `position:absolute; visibility:hidden`（display:none 全为 0）。
**Trade-offs**: 元素仍渲染，注意不要大面积使用。

### 下划线精致化
**When to use**: text-decoration 下划线与中文粘连/穿过英文。
**How**: `text-decoration:none; border-bottom:1px solid;` + `padding-bottom` 调距；border-color 缺省跟随 color（hover 自动变色）；可换 dashed/dotted。
**Trade-offs**: 多行文本每行都会有下划线（text-decoration 同样如此，行为接近）。

## 文本类

### 中文两端对齐
**When to use**: 正文两端对齐需兼容 IE。
**How**: `text-align:justify; text-justify:inter-ideograph;`
**Trade-offs**: text-justify 是 IE 私有值，无未来冲突风险。

### 全局行高重置
**When to use**: 任何站点初始化。
**How**: `body{line-height:1.5}`（数值！不是 150%/1.5em）；表单替换元素补 `input,button{line-height:inherit}`；行高计算**向上舍入**（14px×1.42858 才是 20px）。
**Trade-offs**: 数值继承属性值可随字号缩放；长度值适合像素级精确布局。

### 文本隐藏（logo 等）
**When to use**: SEO 保留文字、视觉显示图片。
**How**: 首选 `text-indent:-120px`（定长）+ `overflow:hidden`；次选 `font-size:0`。不用 `-9999em`（性能+读屏）与 `text-indent:100%`（依赖包含块宽度）。
**Trade-offs**: text-indent 只作用第一行；inline-block 子元素需 `text-indent:0` 重置。

### 输入框视觉大写
**When to use**: 身份证 X、验证码输入。
**How**: `input{text-transform:uppercase}`。
**Trade-offs**: 只改视觉不改值，提交前无需转换。

## 图形与装饰类

### border 三角/梯形
**When to use**: 对话框尖角、下拉箭头，IE8 兼容。
**How**: `width:0; border:10px solid; border-color:#f30 transparent transparent;`（朝下三角）；宽度 10px 不归零则得梯形。
**Trade-offs**: 边框色缺省跟随 color，hover 改 color 一处即可。

### IE8 多背景模拟
**When to use**: 需要多背景但兼容 IE8。
**How**: 父 `position:relative; z-index:0`（造结界）；伪元素 `position:absolute; z-index:-1` 垫底放第 2/3 张背景图。
**Trade-offs**: 最多 3 张；伪元素内容不可选、读屏不可达（本来就是背景，无碍）。

### 无 hack 区分 IE8/IE9+/IE10+
**When to use**: 需要按浏览器能力加载不同资源。
**How**: `background:url(a.png); background:url(a.svg), none;`（IE8 不认多背景）；`background:url(a.gif); background:url(a.png), linear-gradient(to top,transparent,transparent);`（IE9- 不认渐变）。
**Trade-offs**: 依赖"认识/不认识"而非 UA，比 :root hack 更稳。

### 透明能力分层
**When to use**: 需要透明的场景（边框留白、剪裁留白、隐藏文字）。
**How**: `background-color:transparent`（IE6+）→ `border-color:transparent`（IE7+）→ `color:transparent`（IE9+）。透明度换算：`Math.round(256*opacity).toString(16)` 拼 `#AARRGGBB` 给 IE 滤镜。
**Trade-offs**: 兼容模式下的 IE 表现不可信，按原生版本分层。

## 交互类

### outline 布局妙用
**When to use**: 镂空遮罩（头像剪裁）、页脚色块填满屏幕。
**How**: 镂空：剪裁框 `outline:256px solid rgba(0,0,0,.5)` + 父 `overflow:hidden`；填屏：页脚内容 `position:absolute` + `outline:9999px solid` + `clip:rect(0 9999px 9999px 0)` 裁掉上侧。
**Trade-offs**: outline 无法指定方位只能四周扩散，必须配 clip 或 overflow 定向；IE10- 镂空区点击穿透需补背景。

### label 代言按钮（含焦点代理）
**When to use**: 原生按钮/表单控件 UI 难看，用 label 变身。
**How**: 原生控件 `position:absolute; clip:rect(0 0 0 0)`；label `:focus + label.btn{outline:1px dotted Highlight; outline:5px auto -webkit-focus-ring-color}` 补键盘焦点视觉。
**Trade-offs**: 保留原生行为与可访问性；多一层样式维护。

### body 级 progress 光标
**When to use**: 界面已渲染但 JS 未就绪的窗口期。
**How**: 初始 `body{cursor:progress}`，JS 初始化完成改回 `auto`。
**Trade-offs**: 不能替代组件级 loading（键盘用户感知不到）。

### 蒙层背景锁定
**When to use**: 弹窗出现时禁止背景滚动。
**How**: 根元素 `overflow:hidden` + JS 补 `border-right:17px solid transparent`（17px=滚动条宽）防晃动；关闭时移除。根治方案是 absolute 模拟 fixed（页面滚动交给内部容器）。
**Trade-offs**: 根治方案需要调整整站滚动结构，组件需适配。
