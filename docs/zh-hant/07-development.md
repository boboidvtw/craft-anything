# 7. 從原始碼建置與參與貢獻

[← 回到目錄](README.md)

## 7.1 環境需求

| 項目 | 需求 |
|------|------|
| Rust | **1.97 以上**（`rustup update`） |
| Debian / Ubuntu | 額外套件（見下方） |
| 中日韓字型（選用） | 本機編譯想要完整中文字形時需要 craft-fonts |

Debian / Ubuntu：

```sh
sudo apt-get install libxkbcommon-dev libwayland-dev libx11-dev libxrandr-dev libxi-dev libgl1-mesa-dev libgtk-3-dev
```

## 7.2 編譯與執行

```sh
git clone https://github.com/storytold/craft-anything && cd craft-anything

cargo run --release -p craft-anything                # 桌面 App
cargo install --path apps/craft-anything-cli         # CLI
craft-anything-cli doctor
```

### 讓本機編譯的 App 正確顯示中文

官方 Release 與 CI 編譯已內含 craft-fonts 的中日韓備援字型。自行編譯時，請在**編譯前**設定：

```sh
export CRAFT_FONTS_DIR=/path/to/craft-fonts
cargo run --release -p craft-anything
```

否則中文可能顯示為方框。

## 7.3 專案結構

| 路徑 | 層級 | 內容 |
|------|------|------|
| `crates/evidence` | L0 | 證據紀錄、信心程度、錯誤格式 |
| `crates/formats` | L0 | 獨立的檔案格式解析器（ELF / PE / Mach-O / WASM / DEX / APK…） |
| `crates/disasm` | L1 | 自製原生引擎：解碼、函式、控制流程圖、交叉參照、虛擬 C 碼 |
| `crates/js` | L1 | JavaScript / Electron / 網頁 bundle |
| `crates/analysis` | L2 | 產生證據的分析器 |
| `crates/platform` | L2 | 行程管理、沙盒、原子寫檔 |
| `crates/engines` | L3 | 外部引擎：Ghidra、adb、CDP、Frida、JADX、Apktool… |
| `crates/engine` | L4 | Session、專案、工具註冊、設定、收件匣 |
| `crates/ai` | L5 | AI 後端、Agent 迴圈、重建流程 |
| `crates/mcp` | L5 | MCP 伺服器 |
| `crates/setup` | L5 | 連接 Agent App、doctor、uninstall |
| `crates/ui-egui` | L6 | egui 介面（唯一可使用 egui 的 crate） |
| `apps/` | — | 桌面 App、CLI、瀏覽器檢視器 |
| `xtask/` | — | 開發工具指令 |

層級由 `cargo xtask layers` 強制檢查：下層不能依賴上層。

## 7.4 提交前檢查

```sh
cargo fmt --all
cargo test -p <你改過的 crate> --no-fail-fast
cargo clippy -p <crate> --all-targets -- -D warnings
cargo xtask layers
cargo xtask locks
cargo xtask secrets       # 確認沒有金鑰、Token 或本機路徑
cargo xtask wasm          # 有改 L0–L2 時
cargo xtask tools         # 有新增工具時（並 commit docs/tools.md）
```

啟用 pre-commit hook（每個 clone 一次）：

```sh
git config core.hooksPath .githooks
```

完整規範見 [AGENTS.md](../../AGENTS.md)、[CONTRIBUTING.md](../../CONTRIBUTING.md) 與 [docs/pitfalls.md](../pitfalls.md)。

## 7.5 維護繁體中文翻譯

### 翻譯檔

介面翻譯在 `crates/ui-egui/src/i18n/zh-hant.tsv`，每行兩欄，以 Tab 分隔：

```
English source<TAB>繁體中文翻譯
```

- `#` 開頭為註解。
- `\n` 代表換行。
- 缺少的翻譯會退回英文原文。

### 翻譯規則

| 規則 | 範例 |
|------|------|
| 保留佔位符 | `{name}`、`{n}`、`{version}` 原樣保留 |
| 保留結尾刪節號 | `Choose a folder…` → `選擇資料夾…` |
| 產品與技術名稱不翻 | Ghidra、Markdown、MCP、Claude Code |
| 指令 ID、快捷鍵不翻 | `Ctrl/⌘ K` |
| 用台灣用語 | 專案（非項目）、建置（非構建）、檔案（非文件）、設定（非設置）、預設（非默認）、程式（非程序）、發布（非發佈） |

### 固定用語

| 英文 | 繁體中文 |
|------|----------|
| Clean room | 淨室 |
| Deep | 深度 |
| Build | 建置 |
| Evidence | 證據 |
| Project | 專案 |
| Study / Check / Ready | 研究 / 檢查 / 就緒 |

### 新增或修改英文字串時

**所有語系檔**（`cs`、`de`、`ko`、`ru`、`zh-hans`、`zh-hant`）必須同步更新，測試會比對 key 是否一致。

### 驗證

```sh
cargo test -p craft-anything-ui-egui
cargo test -p xtask
cargo clippy -p craft-anything-ui-egui -p xtask --all-targets -- -D warnings
cargo xtask i18n-coverage
```

測試會檢查重複與空白項目、key 一致、佔位符與刪節號。

### 以截圖檢查版面

```sh
cargo run -p craft-anything-ui-egui --example snapshot -- --lang zh-hant --only settings --out-dir target/snapshots/zh-hant
```

翻譯涵蓋率測試無法保證版面正確，請實際檢查文字是否被截斷、窄視窗與原生檔案對話框。

### README 翻譯

`README.zh-Hant.md` 是 `README.md` 的繁體中文翻譯；英文版更新時請一併更新。

## 7.6 擴充

想新增工具、目標類型、檢查、技術堆疊、編碼 Agent、素材供應商、引擎或外掛，見 [docs/extending.md](../extending.md)。
每個使用者可見的功能都是一個 `ToolSpec`（見 AGENTS.md 的「Everything is a tool」），UI、CLI、MCP 與 AI Agent 都依工具 ID 呼叫同一套實作。
