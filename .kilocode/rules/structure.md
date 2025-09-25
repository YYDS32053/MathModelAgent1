---
description: 
globs: 
alwaysApply: false
---
# 项目结构、核心功能与用户使用流程

## 一、项目整体架构

本项目采用前后端分离架构：
- **frontend/**：前端，基于 Vue3 + TypeScript + Vite，主要负责用户界面与交互。
- **backend/**：后端，基于 FastAPI，负责核心建模逻辑、任务调度与 WebSocket 通信。
- **docker-compose.yml**：支持前后端及依赖服务的容器化部署。

### 主要目录说明
- `frontend/src/pages/`：核心页面，包括 chat（主入口）、task（任务详情）、test（测试页）。
- `frontend/src/components/`：通用组件，如 UserStepper（用户操作流程）、AppSidebar（侧边栏）等。
- `backend/app/routers/`：API 路由，`modeling.py` 负责建模任务，`ws.py` 负责 WebSocket 实时通信。
- `backend/app/core/`：核心业务逻辑，如 MathModelWorkFlow（建模主流程）。
- `backend/app/models/`、`schemas/`、`utils/`：数据模型、请求响应结构、工具函数等。

## 二、核心功能
1. **数学建模任务自动化**：用户上传数据集、输入题目，系统自动完成问题拆解、代码生成、论文撰写等。
2. **多 Agent 协作**：后端通过 CoderAgent、WriterAgent 等智能体协作完成建模与写作。
3. **WebSocket 实时推送**：任务进度、结果通过 WebSocket 实时推送到前端。
4. **多格式输出**：支持 Markdown、LaTeX 等多种论文输出格式。
5. **文件上传与管理**：支持多种数据文件上传，自动归档到任务工作目录。

## 三、用户使用流程
1. **进入主页面（chat）**：
   - 展示"数模竞赛助手"介绍。
   - 通过 UserStepper 组件引导用户分步操作。
2. **上传数据文件**：
   - 支持拖拽或点击上传（.txt/.csv/.xlsx 等）。
   - 上传成功后进入下一步。
3. **输入题目与参数选择**：
   - 粘贴完整题目。
   - 选择建模模板（如国赛/美赛）、语言、输出格式。
4. **提交建模任务**：
   - 前端调用 `/modeling/` API，携带表单与文件。
   - 后端生成 task_id，异步处理建模任务。
5. **任务进度与结果查看**：
   - 自动跳转到 `/task/{task_id}` 页面。
   - 前端通过 WebSocket 订阅任务进度与结果。
   - 可在 CoderAgent/WriterAgent Tab 查看代码与论文内容。
6. **下载与后续操作**：
   - 支持结果文件下载、格式转换等后续操作（如有实现）。

---

如需详细 API 或组件说明，请参考 [frontend-rules.mdc](mdc:frontend-rules.mdc) 与 [backend-rules.mdc](mdc:backend-rules.mdc)。
