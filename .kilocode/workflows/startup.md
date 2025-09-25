# startup.md
# 启动开发环境 (Startup Workflow)

你将按步骤启动 MathModelAgent 项目的本地开发环境。请严格遵循以下指令。

1.  首先，检查并确保核心依赖 `Redis` 服务正在运行。使用 `execute_command` 执行以下命令：
    ```bash
    wsl -- sudo service redis-server status
    ```
    如果返回结果中不包含 `is running`，则执行以下命令来启动它：
    ```bash
    wsl -- sudo service redis-server start
    ```

2.  接下来，在一个独立的会话中启动**后端服务**。使用 `execute_command` 执行完整的启动指令链：
    ```powershell
    cd E:\MathModelAgent1\backend && .\.venv\Scripts\activate && set ENV=DEV ; uvicorn app.main:app --host 0.0.0.0 --port 8000
    ```
    *监控此命令的输出，直到出现 `Uvicorn running on http://0.0.0.0:8000` 字样，再继续下一步。*

3.  最后，在**另一个独立会话**中启动**前端服务**。使用 `execute_command` 执行：
    ```powershell
    cd E:\MathModelAgent1\frontend && pnpm run dev
    ```
    *此服务启动成功后，整个开发环境即准备就绪。*
