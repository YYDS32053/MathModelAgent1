# MathModelAgent 项目分析报告

## 一、 项目概述

`MathModelAgent` 是一个基于前后端分离架构的自动化数学建模助手。它旨在通过一系列协同工作的智能体（Agents），将用户提供的原始问题和数据，自动化地转化为一份完整的数学建模报告。

- **前端**: Vue 3 + TypeScript，提供用户交互界面。
- **后端**: FastAPI (Python)，负责核心业务逻辑和任务调度。
- **核心技术**: 多 Agent 协作、通过 Redis 和 WebSocket 实现的实时通信。

## 二、 核心工作流程

项目的核心流程可以概括为：**接收 -> 调度 -> 执行 -> 推送**。下面是详细的步骤和流程图。

```mermaid
sequenceDiagram
    participant User as 用户
    participant Frontend as 前端 (Vue)
    participant Backend_API as 后端 API (/modeling)
    participant Backend_Worker as 后端工作流 (MathModelWorkFlow)
    participant Redis
    participant Backend_WS as 后端 WebSocket (/task)

    User->>+Frontend: 1. 上传文件/输入问题
    Frontend->>+Backend_API: 2. 提交任务 (HTTP POST)
    Backend_API->>Backend_API: 3. 创建 task_id 和工作目录
    Backend_API->>Redis: 4. 注册 task_id
    Backend_API->>Backend_Worker: 5. 启动后台任务 (add_task)
    Backend_API-->>-Frontend: 6. 返回 task_id
    Frontend->>User: 7. 跳转到任务页面 (/task/task_id)
    
    Frontend->>+Backend_WS: 8. 建立 WebSocket 连接
    Backend_WS->>Redis: 9. 订阅频道 (task:task_id:messages)
    
    loop Agent 协作流程
        Backend_Worker->>Redis: 10. 发布进度消息 (publish)
        Redis-->>Backend_WS: 11. 接收消息
        Backend_WS-->>-Frontend: 12. 推送消息给客户端
        Frontend->>User: 13. 实时展示进度
    end

    Backend_Worker->>Backend_Worker: 14. 任务完成
    Backend_Worker->>Redis: 15. 发布“任务完成”消息
    Redis-->>Backend_WS: 16. 接收消息
    Backend_WS-->>-Frontend: 17. 推送“任务完成”消息
    Frontend->>User: 18. 展示最终结果
```

### 流程详解

1.  **任务提交 (1-7)**
    *   用户在前端界面完成文件上传和问题描述，点击提交。
    *   前端将所有信息打包成一个 `multipart/form-data` 请求，发送到后端的 `/modeling` API。
    *   后端 API 接收到请求后，立即创建一个唯一的 `task_id` 和对应的工作目录，并将这个 `task_id` 注册到 Redis 中。
    *   它并**不直接执行**耗时的建模任务，而是将核心的 `MathModelWorkFlow` 作为一个后台任务启动。
    *   API 迅速向前端返回 `task_id`，前端页面跳转到展示进度的任务页。

2.  **实时通信建立 (8-9)**
    *   任务页加载后，前端会向后端的 `/task/{task_id}` 端点发起一个 WebSocket 连接。
    *   后端 WebSocket 服务在接受连接后，会立即订阅 Redis 中与该 `task_id` 对应的消息频道（`task:{task_id}:messages`）。

3.  **异步执行与实时推送 (10-18)**
    *   在后台运行的 `MathModelWorkFlow` 按预设的 Agent 协作流程（规划 -> 建模 -> 求解 -> 撰写）执行。
    *   在每个关键步骤（如“开始求解问题一”、“论文部分完成”），工作流都会将一条进度消息**发布**到 Redis 的任务频道。
    *   WebSocket 服务作为**订阅者**，一旦监听到频道中有新消息，就会立即通过 WebSocket 连接将消息推送给前端。
    *   前端接收到消息后，实时更新界面，向用户展示任务的最新进展。
    *   当工作流执行完毕，会发布一条最终的“任务完成”消息，前端随之展示完整的结果。

## 三、 多 Agent 协作机制

`MathModelWorkFlow` 是一个精心编排的流水线，由多个职责明确的 Agent 顺序协作完成：

1.  **CoordinatorAgent (规划师)**: 接收原始问题，将其拆解为多个独立的、可执行的子问题。
2.  **ModelerAgent (建模师)**: 为每个子问题设计具体的数学模型和求解思路。
3.  **CoderAgent (代码手)**: 根据建模师的思路，编写并执行代码（在沙盒环境中），产出计算结果和图表。
4.  **WriterAgent (写手)**: 结合 `CoderAgent` 的输出，撰写每个子问题的分析和结论，并最终整合成一篇完整的报告。

这种链式调用确保了任务能够被模块化、结构化地高效处理。

## 四、 总结

`MathModelAgent` 项目架构清晰，设计精良。其核心亮点在于：

*   **异步任务处理**: 通过 FastAPI 的 `BackgroundTasks` 将耗时任务与 Web 请求分离，保证了前端的快速响应。
*   **发布/订阅模式**: 利用 Redis 作为消息中间件，将业务逻辑（工作流）与通信逻辑（WebSocket）完全解耦，提高了系统的可维护性和扩展性。
*   **多 Agent 流水线**: 通过定义不同角色的 Agent 并编排其协作流程，实现了复杂任务的自动化拆解和执行。

该项目为构建复杂的、长耗时的、需要实时反馈的 AI 应用提供了一个优秀的架构范例。