---
description: 
globs: 
alwaysApply: false
---
# 前端开发规范与项目结构说明


## 技术栈
- 框架：Vue 3 + TypeScript
- 构建工具：Vite
- 状态管理：Pinia
- 路由：vue-router
- UI 组件库：（如 lucide-vue-next,shadcn-vue ）
- 样式：Tailwind CSS
- 网络请求：Axios，统一封装于 `src/utils/request.ts`

## 前端用户使用流程
1. **登录**：用户访问系统后，首先进入登录页（/login），输入账号信息完成登录。
2. **上传数据**：登录后进入主页面（如 /chat），点击"开始新任务"或相关入口，进入任务流程。
3. **上传数据集**：在任务流程第一步，用户可拖拽或选择本地数据文件（支持 .txt、.csv、.xlsx 等格式）上传。
4. **输入题目**：上传数据后，进入第二步，用户需粘贴完整的竞赛题目描述。
5. **选择参数**：用户可选择建模模板（如"国赛"/"美赛"）、输出格式（Markdown/LaTeX）、语言（中文/英文）等参数。
6. **提交任务**：填写完毕后点击"提交"，系统将自动分配任务编号并跳转到任务详情页。
7. **查看结果**：用户可在任务详情页查看建模进度、AI 生成的代码、分析报告、论文等内容。
8. **历史任务**：侧边栏可访问历史任务，随时回溯和查看过往建模结果。


## 目录结构
- `frontend/`
  - `src/`
    - `main.ts`：应用入口，挂载 App 并注册路由、Pinia。
    - `App.vue`：根组件，渲染 `<router-view />`。
    - `assets/`：静态资源，如图片、全局样式。
    - `components/`：通用组件，含业务组件和 UI 组件（`ui/` 子目录）。
    - `pages/`：页面级组件，按路由分目录组织（如 `chat/`、`login/`、`task/` 等）。
    - `router/`：路由配置，`index.ts` 定义所有页面路由。
    - `apis/`：API 封装，按业务拆分（如 `commonApi.ts`、`submitModelingApi.ts`）。
    - `utils/`：工具函数（如 `request.ts` 封装 axios，`lib/utils.ts` 提供 cn 等工具）。
    - `lib/`：通用逻辑/工具库。
  - `public/`：静态资源目录。
  - `index.html`：主 HTML 文件。
  - `package.json`：依赖与脚本。
  - `vite.config.ts`：Vite 配置。
  - `tailwind.config.js`：Tailwind 配置。

## 组件开发
- vue3 使用组合式 api
- 通用 UI 组件位于 `src/components/ui/`，如 Button、Sidebar、Alert、Select 等，支持按需导入。
- 组件推荐使用 `<script setup lang="ts">` 语法糖，支持类型推断。
- 组件 props、emit、slot 需写明类型。
- 复杂交互建议拆分为多个小组件。

## 页面开发
- 页面组件位于 `src/pages/`，每个路由一个目录，入口为 `index.vue`。
- 路由统一在 `src/router/index.ts` 配置。
- 页面可组合业务组件和 UI 组件。
- 推荐页面只做数据流转和布局，具体业务逻辑下沉到组件。

## API 调用
- 所有接口请求统一通过 `src/utils/request.ts` 封装的 axios 实例发起。
- 每个业务模块可在 `src/apis/` 下新建对应的 API 文件，导出函数供页面/组件调用。
- 支持请求/响应拦截，可在拦截器中统一处理 token、错误提示等。

## 样式体系
- 主题色、圆角等通过 CSS 变量和 Tailwind 配置扩展。

## 工具函数
- `src/lib/utils.ts` 提供常用工具，如 `cn`（合并 class）、`valueUpdater` 等。
- 业务相关工具可按需添加到 `utils/` 或 `lib/`。

## 其他
- TypeScript 路径别名 `@` 指向 `src/`，详见 `tsconfig.json`。
- 推荐使用 pnpm 作为包管理器。
- 代码风格建议遵循官方 Vue 3 + TypeScript 最佳实践。

如需详细组件/页面开发示例，可参考 `src/pages/chat/index.vue`、`src/components/NotebookArea.vue`、`src/components/ui/button/Button.vue` 等文件。
