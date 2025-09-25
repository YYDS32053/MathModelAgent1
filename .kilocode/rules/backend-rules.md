---
description: 
globs: 
alwaysApply: false
---
# 后端开发规则

## 目录结构
- 主要代码位于 `backend/app/`，包含 `main.py`（入口）、`routers/`（API 路由）、`models/`（数据模型）、`schemas/`（请求/响应结构）、`core/`（核心业务）、`utils/`（工具）、`config/`（配置）。
- 配置文件如 `pyproject.toml`、`setting.py`，以及 Docker 支持。

## 技术栈
- 基于 FastAPI 框架，异步支持。
- Redis 用于任务状态与消息推送。
- 日志采用 loguru。
- 依赖 Pydantic 进行数据校验。

## 运行环境
- Python 版本：3.12（`pyproject.toml` 和 `Dockerfile` 均指定）
- FastAPI 版本：`fastapi[standard]>=0.115.8`，全项目基于 FastAPI 异步框架开发，广泛使用 async/await。

## 主要流程
- 入口 `main.py` 初始化 FastAPI 应用，加载 CORS、静态文件、API 路由。
- 路由分为 `/modeling/`（建模任务）、`/task/{task_id}`（WebSocket 实时推送）等。
- 建模任务通过 `modeling.py` 路由，支持文件上传、参数表单，后台异步处理，任务状态通过 Redis 推送。
- WebSocket 路由 `ws.py` 支持任务进度实时推送，结合 Redis 发布订阅。

## 关键文件说明
- `core/agents.py`：智能体（Agent）核心实现，包含 ModelerAgent、CoderAgent、WriterAgent。
- `core/workflow.py`：建模任务主流程，自动分步调用智能体。
- `utils/redis_manager.py`：Redis 任务与消息管理。
- `utils/ws_manager.py`：WebSocket 连接管理。
- `config/setting.py`：环境变量与全局配置。
- `models/`、`schemas/`：Pydantic 数据结构定义。

## 任务与消息机制
- 每个建模任务分配唯一 task_id，相关状态与消息通过 Redis 管理。
- WebSocket 订阅 `task:{task_id}:messages` 频道，实时推送任务进度与结果。

## 约定
- 新增 API 路由请放在 `routers/`，并在 `main.py` 注册。
- 新增核心业务逻辑请放在 `core/`，工具函数放在 `utils/`。
- 配置项统一通过 `config/setting.py` 管理。
