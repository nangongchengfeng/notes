# 技术栈：

Vue 3.4+、Vite 5.4+、TypeScript 5.5+、Vue Router 4.4+、Pinia 2.2+、Element Plus 2.8+、UnoCSS 0.63+、SCSS 1.77+、Axios 1.7+、ECharts 5.5+。



# 构建与测试

- 安装依赖：`npm install`
- 开发模式启动：`npm run dev`
- 生产环境构建：`npm run build`
- 预览生产构建产物：`npm run preview`
- 类型检查：`npm run typecheck`
- ESLint 代码检查（含自动修复可修复项）：`npm run lint`
- Stylelint 样式检查（含自动修复可修复项）：`npm run lint:style`
- 格式化所有可格式化文件：`npm run format`

------

# 代码规范

## 通用规范

- 使用 ES modules（`import/export`），不使用 CommonJS
- 函数参数 **≥4 个时** 使用对象参数，避免参数顺序混淆
- 错误处理使用项目封装的 `AppError`/`ApiError` 类（`@src/lib/errors.ts`）
- 命名约定：
  - API 路径：**kebab-case**（如 `/api/user-management/roles`）
  - 代码变量/属性/方法：**camelCase**
  - 常量：**UPPER_SNAKE_CASE**（如 `DEFAULT_PAGE_SIZE = 10`）
  - 组件文件/文件夹：**PascalCase**（如 `UserTable.vue`、`src/components/Layouts/`）
  - 工具/类型/常量文件：**camelCase**（如 `dateUtils.ts`、`apiTypes.ts`）

## Vue 3 专属规范

- 优先使用 `<script setup lang="ts">`，不使用 Options API（特殊兼容场景除外，需注释说明）
- 组件 Props 使用 `defineProps<{ ... }>()` 或 `withDefaults(defineProps<{ ... }>(), { ... })` 类型化
- 组件 Emits 使用 `defineEmits<{ (e: 'update:modelValue', val: T): void; ... }>()` 类型化
- 模板内尽量使用 UnoCSS 原子类复用基础样式，仅复杂/特殊复用率低的样式用 `<style scoped lang="scss">`
- 组件拆分遵循 **单一职责原则**，优先复用 Element Plus 组件库，避免重复造轮子

## TypeScript 专属规范

- 严格启用 `tsconfig.json` 中的 `strict: true`、`noImplicitAny: true`、`noUnusedLocals: true`、`noUnusedParameters: true`
- 尽量避免使用 `any`，复杂类型先考虑 `unknown` 配合类型守卫，或用 `Partial`/`Pick`/`Omit`/`Record` 等工具类型
- API 请求/响应的数据类型统一放在 `@src/api/types/` 下
- 全局共享类型放在 `@src/types/` 下，使用 `declare global` 时需谨慎（仅扩展 window/Vue 等必要对象）

------

# 架构

## 目录结构（建议）

```js
[项目名]/
├── public/                 # 静态资源（直接复制到构建根目录）
├── src/
│   ├── api/                # API 封装
│   │   ├── index.ts        # Axios 实例配置
│   │   ├── types/          # API 请求/响应类型
│   │   └── modules/        # 按业务模块划分的 API 接口（如 user.ts、order.ts）
│   ├── assets/             # 需要 Vite 处理的静态资源（如 scss 变量、图片）
│   │   └── styles/         # 全局样式
│   │       ├── variables.scss
│   │       ├── mixins.scss
│   │       └── reset.scss
│   ├── components/         # 全局通用组件
│   │   ├── Layouts/        # 布局组件（如 Header.vue、Sidebar.vue）
│   │   └── Business/       # 通用业务组件（如 SearchForm.vue、TableWrapper.vue）
│   ├── lib/                # 工具库封装（非组件）
│   │   ├── axios.ts        # Axios 二次封装（如 errorHandler.ts、requestInterceptor.ts）
│   │   ├── errors.ts       # 自定义错误类（AppError、ApiError）
│   │   ├── storage.ts      # localStorage/sessionStorage 封装
│   │   └── echarts.ts      # ECharts 初始化/销毁/主题配置封装
│   ├── router/             # Vue Router 配置
│   │   ├── index.ts        # 路由初始化/全局守卫
│   │   └── modules/        # 按业务模块划分的路由（如 admin.ts、user.ts）
│   ├── stores/             # Pinia 状态管理
│   │   ├── index.ts        # Pinia 初始化
│   │   └── modules/        # 按业务/全局功能划分的 store（如 app.ts、user.ts）
│   ├── views/              # 页面组件（按路由嵌套结构划分）
│   ├── types/              # 全局共享类型
│   ├── App.vue             # 根组件
│   └── main.ts             # 入口文件
├── .env.development        # 开发环境变量
├── .env.production         # 生产环境变量
├── .eslintrc.cjs           # ESLint 配置
├── .stylelintrc.cjs        # Stylelint 配置
├── .prettierrc             # Prettier 配置
├── tsconfig.json           # TypeScript 配置
├── vite.config.ts          # Vite 配置
└── package.json
```

