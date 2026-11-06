# 6. 疑難排解與常見問題

[← 回到目錄](README.md)

**第一步永遠是：**

```sh
craft-anything-cli doctor
```

它會檢查安裝、Agent 註冊、引擎、AI 後端與 MCP 伺服器，並告訴你缺了什麼。

## 6.1 啟動與畫面

| 症狀 | 可能原因 | 解法 |
|------|----------|------|
| Windows 按了沒反應、沒有視窗 | 顯示卡驅動在開窗時當掉 | App 下次啟動會自動改用較安全的 WARP 繪製；也可手動設定環境變數 `CRAFT_ANYTHING_RENDERER=software` |
| 視窗開啟時出現「Parent device is lost」 | 系統 PATH 上有不完整的 `dxcompiler.dll` | 預設已改用系統內建的 FXC；若有設定 `WGPU_DX12_COMPILER`，請移除 |
| Linux 筆電開啟很慢、整個桌面變卡 | 在獨立顯卡上繪製再複製到內顯 | 新版會在接螢幕的 GPU 上繪製；可用 wgpu 的環境變數覆寫，並查看 `desktop.log` 用的是哪張卡 |
| 老 Mac（OpenCore Legacy Patcher）視窗打不開 | Metal 著色語言版本不符 | 請更新到最新版 |
| 中文字顯示成方框 | 本機自行編譯時沒有提供字型 | 編譯前設定 `CRAFT_FONTS_DIR` 指向 craft-fonts（見[第 7 章](07-development.md)）；官方安裝檔已內含 |
| CPU 持續滿載 | 舊版的轉圈動畫每幀都要求重繪 | 請更新到最新版 |

查看記錄：設定資料夾中的 `desktop.log`（位置見[第 5 章](05-projects-and-files.md#53-設定檔位置)）。

## 6.2 找不到 Agent / 建置無法開始

| 症狀 | 解法 |
|------|------|
| 「Sign in to Claude Code」 | 終端機執行 `claude auth login` |
| macOS 從 Dock 開啟時找不到 `claude` | 新版已會搜尋 `~/.local/bin` 等位置；若仍找不到，設定 → AI 中用「選擇…」或 `ai program` 指定路徑 |
| Windows：「isn't installed or isn't on your PATH」，但明明有裝 | npm 安裝的 `.cmd` 包裝檔；新版會直接用 Node 執行。若錯誤指出缺 `node`，請安裝 Node.js |
| `MCP tool mcp__craft-anything__… not found` 或 MCP log 說 `Executable not found in $PATH: "stdio"` | Claude Code 無法啟動 `craft-anything-cli`。確認 CLI 與 App 放在一起且**檔名未被更改**，執行 `craft-anything-cli doctor` |
| 「No conversation found with session ID」 | 專案搬到別台電腦，或 Flatpak 無法寫入 `~/.claude`。新版會從 `agent-session.jsonl` 接續；Flatpak 見[第 3 章](03-ai-setup.md#flatpak-使用者注意) |
| OpenRouter 回「Provider returned error」 | 某些上游供應商不支援工具呼叫；換模型或查看錯誤中附帶的供應商原因 |
| 建置一直卡在同一個錯誤 | 連續多次建置指令失敗時會自動停止；查看專案頁的說明，或按「重試」 |

## 6.3 macOS 特有

**「Apple 無法驗證 craft-anything-cli」**

用瀏覽器下載的 CLI zip 會被擋。擇一處理：

```sh
xattr -d com.apple.quarantine ~/.local/bin/craft-anything-cli
```

或到 系統設定 → 隱私權與安全性 → 強制打開。最好的做法是改用終端機 `curl` 下載（見 README 下載章節）。

## 6.4 深度模式與引擎

| 症狀 | 解法 |
|------|------|
| 工具被拒絕，說需要深度模式 | 你在淨室模式。標題列切換到「深度」，或 CLI 加 `--deep` |
| Ghidra 已經解壓縮在「下載」資料夾，卻又重新下載 | 新版會搜尋常見位置（下載、Program Files、Homebrew cask…）；也可在 設定 → 引擎 → Ghidra 資料夾 手動指定 |
| Ghidra 分析很慢或記憶體不足 | 設定 → 引擎 → Ghidra 記憶體用量 調高 |
| 測 adb / Frida 工具時說找不到程式 | 安裝對應工具並確認在 PATH 上，或在 設定 → 引擎 指定路徑 |

## 6.5 安全相關

**Q：選了「此專案中一律允許」`npm`，安全嗎？**
之後該專案所有 npm 指令都不再詢問，而 npm 預設會執行套件的安裝腳本。只對你信任其相依套件的專案這麼做；
想避免安裝腳本可用 `npm install --ignore-scripts`。

**Q：淨室模式能保護我的電腦嗎？**
不能。淨室只限制 Craft Anything 提供哪些工具，不是作業系統沙盒。Agent 本身能做什麼，取決於它自己的權限設定。

**Q：「自動模式」會做危險的事嗎？**
自動模式下建置不再逐一詢問，但建置助手仍**不會**刪除專案外的檔案、修改系統設定或發布任何東西，並受專案素材預算限制。

**Q：Craft Anything 會看到我的 Claude 帳號嗎？**
不會。它只執行你安裝的 `claude` 程式，從不讀取、保存或轉送憑證。

## 6.6 一般問題

**Q：要付費嗎？**
Craft Anything 本身開源免費（MIT 或 Apache-2.0）。AI 使用量計入你自己的 Claude / Copilot / API 方案；生成圖片與 3D 模型需要你自己的 fal.ai 金鑰。

**Q：為什麼 AI 回答是英文？**
介面翻譯不影響 AI。用中文提問或在需求中寫「請用繁體中文回答」即可。

**Q：修正幾輪後就停了，還不夠像怎麼辦？**
在專案頁底部輸入更具體的修改要求；或在 設定 → 建置 調高修正輪數 / 「達到要求」門檻。修正輪數上限是跨多次執行累計的，按「重試」不會重置，提出新需求才會。

**Q：可以在沒有網路的電腦使用嗎？**
研究本機檔案的工具可以離線使用；研究網站與 AI 建置需要網路（除非使用本機模型）。

## 6.7 回報問題

1. 執行 `craft-anything-cli doctor`，保存輸出。
2. 找到 `desktop.log`，以及失敗的 Agent 執行所留下的記錄（錯誤訊息會指出位置）。
3. 記下版本：`craft-anything-cli --version`。
4. 到上游 GitHub Issues 或 [Discord](https://discord.gg/artcraft) 回報。

> 回報前請檢查記錄中沒有金鑰、Token 或個人路徑等敏感資訊。
