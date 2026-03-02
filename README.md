# Claude Code Sandbox

Claude Code 的 Dev Container 沙箱環境，提供網路隔離防火牆，限制 AI agent 只能存取必要的外部服務。

## 使用方式

1. 把 `.devcontainer/` 資料夾複製到你的專案根目錄
2. 設定 `.devcontainer/.env`（見下方）
3. 在 VS Code 中打開專案，選擇 **Reopen in Container**

## 檔案說明

| 檔案 | 用途 |
|------|------|
| `.devcontainer/devcontainer.json` | Container 設定（extensions、mount、環境變數等） |
| `.devcontainer/Dockerfile` | Image 定義（Node 20、zsh、git delta、Claude Code CLI） |
| `.devcontainer/init-firewall.sh` | iptables 防火牆腳本，限制對外網路到白名單 |
| `.devcontainer/.env` | GitHub token（不進 git） |

## 設定 `.devcontainer/.env`

此檔案不會進 git（已在 `.gitignore` 中排除），你需要手動建立：

```bash
echo "GITHUB_TOKEN=$(gh auth token)" > .devcontainer/.env
```

Token 用於呼叫 GitHub API 取得 IP ranges（避免未認證的 60 次/小時 rate limit）。不需要任何 scope，純粹提升 rate limit 到 5000 次/小時。

## 防火牆白名單

Container 啟動後，只允許連線到以下服務，其餘一律拒絕：

| 服務 | 用途 |
|------|------|
| GitHub (web/api/git) | git 操作、API 存取 |
| registry.npmjs.org | npm 套件安裝 |
| api.anthropic.com | Claude AI API |
| sentry.io | 錯誤回報 |
| statsig.anthropic.com / statsig.com | Feature flags |
| VS Code Marketplace 相關域名 | Extension 安裝與更新 |

## 需求

- Docker Desktop
- VS Code + Dev Containers extension
- `gh` CLI 已登入（用於產生 token）
