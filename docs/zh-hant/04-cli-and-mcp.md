# 4. CLI 與 MCP 完整指南

[← 回到目錄](README.md)

`craft-anything-cli` 是 App 的完整命令列版本：**每個工具都是一個子指令**，也是一個 MCP 工具。

## 4.1 安裝 CLI

| 安裝方式 | CLI 在哪裡 |
|----------|------------|
| macOS DMG（0.2.0 之後） | 已附在 App 內 |
| macOS 只要 CLI | 下載 `craft-anything-cli-<ver>-macos-universal.zip`（見 README） |
| Linux AppImage | `<AppImage> --cli <指令>`，或建立名為 `craft-anything-cli` 的連結指向 AppImage |
| Windows MSI / zip | 與 App 放在一起 |
| 原始碼 | `cargo install --path apps/craft-anything-cli` |

確認版本：

```sh
craft-anything-cli --version
```

## 4.2 全域選項

這些選項可以放在任何子指令前：

| 選項 | 作用 |
|------|------|
| `--format <格式>` / `--output-format` | 輸出格式：`json`（預設）、`jsonl`、`md`、`text`、`csv`、`tsv` |
| `--json` | 等同 `--format json` |
| `--project <資料夾>` | 使用一個 `*.cany` 專案資料夾保存證據、記憶與日誌；不存在就建立 |
| `--allow-run` | 允許會執行程式碼或改變裝置的工具，不再詢問（請小心） |
| `--deep` | 本次執行使用深度模式 |
| `--clean-room` | 本次執行強制使用淨室，不管設定 |

> 某個工具自己也有 `--format` 參數時（例如 `core-target-open`），放在工具名稱**之後**的 `--format` 屬於工具；
> 要指定輸出格式請用 `--output-format`，或把 `--format` 放在工具名稱**之前**。

## 4.3 主要子指令

