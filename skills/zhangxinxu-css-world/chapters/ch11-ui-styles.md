# Chapter 11: 用户界面样式（outline / cursor）

## Core Idea
outline 与 cursor 是与键盘访问、鼠标交互直接相关的"体验属性"：outline 永远不要全局清零，cursor 的几十个关键字值各自携带明确的交互语义。

## Frameworks Introduced
- **outline 双原则**：
  1. **万万不可全局 `outline:0 none`**——focus 虚框/外发光是键盘用户（Tab 巡航、遥控器/智能电视）定位焦点的唯一线索。现代浏览器点击已不触发 outline，只有 Tab/`element.focus()` 才出现，没有清零的理由。输入框确需重置时，必须补 `:focus` 样式（如 `.input:focus{border-color:Highlight}`）。
  2. **outline 是第一个真正不占据空间的属性**（内联元素 padding 的"不占空间"遇 `overflow:auto` 会露馅，outline 不会；轮廓可穿透）→ 布局化妙用的根基。
- **label"代言"按钮的焦点代理**：`clip` 隐藏原生按钮 + label 变身按钮后，Tab 聚焦的是隐形按钮——补一条 `:focus + label.btn { outline:1px dotted Highlight; outline:5px auto -webkit-focus-ring-color; }` 让代言人连 focus 态一起代言（前者模拟 IE/Firefox 虚框，后者用 Chrome 原生发光）。
- **cursor 语义分类**（支持关键字最多的 CSS 属性）：
  - 常规：`auto`（默认值，按内容自动：输入框 text、链接 pointer、原生按钮 default）/ `default`（注意：**不是**默认值）/ `none`（隐藏光标：视频静止 3 秒隐藏；IE8 用透明 .cur 降级）。
  - 状态：`pointer`（链接手形；按钮全用手形是 `<a>` 模拟按钮时代的历史约定——但模拟禁用态记得把 pointer 还原成 default）/ `help`（问号图标/帮助链接，比 pointer 更细腻）/ `progress`（**body 级 JS 加载期**转圈，初始化完改回 auto——界面已渲染但 JS 未就绪时的焦虑缓解器）/ `wait`（死机联想，勿用）/ `context-menu`（自定义右键菜单提示）。
  - 选择：`text`（文字可选中；配 `user-select:none` 时必须同步 `cursor:default`，否则语义矛盾）/ `vertical-text` / `crosshair`（像素级框选/取色）/ `cell`（Excel 御用，单元格框选）。
  - 拖曳：`move` / `copy` / `alias` / `no-drop` / `not-allowed`——**not-allowed 属于拖曳语义**，禁用按钮应该用 default + 样式变化表达，而非 not-allowed。
  - 拉伸：8 单向（n/e/s/w/ne/nw/se/sw-resize，实际多渲染成双向）+ 4 双向（ew/ns/nesw/nwse-resize，IE10+）→ 声明对写法 `cursor:se-resize; cursor:nwse-resize;` 兜底。
  - 缩放：`zoom-in/zoom-out`；抓取：`grab/grabbing`（各浏览器形状有差异）。
- **自定义光标**：Chrome 可直接 PNG；IE 只认 `.cur`（透明光标做 IE8 降级）。典型应用：图片预览左右半区的上一张/下一张箭头光标。

## Code Examples
```css
/* label 代言按钮的键盘焦点代理 */
[type="submit"] { position:absolute; clip:rect(0 0 0 0); }
:focus + label.btn {
   outline: 1px dotted Highlight;                  /* IE/Firefox 虚框 */
   outline: 5px auto -webkit-focus-ring-color;     /* Chrome 发光 */
}

/* 头像剪裁镂空遮罩：超大 outline + 容器裁剪 */
.crop { overflow:hidden; }
.crop > .crop-area {
   width:80px; height:80px; cursor:move;
   outline:256px solid #000;
   outline:256px solid rgba(0,0,0,.5);
   background:url(about:blank);                    /* 防 IE 点击穿透 */
}

/* 页脚色块自动填满屏幕剩余高度 */
.footer > p {
   position:absolute; left:0; right:0; padding:15px 0;
   background:#a0b3d6;
   outline:9999px solid #a0b3d6;
   clip:rect(0 9999px 9999px 0);                   /* 上/左方向裁掉 */
}

/* JS 未就绪期的全局 progress 光标 */
body { cursor:progress }   /* JS 初始化完成后改回 auto */
```

## Reference Tables
| cursor 兼容梯队 | 值 |
|---|---|
| 放心用 | auto/default/move/text/pointer/help/progress/wait/not-allowed/单向 resize/col-resize/row-resize 等 |
| IE9+ | none/alias/cell/copy/ew|ns|nesw|nwse-resize/context-menu |
| Edge12+ | zoom-in/zoom-out |
| Edge14+ | grab/grabbing |

## Worked Example
**底部填满屏幕**：内容不足一屏时页脚下方露白。解法：页脚内层绝对定位铺满宽度 + `outline:9999px solid` 向四周（含下方）涂色 + `clip:rect(0 9999px 9999px 0)` 以"上、左"为界裁剪（rect 的 top/left 为 0 把上侧轮廓裁没，right/bottom 9999 保留下方涂色）——outline 不占空间所以不影响布局，clip 需要的 absolute 恰好是 clip 的生效前提。

## Anti-patterns
- **全局 reset 写 `*{outline:0 none}`**：键盘用户直接迷失；大站也常犯，不要模仿。
- **禁用按钮 `cursor:not-allowed`**：拖曳语义错位；用 default + 视觉禁用态。
- **`user-select:none` 不配 `cursor:default`**：文字光标却选不中，语义精神分裂。
- **弹窗标题栏可拖但不设 `cursor:move`**：用户学习成本白白上升。
- **用 wait 光标表达"处理中"**：死机既视感；loading 用组件内动画 + 场景合适才用 progress。

## Key Takeaways
1. focus 轮廓是功能不是装饰：清 outline 必须给出替代的 focus 视觉。
2. outline 不占空间 + 可穿透 + IE8 支持 = 镂空遮罩、填满屏幕两大布局妙用（配 clip 定向裁剪）。
3. cursor 选值跟着交互语义走，别只会 pointer；禁用态配 default，加载期配 body 级 progress。
4. IE 光标只认 .cur，跨浏览器自定义光标准备两种格式。

## Connects To
- **Ch 10**: clip 剪裁隐藏与 label 代言按钮同属"隐形但可用"体系。
- **Ch 4**: `Highlight` 系统色与 label/按钮 clip 组合在表单美化中的复用。
- **Ch 12**: `vertical-text` 光标与 writing-mode 垂直排版天然配对。
