# n8n Task Runners Starter

n8n V2 的 Code 節點從原本在網頁執行改由外接的 Task Runner 執行，因此需要額外的 Task Runner 服務。

但基礎 Task Runner 沒辦法安裝自定義套件，也無法使用 pip 安裝，造成每次都需要重新建置映像檔，為了減低這個麻煩，本專案提供一個預先配置好的 n8n 環境，包含 PostgreSQL 資料庫與專用的 Python Task Runner，你可以在這裡修改 task-runner/requirements.txt 來安裝自定義套件。

範本特別針對 **進階資料處理** 預先設置了常用套件（如 pandas, numpy, scikit-learn）。

---

## 🚀 步驟一：Fork 專案 (必要)

為了讓您可以自定義 Python 套件，請先複製一份專案到您的帳號：

1. 登入 GitHub。
2. 點擊本頁面右上角的 **Fork** 按鈕。
3. 建立 Fork 到您的個人帳號。

> **接下來的步驟，請在您 Fork 出來的專案頁面中操作。**

---

## 📦 步驟二：自定義 Python 套件 (可選)

如果您需要額外的 Python 函式庫，請修改需求檔：

1. 在您的 Repo 中，打開 `task-runner/requirements.txt` 檔案。
2. 點擊右上角筆型圖示 (Edit) 進行編輯。
3. 加入您需要的套件名稱（一行一個），例如：
   ```text
   pandas
   numpy
   scikit-learn
   requests  <-- 新增範例
   ```
4. 儲存變更 (Commit Changes)。

---

## 🔄 步驟二補充：如何在現有環境更新套件？

如果您已經啟動了環境（步驟三），之後又修改了 `requirements.txt` 新增套件，請依下列方式套用變更：

### Codespaces / 本機 (Docker)
在終端機 (Terminal) 執行以下指令來重建 Task Runner：
```bash
docker compose build --no-cache task-runners
docker compose up -d task-runners
```
*(重建完成後，新的套件即可在 Code 節點中使用)*

### Zeabur
1. 確保 `requirements.txt` 的變更已 Push 到 GitHub。
2. 在 dashboard 進入 `n8n-python-runner` 服務。
3. 點擊 **Redeploy** 按鈕，Zeabur 會重新讀取設定並安裝新套件。

---

## ⚡ 步驟三：啟動環境

請選擇以下其中一種方式啟動：

### 選項 A：GitHub Codespaces (教學推薦 🔥)
完全免費，直接在瀏覽器中執行，無需安裝軟體。

1. 在您的 GitHub Repo 頁面，點擊綠色的 **Code** 按鈕。
2. 切換到 **Codespaces** 分頁。
3. 點擊 **Create codespace on main**。
4. 等待環境建置完成（約 2-3 分鐘），系統會自動啟動所有服務 (包含自動安裝您設定的套件)。
5. 當右下角出現 "Open in Browser" 提示時，點擊即可開啟 n8n。
   - 若未出現提示，請至下方 **"PORTS"** 分頁，點擊 **Port 5678** 旁的地球圖示。
   - **注意**：首次進入需設定 n8n 帳號。

> **關於資料保存**：
> - 只要 **不刪除** Codespace，即使關閉視窗或暫停，資料庫與設定都會保留。下次啟動即可繼續使用。
> - 若 GitHub 自動刪除閒置過久的 Codespace（預設 30 天未登入），資料將會遺失。建議重要流程定期匯出 JSON 備份。

### 選項 B：一般伺服器 / 本機 (Docker)
適用於您的電腦或 VPS (Linode, DigitalOcean)。

**系統需求**：安裝好 Docker 與 Docker Compose。

```bash
# 1. 下載您的專案 (記得換成您的帳號)
git clone https://github.com/YOUR_USERNAME/n8n-TaskRunners-Starter.git
cd n8n-TaskRunners-Starter

# 2. 設定環境變數
cp .env.example .env

# 3. 啟動服務
docker compose up -d

# 4. 開啟瀏覽器: http://localhost:5678
```

### 選項 C：部署至 Zeabur (PaaS)

由於 Zeabur 設有資源限制，可能會遇到記憶體不足或 IP 滿載的問題。若要部署，建議使用以下方式：

1. 安裝並登入 Zeabur CLI (`npx zeabur auth login`)。
2. 在專案目錄下執行：
   ```bash
   npx zeabur template deploy -f zeabur.yaml
   ```
3. 這會自動讀取您 Repo 中的設定並部署三個服務。

---

## 🔧 架構說明與 Python 使用範例

本專案包含三個核心服務：**n8n** (主程式), **db** (PostgreSQL), **task-runners** (Python 環境)。

要在 n8n 中使用 Python：
1. 建立 "Code" 節點，語言選擇 Python。
2. 範例程式碼：

```python
import json
import pandas as pd

# 讀取輸入
input_data = _query
if isinstance(input_data, str):
    input_data = json.loads(input_data)

# 使用 pandas 處理
df = pd.DataFrame(input_data)
result_count = int(len(df))

return {'count': result_count, 'columns': list(df.columns)}
```
