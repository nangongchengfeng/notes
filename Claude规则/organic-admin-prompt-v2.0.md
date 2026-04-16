# 【有机形态后台生成规范】v2.0 通用版
## 🔴 核心强制规则（所有项目必须严格遵守，AI不得修改）
> 这是本设计体系的灵魂，任何情况下都不能突破
1. 视觉核心三要素：多值不对称圆角 + 曲线clip-path裁切 + 柔和渐变配色，禁止纯直角矩形、严格对称布局
2. 可读性优先：所有文字/表格/表单输入区域必须保持标准矩形，禁止形变破坏可读性
3. 动效规则：背景blob形变时长6-10s，缓慢呼吸感；hover仅允许轻微位移/缩放（位移≤5px，缩放≤1.05）；禁止弹跳/弹性动效
4. 无障碍要求：所有装饰性blob元素必须加 `aria-hidden="true"` 和 `pointer-events: none`
5. 代码规范：所有样式通过CSS变量管理，禁止硬编码色值/圆角/动效时长；使用Vue 3 `<script setup>` Composition API写法
---
## 🟢 项目自定义配置区（每个项目仅需修改这里，AI自动适配）
> 直接填写/选择对应配置即可，无需修改下方规则
### 1. 基础信息
项目名称：【填写你的项目名】
适用行业：□ 创意/设计 □ 健康/医疗 □ 科技/AI □ 品牌运营 □ 其他【填写】
布局模式：□ 侧边栏导航（默认） □ 顶部导航 □ 混合导航
### 2. 主题配置
预设主题：□ 创意粉紫（默认） □ 健康青绿 □ 科技蓝紫 □ 暖调沙棕 □ 自定义
自定义主色：【仅自定义主题填写，如#ff7e79】
### 3. 功能开关
✅ 启用blob背景形变动效
✅ 启用页面错开入场动效
✅ 启用暗黑模式支持
✅ 响应式适配（平板/移动端）
□ 极简模式（关闭所有装饰元素，仅保留核心功能）
### 4. 扩展规则（可选填写）
【这里可以填写项目特殊要求，比如是否需要权限管理、接入特定API、适配特定设备等】
---
## 🟡 视觉系统（固定规则，AI自动生成对应CSS变量）
### 颜色系统（根据配置区主题自动适配）
```css
:root {
 /* 背景层 */
 --bg-base: [根据主题自动生成];
 --bg-surface: [根据主题自动生成];
 --bg-elevated: #ffffff;

 /* 主色调 */
 --color-primary: [根据配置区主色自动生成];
 --color-primary-light: [同色系浅色];
 --color-primary-dark: [同色系深色];

 /* 辅助色 */
 --color-teal: #7ecec4;
 --color-rose: #e8a4b8;
 --color-sand: #d4c5a9;
 --color-sky: #a8cfe0;

 /* 状态色 */
 --color-success: #8ecba8;
 --color-warning: #e8cc8a;
 --color-error: #e8968c;
 --color-info: #8ab4e8;

 /* 文字 */
 --text-primary: #2d2640;
 --text-secondary: #6b5f80;
 --text-muted: #a398b8;

 /* 渐变预设 */
 --gradient-primary: [根据主色自动生成135度渐变];
 --gradient-teal: linear-gradient(135deg, #7ecec4 0%, #a8cfe0 100%);
 --gradient-surface: linear-gradient(160deg, #faf8fc 0%, #f0edf8 100%);

 /* 阴影（带主色调，柔和） */
 --shadow-sm: 0 2px 12px rgba(var(--color-primary-rgb), 0.08);
 --shadow-md: 0 6px 28px rgba(var(--color-primary-rgb), 0.13);
 --shadow-lg: 0 16px 48px rgba(var(--color-primary-rgb), 0.18);

 /* 圆角预设 */
 --radius-organic-sm: 18px 12px 16px 10px;
 --radius-organic-md: 32px 18px 28px 22px;
 --radius-organic-lg: 48px 28px 42px 32px;
 --radius-pill: 50px 28px 44px 30px;
 --radius-blob: 62% 38% 54% 46% / 48% 52% 48% 52%;

 /* 动效 */
 --duration-morph: 8s;
 --duration-hover: 0.4s;
 --duration-active: 0.2s;
 --duration-enter: 0.6s;
 --easing-organic: cubic-bezier(0.34, 0.8, 0.56, 1.02);
 --easing-smooth: cubic-bezier(0.4, 0, 0.2, 1);
}
```
### 排版系统（固定）
```css
--font-display: 'Sora', 'PingFang SC', sans-serif; /* 标题、数字 */
--font-body: 'DM Sans', 'PingFang SC', sans-serif; /* 正文、表格 */
--font-mono: 'JetBrains Mono', monospace; /* 代码 */
/* 字号 */
--text-xs: 11px; --text-sm:13px; --text-base:15px; --text-lg:18px; --text-xl:22px; --text-2xl:28px; --text-3xl:36px;
```
### 动效（固定）
```css
/* blob形变动画 */
@keyframes morphBlob {
 0% { border-radius: 62% 38% 54% 46% / 48% 52% 48% 52%; }
 25% { border-radius: 42% 58% 38% 62% / 55% 45% 58% 42%; }
 50% { border-radius: 54% 46% 66% 34% / 42% 58% 44% 56%; }
 75% { border-radius: 38% 62% 48% 52% / 60% 40% 52% 48%; }
 100% { border-radius: 58% 42% 40% 60% / 46% 54% 50% 50%; }
}
/* 页面入场动画 */
@keyframes fadeSlideUp {
 from { opacity: 0; transform: translateY(20px); }
 to { opacity: 1; transform: translateY(0); }
}
```
---
## 🟣 组件库（固定，AI优先复用已有组件，不得重复开发）
### 已封装公共组件，直接调用即可：
1. `<OrgCard>` - 有机卡片容器
2. `<OrgButton>` - 有机按钮（primary/secondary/danger三种类型）
3. `<OrgInput>` - 有机输入框
4. `<OrgTable>` - 数据表格容器
5. `<OrgBadge>` - 标签/状态徽章
6. `<Sidebar>` - 侧边栏（带右侧曲线裁切）
7. `<TopBar>` - 顶部通栏
8. `<GlobalBlobs>` - 全局背景装饰blob
### 组件样式规则（固定，AI不得修改）
> 已封装在组件内，直接使用即可，不需要重复写样式
- 卡片：多值不对称圆角，每张随机略有差异，带内部blob装饰，hover轻微上浮
- 按钮：有机pill圆角，渐变背景，hover轻微放大
- 侧边栏：右侧曲线clip-path裁切，深色渐变背景，底部blob装饰
- 表格：容器有机圆角，内部表格保持矩形，表头渐变背景
---
## 🟠 布局规范（固定，根据配置区布局模式自动适配）
### 标准布局结构
```
AppLayout
├── Sidebar（宽240px，可配置是否可收起）
├── MainArea
│ ├── TopBar（通栏，含搜索、通知、头像）
│ └── PageContent（padding:24px 28px，背景--bg-base）
│   ├── PageHeader（标题+操作按钮）
│   └── ContentGrid（卡片网格，KPI固定4列，普通卡片自适应）
```
### 间距规范（固定）
4px/8px/12px/16px/24px/28px/40px/56px 分级使用
---
## 🔵 代码生成规则（固定，AI必须遵守）
1. 优先使用已封装的公共组件，不要重复实现相同功能
2. 所有样式引用CSS变量，不得硬编码色值/圆角/动效参数
3. 页面卡片入场动效必须错开延迟，每两个卡片间隔0.1s
4. 装饰元素必须加无障碍属性，不影响屏幕阅读器
5. 自动适配响应式断点：平板KPI卡片2列，移动端KPI卡片1列，侧边栏可收起
6. 自动支持`prefers-reduced-motion`降级，用户关闭动效时停止所有blob动画
---
## 🟢 生成后自检清单（AI生成后必须逐条检查）
- [ ] 卡片使用了多值不对称圆角，没有纯直角矩形
- [ ] 至少有1个缓慢形变的blob装饰元素
- [ ] 所有颜色都来自CSS变量，无硬编码
- [ ] 阴影使用了带主色调的柔和阴影，无硬黑阴影
- [ ] 按钮为有机圆角，非正圆非直角
- [ ] 文字/表格区域保持矩形，未被形变破坏可读性
- [ ] 动效平稳，无弹跳弹性效果
- [ ] 标题/KPI数字使用了font-display字体
- [ ] 卡片入场动效有错开延迟
- [ ] 装饰元素加了`aria-hidden`和`pointer-events: none`

---
### 🚀 使用方法
1. **每次使用前仅需修改【自定义配置区】**：填写项目名、选择主题、勾选功能开关，1分钟即可完成配置
2. **直接告诉AI要生成的内容**：比如「生成数据概览页，包含4个KPI卡片+1个趋势图+1个数据表格」，AI会自动遵守所有规范生成代码
3. **多项目复用**：不同项目只需要修改配置区的内容，不需要修改后面的核心规则，所有项目风格保持统一
### ✨ 额外优化点
- 可以把这段提示词保存为`.coderules`文件放在项目根目录，Claude Code会自动读取，不需要每次粘贴
- 后续迭代只需要修改版本号和更新核心规则，所有项目都能复用最新规范
- 可以根据团队需求添加更多预设主题和组件，直接补充到对应模块即可
