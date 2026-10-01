# Chapter 9: 文本字符处理能力的升级

## Core Idea
文字装饰（text-shadow/stroke/emphasis）、文字方向（text-orientation/text-combine-upright）、字体变体（font-variant 家族）与可变字体（font-variation-settings）构成新世界排版深水区——多数特性需要专门设计的字体才有意义。

## Frameworks Introduced
- **文字装饰四件套**：
  - `text-shadow`（IE9+）：与 box-shadow 两点差异——**不支持 inset**（只有外阴影）、**不支持扩展半径**（最多 3 个数值）。可多值累加：3D 长投影（`1px 1px, 2px 2px, ... 8px 8px`）、描边型发光（四向偏移同色）。可在 ::first-line/::first-letter 中生效。
  - `-webkit-text-stroke`（width+color 缩写）：文字描边；与 border 不同——宽度默认值是 0（border-width 默认 medium），且描边居中于字形边缘（内侵文字）。全兼容描边用 SVG stroke（见 ch14）。
  - `-webkit-text-fill-color`：文字填充色，优先级高于 color——**改变文字颜色同时保护 color**（color 的继承与"默认色"地位保留：光标色、边框色、盒阴影默认色仍跟 color 走）。
  - `text-emphasis`（强调着重号）：color+style 缩写；style 三类 `none | [filled|open] || [dot|circle|double-circle|triangle|sesame] | <string>`；强调符字号=主文字一半，行高不足会自动增高，间距由字体决定；`text-emphasis-position: [over|under] && [right|left]`（默认 over right；**中文习惯 under right**——下加着重号）。
- **文字方向**：`text-orientation: mixed | upright | sideways`（垂直排版下英文数字是否直立；继承性）；`text-combine-upright: none | all | digits <integer>?`——**2~4 个字符横排合并**（竖排里的"78"占一个字位），水平排版同样有效；unicode-bidi 新增 isolate 系属性值（配合 direction 处理双向文本隔离）。
- **尺寸与渲染**：`ch` 单位 = 字符 0 的宽度（等宽字体下精确对齐刻度）；`tab-size: <integer>|<length>`（代码缩进制表宽度——tab 党的翻身工具，配合 pre 渲染）；`text-size-adjust`（移动端自动放大文字的开关）；`text-rendering`（optimizeLegibility 等实用性弱，浏览器默认已最优）；`font-smooth`（无浏览器支持，仅 -webkit- 私有）。
- **字体变体体系**（需字体内含对应 OpenType 特征才生效）：
  - `font-stretch`（胖瘦，IE9+）：9 档关键字 ultra-condensed~ultra-expanded 或百分比；需要字体有窄/宽字体面。
  - `font-synthesis`（合成控制）：默认 `weight style`——字体缺粗体/斜体时浏览器**伪造**；`font-synthesis: none` 禁止合成（中文体验增强：避免伪粗体的糊）。
  - `font-variant` 家族（高级排版正规军）：caps（small-caps 等）、numeric（`tabular-nums` 等宽数字/`slashed-zero` 斜杠零/分数）、alternates（styleset()/swash() 等，Chrome 不支持）、ligatures（连字开关）、east-asian（CJK 字形）。缩写=五者之并。
  - `font-feature-settings`：低阶接口 `"smcp" on, "swsh" 2`（OpenType 特征标签直通，IE10+）；与 font-variant 的关系=高级属性优先，低阶兜底（两者同时用时 font-variant 覆盖同特征）。
  - `font-kerning: auto|normal|none`（字距微调，即 "kern" 特征；中文场景价值有限）。
- **可变字体（Variable Fonts）**：一个文件包含多轴（wght 字重/wdth 宽度/ital 斜体/slnt 倾斜/opsz 光学尺寸+自定义轴），解决传统多字重文件巨大（思源黑体 7 档 55.7MB）与档位粗糙问题。
  - 控制接口 `font-variation-settings: 'wght' 375, 'GRAD' 66`（轴名字符串+数值，可精调任意档位）；高级属性（font-weight/font-stretch/font-style/font-optical-sizing）能映射到标准轴——优先用高级属性，font-variation-settings 兜自定义轴。
  - `font-optical-sizing: auto|none`（是否随字号自动调 opsz 轴）。

## Code Examples
```css
/* 中文着重号（下加着重号） */
em { text-emphasis: dot deepskyblue; text-emphasis-position: under right; }

/* 3D 长投影文字 */
.shadow3d { text-shadow: 1px 1px, 2px 2px, 3px 3px, 4px 4px, 5px 5px; }

/* 等宽数字（表格/账单） */
.price { font-variant-numeric: tabular-nums; }

/* 可变字体精调字重 */
h1 { font-family: 'Source Han VF'; font-variation-settings: 'wght' 375; }

/* 代码块 tab 宽度 */
pre { tab-size: 4; }
```

## Reference Tables
| 需求 | 属性 | 注意 |
|---|---|---|
| 文字描边 | -webkit-text-stroke | 居中描边会侵蚀字形；全兼容用 SVG stroke |
| 换色不破坏 color 体系 | -webkit-text-fill-color | 优先级高于 color |
| 竖排数字占一位 | text-combine-upright: all | 2~4 字符合并 |
| 禁止伪粗体 | font-synthesis: none | 中文小字号体验增强 |
| 任意字重 | font-variation-settings 'wght' | 需可变字体文件 |

## Anti-patterns
- **以为 font-variant-* 不生效是浏览器问题**：绝大多数需要字体本身包含对应 OpenType 特征（中文字体尤其缺）。
- **text-shadow 想要内阴影/扩展**：不支持，那是 box-shadow 的能力。
- **对没有多轴的普通字体写 font-variation-settings**：无效；先确认是可变字体。

## Key Takeaways
1. 文字装饰分两派：text-shadow/stroke/emphasis 直接可用，font-variant/feature 系依赖字体。
2. text-fill-color 的价值是"保护 color"，不是替代 color。
3. 可变字体 = 一个文件多个设计轴；高级属性优先，settings 精调。
4. 中文场景最实用的三个：text-emphasis（under right）、font-synthesis:none、tab-size。

## Connects To
- **《CSS世界》Ch 8**: font-family/@font-face 基础在此升级（配合 ch03 字体新特性）。
- **Ch 14**: SVG stroke/text-anchor 是全兼容文字描边与对齐的正解。
- **Ch 15**: Houdini Font Metrics API 补齐文字度量能力。
