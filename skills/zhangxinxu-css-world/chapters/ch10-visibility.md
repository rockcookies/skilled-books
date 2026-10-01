# Chapter 10: 元素的显示与隐藏

## Core Idea
"不可见"只是一个视觉结果——空间占据、可点击性、键盘/读屏可达性、资源加载、过渡动画能力构成隐藏方案的完整决策矩阵，按场景选工具而不是无脑 `display:none`。

## Frameworks Introduced
- **作者总结的隐藏实践矩阵**（按需求维度选型）：

| 需求组合 | 方案 |
|---|---|
| 不渲染、无请求、读屏不可达（模板） | `<script type="text/html">`（不支持嵌套，内嵌 `<textarea>`，取值 `script.innerHTML` / `textarea.value`） |
| 干净利落隐藏（无空间、不可点、读屏不可达、DOM 可访问） | `display:none` |
| 需要淡入淡出 transition + 不占空间 | `position:absolute; visibility:hidden` |
| 占空间保留、不可点、读屏不可达 | `visibility:hidden` |
| 不占空间、不可点、**键盘可访问** | `position:absolute; clip:rect(0 0 0 0)` 或 `position:relative; left:-999em` |
| 占空间、不可点、键盘可访问 | `position:relative; z-index:-1`（需父级有背景色配合） |
| 不可见但**可点击**、不占空间 | `position:absolute; opacity:0; filter:Alpha(opacity=0)` |
| 原地隐形（占位、可点可选） | `opacity:0` |
| 标签受限藏文字 | `text-indent` 缩进；需展开动画用 `max-height:0 + overflow:hidden` |

## Key Concepts
- **display:none 的隐藏资源加载差异**（未定义行为）：Firefox 不加载其 background-image；Chrome 看父级——父级 none 不加载、自身元素隐藏仍加载（头图轮播把背景图放在隐藏元素的**子元素**上即可延迟加载）；IE 全都加载。`<img>` 元素则任何 display:none 都照样请求。
- **display:none 的例外与暗面**：`<label for>` 仍能触发隐藏 submit 按钮的 click（IE9+；但按钮丢失键盘可达性，不推荐）；`<style>`/`<script>` 等天然 none 的标签设 `display:block` 可在页面显示代码，配 `contenteditable="true"` 可实时编辑预览；`hidden` 属性 IE11+ 原生支持，桌面兼容需补 `[hidden]{display:none}`；display:none 会使元素**退出计数器队列**，且不支持 transition（animation 不受影响）。
- **visibility 的继承性是特性不是 bug**：父 hidden 子 `visibility:visible` 可单独复活——异步加载场景"容器隐藏 + loading 图标 visible"，尺寸就绪后整体 visible，体验优于"占位→加载→界面"三段跳。
- **visibility 与 transition**：transition 属性列表包含 visibility（不含 display）→ hover 延时显示：`.list{visibility:hidden} td:hover .list{visibility:visible; transition:visibility 0s .2s}`——hover 进入延时 0.2s 出现，移出瞬间消失（transition 声明在 hover 态只影响进入方向），解决"路过误触覆盖目标"问题。
- **visibility 对 JS 更友好**：`visibility:hidden`（配 absolute）元素的 clientWidth/getBoundingClientRect 都有真实值，可预先测量定位；display:none 全是 0。
- **visibility 与计数器/读屏**：hidden 元素仍参与计数（display:none 不参与）；VoiceOver 场景下 visibility 隐藏保留 focus 区域"遗骸"，能朗读 title 状态变化（"浮层已关闭"），display:none 则直接跳到下面元素。
- **visibility:collapse**：表格类元素解析各浏览器不准、普通元素等同 hidden——没有理由使用。

## Code Examples
```css
/* hover 延时显示下拉（防误触），移出立即隐藏 */
.list { position:absolute; visibility:hidden; }
td:hover .list { visibility:visible; transition:visibility 0s .2s; }

/* 需要预测量尺寸的隐藏 */
.hidden { position:absolute; visibility:hidden; }

/* 展开动画式隐藏（配合 transition:max-height） */
.hidden { max-height:0; overflow:hidden; }
```

## Worked Example
**选隐藏方案的推理链**：先问"读屏/键盘要不要够到？"→ 要，排除 display:none 与 visibility:hidden，剩 clip/z-index 负值/text-indent；再问"要不要留空间？"→ 要 → visibility:hidden 或 relative+z-index:-1；再问"要不要能点？"→ 要 → opacity:0；再问"要不要过渡动画？"→ 要 → visibility 或 max-height（display:none 无 transition）。每问一步砍掉一批选项，最后剩下的就是正确答案。

## Anti-patterns
- **以为 visibility:hidden 与 display:none 只差占不占空间**：继承可逆性、计数器、transition、可测量性、读屏行为五个维度都不同。
- **display:none 元素指望 transition**：不生效；换 visibility 或 max-height。
- **依赖 `hidden` 属性不做兼容**：IE10 及以下不认识，补 `[hidden]{display:none}`。
- **用 cursor:none 类推 display:none 可藏表单按钮**：label 照样能触发隐藏的 submit——语义上应保键盘可达，别玩火。

## Key Takeaways
1. 隐藏是光谱不是开关：渲染、空间、点击、键盘、读屏、加载、动画七个维度任选组合。
2. 键盘可访问的隐藏 = clip 或负 z-index/屏幕外定位；读屏状态播报 = visibility 优于 display。
3. hover 延时显示的标配是 visibility + transition 延时（声明写在 hover 态）。
4. 需要测量隐藏元素的尺寸/位置，用 visibility 而非 display。

## Connects To
- **Ch 7**: relative + z-index:-1 隐藏依赖"第一个层叠上下文 + 父级背景"的负值渲染规则。
- **Ch 3**: max-height 展开收起动画是显隐与动画的交界。
- **Ch 11**: clip 与 outline 一样属于"不占空间"家族；剪裁隐藏的焦点细微变化在 UI 样式章有对照。
