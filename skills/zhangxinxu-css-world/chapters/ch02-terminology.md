# Chapter 2: 需提前了解的术语和概念

## Core Idea
读规范文档和讨论 CSS 必须先统一术语；浏览器间"不一样的表现"未必是 bug，更多是规范未覆盖的"未定义行为"。

## Frameworks Introduced
- **CSS 术语层级链**（从词法到语法，逐级组装）：
  属性 → 值/关键字/功能符 → 属性值 → 声明（属性+属性值）→ 声明块（{}）→ 规则集（选择器+声明块）。
  - When to use: 读 MDN/W3C 文档、写技术讨论时精确指称，避免"高度/颜色"这类口语歧义。
- **单位换算公式**：`<number> + 长度单位 = <length>`。
  - `%` 不是长度单位——`2%` 是一个完整的值（整体），不是"数值+单位"。

## Key Concepts
- **值的类型**：整数 `<integer>`（⊂ 数值 `<number>`）、数值 `<number>`、百分比值 `<percent>`、长度值 `<length>`、颜色值；CSS3 另有角度/频率/时间值。
- **关键字**：CSS 里的英文单词（如 `solid`、`transparent`）；`inherit` 是**泛关键字**（"公交车关键字"）——所有 CSS 属性都可使用。
- **变量**：CSS2.1 时代几乎没有，`currentColor` 属于 CSS3 新世界（详见《CSS 新世界》）。
- **长度单位**：相对字体单位（em、ex；CSS3 的 rem、ch）、相对视区单位（vh、vw、vmin、vmax）；绝对单位除 px 外（pt/cm/mm/pc）实用性近乎零。
- **功能符**：以函数形式出现的值——`rgba()`/`hsla()`、`url()`、`attr()`、`calc()`、`scale()` 等。
- **选择器分类**：类（`.`，天生被公用）、ID（`#`，权重高；多处使用会"雨露均沾"但不应这么做）、属性（`[]` 及 `^`/`$`/`~` 等匹配）、伪类（单冒号 `:`）、伪元素（双冒号 `::`）。
- **关系选择器**：后代（空格）、相邻后代/子选择器（`>`）、兄弟（`~`）、相邻兄弟（`+`）；后三者 IE7+。

## Mental Models
- "未定义行为"判断法：浏览器表现不一致 ≠ bug。规范有规定而违反 = bug；规范空白处厂家按自己理解实现 = 未定义行为（undefined behavior），谁都没有错。
- 术语较真的价值判断：平时写页面不必较真，但要常读 MDN/W3C 原始文档，就必须概念精确，否则看不懂英文规范在说什么。

## Code Examples
```css
.vocabulary {
   height: 99px;          /* 声明 = 属性 height + 属性值 99px */
   color: transparent;    /* transparent 是关键字 */
}
a:active { background-color: red; }
```
```js
// 未定义行为实例：mousedown 里 preventDefault() 之后
button.addEventListener("mousedown", function(event) {
       event.preventDefault();
});
// Chrome/IE: :active 正常变红；Firefox: :active 阵亡（按下无变化）
```
- **What it demonstrates**: Firefox 认为 `:active` 发生在 `mousedown` 默认行为之后，规范未规定此场景，故表现差异属未定义行为，不是 Firefox 的 bug。

## Reference Tables
| 术语 | 含义 | 示例 |
|---|---|---|
| 属性 | 样式名称 | `height` |
| 值 | 数字/数值类内容 | `99px`、`50%` |
| 关键字 | 特定英文单词 | `transparent`、`inherit`（泛） |
| 功能符 | 函数形式值 | `rgba(0,0,0,.5)`、`calc()` |
| 声明 | 属性+属性值 | `color: transparent;` |
| 规则集 | 选择器+声明块 | `.vocabulary {...}` |

## Worked Example
判断"表现差异"的归因流程：① 查规范是否覆盖该场景 → 覆盖且违反 → 报 bug/写 hack；② 未覆盖（如上例 `:active`+`preventDefault`）→ 未定义行为 → 不要当 bug 修，按浏览器实际表现做特性检测或规避。作者以此纠正"把一切差异当浏览器 bug"的习惯。

## Anti-patterns
- **把 `%` 当长度单位**：它是值的一部分；概念混乱会让你读规范时理解错 `<length>` 相关语法。
- **一见浏览器差异就喊 bug**：规范空白处的差异（未定义行为）无从"修复"，只能理解与适配。
- **全局 reset 中给 ID 样式多处复用**：技术上会全部渲染（雨露均沾），但违背 ID 语义。

## Key Takeaways
1. `inherit` 是所有属性通用的泛关键字；`%` 是值不是单位——这两点是最常被混淆的术语细节。
2. 术语精确是读英文规范的前提：属性/属性值/声明/声明块/规则集各有所指。
3. 跨浏览器差异先归因：bug（违反规范）vs 未定义行为（规范空白），处理策略完全不同。

## Connects To
- **Ch 5**: 内联盒模型等概念建立在本章术语之上。
- **《CSS 新世界》**: `currentColor`、CSS 变量等新世界内容作者另行著书，不在本技能范围。
