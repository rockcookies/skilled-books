# Cheatsheet — 《CSS新世界》决策速查

## 判断规则（When X → do Y）

- 学新属性 → 先看它支持的**数据类型**（`<image>`/`<color>`/`<basic-shape>`），类型即能力清单。
- 缩写语法记不住 → 记"斜杠前后类型必须相同/部分相同"（background: position/size；font: size/line-height；grid: rows/columns）。
- 想还原浏览器默认样式 → `revert`（initial 是规范初始值，会丢 ol 序号）；想变 span → `all: unset`。
- 绝对定位弹框要居中还要动画 → `width/height: fit-content + inset:0 + margin:auto`，transform 留给动画。
- flex 子项尺寸诡异 → 先查 flex 缩写补全：`flex:1 = 1 1 0%`（basis 不是 auto！）；`flex:none` 防挤压；`flex:auto` 保内容。
- fr 不铺满 → fr 总和 <1 只按比例拿份额；想铺满用 ≥1 的整数值。
- grid 卡片列数不定 → `repeat(auto-fit, minmax(min, 1fr))`；末行留白难看换 auto-fit（非 auto-fill）。
- sticky 没效果 → 三查：祖先有无 overflow≠visible？父容器高度是否与元素等高？多个 sticky 是否没分容器？
- 渐变不生效/断开 → 检查 background-origin（粗边框断开设 border-box）；渐变想动画 → @property 注册颜色。
- 圆角边框图片不圆 → border-radius 管不了 border-image，用 `clip-path: inset(0 round N)`。
- 文字描边侵蚀字形 → -webkit-text-stroke 居中描边；换 SVG `paint-order: stroke`。
- 混合模式"失灵" → 祖先层叠上下文成了边界；`isolation: isolate` 显式控制范围。
- 想让变量动画 → 必须 `@property` 注册 syntax；未注册变量是任意 token 串。
- 弹层滚动带动页面 → `overscroll-behavior: contain`。
- 长页渲染慢 → 列表项 `content-visibility:auto + contain-intrinsic-size`；别全站撒 will-change。
- 剪裁区还能被点到/点不到 → clip-path/mask 隐藏区域交互一并失效（特性也是坑）。

## 决策树

**隐藏/裁剪选型？**
按形状裁（可过渡、可交互边界）→ clip-path
按图像/渐变淡出（PNG 透明替代、图标变色）→ mask
按盒模型裁（兼容 IE9+）→ background-clip / overflow

**适配方案？**
流式数值（字号/宽度区间）→ clamp(MIN, calc(...vw...), MAX)
安全区 → env(safe-area-inset-*) + viewport-fit=cover
多倍图 → image-set() / 多倍图+background-size
鼠标 vs 触屏 → @media (hover:hover) and (pointer:fine)

**布局选型？**
一维分配 → flex（flex:0/none/1/auto 四单值起步）
二维骨架/卡片 → grid（template + repeat/fr/minmax）
文字环绕形状 → shape-outside（浮动元素上）
报纸分栏 → columns
flex 最后一行对齐死角 → 换 grid

**性能问题？**
渲染量大 → content-visibility（零风险）
局部重排频繁 → contain: content
动画掉帧 → 只动 transform/opacity + will-change（少量、短命）

## 阈值与默认值

| 项 | 值 |
|---|---|
| object-fit 默认 | fill（变形）；scale-down=min(none,contain) |
| opacity 透明度叠加 | 父×子（乘积），混合公式 fg×α+bg×(1−α) |
| CSS 渐变角度 | 0deg=向上，顺时针为正（与 PS 相反） |
| radial 默认 | ellipse farthest-corner at center |
| conic 断点换算 | 45deg ≡ 12.5% |
| stroke 描边动画 | dasharray=L; dashoffset: L→0（L=路径长） |
| miterlimit 默认 | 4 |
| text-emphasis 位置 | 默认 over right；中文用 under right |
| will-change 副作用 | 声明即生效对应属性的层叠上下文 |
| pointer-events:none | 不拦键盘；子元素可 auto 复活 |
| var() fallback 触发 | 未定义 / 空值 / 全局关键字；非法值不走 fallback 而回落初始值 |
| --变量命名 | 支持数字开头/中文/空格；特殊字符需转义 |

## 征兆速诊（Tells & Smells）

| 征兆 | 病根 |
|---|---|
| flex:1 的子项不按内容宽 | basis 被缩写补成 0%，非 auto |
| sticky 一动不动 | 祖先 overflow / 父等高 / 未分容器 |
| grid 列塌成最小宽 | minmax(1fr,200px) 写反（fr 只能第二参） |
| 渐变 hover 突变不平滑 | 渐变不可过渡；@property 注册颜色 |
| margin:auto 居中失效 | 元素非 fill-available；用 inset+fit-content |
| mask 显示反了 | 遮罩白/不透明=显示；JPG 遮罩需 mask-mode:luminance |
| clip-path 动画跳变 | 前后关键点数量不一致 |
| offset 缩写整条失效 | 子属性兼容参差；分开写 |
| will-change 加了更卡 | 合成层爆炸；少量元素+用完移除 |
| initial 恢复不了 ol 序号 | initial=规范初始值；用 revert |
| env() 恒为 0 | 缺 viewport-fit=cover |
| 深色模式图片刺眼 | mix-blend-mode: difference / 提供双图 |

## 新旧对照速记

| 旧世界做法 | 新世界替代 |
|---|---|
| display:table+margin:auto 居中收缩元素 | width: fit-content + margin:auto |
| transform: translate(-50%,-50%) 居中 | inset:0 + margin:auto（fit-content） |
| 负 margin/伪元素做两端对齐列表 | flex gap / grid auto-fit |
| JS 监听滚动吸顶 | position: sticky |
| JS 平滑滚动 | scroll-behavior: smooth |
| body 锁滚动 JS | overscroll-behavior: contain |
| JS 计算路径动画 | offset-path + offset-distance |
| 多张图 hover 切换 | cross-fade / opacity 过渡 |
| float 环绕 + 手抠形状 | shape-outside |
| Sass 变量换肤 | CSS 自定义属性 + @property |
| background-position sprite | img + object-fit/object-position |
