# EXPERIENCE §1.1.1 · 对着白昼主题清剿深色表面 第 1 批（checkpoint 14）

cp13 的浅色主题"照出"了 10 类未 token 化的硬编码深色表面。本 checkpoint 按该清单清剿主力——
**输入/文本域/内容面板底 + nav/composer 玻璃面板**。

## 交付

- `styles.css` 新增主题感知 token `--field-bg`（暗色 `#1d1512cc`，白昼 `#FCFAF6`），用于输入框/文本域/
  内容面板底。
- 组件区映射（perl，仅 92 行起）：
  - 输入/文本域/skill 面板/plaza 搜索的 `#1d1512*` 家族 + `#170f09cc` → `var(--field-bg)`（18 处）。
  - nav/composer 玻璃面板 `#251d18dc`/`#241b17e8`/`#201a15f2` → `var(--glass-raised)`（已随主题翻转）。

## 验证（真实后端端到端，白昼 + 暗色两态）

- `npm test`：**115/115**；`npm run build`：OK。
- 启动后端登录 cp13probe：
  - **白昼**：`--field-bg` 解析为 `rgb(252,250,246)`=#FCFAF6（浅）、nav 为 `rgba(252,248,242,0.9)`=白昼玻璃；
    **残留深色表面 10 → 3**（仅剩：某 `button`、`.active`(sage 强调 pill，有意保留)、一个小 `<i>` 状态点）。
  - **暗色**：字段 `rgba(29,21,18,0.8)`=#1d1512cc（**与原值完全一致**）、nav `rgba(42,34,32,0.863)`=--glass-raised
    （较原 #251d18dc 微移 ~5 RGB、与其他玻璃面板统一，肉眼无感）——暗色渲染实质不变。
  - 截图工具动画页超时，用 computed-style 取证。

## 残留（cp15）

白昼下仍深的 3 类：具体某按钮的深色底、`.active` 选中 pill（sage 强调，需评估浅底对比而非直接改）、
小状态点 `<i>`。以及 `#ffffffXX` 白色 alpha 边框族（浅底上偏淡，应归到 `--hairline*` 使其翻为深色 alpha）。
下一步对着白昼主题继续清这几项 + 边框，之后截图两态存证。
