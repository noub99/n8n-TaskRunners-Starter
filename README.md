# n8n Task Runners Starter

> n8n + 外部 Python Task Runner + PostgreSQL — 支援 GitHub Codespaces、Docker 與 Zeabur 一鍵部署。

> n8n with external Python Task Runner + PostgreSQL — ready to deploy on GitHub Codespaces, Docker, or Zeabur.

![n8n](https://img.shields.io/badge/n8n-2.2.3-EA4B71?style=flat-square&logo=n8n)
![Python](https://img.shields.io/badge/Python-Task_Runner-3776AB?style=flat-square&logo=python)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?style=flat-square&logo=postgresql)

---

## 為什麼需要這個專案 | Why This Exists

從 n8n v2 開始，Code 節點不再於瀏覽器中執行 JavaScript/Python，而是需要外部的 **Task Runner** 服務。預設的 Task Runner 不支援自定義套件，每次新增依賴都需要重建 Docker 映像檔。

本專案解決了這個問題 — 只需編輯 `task-runner/requirements.txt` 並重新部署即可。

---

Starting from n8n v2, Code nodes no longer run in-browser — they require an external **Task Runner** service. The default runner doesn't support custom packages, meaning you'd need to rebuild a Docker image every time you add a dependency.

This starter solves that by letting you simply edit `task-runner/requirements.txt` and redeploy.

---

## 包含內容 | What's Included

| 服務 / Service | 說明 / Description |
|----------------|-------------------|
| **n8n** | 工作流程自動化 / Workflow automation (v2.2.3) |
| **PostgreSQL 15** | n8n 的持久化資料庫 / Persistent database for n8n |
| **Python Task Runner** | 預裝套件的外部執行器 / External runner with pre-installed packages |

### 預裝 Python 套件 | Pre-installed Python Packages

| 類別 / Category | 套件 / Packages |
|-----------------|----------------|
| 資料處理 / Data | `pandas`, `numpy`, `scipy`, `scikit-learn`, `xgboost` |
| 視覺化 / Visualization | `matplotlib`, `seaborn` |
| AI / LLM | `openai`, `langchain`, `crewai`, `instructor` |
| 文件處理 / Documents | `openpyxl`, `python-pptx`, `markitdown`, `mammoth`, `pdfminer` |
| 網路 / Web | `requests`, `aiohttp`, `beautifulsoup4` |
| Jupyter | 完整 Jupyter + JupyterLab 環境 / Full Jupyter + JupyterLab stack |

> 完整清單 / Full list: [`task-runner/requirements.txt`](./task-runner/requirements.txt)

---

## 快速開始 | Quick Start

### 選項 A — GitHub Codespaces（推薦）| Option A — GitHub Codespaces (Recommended)

無需安裝任何軟體，直接在瀏覽器中執行。  
No installation required. Runs entirely in your browser.

1. **Fork** 本專案到你的 GitHub 帳號 / **Fork** this repo to your GitHub account
2. 點擊 **Code → Codespaces → Create codespace on main** / Click **Code → Codespaces → Create codespace on main**
3. 等待約 2–3 分鐘建置環境 / Wait ~2–3 minutes for the environment to build
4. 出現提示時點擊 **Open in Browser**，或至 **Ports** 分頁開啟 port `5678` / When prompted, click **Open in Browser** — or go to the **Ports** tab and open port `5678`
5. 首次進入需建立 n8n 帳號 / Create your n8n account on first launch

> **資料保存 / Data persistence：** 只要不刪除 Codespace，資料即會保留。GitHub 預設會自動刪除閒置超過 30 天的 Codespace，建議定期將重要流程匯出為 JSON 備份。  
> Your data is preserved as long as you don't delete the Codespace. GitHub may auto-delete Codespaces inactive for 30+ days — export important workflows as JSON backups.

---

### 選項 B — Docker（本機 / VPS）| Option B — Docker (Local / VPS)

**需求 / Requirements：** 已安裝 Docker 與 Docker Compose。

```bash
# 1. 下載你的 Fork（替換 YOUR_USERNAME）/ Clone your fork (replace YOUR_USERNAME)
git clone https://github.com/YOUR_USERNAME/n8n-TaskRunners-Starter.git
cd n8n-TaskRunners-Starter

# 2. 設定環境變數 / Set up environment variables
cp .env.example .env
# 編輯 .env 填入密碼與金鑰 / Edit .env and fill in your passwords and keys

# 3. 啟動所有服務 / Start all services
docker compose up -d

# 4. 開啟瀏覽器 / Open browser: http://localhost:5678
```

產生安全的隨機金鑰 / Generate secure values for your `.env`:
```bash
openssl rand -hex 32   # 用於 N8N_ENCRYPTION_KEY 與 TASK_RUNNER_AUTH_TOKEN
                       # use for N8N_ENCRYPTION_KEY and TASK_RUNNER_AUTH_TOKEN
```

---

### 選項 C — Zeabur（PaaS）| Option C — Zeabur (PaaS)

> 注意：Zeabur 免費版有資源限制，若遇到記憶體不足（OOM），建議改用 Codespaces。  
> Note: Zeabur free tier has resource limits. If you hit OOM errors, use Codespaces instead.

1. Fork 本專案 / Fork this repo
2. 在終端機執行 / Run in your terminal:
   ```bash
   npx zeabur template create -f zeabur.yaml
   ```
3. 點擊產生的模板連結即可部署 / Click the generated template link to deploy

---

## 新增自定義 Python 套件 | Adding Custom Python Packages

1. 開啟你 Fork 中的 `task-runner/requirements.txt` / Open `task-runner/requirements.txt` in your forked repo
2. 新增套件（每行一個）/ Add your packages (one per line):
   ```
   pandas
   numpy
   requests
   my-new-package   # ← 在此新增 / add here
   ```
3. 儲存並 Commit / Save and commit

套用變更 / Apply the changes:

| 部署方式 / Deployment | 指令 / Command |
|----------------------|---------------|
| **Docker / Codespaces** | `docker compose build --no-cache task-runners && docker compose up -d task-runners` |
| **Zeabur** | Push 變更 → 進入 `n8n-python-runner` 服務 → 點擊 **Redeploy** / Push changes → go to `n8n-python-runner` service → click **Redeploy** |

---

## 在 n8n Code 節點使用 Python | Using Python in n8n Code Nodes

1. 新增 **Code** 節點到你的工作流程 / Add a **Code** node to your workflow
2. 將語言設定為 **Python** / Set the language to **Python**
3. 範例 / Example:

```python
import json
import pandas as pd

input_data = _query
if isinstance(input_data, str):
    input_data = json.loads(input_data)

df = pd.DataFrame(input_data)

return {
    "row_count": len(df),
    "columns": list(df.columns),
    "summary": df.describe().to_dict()
}
```

---

## 環境變數 | Environment Variables

| 變數 / Variable | 說明 / Description | 預設值 / Default |
|-----------------|-------------------|-----------------|
| `POSTGRES_USER` | PostgreSQL 使用者名稱 / username | `postgres` |
| `POSTGRES_PASSWORD` | PostgreSQL 密碼 / password | *(必填 / required)* |
| `POSTGRES_DB` | 資料庫名稱 / Database name | `n8n` |
| `N8N_ENCRYPTION_KEY` | 憑證加密金鑰 / Encryption key for credentials | *(必填 / required)* |
| `N8N_HOST` | n8n 主機名稱 / Hostname | `localhost` |
| `WEBHOOK_URL` | Webhook 公開網址 / Public URL for webhooks | `http://localhost:5678` |
| `TIMEZONE` | 時區 / Timezone | `Asia/Taipei` |
| `TASK_RUNNER_AUTH_TOKEN` | Task Runner 驗證金鑰 / Auth token | *(必填 / required)* |

---

## 架構說明 | Architecture

```
┌─────────────────────────────────┐
│        Docker Network           │
│                                 │
│  ┌─────────┐    ┌───────────┐  │
│  │  n8n    │◄──►│ PostgreSQL│  │
│  │ :5678   │    │    :5432  │  │
│  └────┬────┘    └───────────┘  │
│       │                        │
│  ┌────▼──────────────────────┐ │
│  │  Python Task Runner       │ │
│  │  (pandas, langchain, ...) │ │
│  └───────────────────────────┘ │
└─────────────────────────────────┘
```

---

## 授權 | License

MIT
