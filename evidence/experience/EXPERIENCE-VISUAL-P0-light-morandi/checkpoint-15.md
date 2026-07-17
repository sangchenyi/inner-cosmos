# EXPERIENCE §1.1.1/§1.1.6 · 白昼主题：边框 + accent-soft 调色板修正（checkpoint 15）

承接 cp14。本 checkpoint 清掉浅色主题下的白色 alpha 边框，并修正一个 cp13 引入的真实调色板缺陷。

## 交付

1. **`#ffffffXX` 白色 alpha 边框/叠加 → hairline token**（96 处，按 alpha 分 3 桶：≤0x0f→--hairline，
   0x10-0x1f→--hairline-mid，≥0x20→--hairline-strong）。这些 token 在白昼翻为**深色 alpha**，边框/分隔
   在浅底可见；暗色下由冷白变暖白、肉眼无感。
2. **修正 cp13 的日间 accent-soft 调色板**：cp13 误把日间 `-soft` 设成中调（如 sage-soft #6E8270），
   但 `-soft` 是用作**浅底 pill/tint**（配深字）的，日间应为**淡色**。改为淡色：
   sage-soft `#D3E0D5` / sky-soft `#CFD9E4` / plum-soft `#E2D4DE` / aurora-soft `#E8D3B4`。
3. **新增 `--ink-on-accent`（#241b12，两态皆深，不随主题翻转）**：用于浅 accent pill 上的文字。
   把 cp14 误改成 `var(--surface-canvas)`（日间会变浅字→浅底不可见）的 5 处 active-pill 文字改用它。

## 验证（真实后端端到端，干净重载后）

- `npm test` **115/115**；`npm run build` OK。
- 白昼实测（getComputedStyle + WCAG 对比）：
  - `.app-shell-nav` 边框：白 → `rgba(46,38,32,0.12)`=--hairline 深 alpha，**浅底可见** ✓。
  - `.space-tabs button.active`：**深墨 `#241b12` on 淡沙绿 `#d3e0d5`，对比 12.41:1**（远超 AA 4.5）✓。
  - `--accent-sage-soft` 日间解析 `#d3e0d5`（淡）✓。
  - 主面板深色残留：cp13 的 10 类经 cp14+cp15 降至 **3 类边角元素**（某 button、某 active 变体、一个小 `<i>` 点）。
  - （静态资源缓存导致中途多次读到旧值；干净重载后确认为新值——过程见提交说明，非代码问题。）
- 暗色不变：active pill 仍深墨 on 亮沙绿 #a9bcae（--ink-on-accent 两态皆深，符合原意）；边框暖白 alpha。

## 残留（cp16）

白昼下仍深的 3 类边角元素（具体某 button 深底、某 `.active` 变体、小状态点 `<i>`），以及各组件
`.active` 的 accent-alpha 着色（`#8d748226` 等低 alpha tint，不破坏主题但可统一）。清完后两态整屏截图存证。
