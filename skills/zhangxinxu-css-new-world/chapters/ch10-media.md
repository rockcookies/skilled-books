# Chapter 10: 图片等多媒体的处理

## Core Idea
object-fit/object-position 补上了替换元素内容适配这块最后拼图；`<image>` 数据类型的三大图像函数（cross-fade/element/image-set）让"图像"成为可组合的 CSS 对象。

## Frameworks Introduced
- **object-fit 五值**（替换元素内容适配，补 ch03 遗留的"fill 适配不可改"）：
  - `fill`（默认）：拉伸填满 content-box，不保比例（原图变形）。
  - `contain`：保比例完整显示，可能留白。
  - `cover`：保比例覆盖裁剪，可能裁边。
  - `none`：按原始尺寸显示，无视外部尺寸。
  - `scale-down`：= min(none, contain) 的效果——只缩不放。
  - 与 background-size 的 cover/contain 语义一致；移动端 Android 4.4.4+ 放心用。
- **object-position**：控制替换内容在容器内的位置（语法同 background-position，支持 4 值 `right 20px bottom 10px`，初始 50% 50%）。**被低估**：`object-fit:cover + object-position:0%/100%` 可替代传统 CSS Sprites 的 background-position 定位（img 语义更好、可 alt、可懒加载）。
- **image-orientation: from-image | none**：是否按图片 EXIF 方向信息自动旋转（现代浏览器默认 from-image）。
- **image-rendering**（位图缩放算法）：`auto | crisp-edges | pixelated`——pixelated 是像素风放大的标准解（Retina 下 1px 图标、像素游戏图）；crisp-edges 各浏览器支持参差（webkit 用 -webkit-optimize-contrast 兜底）。
- **图像类型函数**（`<image>` 数据类型成员）：
  - `cross-fade()`：图像半透明混合。新语法 `cross-fade(url(a.png) 75%, url(b.png) 75%)` 可多图并各自指定透明度；**最实用场景其实是单图透明度调节**（`cross-fade(url(a.png), transparent)` 之类思路——hover 半透明免做两张图）；移动端兼容性极好。
  - `element()`（仅 Firefox，-moz-element）：**把任意 DOM 元素变成实时图像**（`background: element(#title)`）——活的截图，源元素变化背景同步变化；生成图片预览、进度指示的神器但跨浏览器无解。
  - `image-set()`：按分辨率选图（见 ch07）。
- **遮罩与剪裁在 ch12 展开**；`<img>` 的 width/height 属性防布局偏移（CLS）是新世界最佳实践（配合 aspect-ratio）。

## Code Examples
```css
/* 头图裁剪：保持比例填满 + 顶部对齐（人脸不裁掉） */
.cover-img { width: 100%; height: 240px; object-fit: cover; object-position: top; }

/* 用 img 替代 sprite：同一张图,移动 object-position 取不同区域 */
.icon { width: 20px; height: 20px; object-fit: none; object-position: -40px 0; }

/* hover 半透明免双图 */
a img { transition: opacity .2s; }
a:hover img { opacity: .6; }   /* 或 cross-fade 方案 */
```

## Reference Tables
| object-fit | 比例 | 溢出 | 空隙 |
|---|---|---|---|
| fill | 不保 | 无 | 无（变形） |
| contain | 保 | 无 | 有 |
| cover | 保 | 有（裁剪） | 无 |
| none | 原始 | 有 | 有 |
| scale-down | 保 | 无（只缩） | 可能 |

## Anti-patterns
- **object-fit:none 以为等比缩小**：none 是原尺寸；只缩不放用 scale-down。
- **继续用双层背景图做 hover 变透明**：cross-fade 或 opacity 过渡更省资源。

## Key Takeaways
1. object-fit 管形状、object-position 管位置——两者配合接管全部替换元素适配。
2. cover+object-position:top 是"人像头图不裁脸"的标配。
3. `<image>` 函数家族（url/渐变/cross-fade/element/image-set/paint）互通于 background/mask/clip-path——学一次处处可用（呼应 ch02 数据类型思维）。

## Connects To
- **Ch 3**: 替换元素固有尺寸与 fill 适配的底层解释。
- **Ch 12**: mask-image 同样吃 `<image>` 全家桶。
- **Ch 7**: image-set() 多倍图。
