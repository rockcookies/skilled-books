# Chapter 4: 更细致的样式表现（opacity / border-radius / box-shadow / 2D 变换 / calc 系函数）

## Core Idea
视觉表现四件套各藏一个"系统级副作用"：opacity≠1 与 transform≠none 都会创建层叠上下文；border-radius 的圆角是椭圆曲线（有重叠收缩算法）；box-shadow 的第四个长度值（扩展半径）是被低估的图形利器。

## Frameworks Introduced
- **opacity 三个系统级事实**：
  1. 所有可过渡属性中**性能最高**——重活（阴影动画）都应转嫁为 opacity 过渡。
  2. ≠1 时**创建层叠上下文**——想让 DOM 靠前的元素覆盖靠后的，`opacity:.99` 一行搞定。
  3. 无继承性 → 父 .5 × 子 .5 = 视觉透明度 **0.25（乘积叠加）**；颜色混合公式 `r = fg×α + bg×(1−α)`。
- **边界特性（clamp 行为）**：opacity/-alpha/-lightness 等数值超范围按边界解析（opacity:-999→0，hsl 亮度 200%→100%）。配合 CSS 变量与 calc 实现 **if...else**：
  - 自动配色按钮：`color: hsl(0,0%,calc((var(--lightness) - 0.5) * -999999%))`——亮度>0.5 得巨大负数→L 钳到 0%（黑字），反之白字；text-shadow/border 同理按阈值显隐。
  - 静态饼图：`opacity: calc(99999 * (var(--percent) - 50))`——<50% 时结果为负钳到 0（隐藏），>50% 钳到 1（显示）。
- **border-radius 深水区**：
  - 每角是**水平半径+垂直半径**的椭圆；border-radius 缩写中斜杠分轴：`border-radius: 30px / 60px`（水平/垂直半径）。1~4 值方位=左上起顺时针。
  - **重叠收缩算法**：f = min(各方向边长/相邻半径之和)；f<1 时所有半径 ×f。推论：正方形中 `border-radius:100%` ≡ 150px；**长宽不等的矩形中两者不等**（f 分轴计算，100%→75px/50px，150px→50px）。
  - 内半径 = 外半径 − border-width（负则 0）；相邻边框厚度不同时圆角处平滑过渡（可做带尾巴尖角）；圆角外区域**不可点击**；无继承（父圆角裁子元素用 overflow:hidden）；table 需 border-collapse:separate。
  - 图形绘制：不规则圆角头像（`70% 30% 30% 70% / 60% 40% 60% 40%`）、蝉原则随机圆角（nth-child(2n+1)/(3n+2)... 质数轮换）、1/4 圆角标（`border-bottom-right-radius:100%`）。
- **box-shadow 四段式**：`offset-x offset-y blur spread color`（+可选 inset，顺序因 && 而随意）。
  - **inset 内阴影三用途**：① 模拟边框（`inset 1px 0 #ccc, inset -1px 0 #ccc, inset 0 1px #ccc, inset 0 -1px #ccc`——四条 1px，不影响尺寸，按钮统一高度）；② **颜色覆盖层**（阴影在文字下、背景上）：盖 Chrome 自动填充黄底 `inset 0 0 0 1000px #fff`、全站 `button:active` 按压加深 `inset 0 0 0 999px rgba(0,0,0,.1)`（对 img 等替换元素无效，改用 `outline`+负 outline-offset）；③ 内嵌层级感。
  - **第四值扩展半径 spread**：正=放大（蒙层神器 `box-shadow: 0 0 0 9999px rgba(0,0,0,.75)` 新手引导镂空，比 outline 少 bug 且支持圆角）；负=收缩（**单侧阴影** `0 7px 5px -5px`——blur 溢出的左右部分被裁掉）。
  - 多阴影累加：多边框（0 0 0 4px/8px/12px...）、伪渐变边框、旋转 loading（8 向阴影 + steps(8)）、云朵（克隆多个圆）、3D 按钮（1px 1px/2px 2px/3px 3px 叠加，active 时位移+减一层）。
  - **性能**：box-shadow 动画开销大 → 伪元素双阴影 + **opacity 过渡**切换（::before 常态阴影、::after hover 阴影 opacity 0→1），把样式重计算降为合成。
