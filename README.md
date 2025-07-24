# Release Notes Workflow 設置說明

自動產生發布通知並發送到 Slack 的 GitHub Actions 工作流程。

## 前置設定

### 1. 檔案配置

將 `.github/workflows/deploy.yml` 放在你的專案根目錄下的 `.github/workflows/` 資料夾中。

### 2. GitHub Secrets 設定

在 GitHub 專案設定中，到 `Settings` → `Secrets and variables` → `Actions`，新增以下 secrets：

```
JIRA_API_EMAIL=your-email@company.com
JIRA_API_TOKEN=your-jira-api-token
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/xxx/xxx/xxx
```

#### 取得 JIRA API Token：

1. 登入 JIRA，到 `Account Settings` → `Security` → `API tokens`
2. 點擊 `Create API token`
3. 複製產生的 token

#### 取得 Slack Webhook URL：

1. 到 Slack App 管理頁面
2. 建立新的 Incoming Webhook
3. 選擇要發送的 channel
4. 複製 Webhook URL

### 3. JIRA 設定

確認你的 JIRA base URL 是否為 `https://buyandship.atlassian.net`，如果不是請在 yml 檔案中修改 `JIRA_BASE_URL`。

## 使用方式

### 自動觸發（PR 合併）

當 PR 合併到 `main` 分支時，會自動執行：

1. **有 BC 票據的情況**：

   - 從 PR description 中的 `Release notes: BC-1234, BC-5678` 行提取票據
   - 如果沒有 release notes，則從 PR title 提取
   - 發送包含所有票據資訊的通知

2. **無 BC 票據的情況**：
   - 發送簡單的部署通知，顯示 PR title

### 手動觸發

1. 到 GitHub Actions 頁面
2. 選擇 `Release Note on PR Merge` workflow
3. 點擊 `Run workflow`
4. 輸入 run name（可以是 BC 票據號或任意文字）

## PR Description 格式（推薦）

在 PR description 中加入：

```
Release notes: BC-1234, BC-5678, BC-9999
```

這樣可以精確控制要包含在 release notes 中的票據。

## 輸出格式

```
🚀 **Release Deployed**
Repo: your-org/your-repo
Hash: 2871f7e0a99377ca0146f30ef6e80710ee9d692de6fa
Date: 2025/01/15 14:30
- BC-10430 [WEB] Clicking the MCR menu should redirect to corresponding page
- BC-10431 [WEB] Click Cancel button should redirect to B4U orders page
```

- 時間顯示為台北時區
- BC 票據號碼和描述整行都是可點擊連結
- 格式適合複製到 Lark 使用

## 注意事項

1. **票據格式**：必須是 `BC-` 開頭加數字（如 `BC-1234`）
2. **權限**：確保 JIRA API token 有讀取票據的權限
3. **網路**：GitHub Actions 需要能訪問你的 JIRA 和 Slack
4. **分支**：目前設定為監聽 `main` 分支，如需修改請調整 yml 檔案中的 `branches: [main]`
