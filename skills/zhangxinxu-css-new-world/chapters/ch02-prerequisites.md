# Chapter 2: 需要提前了解的知识（数据类型 / 值定义语法 / 全局关键字 / 渐进增强）

## Core Idea
学会看 `<尖括号数据类型>` 和属性值定义语法，就能举一反三地掌握任何 CSS 新属性；四个全局关键字（inherit/initial/unset/revert）+ 一套渐进增强技巧是新世界的通行证。

## Frameworks Introduced
- **CSS 数据类型思维**（最重要的学习方法论）：属性值 = 数据类型的组合（尖括号标记）。`<image>` = url() / 渐变 / element() / image() / image-set() / cross-fade() / paint()——凡支持 `<image>` 的属性（background-image、mask-image、shape-outside…）自动获得全部成员，学一次通用所有属性，新成员（如 paint()）自动同步。
  - 常见类型：`<box>`（content/padding/border-box）、`<basic-shape>`（inset()/circle()/ellipse()/polygon()/path()）、`<color>`（rgb/rgba/hsl/hsla/hex/named/currentColor/系统色）。
- **CSS 属性值定义语法**（重要性堪比 JS 正则）：
  - 字面符号：`,`（并列分隔）、`/`（缩写分隔——**斜杠前后数据类型必须相同或部分相同**，如 background 里 `/` 只能隔 position 和 size：`background: #eee url(..) no-repeat 0 0 / contain` 合法，`no-repeat / contain` 非法）。
  - 组合符（优先级从低到高）：空格（并列且按序）→ `&&`（都要出现、顺序随意）→ `||`（至少一个、顺序随意）→ `|`（互斥，恰好一个）→ `[]`（分组）。
  - 数量符号：无符号=恰好一次；`*` 任意次；`+` 一或多；`?` 零或一；`{A,B}`；`#` 一或多且逗号分隔（可加范围如 `<number>#{3}`）；`!` 分组必须产生值。
  - 用语法发现细节：linear-gradient 的 `to bottom,` 是可省的（语法里带 `?`）；rgba() 支持斜杠新语法 `rgba(255 0 0 / .5)`。
- **全局关键字四件套**（含实用性/兼容性评级）：
  - `inherit`（A+/IE8 起，文档说 IE9 是错的）：重置输入框字体、height:inherit、background-image:inherit。
  - `initial`（B/移动端放心用）：还原为**规范初始值**——注意误区：`ol{padding:initial; list-style-type:initial}` 得到的是 `padding:0; list-style-type:disc`，不是浏览器默认样式！
  - `unset`（B−）：继承属性→inherit，非继承属性→initial；**只在 all 上用才有意义**（`dialog{all:unset}` 一键变 span）。
  - `revert`（B/Chrome84+、Firefox67+）：还原为**浏览器内置样式**——`ol{padding:revert; list-style-type:revert}` 才能找回序号。
- **all 属性**：`all: initial|inherit|unset|revert`，重置除 unicode-bidi、direction、CSS 自定义属性外的一切。direction 不受影响是历史设计失误的补偿（初始值应为 auto 而非 ltr，否则 all:initial 会毁掉阿拉伯文页面）；有实用价值的是 `all:unset`（元素变 span）与 `all:revert`（如 iOS 下 progress 一键还原系统样式）。
- **渐进增强五技巧**（旧 CSS Hack 已过时，别再用 `\0`/`\9`/`*property`）：
  1. **直接使用**——纯体验升级类（border-radius/box-shadow/filter/scroll-behavior）不兼容就保持原样："好的浏览器有更好的显示，糟糕的浏览器就只有普通的显示"。
  2. **属性值语法差异**——新版语法覆盖旧版声明（IE9 不识 linear-gradient() → GIF/PNG 分流；`font-size:.1px` + `-webkit-calc(1px - 1px)` 保留空格特性绕过 Chrome 12px 限制；`#00000000` 八位色值排除 IE/Edge）。
  3. **伪类/伪元素区分**——规则集中有无法识别的伪类/伪元素则整条忽略：`_:valid, .x{}`（IE10+）、`_::-ms-backdrop, .x{}`（IE11+）、`_:in-range, .x{}`（Edge13+）、`_::-moz-progress-bar, .x{}`（仅 Firefox）、`:-webkit-any(_), .x{}`（仅 webkit）。风险：浏览器规则可能变，勿当金科玉律。
  4. **@supports 规则**——最正统；Edge12+（IE 全系不支持）；复杂条件靠**括号嵌套递归**：`@supports (display:flex) and (not (display:grid)) {}`；花括号内可嵌任意 @ 规则；JS 侧配 `CSS.supports(prop, value)`。
  5. **CSS 之外的 JS 兜底**——`if (!CSS.supports('position','sticky')) { 传统 JS 方案 }`。

## Code Examples
```css
/* 属性值语法差异分流（IE9 GIF / IE10+ PNG+动画） */
.icon-loading {
   background: url(./loading.gif);
   background: url(./loading.png), linear-gradient(transparent, transparent);
   animation: spin 1s linear infinite;
}

/* unset vs revert 的分水岭 */
dialog { all: unset; }                        /* 变成 span */
ol { padding: revert; list-style-type: revert; } /* 找回浏览器默认序号 */

/* @supports 嵌套递归写法 */
@supports (display: flex) and (not (display: grid)) { /* Edge12~15 匹配 */ }
```

## Reference Tables
| 全局关键字 | 作用 | 评级 | 陷阱 |
|---|---|---|---|
| inherit | 取父级计算值 | A+ | — |
| initial | 规范初始值 | B | ≠浏览器默认样式（UA 样式） |
| unset | 继承?inherit:initial | B− | 单属性场景没意义 |
| revert | 浏览器内置样式 | B | Chrome84+/Firefox67+ |

## Anti-patterns
- **继续用 CSS Hack（`\0`/`\9`/`*` 前缀）**：现代问题是"支持与否"而非渲染 bug，用语法差异/@supports。
- **把 initial 当"浏览器默认"还原 list-style/padding**：那是 revert 的活。
- **对 `<li>` 做样式重置**：所有浏览器 li 默认无 margin/padding、list-style 继承自父级——重置纯属浪费且妨碍序号定制。
- **@supports 复杂条件直写 `and not`**：必须括号嵌套 `(not (...))`。

## Key Takeaways
1. 记数据类型不记属性值——`<image>` 的成员变化自动惠及所有相关属性。
2. 斜杠规则一句话记住所有含 `/` 缩写：斜杠前后类型必须相同/部分相同。
3. unset 配 all、revert 找默认、initial 是规范初始值——三者场景严格区分。
4. 渐进增强优先级：直接用 > 语法差异 > 伪类区分 > @supports > JS 兜底。

## Connects To
- **Ch 5**: linear-gradient 语法细节在本章已用语法视角拆解。
- **Ch 7**: @media 与 @supports 同属条件规则，可嵌套组合。
- **《CSS世界》Ch 2**: 本章的术语/未定义行为思想在新世界延续为渐进增强方法论。