- **transform 2D 细节**：
  - 四函数：translate（百分比**相对自身**——唯一，居中弹框专用）、rotate（deg/grad/rad/turn，实战只用 deg）、scale（仅数值，负值翻转 scaleX(-1)）、skew（180° 一轮回，90/270° 时尺寸理论无限→实际 0，被低估的图形工具——斜切矩形拼箭头导航）。
  - **盒模型尺寸位置不变**：任何变换不推开兄弟元素（同 relative 无侵入）；间接影响：旋转触发滚动条出现→容器可用宽度 -17px。
  - **内联元素（非替换）无效**：需 inline-block 或改用 relative。
  - 顺序敏感：`translateX(40px) scale(.75)` 实际位移 40px，反序只有 30px（先缩小再位移）。
  - 与 clip-path 同用时**先剪裁再变换**（clip-path 作用在变换前图形上）。
  - 高性能动画三要素：**绝对定位 + opacity + transform**——移动动画用 transform 不用 margin。
  - transform≠none 创建层叠上下文：`scale(1)` 即可让 DOM 靠前图片覆盖靠后图片（hover 放大无需 z-index）。
- **calc() 家族**：
  - calc 四则约束：单位值只能加减不能乘除（`10px * 10px` 非法）；除法右侧必须非 0 数值；**+ − 两侧必须有空格**（浏览器无法区分正负号），× ÷ 不需要。
  - 现代爆发场景：CSS 变量的算术底座（`width: calc(1% * var(--percent))` 变量驱动进度条）。
  - **min()/max() 名不副实**：min() 取最小→实际是**限制最大值**（`width: min(1024px, 100%)` 替代 width+max-width 两行）；max() 反之限制最小值。
  - **clamp(MIN, VAL, MAX)** ≡ `max(MIN, min(VAL, MAX))`——流式字号标准写法：`font-size: clamp(16px, calc(16px + 2 * (100vw - 375px) / 39), 20px)`（旧浏览器用 16px 声明兜底）。
  - 三函数可与 calc 互相嵌套。

## Code Examples
```css
/* 阴影动画性能优化：opacity 代替 box-shadow 过渡 */
.optimize::before, .optimize::after { content:""; transition: opacity .6s; }
.optimize::before { box-shadow: 0 8px 12px rgba(0,0,0,.5); }
.optimize::after  { opacity: 0; box-shadow: 0 16px 24px rgba(0,0,0,.7); }
.optimize:hover::before { opacity: 0; }
.optimize:hover::after  { opacity: 1; }

/* 单侧阴影（负扩展半径） */
header { box-shadow: 0 7px 5px -5px rgba(0,0,0,.5); }

/* 新手引导镂空蒙层 */
.guide { border-radius: 50%; box-shadow: 0 0 0 9999px rgba(0,0,0,.75); }

/* 流式根字号（clamp） */
html { font-size: 16px;
       font-size: clamp(16px, calc(16px + 2 * (100vw - 375px) / 39), 20px); }

/* 边界特性 = CSS if/else（自动配色） */
.button { color: hsl(0, 0%, calc((var(--lightness) - 0.5) * -999999%)); }
```

## Reference Tables
| transform 事实 | 后果/用途 |
|---|---|
| 盒模型不变 | 无侵入动画；但推不动兄弟 |
| 内联元素无效 | 先 inline-block |
| 顺序敏感 | 先 translate 后 scale |
| clip-path 先于 transform | 剪裁变换前图形 |
| ≠none 创建层叠上下文 | scale(1) 抢覆盖权 |

| calc 系 | 语义 | 记忆 |
|---|---|---|
| min(a,b) | 取小 | 限最大值 |
| max(a,b) | 取大 | 限最小值 |
| clamp(MIN,VAL,MAX) | 区间 | = max(MIN, min(VAL,MAX)) |

## Anti-patterns
- **`calc(100%-2rem)` 没空格**：不合法，浏览器分不清正负号。
- **box-shadow 直接做 hover 过渡**：样式重计算+GPU 开销大；换伪元素+opacity。
- **对 img 用 inset 阴影做按压反馈**：替换元素内容在阴影之上，无效；用 outline + outline-offset:-50px。
- **IE 上用扩展半径模拟圆角轮廓**：四角阴影重叠 bug；圆角轮廓用 box-shadow 四向 1px 或直接 spread 大色块场景。
- **用 margin 做移动动画**：触发布局；transform 才是高性能三要素。

## Key Takeaways
1. opacity/transform 的层叠上下文副作用是免费的 z-index 管理工具。
2. 圆角 = 椭圆曲线 + 重叠收缩算法；`border-radius:50%` 与固定像素在非正方形上不等价。
3. box-shadow spread 负值 = 单侧阴影，大正值 = 蒙层；inset = 不占尺寸的边框与覆盖层。
4. 动画性能铁律：transition/animation 只碰 transform、opacity（+必要时 clip-path）。
5. min()/max()/clamp() 名字与作用相反/组合，clamp 是响应式数值的终极写法。

## Connects To
- **Ch 5**: 3D 变换、transition/animation 建立在本章 transform/opacity 基础上。
- **Ch 7**: clamp+vw 是移动端适配最佳实践的内核。
- **Ch 8**: calc × CSS 变量 = 新世界的样式编程模型；边界特性是变量"逻辑判断"的基石。
