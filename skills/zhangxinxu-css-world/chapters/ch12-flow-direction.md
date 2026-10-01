# Chapter 12: 流向的改变（direction / unicode-bidi / writing-mode）

## Core Idea
CSS 的"自左往右、自上而下"不是永恒规律——direction 改内联顺序，writing-mode 直接颠覆纵横规则，"三剑客"把水平世界的所有规则搬到垂直方向。

## Frameworks Introduced
- **direction（IE5.5+，极简两值 `ltr`/`rtl`）**：改变**替换元素与 inline-block/inline-table 元素**的水平呈现顺序（纯字符不受影响）。
  - 经典性价比场景：confirm 弹窗桌面端"确认在左、取消在右"，移动端反转——`@media (max-width:480px){ .dialog-footer{direction:rtl} }` 一行搞定，免 JS 改 DOM、免浮动 hack（居中对齐时浮动根本无解）。
  - **开头打点**：`dir="rtl"` + 单行打点三件套 → 省略号出现在开头（省前面文字），配合 `text-align:left` 保住尾部图标不被打点。
  - 其他：反转表格列顺序；让 `text-align:justify` 最后一行落单元素右对齐；不支持 `text-align:start/end` 的浏览器里，direction 决定 text-align 初始值（ltr→left，rtl→right）。
- **unicode-bidi（direction 的黄金搭档，所有浏览器支持）**：处理混合文字方向的"双向性"。
  - `normal`：仅图片/按钮/符号等按 direction 反排，中英文字符照旧；
  - `embed`：开启独立嵌入层重排（内部不受外部 bidi-override 影响）；原理 = 元素首尾插入 U+202A/B + U+202C 字符（可直接写实体字符替代属性）；
  - `bidi-override`：强制所有字符按 direction 方向反排（`&#x202E;一二三四五六七八九十` 直接呈现"十九八七…"）。
  - 规范提醒：嵌入限 61 层，非 normal 值应克制使用。
- **writing-mode（CSS 世界最强属性之一；现代浏览器已全支持）**：
  - 实战记忆版：`writing-mode: horizontal-tb | vertical-rl | vertical-lr`（CSS3）≡ `lr-tb | tb-rl | tb-lr`（IE8+）；只需兼容 IE7 记 `lr-tb`/`tb-rl` 两个。相同值父子不累加。
  - **垂直流的规则平移**（水平流专属规则全部换轴）：
    1. margin 合并改发生在**水平方向**；
    2. `margin:auto` 可实现**垂直居中**（普通块元素全浏览器 OK；替换元素 IE 不行，改用 `text-align:center`）；
    3. `text-align:center` 变成图片垂直居中；
    4. `text-indent` 变成垂直方向位移——按钮 `:active{ text-indent:2px }` 免高度/padding 计算的"文字下沉"效果（中文直立不旋转，单字按钮最妙）；
    5. 英文/数字符号"躺着"= 天然 90° 旋转 → icon fonts 图标免 transform 旋转（IE6/7 都行）；
    6. 高度天然自适应 → 垂直方向等分/自适应布局。
- **三剑客关系**：direction 与 unicode-bidi 是近亲（仅有的两个不受 `all` 属性影响的属性，服务内联元素，为阿拉伯文设计）；writing-mode 改变块级纵横规则，与 direction 一横一纵**没有交集**（vertical-rl 的 rl 是水平列进方向，direction:rtl 改的是垂直行内的文本方向），为东亚文字设计。

## Code Examples
```css
/* 移动端按钮顺序反转（免 JS、免 DOM 调整） */
@media screen and (max-width: 480px) {
   .dialog-footer { direction: rtl; }
}

/* 开头打点：省略号在文字前面 */
.ell { width:240px; white-space:nowrap; text-overflow:ellipsis; overflow:hidden; }
/*  <p class="ell" dir="rtl">长文本…</p>  */

/* 全浏览器垂直流声明（IE8 + 现代浏览器） */
.verticle-mode {
   writing-mode: tb-rl;            /* IE8+ */
   -webkit-writing-mode: vertical-rl;
   writing-mode: vertical-rl;
}

/* 垂直流下的按钮按下文字下沉 */
.btn:active { text-indent: 2px; }

/* 垂直流下的图片垂直居中（IE 替换元素用这个） */
.verticle-mode img { display:block; }   /* 父级 text-align:center */
```

## Reference Tables
| 想要的效果 | direction 系 | writing-mode 系 |
|---|---|---|
| 水平元素顺序反转 | `direction:rtl` | — |
| 省略号打到开头 | `dir=rtl` + 打点三件套 | — |
| 字符逐个反向 | `unicode-bidi:bidi-override` | — |
| 垂直排版 | — | `vertical-rl` |
| margin:auto 垂直居中 | — | 垂直流 + 块元素 |
| 图标旋转（免 transform） | — | 垂直流让字符"躺倒" |

## Anti-patterns
- **以为 writing-mode 弱是因为没用过**：现代浏览器支持已无障碍，它是"换轴开关"，很多垂直居中/旋转难题在这里是一行代码。
- **给纯中文文本设 direction:rtl 期待反排**：字符不动，只有替换元素/inline-block 反序——要逐字反向必须 `unicode-bidi:bidi-override`。
- **混用 vertical-rl 与 direction:rtl 想叠加效果**：一横一纵无交集，别指望联动。
- **verticle-mode 内再套相同 writing-mode**：值不累加，不会二次旋转。

## Key Takeaways
1. 改内联顺序想 direction（一行、免 JS）；改纵横规则想 writing-mode。
2. 垂直流 = 水平规则的镜像：margin 合并、margin:auto、text-align、text-indent 全部换轴。
3. bidi-override 可用 U+202E/U+202C 字符直接替代；embed 的本质是插入 U+202A/B 隔离层。
4. writing-mode 与 direction/unicode-bidi 无交集：一个管块级纵横，一个管内联方向。

## Connects To
- **Ch 1**: "文档流自上而下"的说法在本章被正式推翻——流向可改变。
- **Ch 4/3**: margin 合并方向、margin:auto 的 fill-available 前提在垂直流下的镜像验证。
- **Ch 11**: cursor:vertical-text 与垂直排版配对；逻辑属性（*-start/*-end）是流方向的新世界延伸。