| 指令 | 作用 |
|------|------|
| `doctor` | 檢查安裝、Agent 註冊、引擎、AI 後端與 MCP 伺服器 |
| `setup` | 把 Craft Anything 連接到你的編碼 Agent App |
| `uninstall` | 從 Agent App 移除 Craft Anything 的註冊與 skill |
| `commands` | 依工具集列出所有工具 |
| `capabilities` | 這個安裝能做什麼：工具集、工具可用性、引擎 |
| `triage <路徑>` | 開啟一個檔案並做初步檢視 |
| `report [路徑]` | 產生調查報告（Markdown；加 `--html` 輸出 HTML） |
| `chat "<問題>"` | 向 AI Agent 詢問關於目標的問題，答案串流輸出 |
| `rebuild <目標> "<需求>"` | 研究並重建網站、App 或遊戲 |
| `ai …` | 管理 AI 後端（見[第 3 章](03-ai-setup.md#36-管理後端的常用指令)） |
| `mcp` | 以 stdin/stdout 啟動 MCP 伺服器 |
| `<工具 id>` | 直接執行任何工具 |

查看任何指令的說明：

```sh
craft-anything-cli --help
craft-anything-cli rebuild --help
craft-anything-cli native-strings-list --help
```

## 4.4 rebuild：重建

```sh
craft-anything-cli rebuild <目標> "<需求>" [選項]
```

- `<目標>`：網址、檔案、App 或資料夾；沒有目標用 `-`。
- `<需求>`：中英文都可以。

| 選項 | 說明 |
|------|------|
| `--rounds <n>` | 首次建置後最多修正幾輪（CLI 預設 **2**） |
| `--backend <名稱>` | 指定建置用的 Agent（`ai list` 中的名稱，或已安裝的 Agent 如 `Claude Code`） |
| `--build-with <引擎或技術堆疊>` | 例如 `unity`、`unreal`、`godot`、`rust`，或技術堆疊全名；僅限新專案 |
| `--projects-dir <資料夾>` | 新專案放在哪裡（預設為設定中的專案資料夾） |
| `--project-folder <資料夾>` | 直接在這個資料夾開始專案 |
| `--existing edit\|start-from` | 搭配 `--project-folder`：已有檔案是 App 本身（`edit`）或起始素材（`start-from`） |
| `--continue-in <專案資料夾>` | 延續既有專案，需求會成為後續訊息 |
| `--resume` | 搭配 `--continue-in`：從上次中斷處繼續，忽略需求 |
| `--unattended` | 問題自動選第一個選項、核准一律拒絕，不詢問 |
| `--generate-assets` | 允許為新遊戲生成圖片與 3D 模型（需 fal.ai 金鑰，會產生費用） |

範例：

```sh
# 重現網站
craft-anything-cli rebuild https://example.com "重現這個網站"

# 全新遊戲，只修正一輪
craft-anything-cli rebuild - "用 Rust 做一個貪食蛇遊戲" --rounds 1

# 指定 Godot
craft-anything-cli rebuild - "一個 2D 平台跳躍遊戲" --build-with godot

# 在既有專案上追加修改
craft-anything-cli rebuild https://example.com "把頁首改成藍色" --continue-in "~/Documents/Craft Anything/example-com"

# 修改你自己的既有專案
craft-anything-cli rebuild - "加一個聯絡表單" --project-folder ~/code/my-site --existing edit

# 電腦重開機後接續上次的建置
craft-anything-cli rebuild - "" --continue-in "<專案資料夾>" --resume
```

建置中的問題會直接在終端機詢問。按一次 `Ctrl+C` 會請求停止；再按一次立即結束。

## 4.5 直接執行工具

```sh
craft-anything-cli <工具 id> [路徑] [--參數 值] [--json '{...}' | @檔案.json | -]
```

工具 id 在 CLI 中底線換成連字號：`native_strings_list` → `native-strings-list`。

```sh
craft-anything-cli commands                              # 列出所有工具
craft-anything-cli triage ./some-app.exe                 # 初步檢視
craft-anything-cli --deep native-strings-list ./a.out    # 深度模式下列出字串
craft-anything-cli core-evidence-browse --json '{"offset":0,"limit":50}'
```

### 工具集一覽（共 301 個引擎工具，加上 AI 與設定層 → 約 320 個）

| 工具集 | ID | 數量 | 涵蓋範圍 |
|--------|----|------|----------|
| 基本 | `core` | 35 | 開啟目標、證據、待確認事項、封存檔、詢問與核准 |
| 程式（原生碼） | `native` | 55 | ELF / PE / Mach-O、反組譯、函式、呼叫圖、虛擬 C 碼 |
| Apple App | `apple` | 14 | Mach-O、plist、簽章、Objective-C / Swift |
| 檔案格式 | `formats` | 36 | 各種檔案格式解析 |
| .NET App | `managed` | 8 | .NET 組件 |
| Android App | `android` | 14 | APK、DEX |
| Java 程式 | `java` | 6 | JAR、class |
| 連接的手機 | `adb` | 24 | 透過 adb 操作手機 |
| 網站 | `browser` | 22 | 截圖、版面、網頁擷取 |
| Electron 與 JavaScript | `javascript` | 11 | ASAR、bundle、IPC、source map |
| 執行中的程式 | `runtime` | 14 | 執行、觀察、視窗擷取 |
| Frida 動態插樁 | `frida` | 9 | Frida 腳本 |
| 比對與重建 | `application` | 17 | 比對原作與成果 |
| 資料庫 | `database` | 1 | 資料庫檔案 |
| 專案 | `projects` | 20 | 專案管理 |
| 引擎與設定 | `setup` | 9 | 引擎安裝、設定 |
| 建置 | `build` | 6 | 建置助手使用的檔案與指令工具 |

完整清單（英文）：[docs/tools.md](../tools.md)。工具命名規則：`<領域>_<對象>_<動作>`，見 [docs/tool-ids.md](../tool-ids.md)。

### 結果格式

每個分析結果都是一筆**證據紀錄**，包含：

- **信心程度**（confidence）：例如 observed（實際觀察到）或 inferred（推論）
- **涵蓋範圍**（coverage）：看了多少
- **限制**（limitations）：哪些部分沒有看到
- **位置**（locations）：證據在哪裡

結果太大時會先回傳摘要，再用 `core_evidence_browse` 分頁瀏覽（`offset` / `limit`）。

## 4.6 MCP 伺服器

### 自動設定（建議）

```sh
craft-anything-cli setup --dry-run          # 先看會改哪些設定檔
craft-anything-cli setup --all-detected     # 設定所有偵測到的 Agent App
craft-anything-cli setup --client claude_code --client cursor --yes
craft-anything-cli doctor                   # 確認註冊成功
```

| `setup` 選項 | 說明 |
|--------------|------|
| `--dry-run` | 只顯示會變更什麼，不寫入 |
| `--client <id>` | 指定要設定的用戶端，可重複 |
| `--all-detected` | 設定所有偵測到的用戶端 |
| `--yes`、`-y` | 不詢問直接套用 |
| `--no-skill` | 不安裝 skill |

支援的用戶端 ID 包括：`claude_code`、`claude_desktop`、`codex`、`cursor`、`windsurf`、`vscode`、`opencode`、`qwen_code`、`gemini_cli`、`copilot_cli`、`antigravity`、`hermes`、`trae`、`qoder` 等（以 `setup --help` 與 `doctor` 輸出為準）。

移除：

```sh
craft-anything-cli uninstall
```

### 手動設定

任何 MCP 用戶端都可以這樣啟動：

```json
{
  "mcpServers": {
    "craft-anything": {
      "command": "craft-anything-cli",
      "args": ["mcp"]
    }
  }
}
```

`mcp` 子指令的選項：

| 選項 | 說明 |
|------|------|
| `--all-tools` | 列出所有工具，而非「基本工具 + 已啟用的工具集」 |
| `--inbox <資料夾>` | 問題與核准貼到這個資料夾，由 Craft Anything App 回答 |
| `--target <JSON>` | 桌面端傳入的目標中繼資料，可重複 |

全域 `--deep` / `--clean-room` 一樣適用，例如 `craft-anything-cli --deep mcp --all-tools`。

> 工具是**漸進式**提供的：預設只列出基本工具與已啟用的工具集，避免一次塞給模型太多工具描述。

## 4.7 環境變數

| 變數 | 用途 |
|------|------|
| `CRAFT_ANYTHING_CONFIG_DIR` | 改變設定資料夾位置（測試或多組設定時好用） |
| `CRAFT_ANYTHING_LOG` | CLI 記錄層級：`trace` / `debug` / `info` / `warn` / `error` |
| `CRAFT_ANYTHING_RENDERER` | Windows：設為 `software` 以不用顯示卡繪製 |
| `WGPU_BACKEND`、`WGPU_DX12_COMPILER` | 進階：指定 wgpu 繪圖後端與 DX12 著色器編譯器 |

## 4.8 結束碼

| 結束碼 | 意義 |
|--------|------|
| `0` | 成功 |
| `1` | 工具錯誤（錯誤以結構化格式印到 stdout，方便腳本解析） |
| `2` | 用法錯誤（參數不對） |
| `130` | 被 `Ctrl+C` 中斷 |
