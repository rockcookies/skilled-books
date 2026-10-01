# Chapter 7: 不同设备的适配与响应（@media / env() / rem+vw / touch-action / image-set）

## Core Idea
设备适配的两条主线：@media 查询环境（宽度、深色模式、输入方式），vw/rem+calc 的流式换算；env() 是 iPhone 刘海逼出来的新函数。

## Frameworks Introduced
- **@media 四部分**：修饰符 + 媒体类型 + 条件 + 特性。
  - 修饰符：`only`（历史产物——防老浏览器把 `screen and (color)` 误读成 screen，今已无用）、`not`（否定的是**整个查询语句**）。
  - 媒体类型：只用 screen/print/all 三个；屏幕阅读器识别的是 screen（不是已废弃的 speech）；`@media screen, print` 逗号多值。
  - 条件：and / or（`@media (update: slow) or (hover: none)`）/ not（`screen not (color)` 无效——not 实际是修饰符）。
  - 媒体特性分层记忆：
    - **日常主力**：width/height（+min/max 变体）、orientation（横竖屏；软键盘弹出会误判）、aspect-ratio、resolution（`2dppx` 替代私有 device-pixel-ratio）。
    - **现代必会**：`prefers-color-scheme`（light/dark——深浅主题适配标准入口，JS 侧配 `window.matchMedia().matches`）、`prefers-reduced-motion`（尊重"减少动态效果"设置）。
    - **输入方式检测**：hover/any-hover、pointer/any-pointer（精度 fine/coarse——区分鼠标与触屏）。
    - 其余（color-gamut、display-mode、monochrome、update 等）了解即可；device-height/device-width 已废弃。
- **env() 环境变量函数**：读取设备**安全区域**四向内边距 `safe-area-inset-top/right/bottom/left`（iPhone 刘海/底部横条），支持第二参数默认值 `env(safe-area-inset-bottom, 20px)`。
  - 两个坑：**变量名区分大小写**（Safe-Area-Inset 无效）；必须配置 viewport `<meta name="viewport" content="viewport-fit=cover">` 才有准确值。与 var() 的区别：env() 可用于媒体查询语句中。
- **移动端适配最佳实践**（rem + vw + calc）：
  - 根字号随屏宽线性缩放：`html { font-size: calc(16px + 2 * (100vw - 375px) / 39); }`——375px 基准 16px，每增加 1px 屏宽字号增 2/39px（375~414 设计区间）。
  - 升级版加 min/max 限制（或 clamp）：`font-size: clamp(16px, calc(...), 20px)`，并在前面写 `font-size:16px` 给旧浏览器兜底。
  - 纯 vw 布局（新手流）：设计稿 120px 在 375 稿上 = `120/375*100 = 32vw`——简单直接但"搬砖砌墙"，不利于 CSS 深入，作者不推荐为主流方案。
- **touch-action**（控制浏览器默认触摸行为）：
  - `manipulation`：只允许滚动+持续缩放，禁双击缩放——**消除移动端点击 300ms 延迟的官方姿势**（`html { touch-action: manipulation; }`）。
  - `none`：全禁（自绘手势场景，如画板）；`pan-x/pan-y/pan-left/right/up/down`：限定方向（轮播横滑）；`pinch-zoom`：捏合缩放；值可组合 `pan-left pan-up`。
- **image-set()**：按屏幕密度/分辨率加载不同背景图（属 `<image>` 数据类型，可用于 background-image/mask-image 等）：`background-image: image-set(url(1x.png) 1x, url(2x.png) 2x)`。适用条件：不同密度显示**完全不同的图**（非仅尺寸差异）、大用户基数产品的流量收益明显——一般场景还是多倍图+background-size。

## Code Examples
```css
/* 深色模式适配 */
@media (prefers-color-scheme: dark) {
   body { background: #333; color: #fff; }
}

/* 安全区适配（刘海屏） */
.footer {
   padding-bottom: env(safe-area-inset-bottom, 20px);
}
/* HTML: <meta name="viewport" content="width=device-width, viewport-fit=cover"> */

/* 流式根字号（375~414 区间 16→20px） */
html {
   font-size: 16px;
   font-size: clamp(16px, calc(16px + 2 * (100vw - 375px) / 39), 20px);
}

/* 消除移动端点击延迟 */
html { touch-action: manipulation; }
```

## Reference Tables
| 需求 | 媒体特性 |
|---|---|
| 断点布局 | `(min-width/max-width)` |
| 深色模式 | `(prefers-color-scheme: dark)` |
| 尊重减少动画 | `(prefers-reduced-motion: reduce)` |
| 鼠标 vs 触屏 | `(hover: hover) and (pointer: fine)` |
| 高分屏图 | `(min-resolution: 2dppx)` |

## Anti-patterns
- **env() 不配 viewport-fit=cover**：safe-area 值恒为 0。
- **继续用 -webkit-device-pixel-ratio**：用标准 `resolution: 2dppx`。
- **依赖 orientation 判断横竖屏**：软键盘弹出会翻转判定；关键布局用 width 断点。
- **全站纯 vw 布局**：一切随屏宽线性缩放，大屏字大到失真；clamp 限区间才是正解。

## Key Takeaways
1. @media 只记三类型（screen/print/all）+ 五特性（width/height/orientation/resolution/prefers-*）。
2. env(safe-area-inset-*) 三要素：区分大小写、viewport-fit=cover、默认值兜底。
3. 移动适配黄金公式：`clamp(最小, calc(基准 + 斜率*(100vw - 设计宽)/区间宽), 最大)`。
4. touch-action:manipulation 是移动端交互优化的免费午餐。

## Connects To
- **Ch 8**: var() 与 env() 语法相似但 env 可入媒体查询；主题切换=CSS 变量+prefers-color-scheme。
- **Ch 2**: @supports 与 @media 同为条件规则可互相嵌套。
- **Ch 6**: auto-fit 网格让多数卡片布局不再需要断点。
