# hostship

可以连续  点一次有10天 自动化10天即可

## 自动化

GitHub Actions 每 10 天自动登录面板执行一次续期（`30 13 */10 * *`，即每月 1/11/21 日 UTC 13:30 = 北京时间 21:30）。
也可在 Actions → **Host-Ship Auto Renew** → **Run workflow** 手动触发。

需要配置的 Secrets（Settings → Secrets and variables → Actions）：

| Secret | 说明 |
|--------|------|
| `HOSTSHIP_EMAIL` | 面板登录邮箱 |
| `HOSTSHIP_PASSWORD` | 面板登录密码 |
| `TG_BOT_TOKEN` | Telegram Bot Token（可选，用于通知） |
| `TG_CHAT_ID` | Telegram Chat ID（可选，用于通知） |

## 运行记录自动清理

每次运行结束时会执行 `cleanup-runs` job：调用 GitHub API 把本工作流的**历史运行记录全部删除，只保留最新一份**（当前这次），Actions 页面不再堆积历史。

- 只删除状态为 `completed` 的记录，正在进行中的运行不受影响
- 清理失败不影响续期结果（该 job 标记为 `continue-on-error`）
- 默认额外保留 `0` 条；如需再留几条历史，添加 Variable `KEEP_RUNS`（如 `1` 表示保留最近 1 条已完成记录 + 当前运行）
- 依赖 `permissions: actions: write`（已写入 workflow）。若仓库把 `GITHUB_TOKEN` 设为只读，需到 Settings → Actions → General → Workflow permissions 改为 **Read and write**

## 过程截图

每次运行会把 `*.png` 截图作为 artifact 上传（名称带 run id，保留 7 天），便于排查登录 / 续期异常。
