# Chapter 8: CSS 变量函数 var() 与自定义属性

## Core Idea
CSS 变量 = 变量函数 var() + CSS 自定义属性的统称——最具颠覆性的新特性：换肤、JS/CSS 职责重划（JS 只改一个变量）、自定义语法扩展的地基。

## Frameworks Introduced
- **声明与使用**：`--name: value;`（自定义属性）+ `var(--name[, fallback])`（使用）。声明即普通属性：继承、级联、优先级全套适用。
- **命名宽松度**：支持数字开头（`--1`）、短横线（`---`）、空格（`-- `）、中文（`--深蓝`）；特殊字符（`$ [ ] ^ ( ) % "`）需反斜杠转义（`--\$`）。与选择器/动画名（不能数字开头）规则不同。
- **var() fallback 的精确语义**（本章最重要考点）：后备值**只在第一个参数"一定无效"时生效**。"一定无效"三种情况：未定义、值是空格前无内容的空值（`--x:;`）、值为全局关键字（inherit/initial/unset/revert）。
  - **"可能有效就用第一参，哪怕最终渲染非法"**：`--color: 20px; background-color: var(--color, deeppink)` → 背景是 **transparent（初始值）** 而非 deeppink——var() 解析成功（20px 语法上"可能有效"），渲染时 20px 对 background-color 非法 → 按 unset 规则回落初始值/继承值，**不走 fallback**。
  - **空格开关技巧**：`--open: ;`（值为一个空格，"可能有效"）+ `border: 1px solid var(--open, rgba(0,0,0,.1))` → 默认渲染为 currentColor（非法值回落）；`:active { --open: inherit }`（一定无效）→ 四个属性同时切换到各自的 fallback 值。**一个变量同时开关多个属性**，替代传统"每属性写两遍"。
  - **尾随特性**：`var(--size)px`（--size:20）= `20 px`（带空格）非法 → font-size 回落继承值，**不是 20px**。数值转长度必须 `calc(var(--size) * 1px)` 或直接存 `20px`。
- **作用域 = 继承**：自定义属性只能被**自身与后代**使用——`.box{--color:red}` 对父级 body 里的 var(--color) 不可见（反向可见）。全局变量放 `:root`。**Shadow DOM 唯一穿透通道**：外部普通 CSS 无法进 Shadow DOM（::part 兼容差），但自定义属性可继承进去——Web 组件样式定制的标准接口（组件内部写 `border: var(--ui-button-border, 1px solid #ccc)`，外部设 `ui-button { --ui-button-border: ... }` 即可换肤）。

## Key Concepts
- JS 交互：`element.style.setProperty('--x', v)` 设置、`getComputedStyle(el).getPropertyValue('--x')` 获取；HTML 上 `style="--percent: 40"` 内联声明是"数据驱动样式"的常用入口。
- content 显示变量：`counter-reset: progress var(--percent); content: counter(progress) "%"`——变量值进 content 的经典桥（需整数）。
- 自定义语法模拟（8.4 概览）：利用变量让 CSS"发明"新写法——如用 `--tick: ;` 空格开关模拟 attr() 类特性、变量组合出组件变体语法。

## Code Examples
```css
/* 主题变量 + 组件穿透 Shadow DOM */
:root { --primary: #2a80eb; --radius: 4px; }
ui-button { --ui-button-background: var(--primary); }

/* 一个变量切换多属性（空格 = "可能有效"的关闭态） */
button {
   --open: ;
   border: 1px solid var(--open, rgba(0,0,0,.1));
   box-shadow: var(--open, inset 0 1px 2px rgba(0,0,0,.1));
   background: var(--open, linear-gradient(#0003, transparent));
}
button:active { --open: inherit; }  /* 一定无效 → 全部启用 fallback */

/* 数值变量 → 长度 */
.bar::before {
   width: calc(1% * var(--percent));   /* 数据驱动宽度 */
   counter-reset: progress var(--percent);
   content: counter(progress) "%";
}
```

## Reference Tables
| var(--x, fb) 中 --x 的值 | var() 解析 | 最终渲染 |
|---|---|---|
| 未定义 | 用 fallback | fallback |
| 正常值 | 用 --x | 合法则正常；非法则回落初始/继承值 |
| 空格（`--x: ;`） | 用 --x（可能有效） | 多数属性回落初始值 |
| 全局关键字 | **用 fallback**（一定无效） | fallback |

## Anti-patterns
- **指望 fallback 兜非法值**：`var(--color, red)` 中 --color:20px 时得到 transparent 不是 red——fallback 只救"一定无效"。
- **`var(--n)px` 拼单位**：产生 "20 px" 非法值静默回落；用 calc 乘 1px。
- **在子元素定义变量指望父级用**：作用域只向下继承。
- **把 --变量 当 Sass 变量**：它是运行时继承属性，可被 JS/媒体查询/伪类动态改写——这正是它的价值。

## Key Takeaways
1. fallback 三触发条件：未定义、空值、全局关键字；"可能有效"的垃圾值不走 fallback。
2. 空格值 + 全局关键字切换 = 单变量多属性开关（新世界组件变体的惯用法）。
3. :root 放全局变量；Shadow DOM 样式定制只能靠自定义属性穿透。
4. 变量 + calc = 样式编程；变量 + counter = 变量进 content。

## Connects To
- **Ch 4**: 边界特性 + calc + 变量 = if/else（自动配色、变量饼图）。
- **Ch 7**: prefers-color-scheme 改变量实现零 JS 深色模式。
- **Ch 15**: @property 给变量注册类型，让渐变/长度变量可过渡。