## 核心技术使用规范

### 状态管理（Pinia）

- 必须使用 `defineStore` 定义 store，支持 `options` 或 `setup` 写法，**优先 setup 写法**（更符合 Vue 3 Composition API 风格）
- 全局共享状态（如用户信息、主题、侧边栏折叠状态）放在 `stores/modules/app.ts`/`user.ts` 等，页面级状态尽量放在组件内，**仅当跨多组件/路由共享时才使用 Pinia**
- store 持久化使用 `pinia-plugin-persistedstate`（需提前安装配置），配置持久化时指定需要持久化的 state 字段，避免敏感信息持久化到 localStorage

### UI 组件库（Element Plus）

- 统一使用 Element Plus 组件，如需自定义主题，在 `vite.config.ts` 中配置 Element Plus 的 SCSS 变量，或在 `src/assets/styles/variables.scss` 中覆盖
- 国际化统一配置在 `src/main.ts` 中，默认使用中文（`zhCn`）
- 表单组件必须结合 `el-form`/`el-form-item` 的 `rules` 属性做类型化的表单校验

### CSS 工具库（UnoCSS）

- 优先使用 UnoCSS 的原子类，基础类参考 Tailwind CSS v3
- 项目已配置 `presetUno`、`presetAttributify`、`presetIcons`（Iconify 图标）、`presetTypography`（可选），可直接使用
- 如需自定义 UnoCSS 预设/规则，在 `vite.config.ts` 的 `unoCSS()` 插件中配置

### 请求库（Axios）

- 必须使用 `src/api/index.ts` 中封装的 Axios 实例，不直接使用原生 `axios`
- 请求/响应拦截器统一处理：
  - 请求拦截：统一添加 `Authorization`、`Content-Type` 等请求头
  - 响应拦截：统一处理响应状态码、API 业务错误码（如 401 跳转登录、403 权限提示）、统一返回 `data` 字段
- API 接口按业务模块划分在 `src/api/modules/` 下，函数名尽量语义化（如 `getUserList`、`createOrder`）

### 数据可视化（ECharts 5）

- 必须使用 `src/lib/echarts.ts` 中封装的 `useECharts` Composable，处理初始化、响应式窗口大小变化、主题、销毁等逻辑
- 图表配置项尽量语义化拆分，复杂图表可单独创建 `views/[业务模块]/charts/[图表名].config.ts` 存放配置

------

# 工作流

## 开发前

1. 确认 Node.js 版本（建议 ≥20，可使用 `nvm` 管理）
2. 克隆代码后，先运行 `npm install` 安装依赖
3. 复制 `.env.example`（需提前创建）为 `.env.development`，根据开发环境配置变量

## 开发中

- **IMPORTANT**: 修改代码后，**必须先运行 `npm run typecheck && npm run lint && npm run lint:style`**，确保无类型错误和样式错误后再提交

- **IMPORTANT**: 提交代码前，**先本地测试功能是否正常**

- **NEVER**: 不要修改 `.env.production` 等生产敏感环境变量文件

- **NEVER**: 不要提交 `.env.local`、`node_modules/`、`dist/` 等本地/临时文件（已配置 `.gitignore`）

- 提交信息严格遵循

   

  Conventional Commits

   

  规范：

  ```
  <type>(<scope>): <subject>
  // 空行
  <body>（可选，详细描述）
  // 空行
  <footer>（可选，关联 issue、BREAKING CHANGE）
  ```

  常用

  ```
  type
  ```

  - `feat`: 新增功能
  - `fix`: 修复 bug
  - `refactor`: 重构代码（不改变功能）
  - `style`: 格式调整（不改变代码逻辑）
  - `docs`: 文档更新
  - `test`: 测试相关
  - `chore`: 构建/依赖/工具相关

## 开发后

- 生产环境构建前，建议先运行 `npm run build:debug`（需在 `package.json` 中添加，如 `vite build --mode production --debug`）检查构建问题
- 生产构建产物会放在 `dist/` 下，可通过 `npm run preview` 预览

------

# 压缩指令

When compacting, preserve:

- 修改过的文件完整列表
- 测试命令和结果
- 未完成的任务
- 关键技术使用规范的核心点





