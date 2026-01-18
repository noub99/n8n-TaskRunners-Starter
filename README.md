# n8n Task Runners Starter

[English](README_EN.md) | [繁體中文](README.md)

這個專案提供了一個預先配置好的 n8n 環境，包含 PostgreSQL 資料庫與專用的 Python Task Runner。
特別針對 **教學** 與 **進階資料處理** 需求設計，讓您能輕鬆在 n8n 中執行 Python 腳本並安裝自定義套件（如 pandas, numpy, scikit-learn）。

---

## ⚡ 快速開始 (Quick Start)

### 選項 A：GitHub Codespaces (推薦教學使用 🔥)
最適合教學與測試，完全免費，無需任何安裝步驟。

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/richblack/n8n-TaskRunners-Starter)

1. 點擊上方按鈕，登入 GitHub 並建立 Codespace。
2. 等待環境建置完成（約 2-3 分鐘），系統會自動啟動服務。
3. 當右下角出現 "Open in Browser" 提示時，點擊即可開啟 n8n。
   - 若未出現提示，請至下方 **"PORTS"** 分頁，點擊 **Port 5678** 旁的地球圖示。
   - **注意**：首次進入需設定 n8n 帳號，資料庫初始化可能需要幾秒鐘。

> **關於資料保存**：
> - 只要 **不刪除** Codespace，即使關閉視窗或暫停，資料庫與設定都會保留。下次啟動即可繼續使用。
> - 若 GitHub 自動刪除閒置過久的 Codespace（預設 30 天未登入），資料將會遺失。建議重要流程定期匯出 JSON 備份。

### 選項 B：一般伺服器 / 本機 (Docker)
適用於 Linode, DigitalOcean, AWS EC2 或您的本機電腦。

**系統需求**：安裝好 Docker 與 Docker Compose。

```bash
# 1. 下載專案
git clone https://github.com/richblack/n8n-TaskRunners-Starter.git
cd n8n-TaskRunners-Starter

# 2. 設定環境變數
cp .env.example .env
# (可選) 編輯 .env 修改密碼

# 3. 啟動服務
docker compose up -d

# 4. 開啟瀏覽器
# 前往 http://您的IP:5678 (本機為 http://localhost:5678)
```

---

## 📦 如何新增 Python 套件

本專案已支援 `requirements.txt` 安裝方式，方便學員自定義。

1. 打開 `task-runner/requirements.txt` 檔案。
2. 加入您需要的 Python 套件名稱（一行一個）：
   ```text
   pandas
   numpy
   scikit-learn  <-- 新增這行
   requests
   ```
3. 重建 Task Runner 服務以套用變更：
   
   **在 Codespaces / 本機：**
   ```bash
   docker compose build --no-cache task-runners
   docker compose up -d task-runners
   ```

   **在 Zeabur：**
   - 進入 `n8n-python-runner` 服務，點擊 **Redeploy**，Zeabur 會自動讀取新的 `requirements.txt` 並重建。

---

## ☁️ 部署至 Zeabur (PaaS)

<a href="https://zeabur.com/templates/56Y03Z?referralCode=richblack"><img src="https://zeabur.com/button.svg" alt="Deploy on Zeabur"/></a>

Zeabur 提供了方便的雲端部署，但需注意資源消耗。n8n + PostgreSQL + Python Runner 至少需要一定的記憶體資源。

**注意事項**：
- **資源限制**：免費版 Zeabur 可能因記憶體不足導致服務無法啟動 (OOM Killed) 或啟動極慢。建議付費方案或確保足夠 credit。
- **啟動時間**：首次部署因需下載映像檔與安裝 Python 套件，可能需 3-5 分鐘。請耐心等待所有服務燈號轉綠。
- **IP 資源**：偶爾可能遇到 `FailedCreatePodSandBox` (IP 不足) 錯誤，通常稍等幾分鐘後重試部署即可恢復。

**手動設定 (Troubleshooting)**：
如果使用一鍵部署模板遇到問題，我們提供的 `zeabur.yaml` 包含了完整架構設定。您可以：
1. 確認 `zeabur.yaml` 中的 `repo` ID 是否正確指向您的 Fork。
2. 使用 Zeabur CLI 部署：`npx zeabur template deploy -f zeabur.yaml`

---

## 🔧 架構說明

包含三個核心獨立服務：

| 服務名稱 (Docker) | 說明 | 端口 |
|------------------|------|------|
| **n8n** | 主程式 (Workflow Engine) | 5678 |
| **db** | PostgreSQL 資料庫 | 5432 |
| **task-runners** | 專用 Python 執行環境 (Isolated Worker) | - |

**為什麼需要獨立 Runner？**
- **安全性**：Python 程式碼在獨立容器中執行，不會影響主程式。
- **彈性**：可以隨意安裝龐大的資料分析套件 (如 pandas)，而不需修改 n8n 官方映像檔。

## 🐍 Python Code Tool 使用範例 (n8n v2.0+)

在 n8n 的 "Code Node" 中選擇 Language 為 Python：

```python
import json
import pandas as pd

# 讀取 n8n 輸入
input_data = _query
if isinstance(input_data, str):
    input_data = json.loads(input_data)

# 使用 pandas 處理
df = pd.DataFrame(input_data)
result_count = int(len(df)) # 轉換 numpy int 為一般 int

return {'count': result_count, 'columns': list(df.columns)}
```
