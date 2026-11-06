# 1. 快速上手

[← 回到目錄](README.md)

目標：15 分鐘內安裝好 Craft Anything、把介面切成繁體中文，並完成第一個重建專案。

## 1.1 事前準備

| 需要什麼 | 為什麼 | 怎麼確認 |
|----------|--------|----------|
| 一台 macOS / Windows / Linux / FreeBSD 電腦 | 執行桌面 App | — |
| **一個已登入的編碼 Agent**（建議 Claude Code） | 「建置」步驟要靠它寫程式 | 終端機執行 `claude auth status` |
| 網路 | 研究網站、Agent 呼叫模型 | — |
| （選用）Ghidra | 深度模式下更強的反編譯 | 可在 App 的「引擎」設定中安裝或指定資料夾 |

> 只想「研究」、不打算「建置」的話，不需要任何 AI。檢查工具本身完全離線可用。

### 安裝 Claude Code（若還沒有）

依 Anthropic 官方說明安裝後，在終端機登入：

```sh
claude auth login
```

Craft Anything 只會執行你自己安裝、**未經修改**的 `claude` 程式，從不讀取或保存你的登入憑證。
其他 Agent 的設定方式見 [AI 與編碼 Agent 設定](03-ai-setup.md)。

## 1.2 下載安裝

到 [Releases 頁面](https://github.com/storytold/craft-anything/releases/latest) 下載對應版本（`<ver>` 為版本號）：

| 系統 | 建議下載 | 備註 |
|------|----------|------|
| Windows 64 位元 | `craft-anything-<ver>-windows-x64.msi` | 想免安裝就選 `-portable.zip` |
| Windows ARM（Snapdragon） | `craft-anything-<ver>-windows-arm64.msi` | |
| macOS（Apple 晶片或 Intel） | `craft-anything-<ver>-macos-universal.dmg` | 已簽章與公證；App 內已含 CLI |
| Linux | `.AppImage`（最通用）、`.deb`、`.rpm`、`.flatpak` | 有 x86_64 與 aarch64 |
| FreeBSD | `craft-anything-<ver>-freebsd-x86_64.tar.gz` | |

驗證下載檔：每個 Release 都附 `SHA256SUMS.txt`。

```sh
shasum -a 256 craft-anything-<ver>-macos-universal.dmg   # macOS / Linux
```

```powershell
Get-FileHash craft-anything-<ver>-windows-x64.msi         # Windows PowerShell
```

把輸出與 `SHA256SUMS.txt` 對照，一致才安裝。macOS 簽章檢查的完整指令見 [README 的下載章節](../../README.zh-Hant.md#確認-mac-下載檔已簽章並經過公證)。

### 想用原始碼執行？

見 [從原始碼建置](07-development.md)。

## 1.3 切換成繁體中文介面

1. 開啟 App，打開 **Settings（設定）**（標題列上的齒輪圖示）。
2. 進入 **General（一般）** → **Language（語言）**。
3. 選 **繁體中文**。

| 選項 | 行為 |
|------|------|
| 跟隨系統（System） | 依作業系統語言自動挑選；系統是 `zh-TW`、`zh-HK`、`zh-MO` 或明確標示 `Hant` 時會選繁體中文 |
| 繁體中文 | 不管系統語言，固定顯示繁體中文 |

> 為什麼有些字仍是英文？依設計，**你輸入的文字、AI 的回答、證據與原始工具輸出、記錄檔與匯出的報告**都保留原文；
> 產品名稱與技術名稱（Ghidra、Markdown、MCP…）也不翻譯。介面上的按鈕、選單、說明文字則全部是繁體中文。

## 1.4 第一個專案：重現一個網站

1. 在首頁「**你想製作什麼？**」選 **網站**。
2. 輸入網址，例如 `https://example.com`。
3. 模式保留預設的 **淨室**（只看外觀，不讀原始碼）。
4. 在需求欄輸入你的需求，中英文皆可，例如：
   - `重現這個網站`
   - `重現這個網站，但改成深色主題`
   - `用 React + Tailwind 重現這個網站`（指定技術堆疊）
5. 按 **開始**。

接下來你會在專案頁看到：

| 區塊 | 內容 |
|------|------|
| 步驟列 | 研究 → 建置 → 檢查 → 就緒，目前進行到哪一步 |
| 動態 | 用白話說明 Agent 正在做什麼 |
| 計畫 | 共有幾個步驟、完成了幾個 |
| 卡片 | Agent 的**問題**（點按鈕回答）或**核准**請求 |
| 相似度 | 每次檢查後，各頁面與原作有多接近 |

### 遇到核准卡片怎麼辦？

Agent 想執行規則以外的動作（例如第一次跑 `npm install`）時會詢問你：

| 按鈕 | 意思 | 建議 |
|------|------|------|
| 允許一次 | 只允許這一次 | 不確定時選這個 |
| 此專案中一律允許 | 這個專案之後同類指令都不再問 | 只對你信任其相依套件的專案使用 |
| 不允許 | 拒絕 | Agent 會換別的做法或回報 |

## 1.5 第二個專案：做一個遊戲

1. 選 **遊戲**。
2. 不需要目標，直接描述：`用 Rust 做一個貪食蛇遊戲`。
3. 按 **開始**，完成後按 **執行遊戲**。

也可以指定 Unity、Unreal 或 Godot（需要已安裝對應引擎）。

## 1.6 從命令列做同樣的事

```sh
craft-anything-cli doctor                                           # 先檢查環境
craft-anything-cli rebuild https://example.com "重現這個網站"
craft-anything-cli rebuild - "用 Rust 做一個貪食蛇遊戲" --rounds 1
```

`-` 代表沒有目標。完整說明見 [CLI 與 MCP 完整指南](04-cli-and-mcp.md)。

## 下一步

- 想熟悉所有畫面 → [桌面 App 操作指南](02-app-guide.md)
- 想換用其他 AI → [AI 與編碼 Agent 設定](03-ai-setup.md)
- 出問題了 → [疑難排解](06-troubleshooting.md)
