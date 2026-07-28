# personal-codex

個人 Codex 設定範本，目標是讓 Codex 在處理 `.NET + Angular + SQL Server / Oracle` 專案時，能依任務內容拆分工作、選擇適合的 Subagent，並由主 Agent 統整與驗證結果。

> 這是一份可持續調整的初版。模型、目錄名稱、建置指令與團隊規範都應依實際專案修改。

## 主要能力

- 使用 `AGENTS.md` 定義全域開發規範與多 Agent 委派策略。
- 使用 `.codex/config.toml` 啟用 multi-agent、控制並行數、預設模型與安全模式。
- 使用 `.codex/agents/*.toml` 定義專門 Agent：
  - `explorer`：唯讀探索程式碼與影響範圍。
  - `dotnet_worker`：實作 .NET 後端功能。
  - `angular_worker`：實作 Angular 前端功能。
  - `database_reviewer`：檢查 SQL Server、Oracle、EF Core 與 Dapper 資料存取。
  - `test_runner`：執行最小必要的 build、lint 與 test。
- 使用巢狀 `AGENTS.md` 定義 backend、frontend、database 的局部規範。
- 使用 `.agents/skills/implement-full-stack-feature/SKILL.md` 保存可重複的全端功能實作流程。

## 目錄結構

```text
personal-codex/
├─ README.md
├─ AGENTS.md
├─ .codex/
│  ├─ config.toml
│  └─ agents/
│     ├─ explorer.toml
│     ├─ dotnet-worker.toml
│     ├─ angular-worker.toml
│     ├─ database-reviewer.toml
│     └─ test-runner.toml
├─ .agents/
│  └─ skills/
│     └─ implement-full-stack-feature/
│        └─ SKILL.md
├─ backend/
│  └─ AGENTS.md
├─ frontend/
│  └─ AGENTS.md
└─ database/
   └─ AGENTS.md
```

## Codex 如何分配 Agent

單純建立 `.codex/agents/*.toml` 只代表這些 Agent 可用，不保證每個任務都一定會被拆分。

本範本在根目錄 `AGENTS.md` 中加入委派規則：

1. 簡單說明、單檔小修改由主 Agent 處理。
2. 跨模組、需要探索、實作、資料庫檢查或測試的任務，主 Agent 應拆分工作。
3. 唯讀工作可平行執行。
4. 寫入工作要避免多個 Agent 同時修改相同檔案。
5. 最終整合、衝突處理與結果回報仍由主 Agent 負責。

Codex 目前會在以下情況使用 Subagent：

- 你在任務中明確要求委派。
- 適用的 `AGENTS.md` 指示要求委派。
- 適用的 Skill 工作流程要求委派。

## 模型配置

初版採用以下配置：

| Agent | 模型 | Reasoning | 權限 | 適合工作 |
|---|---|---:|---|---|
| Primary Agent | `gpt-5.6` | high | workspace-write | 規劃、協調、整合 |
| explorer | `gpt-5.6-luna` | low | read-only | 搜尋與程式碼導覽 |
| dotnet_worker | `gpt-5.6` | high | workspace-write | 後端實作 |
| angular_worker | `gpt-5.6-terra` | medium | workspace-write | 前端實作 |
| database_reviewer | `gpt-5.6-terra` | high | read-only | SQL 與資料存取審查 |
| test_runner | `gpt-5.6-luna` | low | workspace-write | Build、lint、test |

模型名稱可以在 `.codex/config.toml` 或各 Agent TOML 中替換。若帳號或工作區無法使用指定模型，請改成目前 Codex 可選的模型。

## 使用方式

### 方式一：把設定複製到既有專案

將以下項目複製到目標專案根目錄：

```text
AGENTS.md
.codex/
.agents/
```

再依專案實際目錄放置局部規範：

```text
<backend-folder>/AGENTS.md
<frontend-folder>/AGENTS.md
<database-folder>/AGENTS.md
```

