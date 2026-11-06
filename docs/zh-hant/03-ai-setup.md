# 3. AI 與編碼 Agent 設定

[← 回到目錄](README.md)

## 3.1 先搞懂：AI 在這裡做兩種事

| 用途 | 需要什麼 | 可用的後端 |
|------|----------|------------|
| **建置**（寫程式） | 能讀寫檔案、執行指令的**編碼 Agent** | Claude Code（主要支援）、GitHub Copilot CLI、其他 ACP Agent |
| **提問與分析** | 能對話的模型即可 | 上述 Agent，加上 Anthropic API、OpenAI、OpenRouter、本機 OpenAI 相容伺服器 |

不設定任何 AI 也能用：所有檢查工具都是內建引擎，**研究**不需要 AI。

### 支援程度一覽

| 後端 | 類型 | 建置 | 驗證狀態 |
|------|------|------|----------|
| Claude Code | 本機 Agent CLI | ✅ 主要支援 | 已實際跑過完整流程 |
| GitHub Copilot CLI | 透過 ACP | ✅ | 部分實測（ACP 握手與模型選單以 CLI 1.0.105 驗證過） |
| Maki | 透過 ACP | ✅ | 已實測 |
| Gemini CLI、Qwen Code、opencode、goose、Hermes | 透過 ACP | 可切換使用 | 尚未實際跑過 |
| Codex | 原生轉接器 | 已實作 | **尚未**在真實安裝驗證 |
| OpenCode V2 | 本機或 SSH | 見 3.4 | 部分 |
| Anthropic / OpenAI / OpenRouter / 本機伺服器 | API 金鑰 | 已實作、尚未實測 | 主要用於提問與分析 |

> 「已實作、尚未實測」代表程式碼與單元測試存在，但官方尚未用真實帳號完整跑過。正式使用前請先小規模測試。

## 3.2 Claude Code（建議）

### 設定步驟

1. 依 Anthropic 官方說明安裝 Claude Code。
2. 在終端機登入：
   ```sh
   claude auth login
   claude auth status     # 確認已登入
   ```
3. 開啟 Craft Anything → **設定 → AI → 偵測**，新增 **Claude Code**，按 **用它建置**。
4. 執行 `craft-anything-cli doctor` 確認一切正常。

### 它是怎麼運作的（為什麼安全）

| 項目 | 做法 |
|------|------|
| 執行方式 | 以子行程執行你自己的 `claude`，使用官方文件記載的無頭模式：`claude -p --output-format stream-json` |
| 帳號 | **不讀取、不保存、不轉送**任何憑證；登入完全在 Claude Code 內完成 |
| 費用 | 計入你自己與 Anthropic 的方案，Craft Anything 不轉售、不計量 |
| 工具 | 透過 `--mcp-config` 提供 Craft Anything 的 MCP 伺服器 |
| 權限 | 其他動作一律經過 `core_action_approve` 審核：`app/` 內編輯與固定清單的建置指令（`npm install`、`cargo build`…）自動允許；開發伺服器（不會結束）拒絕；其他都跳卡片問你 |
| 提問 | 停用 Claude Code 自己的提問工具，改用 Craft Anything 的問題卡片 |

### 用不同帳號

設定 → AI 中可為某個 Claude Code 後端指定 **Claude Code 資料夾（CLAUDE_CONFIG_DIR）**，換一個資料夾就是換一個帳號。也可以把同一個 Agent「再新增一次」，各自指定不同資料夾。CLI：

```sh
craft-anything-cli ai folder "Claude Code" ~/.claude-work
craft-anything-cli ai folder "Claude Code"          # 不給路徑 = 回到預設資料夾
```

### 調整思考強度與模型

```sh
craft-anything-cli ai effort "Claude Code"          # 查看目前等級與選項
craft-anything-cli ai effort "Claude Code" high     # low / medium / high / xhigh / max / default
craft-anything-cli ai model  "Claude Code"          # 列出可用模型
```

### Flatpak 使用者注意

Flatpak 版無法寫入家目錄，App 會自動把 Claude Code 的設定放在
`~/.var/app/ai.storyteller.craft-anything/data/craft-anything/claude`，因此要在沙盒內**另外登入一次**：

```sh
flatpak run --command=sh ai.storyteller.craft-anything -c 'CLAUDE_CONFIG_DIR="$XDG_DATA_HOME/craft-anything/claude" "$HOME/.local/bin/claude" auth login'
```

## 3.3 GitHub Copilot CLI

1. 依 GitHub 官方說明安裝獨立版 Copilot CLI。
2. 在你自己的終端機執行 `copilot login` 並完成 GitHub 登入（Craft Anything 不經手任何憑證）。
3. **設定 → AI → 偵測**，新增 **GitHub Copilot**，選擇用於聊天或 **用它建置**。
   若安裝在非預設位置，按「選擇…」指定執行檔。

