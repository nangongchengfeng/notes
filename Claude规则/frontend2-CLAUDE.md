# 设计系统：极简现代风格
## 核心理念
- **清晰通过结构，个性通过大胆细节**
- **极简不是缺席，而是大胆选择**
- 宽敞留白 + 大胆渐变 + 微妙动画 + 双字体系统
## 设计令牌
### 颜色
| 令牌 | 值 | 用法 |
|:------|:------|:------|
| `background` | `#FAFAFA` | 主画布 |
| `foreground` | `#0F172A` | 主文本/反转背景 |
| `muted` | `#F1F5F9` | 次要表面 |
| `muted-foreground` | `#64748B` | 次要文本 |
| `accent` | `#0052FF` | 主强调色 |
| `accent-secondary` | `#4D7CFF` | 渐变终点 |
| `accent-foreground` | `#FFFFFF` | 强调色上的文本 |
| `border` | `#E2E8F0` | 边框 |
| `card` | `#FFFFFF` | 卡片背景 |
| `ring` | `#0052FF` | 焦点环 |
**标志性渐变：** `linear-gradient(to right, #0052FF, #4D7CFF)`
### 字体
- **展示：** `"Calistoga", Georgia, serif` — h1/h2
- **UI/正文：** `"Inter", system-ui, sans-serif` — 所有其他
- **等宽：** `"JetBrains Mono", monospace` — 标签/徽章
### 间距
- 区域：`py-28` → `py-44`
- 容器：`max-w-6xl mx-auto`
- 网格：`gap-5` → `gap-8`
### 阴影
- `shadow-accent`: `0 4px 14px rgba(0,82,255,0.25)`
- `shadow-accent-lg`: `0 8px 24px rgba(0,82,255,0.35)`
## 组件规范
### 按钮
```jsx
// 主按钮
<button className="h-12 rounded-xl bg-gradient-to-r from-[#0052FF] to-[#4D7CFF] px-6 text-white shadow-sm transition-all duration-200 hover:-translate-y-0.5 hover:shadow-accent-lg hover:brightness-110 active:scale-[0.98]">
  按钮文字
</button>
```

### 卡片

```JSX
// 标准卡片
<div className="rounded-xl border border-[#E2E8F0] bg-white p-6 shadow-md transition-all duration-300 hover:shadow-xl">
  内容
</div>
// 特色卡片（渐变边框）
<div className="rounded-xl bg-gradient-to-br from-[#0052FF] via-[#4D7CFF] to-[#0052FF] p-[2px]">
  <div className="h-full w-full rounded-[calc(12px-2px)] bg-white p-6">
    内容
  </div>
</div>
```

### 区域标签

```JSX
<div className="inline-flex items-center gap-3 rounded-full border border-[#0052FF]/30 bg-[#0052FF]/5 px-5 py-2">
  <span className="h-2 w-2 rounded-full bg-[#0052FF]" />
  <span className="font-mono text-xs uppercase tracking-[0.15em] text-[#0052FF]">
    区域名称
  </span>
</div>
```

### 渐变文字

```JSX
<span className="bg-gradient-to-r from-[#0052FF] to-[#4D7CFF] bg-clip-text text-transparent">
  强调文字
</span>
```

## 动画

- 标准过渡：`transition-all duration-200 ease-out`
- 悬停提升：`-translate-y-0.5`
- 脉冲点：`animate-pulse`

## 响应式

- Hero：单列（移动端）→ 1.1fr/0.9fr 网格（桌面端）
- 卡片：1 → 2 → 3 列
- 按钮：`w-full sm:w-auto`

## 可访问性

- 焦点：`ring-2 ring-[#0052FF] ring-offset-2`
- 触摸目标：≥44px
- 对比度：WCAG AA 达标











