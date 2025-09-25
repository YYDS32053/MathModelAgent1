# shutdown.md
# 关闭开发环境 (Shutdown Workflow)

你将关闭 MathModelAgent 项目所有正在运行的服务。

1.  首先，关闭正在运行的**前端服务**。它通常运行在 `5173` 端口。使用 `execute_command` 找到对应的进程ID (PID) 并终止它。
    * **步骤 A: 查找 PID**
        ```powershell
        netstat -ano | findstr :5173
        ```
    * **步骤 B: 终止进程** (将 `[PID]` 替换为上一步找到的ID)
        ```powershell
        taskkill /PID [PID] /F
        ```

2.  接下来，用同样的方法关闭正在运行的**后端服务**。它运行在 `8000` 端口。
    * **步骤 A: 查找 PID**
        ```powershell
        netstat -ano | findstr :8000
        ```
    * **步骤 B: 终止进程** (将 `[PID]` 替换为上一步找到的ID)
        ```powershell
        taskkill /PID [PID] /F
        ```

3.  所有服务已关闭。