`config.toml` 中對應的設定：

```toml
[[ai.backends]]
name = "GitHub Copilot"
kind = "agent_cli"
model = "copilot"
enabled = true
# 選用：
# cli_model = "<模型 id>"
# cli_context = "long_context"   # Standard = default，Extended = long_context
# cli_thinking = "high"
```

注意事項：

- ACP 會自動啟用；**不要**設定 `acp = false`、SSH 主機或額外的 `args`，這些組合會被拒絕。
- **Context window**（脈絡長度）是獨立的控制項：預設 / Standard / Extended。
- 模型存取權、組織政策與計費都由 GitHub 管理；Copilot 訂閱不等於無限量 API。

## 3.4 OpenCode V2

OpenCode 自己管理供應商連線與登入，請先在要執行它的電腦上設定好 OpenCode。

**桌面 App：** 設定 → AI → 偵測 → 新增 **OpenCode** 或 **OpenCode on `<主機>`**（SSH）→ 按「變更…」載入模型 → 選模型與**思考深度**（Low / Medium / High / Xhigh / Max，只列出該模型支援的）→ 套用。

**CLI：**

```sh
craft-anything-cli ai add OpenCode --kind agent_cli --model opencode
craft-anything-cli ai model OpenCode                                  # 列出模型
craft-anything-cli ai model OpenCode my-provider/my-model --thinking high
craft-anything-cli ai model OpenCode --default                       # 交給 OpenCode 預設

# 遠端主機（SSH 別名）
craft-anything-cli ai add "OpenCode on studio" --kind agent_cli --model opencode --ssh-host studio
craft-anything-cli ai program "OpenCode on studio" /opt/opencode/bin/opencode
```

> 目前限制：指定明確模型後執行會被拒絕（ACP 傳輸無法選模型，原生轉接器尚未提供 MCP 伺服器）。
> 本機使用時請清除兩個選擇並啟用 ACP，或改用其他 AI。

## 3.5 API 金鑰後端（Anthropic / OpenAI / OpenRouter / 本機）

適合提問與分析。金鑰**存在系統鑰匙圈**，且 CLI **絕不接受**直接寫在命令列上的金鑰：

```sh
# 從環境變數讀取
export ANTHROPIC_API_KEY="<你的金鑰>"
craft-anything-cli ai add Claude --kind anthropic --key-env ANTHROPIC_API_KEY

# 從 stdin 讀取
pbpaste | craft-anything-cli ai add OpenRouter --kind openrouter --key-stdin

# 本機 OpenAI 相容伺服器（例如 Ollama、LM Studio）
craft-anything-cli ai add Local --kind openai_compatible --base-url http://localhost:11434/v1 --model qwen3:14b
```

可用的 `--kind`：`anthropic`、`openai`、`openrouter`、`openai_compatible`、`agent_cli`。

> 小型本機模型（約 14B）可能無法穩定地使用工具完成建置；建議只用來提問，建置交給 Claude Code。

## 3.6 管理後端的常用指令

| 指令 | 作用 |
|------|------|
| `ai list` | 列出已設定的後端 |
| `ai detect` | 尋找金鑰、本機伺服器、Agent CLI 與 SSH 主機 |
| `ai add <名稱> --kind …` | 新增後端 |
| `ai remove <名稱>` | 移除 |
| `ai on` / `ai off <名稱>` | 啟用 / 停用（停用保留設定與金鑰） |
| `ai test <名稱>` | 送一個小請求，回報延遲 |
| `ai builder [名稱]` | 指定建置用的後端；不給名稱 = 自動選擇 |
| `ai extractor [名稱]` | 深度模式下負責取出遊戲檔案的 API 後端；不給名稱 = 關閉 |
| `ai program <名稱> [路徑]` | Agent 不在預設位置時指定執行檔 |
| `ai model <名稱> [模型]` | 設定或列出模型 |
| `ai effort <名稱> [等級]` | Claude 後端的思考強度 |

所有指令前面都加 `craft-anything-cli`。

## 3.7 在其他電腦上建置（SSH）

後端可以設定 `--ssh-host <別名>`，讓 Agent 在另一台電腦（例如家用伺服器）上執行。
注意：

- SSH 以 `BatchMode=yes` 執行，不會停下來等密碼，請先設定好金鑰登入。
- `~/.ssh/config` 中只用來連 Git 伺服器的別名（如 `github.com-work`）不會被當成建置主機。
- ssh 結束碼 255 代表 ssh 本身連線失敗，不是遠端程式的錯。