例如實際專案目錄是：

```text
src/Api/
src/Web/
database/
```

則可把：

```text
backend/AGENTS.md  → src/Api/AGENTS.md
frontend/AGENTS.md → src/Web/AGENTS.md
database/AGENTS.md → database/AGENTS.md
```

### 方式二：以本 Repository 作為範本

建立新專案時，先複製或 fork 此 repo，再把程式碼放入對應目錄，最後調整：

- .NET 與 Angular 版本。
- Solution、Project、Package Manager 路徑。
- Build、lint、test 指令。
- API 錯誤格式與架構規範。
- SQL Server、Oracle schema 與 migration 規則。
- 可使用的模型與並行數。

## 建議任務寫法

### 跨前後端功能

```text
使用 implement-full-stack-feature skill 實作使用者查詢功能。
後端使用 .NET API，前端使用 Angular，資料來自 SQL Server。
請先讓 explorer 找出既有模式，再將可獨立工作委派給適合的 Agent，最後執行 build 與 test。
```

### 資料庫查詢調整

```text
分析這個 Repository 查詢的效能與正確性。
請讓 database_reviewer 檢查 SQL Server / Oracle 語法差異、索引、鎖定與重複資料風險；不要執行任何寫入語句。
```

### PR / Branch Review

```text
Review this branch against main.
Have explorer map the affected paths, database_reviewer inspect data-access risks, and test_runner run the smallest relevant validation commands.
Return findings by severity before proposing changes.
```

## 安全原則

- 不把密碼、Connection String、Token 或 API Key 提交到 Git。
- Database Agent 預設唯讀。
- 禁止自動執行 `DROP`、`TRUNCATE`、無條件 `DELETE`、無條件 `UPDATE`。
- 任何 DDL、DML 或 migration 執行前，都應先說明影響範圍並取得明確授權。
- 真正的資料庫安全邊界必須由唯讀 DB 帳號與 MCP Server 驗證實作，不能只依賴 prompt。
- Subagent 平行化優先用於探索、審查、測試與摘要；多個寫入 Agent 不應同時修改相同檔案。

## MCP 延伸

MCP Server 可加入 `.codex/config.toml`，但本範本不放任何實際帳密。

範例：

```toml
[mcp_servers.dbhub]
command = "npx"
args = ["-y", "@bytebase/dbhub@0.21.1"]
env_vars = [
  "DSN",
  "DB_TYPE",
  "DB_HOST",
  "DB_PORT",
  "DB_USER",
  "DB_PASSWORD",
  "DB_NAME"
]
```

建議同時使用三層保護：

1. MCP Server 僅允許安全的查詢操作。
2. 資料庫帳號只授予必要權限，查詢型 MCP 使用唯讀帳號。
3. `AGENTS.md` 與 Agent 指令禁止未授權寫入。

## 初版限制

- 尚未加入 hooks 與 command rules，避免在不清楚執行環境時自動執行命令。
- 尚未綁定特定 Solution、Angular workspace 或測試專案名稱。
- 尚未設定實際 MCP Server。
- Agent 委派不是固定工作流引擎，主 Agent 仍會依任務內容判斷是否需要拆分。

## 後續可擴充

- 新增 `security_reviewer`。
- 新增 Kubernetes / Azure DevOps Agent。
- 新增 EF Core migration Skill。
- 新增 API code review Skill。
- 新增 `.codex/rules/*.rules` 限制危險 shell command。
- 新增 hooks，在任務結束時自動執行 `dotnet build`、`dotnet test`、`npm run lint`。

## 參考文件

- Codex Subagents：<https://developers.openai.com/codex/subagents>
- Codex Config Reference：<https://developers.openai.com/codex/config-reference>
- Codex AGENTS.md：<https://developers.openai.com/codex/agent-configuration/agents-md>
- Codex Skills：<https://developers.openai.com/codex/build-skills>
