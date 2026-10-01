# Chapter 15: Houdini 是 CSS 新的未来

## Core Idea
Houdini = 把 CSS 引擎的各个环节开放给 JavaScript：用 paint() 自绘图像、用 @property 注册可过渡的变量、用 Layout API 自定义布局——现实可用的两个入口是 Paint API 与 Properties & Values API。

## Frameworks Introduced
- **CSS Paint API**（paint() 工作帧）：把 `paint(name)` 作为图像使用（属 `<image>` 类型——background-image/mask-image/ border-image 通吃，呼应 ch02 数据类型思维）。JS 侧 `registerPaint('name', class)`，类的 `paint(context, size, properties)` 方法≈Canvas 绘制（context 是绘制上下文、size 是元素尺寸、properties 可读 CSS 变量）。**CSS 变量让 paint 如虎添翼**：改一个变量触发重绘换样式（`background: paint(circle); --circle-color: red`）。
- **CSS Properties & Values API（@property）**——**当下最实用**：给自定义属性注册类型与继承行为：
  ```css
  @property --start-color {
     syntax: '<color>';
     inherits: false;
     initial-value: deepskyblue;
  }
  ```
  或 JS `CSS.registerProperty({name, syntax, inherits, initialValue})`。
  **核心价值**：注册了 `<color>`/`<length>`/`<angle>` 等类型的变量**可以参与 transition/animation**——补上"渐变不能过渡"的历史缺憾（渐变两端色注册后 hover 平滑变色）、角度旋转变量动画。syntax 合法值含 `<length>/<percentage>/<color>/<angle>/<number>/<integer>/<image>/+`（列表）`|`（或）等。
- **CSS Parser API**：把 CSS 文本解析为规则树（CSS.parse），尚在演进。
- **CSS Layout API**：`layout(name)` 自定义布局——子元素布局逻辑用 JS 的 `layout(children, edges, constraints, styleMap, breakToken)` 实现，CSS 里 `display: layout(name)` 调用；可实现"文本居中同时一侧对齐"等原生布局做不到的排版。
- **CSS Typed OM**：CSSOM 的类型化版本——`el.attributeStyleMap.get('width')` 返回带单位数值对象（CSSUnitValue），替代字符串解析，计算样式读写性能与安全兼得。
- **Animation Worklet**：在合成器线程跑的 JS 动画（`animation-worklet`），滚动驱动动画/高帧率动画不阻塞主线程。
- **Font Metrics API**：暴露文字度量（width/height/emHeightAscent/emHeightDescent/fontBoundingBox* 等），精确文字排版（如按基线对齐自定义组件）。
- **冷静判断**（作者总结）：① 安全考虑厂商不会开放太多底层；② 所有模块离不开 JS、学习与使用成本高——短期落地主力就是 Paint API 与 @property。

## Code Examples
```css
/* @property：让渐变颜色可过渡（历史性补完） */
@property --start-color {
   syntax: '<color>';
   inherits: false;
   initial-value: deepskyblue;
}
.button {
   background: linear-gradient(var(--start-color), deeppink);
   transition: --start-color .3s;
}
.button:hover { --start-color: deeppink; }

/* Paint API 作为图像 */
.fancy {
   background-image: paint(ripple);
   --ripple-color: deepskyblue;   /* JS 里 properties.get() 读取 */
}
```
```js
// Paint API 注册（配套 worklet 脚本）
registerPaint('ripple', class {
   paint(ctx, size, properties) {
      const color = properties.get('--ripple-color');
      // ctx 同 Canvas 2D context；size 是元素 content-box 尺寸
   }
});
// CSS.registerProperty({...}) 是 @property 的 JS 形式
```

## Reference Tables
| Houdini 模块 | 作用 | 成熟度 |
|---|---|---|
| Paint API | JS 自绘图像 paint() | 可用（Chrome 系） |
| Properties & Values | 注册类型化变量→可过渡 | **最实用，渐进可用** |
| Layout API | display: layout() 自定义布局 | 实验性 |
| Parser/Typed OM/Worklet/Font Metrics | 解析/类型化 CSSOM/合成器动画/字体度量 | 演进中 |

## Anti-patterns
- **以为 @property 只是"写法好看"**：不注册 syntax 的变量是"任意 token 串"，永远不可过渡——类型注册是过渡的前提。
- **@property 漏写 initial-value**（syntax 非 "*" 时必填）：注册无效。
- **把 Houdini 当当下生产力**：除 Paint/@property 外多数模块浏览器支持有限，先渐进增强。

## Key Takeaways
1. paint() 是 `<image>` 家族新成员——background/mask/border-image 通吃。
2. @property 三要素 syntax/inherits/initial-value；注册后变量获得类型、可动画——渐变过渡、角度动画的官方解。
3. Houdini 的哲学：CSS 引擎的每个环节（绘制/布局/解析/动画）都可被 JS 扩展——但当下只押注前两个。
4. 与 ch08 呼应：CSS 变量是 Houdini 的数据通道，@property 给它装上类型系统。

## Connects To
- **Ch 5**: 渐变不可过渡 → @property 注册 color 后可行。
- **Ch 8**: 变量的"自定义语法模拟"升级为真正的类型注册。
- **《CSS世界》**: paint() 补完 `<image>` 数据类型的版图（ch02）。
