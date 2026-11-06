# 5. 專案資料夾與檔案格式

[← 回到目錄](README.md)

## 5.1 專案放在哪裡

| 情況 | 位置 |
|------|------|
| 預設 | `文件/Craft Anything`（`~/Documents/Craft Anything`） |
| 自訂 | 設定 → 一般 → 新專案儲存位置；CLI 用 `--projects-dir` |
| 指定資料夾 | 「選擇資料夾…」或 CLI `--project-folder`，該資料夾會加入專案清單 |

## 5.2 專案資料夾結構

```
我的專案/
├── craft.json              # 需求、目標、狀態與歷程（專案的「身分證」）
├── capture/                # 從原作記錄下來的內容（截圖、版面量測…）
├── app/                    # 產生的專案原始碼：這就是你的成果
├── checks/
│   └── round-N/            # 第 N 輪與原作的比對結果
│       └── self-test.json  # 遊戲自我測試結果（遊戲專案）
├── investigation.cany/     # 證據帳本與調查記憶
├── inbox/                  # 待你回答的問題與核准
│   └── build-progress.json # 建置進度（只由建置流程寫入）
├── attachments/            # 你附上的圖片副本
├── pinned/<n>/             # 釘選保存的版本
├── feed.json               # 專案頁顯示的動態
├── questions.json          # Agent 問過的問題
├── usage.json              # 使用量統計
└── agent-session.jsonl     # Agent 對話紀錄（讓專案搬到別台電腦後仍能續作）
```

> **你真正要的成果在 `app/`。** 其他檔案是研究過程與紀錄，可以保留以便日後繼續修改。

### 已有檔案的資料夾

用 `--project-folder` 或「選擇資料夾…」挑一個已有檔案的資料夾時，要指定它們的角色：

| 選擇 | CLI | 行為 |
|------|-----|------|
| 它就是 App 本身 | `--existing edit` | 建置助手直接修改這些檔案 |
| 起始素材 | `--existing start-from` | 保留原檔，App 建在旁邊的 `app/` |

## 5.3 設定檔位置

設定存在 `config.toml`，位置依序決定：

1. 環境變數 `CRAFT_ANYTHING_CONFIG_DIR`（有設定時優先）
2. 免安裝版：執行檔旁有 `portable.txt` 時，使用執行檔旁的 `CraftAnythingData/`
3. 系統預設設定資料夾下的 `craft-anything/`：

| 系統 | 預設路徑 |
|------|----------|
| macOS | `~/Library/Application Support/craft-anything/` |
| Windows | `%APPDATA%\craft-anything\` |
| Linux | `~/.config/craft-anything/`（或 `$XDG_CONFIG_HOME/craft-anything/`） |

桌面 App 的記錄檔 `desktop.log` 就在這個資料夾裡（`logs/` 子資料夾則存放其他記錄）。**API 金鑰不存在 `config.toml`，而是存在系統鑰匙圈。**

## 5.4 版本與相容性

每個 Craft Anything 儲存的檔案都有版本號：

| 情況 | 行為 |
|------|------|
| 舊版檔案 | 自動升級到目前格式 |
| 新版 Craft Anything 產生的檔案 | 以唯讀方式盡量讀取，**絕不覆寫**（避免遺失不認得的欄位），並提示你更新 |
| 檔案損壞 | `config.toml` 會改名為 `config.toml.broken` 並使用預設值；壞掉的 `craft.json` 會在開啟時說明錯誤並從專案清單中排除 |

主要檔案的目前版本：

| 檔案 | 版本 |
|------|------|
| `craft.json` | 2 |
| `config.toml` | 2 |
| `<名稱>.cany/project.toml` | 1 |
| `feed.json` | 4 |
| `pinned/<n>/pin.json` | 2 |

JSON Schema 在 [`docs/schemas/`](../schemas/)。完整技術說明（英文）見 [docs/file-formats.md](../file-formats.md)。

## 5.5 專案搬家與同步

- 專案資料夾可以整個複製到另一台電腦或放在同步資料夾。
- Claude Code 的對話本來存在電腦本機的 `~/.claude/projects/`；Craft Anything 會把對話另存為專案內的 `agent-session.jsonl`，所以換電腦後仍能延續。
- 從清單「移除」專案只會把它從清單拿掉，**不會刪除檔案**。

## 5.6 釘選版本（Pin）

覺得某個版本不錯時可以「釘選」保存一份副本到 `pinned/<n>/`，之後繼續修改也不怕弄壞。
