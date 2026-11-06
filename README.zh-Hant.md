<!-- 本文件是 README.md 的繁體中文（台灣）翻譯；英文版有改動時請一併更新本文件。 -->

<p align="center">
  <a href="https://getartcraft.com/">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="docs/brand/artcraft-logo-white.svg">
      <img alt="ArtCraft" src="docs/brand/artcraft-logo.svg" width="200">
    </picture>
  </a>
</p>

<p align="center">
  <img src="assets/app-icon/hicolor/256x256/apps/ai.storyteller.craft-anything.png" width="128" height="128" alt="Craft Anything 圖示：藍色底塊上雕刻的烏鴉">
</p>

<h1 align="center">Craft Anything</h1>

<p align="center">
  <a href="README.md">English</a> · <a href="README.ja.md">日本語</a> · <a href="README.zh.md">简体中文</a> · <b>繁體中文</b> · <a href="README.ko.md">한국어</a>
</p>

<p align="center">
  <b>看懂任何網站、App 或遊戲是怎麼做出來的，再讓 AI 幫你做出自己的版本；一套以純 Rust 撰寫的開源淨室（Clean room）引擎。</b><br>
  指定目標、說出你的需求，它就會用真正的檢查工具研究原作，<br>
  讓編碼 Agent 建立新專案，把成果與原作比對，再反覆修正到夠接近為止。
</p>

<p align="center">
  <img alt="100% Rust" src="https://img.shields.io/badge/100%25-Rust-b7410e?style=flat-square&logo=rust">
  <img alt="macOS · Windows · Linux · FreeBSD · Web" src="https://img.shields.io/badge/macOS%20%C2%B7%20Windows%20%C2%B7%20Linux%20%C2%B7%20FreeBSD%20%C2%B7%20Web-native-2d81ff?style=flat-square">
  <img alt="授權：MIT 或 Apache-2.0" src="https://img.shields.io/badge/license-MIT%20%2F%20Apache--2.0-3a3a3a?style=flat-square">
  <img alt="狀態：早期 Alpha" src="https://img.shields.io/badge/status-early%20alpha-d69e2e?style=flat-square">
</p>

<p align="center">
  <a href="https://discord.gg/artcraft"><img alt="加入 ArtCraft 的 Discord 社群" src="https://img.shields.io/badge/Join%20us%20on%20Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white" height="40"></a>
</p>

<p align="center">
  <a href="#下載"><b>下載 Craft Anything</b></a> ·
  <a href="https://getartcraft.com/">ArtCraft</a> ·
  <a href="https://getartcraft.com/apps">所有 Crafting App</a> ·
  <a href="docs/zh-hant/README.md"><b>繁體中文使用說明</b></a>
</p>

<br>

<p align="center">
  <img src="docs/images/project-website.png" alt="Craft Anything 裡的網站專案：檢查結果列出尚未相符之處與各頁面的相似度，建置助手表示版本已完成，核准卡片在執行指令前先詢問，計畫顯示四個步驟已完成兩個" width="100%">
  <br>
  <sub>網站專案：每次檢查都會說明成果有多接近原作，規則以外的動作都要你核准，計畫則顯示還剩哪些步驟。</sub>
</p>

> [!NOTE]
> **ArtCraft 是一個來自各行各業的創作者社群。** 畫家、攝影師、影像創作者、插畫家、設計師、
> 動畫師、業餘愛好者，以及上週才剛拿起鉛筆的人。只要你在創作，你就是我們的一份子。
> **[來 Discord 打聲招呼吧](https://discord.gg/artcraft)。**

<p align="center">
  <a href="#運作方式">運作方式</a> ·
  <a href="#兩種使用方式">兩種使用方式</a> ·
  <a href="#ai">AI</a> ·
  <a href="#為-agent-而設計">Agent</a> ·
  <a href="#技術核心">技術核心</a> ·
  <a href="#開始使用">開始使用</a> ·
  <a href="#目前狀態">目前狀態</a> ·
  <a href="#下載">下載</a> ·
  <a href="https://discord.gg/artcraft">Discord</a>
</p>

<br>

<table>
  <tr>
    <td width="25%" valign="top">
      <h3>🔍 研究任何東西</h3>
      網站、桌面應用程式、手機 App、遊戲和檔案：用真正的檢查工具找出它們如何建構、做了什麼，每個答案都附上證據。
    </td>
    <td width="25%" valign="top">
      <h3>🛠️ 做出自己的版本</h3>
      說出你要什麼（「重現這個網站」、「用 Rust 重現這款遊戲的玩法」），編碼 Agent 就會用你指定的技術堆疊建立新專案。
    </td>
    <td width="25%" valign="top">
      <h3>✅ 實測比對，不靠猜</h3>
      每次建置都會與原作比對（網頁在三種螢幕寬度下比對、遊戲跑自己的自我測試、App 實際執行），反覆修正到夠接近為止。
    </td>
    <td width="25%" valign="top">
      <h3>🤖 為 Agent 準備好</h3>
      320 個工具，每個都是一個 CLI 指令，也是一個 MCP 工具，任何 Agent 都能驅動與 App 相同的引擎。單純檢查不需要 AI。
    </td>
  </tr>
</table>

<br>

## 運作方式

把 Craft Anything 指向一個目標、說出你要什麼，然後按下「開始」。它分四個步驟進行，
遇到需要你決定的事，會用白話問你：

1. **研究（Study）**：檢查原作：各種寬度下的頁面、它的程式碼、檔案，以及它如何執行。
2. **建置（Build）**：編碼 Agent 用你需求中指定的語言、框架或引擎（沒指定就用合理的預設）
   建立新專案。
3. **檢查（Check）**：把成果與原作比對，列出還不相符的地方。
4. **就緒（Ready）**：開啟、遊玩或預覽成果；也可以提出修改，它會繼續改進。

<table>
  <tr>
    <td width="50%" valign="top">
      <img src="docs/images/make.png" alt="「製作」畫面：選擇要重現網站、App 或遊戲，輸入網址，選擇淨室或深度模式，說出需求後按下開始" width="100%"><br>
      <sub><b>製作：</b>選擇要重現什麼、說出需求、按下開始。</sub>
    </td>
    <td width="50%" valign="top">
      <img src="docs/images/project-game.png" alt="已完成的貪食蛇遊戲專案，所有步驟都已完成，並有「執行遊戲」按鈕" width="100%"><br>
      <sub><b>就緒：</b>直接玩遊戲，或在下方提出修改。</sub>
    </td>
  </tr>
</table>

## 兩種使用方式

- **簡易模式**（預設）：「你想做什麼？」選擇網站、App 或遊戲，描述它，然後按下開始。
  專案頁面會顯示四個步驟（研究 → 建置 → 檢查 → 就緒）、用白話說明 Agent 正在做什麼、
  問題與核准卡片、成果與原作的相似度，以及開啟成果或其資料夾的按鈕。想修改就在下方的輸入框提出。
  「只檢視內部」會開啟目標讓你提問，但不建置任何東西（需在深度模式下）。
- **進階模式**：完整的工作台：所有工具（Ctrl/Cmd+K）、函式、字串、虛擬 C 碼（pseudo-C）、
  反組譯、十六進位檢視、呼叫圖、證據、未解問題、工作、記錄檔和 Agent 主控台。

兩種模式驅動的是同一個引擎。

## 淨室或深度

- **淨室（Clean room）**（預設）：不讀取原作內部的任何東西，包括程式碼、檔案或網頁原始碼。
  Craft Anything 只根據看得到的東西工作：你加入的圖片、訪客眼中的網站（螢幕截圖、可見文字、
  量測到的版面、顏色、字型與間距、展開的選單），以及在你允許時，App 視窗的畫面。
  會去看內部的工具會被拒絕，並說明如何開啟深度模式。
- **深度（Deep）**：使用所有工具，供你私人使用。程式的程式碼與檔案會由內建引擎讀取
  （有安裝 Ghidra 時也會用 Ghidra），網站的 JavaScript 會被讀取、首頁載入過程會被監看，
  建置助手可以運用以上所有資訊。

淨室是規範 Craft Anything 提供與拒絕哪些操作的規則，**不是**作業系統層級的沙盒：它不會隔離你的電腦。
你執行的編碼 Agent 仍以你的身分行動，它能讀、寫或執行什麼，取決於它自己的權限。
Craft Anything 能拒絕 Agent 自帶的哪些工具，依 Agent 而有所不同；Claude Code 在聊天與提問時的工具，請見
[`docs/claude-code.md`](docs/claude-code.md)。

可在標題列或「設定」切換，也可以用 `settings_set deep_mode true`，或在單次 CLI 執行時加上
`--deep`（或 `--clean-room`）。

<p align="center">
  <img src="docs/images/workbench.png" alt="進階工作台：函式清單、所選函式的虛擬 C 碼、Agent 透過工具呼叫回答問題，以及證據帳本" width="100%">
  <br>
  <sub><b>進階模式：</b>函式、虛擬 C 碼、Agent，以及每個答案背後的證據。</sub>
</p>

## AI

建置需要你電腦上有一個編碼 Agent。優先支援 **Claude Code**：Craft Anything 執行的是你自己安裝、
未經修改的 `claude` 程式，使用你自己的登入；Craft Anything 從不接觸或處理你的帳號。
你的使用受你與 Anthropic 之間的協議規範，原因與做法請見 [docs/claude-code.md](docs/claude-code.md)。
Codex 的轉接器已經寫好，但尚未在真實安裝環境中驗證。**GitHub Copilot** 可以透過 ACP 使用你已安裝的
Copilot CLI：請另外用 `copilot login` 登入。其他支援 ACP 的 Agent（Gemini CLI、Qwen Code、opencode、
goose、Hermes、Maki）可以依後端切換使用；其中只有 Maki 實際跑過。Anthropic、OpenAI、OpenRouter
以及本機的 OpenAI 相容伺服器，可用於提問與分析（設定 → AI）。

## 為 Agent 而設計

每個工具同時也是 CLI 子指令和 MCP 工具，所以 Agent 不需要 App 就能研究目標，甚至跑完整個重建流程：

```sh
craft-anything-cli commands                                   # 依工具集列出所有工具
craft-anything-cli mcp                                        # 把工具提供給任何 MCP 用戶端
craft-anything-cli rebuild https://example.com "Recreate this website"
```

結果是附帶信心程度、涵蓋範圍與限制的證據紀錄；內容較大的結果會先回傳摘要，再用
`core_evidence_browse` 分頁瀏覽。`craft-anything-cli setup` 可把 Craft Anything 連接到各種編碼 Agent App。

## 技術核心

一套純 Rust 的逆向工程引擎，共 320 個工具（引擎本身 300 個，其餘來自 AI 層與設定層），
涵蓋原生程式、.NET、Java、Android、Apple、Electron/JavaScript、網站、韌體、封包擷取、資料庫與遊戲檔案。
它自己的解碼器、反組譯器與反編譯器不依賴任何外部程式即可運作；深度模式會全部用上，有安裝 Ghidra 時也會使用它。
它驅動桌面 App（egui）、CLI 與 MCP 伺服器，部分功能還能以 WebAssembly 在瀏覽器中執行。
完整清單見 [docs/tools.md](docs/tools.md)。

## 開始使用

需要 Rust **1.97 以上**（某個相依套件需要；執行 `rustup update`）。在 Debian 或 Ubuntu 上，
桌面 App 另外需要：

```sh
sudo apt-get install libxkbcommon-dev libwayland-dev libx11-dev libxrandr-dev libxi-dev libgl1-mesa-dev libgtk-3-dev
```

接著：

```sh
git clone https://github.com/storytold/craft-anything && cd craft-anything
cargo run --release -p craft-anything                # 桌面 App
cargo install --path apps/craft-anything-cli         # CLI，安裝後指令為 `craft-anything-cli`
craft-anything-cli doctor                            # 檢查安裝、編碼 Agent 與引擎
```

建置需要你電腦上有一個已登入的編碼 Agent（見 [AI](#ai)）；`doctor` 會告訴你缺了什麼。
從命令列重建（問題會在終端機中詢問）：

```sh
craft-anything-cli rebuild https://example.com "Recreate this website"
craft-anything-cli rebuild - "A snake game in Rust" --rounds 1
craft-anything-cli rebuild https://example.com "Make the header blue" --continue-in "<專案資料夾>"
craft-anything-cli rebuild - "Add a contact form" --project-folder "<你的資料夾>" --existing edit
```

需求文字也可以直接寫中文，例如 `"用 Rust 做一個貪食蛇遊戲"`。

新專案預設放在 `Documents/Craft Anything`（可在 設定 → 一般 更改）。每個專案資料夾包含
`craft.json`（需求、目標、狀態與歷程）、`capture/`（從目標記錄下來的內容）、`app/`（產生的專案）、
`checks/`（每一次與原作的比對）、`investigation.cany/`（證據）以及 `inbox/`（待回答的問題）。

專案也可以從你挑選的資料夾開始（「開始」底下的「選擇資料夾…」，或 `--project-folder`）。
如果資料夾裡已經有檔案，你要說明它們是什麼：就是 App 本身（建置助手會直接修改），
或是起始素材（App 會建在旁邊的 `app/`）（設定 → 一般 可以記住你的選擇）。
設定 → 一般 也能列出其他資料夾中的專案；移除某個專案只會把它從清單中拿掉。

> 更完整的安裝、設定與操作教學，請見 **[繁體中文使用說明](docs/zh-hant/README.md)**。

## 目前狀態

> [!IMPORTANT]
> **狀態：** Craft Anything 處於早期 Alpha 階段。目前可用、部分可用與接下來的計畫：
>
> - **目前可用：** 桌面 App（簡易與進階模式）、CLI 與 MCP 伺服器；以 Claude Code 為建置助手，
>   針對網站、App 與遊戲的 研究 → 建置 → 檢查 → 修正 流程；網站在三種螢幕寬度下的檢查；
>   以 Unity、Unreal 或 Godot 重建遊戲；搭配 Ghidra 的深度模式；生成圖片與 3D 模型
>   （需手動開啟，使用你自己的 fal.ai 金鑰）；外掛（MCP 伺服器，以及在啟用 `wasm-plugins` 選項的版本中支援 WASM 元件）。
> - **部分可用：** 以 API 金鑰和其他編碼 Agent 進行建置（已實作，尚未實際跑過）、
>   App 與遊戲的行為檢查、少數只用替代品測試過的引擎工具。
> - **已發布：** Windows、macOS、Linux 與 FreeBSD 的安裝檔已公開在
>   [Releases 頁面](https://github.com/storytold/craft-anything/releases)；它們仍是 Alpha 版。
> - **接下來：** 代管方案（managed tier）。
>
> 細節與每個部分如何驗證，見 [docs/STATUS.md](docs/STATUS.md)；逐一工具的檢查清單見
> [docs/tool-status.md](docs/tool-status.md)；逐項計畫見 [docs/ROADMAP.md](docs/ROADMAP.md)。
> 歡迎提出 Issue，或在 [Discord](https://discord.gg/artcraft) 告訴我們哪裡壞了。

**負責任地使用。** 請只對你擁有或獲准檢查的軟體使用 Craft Anything。它絕不繞過 DRM、登入機制或反作弊系統。
重建是根據證據重現版面、風格與行為；Agent 被告知不得複製標誌、照片或文字，除非你確認自己擁有或已取得授權。

**安裝相依套件會執行程式碼。** 建置助手安裝專案相依套件時，指令會在你的電腦上執行，套件在安裝過程中也可能執行程式碼。
npm 預設會執行每個套件的安裝腳本，Rust 的 build script 和 Python 原始碼套件也會執行自己的設定程式。
遇到這個專案中沒見過的指令類型（`npm`、`cargo`…），Craft Anything 會先詢問你。
對 `npm` 選擇「一律允許」，之後所有 npm 指令都不會再詢問，因此只對你信任其相依套件的專案這麼做。
若要安裝 npm 套件但不執行它們的安裝腳本，請執行 `npm install --ignore-scripts`。

## 下載

在 GitHub 上，[最新版本](https://github.com/storytold/craft-anything/releases/latest)包含下方列出的所有版本，
[所有版本](https://github.com/storytold/craft-anything/releases)則有舊版與其說明。檔名中的 `<ver>` 是版本號，
`SHA256SUMS.txt` 列出每個檔案的總和檢查碼。

### Windows

| 版本 | 安裝檔 | 免安裝版 |
|---|---|---|
| x64（64 位元 Intel/AMD） | `craft-anything-<ver>-windows-x64.msi` | `craft-anything-<ver>-windows-x64-portable.zip` |
| arm64（Snapdragon 及其他 ARM 電腦） | `craft-anything-<ver>-windows-arm64.msi` | `craft-anything-<ver>-windows-arm64-portable.zip` |
| x86（32 位元） | `craft-anything-<ver>-windows-x86.msi` | `craft-anything-<ver>-windows-x86-portable.zip` |

安裝檔與執行檔都有程式碼簽章。免安裝版的設定會存在 App 旁邊。

### macOS

| 版本 | 檔案 | 說明 |
|---|---|---|
| App，通用版（Apple 晶片 + Intel） | `craft-anything-<ver>-macos-universal.dmg` | 已簽章並經過公證 |
| 命令列工具，通用版 | `craft-anything-cli-<ver>-macos-universal.zip` | 已簽章並經過公證；若被 macOS 擋下，見下方 |

0.2.0 之後的版本，App 內已附帶命令列工具，因此不需要另外下載 zip。只有在不使用 App、只想用命令列時才需要它。

用瀏覽器下載 zip 時，可能會出現「Apple 無法驗證 craft-anything-cli」而無法執行（單一程式無法隨身攜帶公證資訊，
macOS 必須上網查詢）。從終端機下載則不會經過這項檢查，所以請改用：

```sh
ver=0.2.0   # 改成你要的版本
curl -fLO "https://github.com/storytold/craft-anything/releases/download/v$ver/craft-anything-cli-$ver-macos-universal.zip"
ditto -x -k "craft-anything-cli-$ver-macos-universal.zip" .
mkdir -p ~/.local/bin
mv "craft-anything-cli-$ver-macos-universal/craft-anything-cli" ~/.local/bin/
~/.local/bin/craft-anything-cli --version
```

若想直接用 `craft-anything-cli` 執行，請把 `~/.local/bin` 加進你的 `PATH`。

如果你已經有一份被擋下的檔案，請按「完成」（不要按「丟到垃圾桶」），然後執行
`xattr -d com.apple.quarantine <craft-anything-cli 的路徑>`，或到 系統設定 → 隱私權與安全性 → 強制打開 允許它。

#### 確認 Mac 下載檔已簽章並經過公證

每個 Mac 版本都以 Developer ID 憑證簽章，並經 Apple 公證。若要自行檢查下載檔，請在終端機執行以下指令
（把 `<ver>` 換成版本號，路徑換成你的）：

```sh
# 磁碟映像檔：出現 "accepted" 與 "source=Notarized Developer ID" 代表通過
spctl --assess --type open --context context:primary-signature -v "craft-anything-<ver>-macos-universal.dmg"
# 公證票證已附加：會印出 "The validate action worked!"
xcrun stapler validate "craft-anything-<ver>-macos-universal.dmg"

# 打開磁碟映像檔後的 App：簽章必須驗證通過
codesign --verify --deep --strict --verbose=2 "/Volumes/<磁碟映像檔名稱>/Craft Anything.app"
spctl --assess --type execute --verbose "/Volumes/<磁碟映像檔名稱>/Craft Anything.app"

# 命令列工具：單一程式無法附帶票證，所以 macOS 會上網檢查
codesign --verify --strict --verbose=2 ~/.local/bin/craft-anything-cli
codesign -dv --verbose=4 ~/.local/bin/craft-anything-cli 2>&1 | grep Authority
```

`Authority` 那幾行應該顯示「Developer ID Application」憑證。若檢查失敗，請不要執行該檔案，
並附上輸出內容與版本標籤回報問題。

### Linux

| 格式 | x86_64 | aarch64（ARM64） | 說明 |
|---|---|---|---|
| AppImage | `craft-anything-<ver>-linux-x86_64.AppImage` | `craft-anything-<ver>-linux-aarch64.AppImage` | 到處都能跑；可用 [AppImageUpdate](https://github.com/AppImageCommunity/AppImageUpdate) 自我更新（`.zsync` 檔） |
| Flatpak | `craft-anything-<ver>-linux-x86_64.flatpak` | `craft-anything-<ver>-linux-aarch64.flatpak` | 沙盒化；`flatpak install --user <檔案>` |
| Debian/Ubuntu | `craft-anything-<ver>-linux-x86_64.deb` | `craft-anything-<ver>-linux-aarch64.deb` | |
| Fedora/RHEL/openSUSE | `craft-anything-<ver>-linux-x86_64.rpm` | `craft-anything-<ver>-linux-aarch64.rpm` | |
| Tarball | `craft-anything-<ver>-linux-x86_64.tar.gz` | `craft-anything-<ver>-linux-aarch64.tar.gz` | 解壓縮到任何位置即可 |

RISC-V（riscv64）：只有 tarball，`craft-anything-<ver>-linux-riscv64.tar.gz`。

AppImage 也內含 CLI：執行 `<AppImage> --cli <指令>`（例如給 MCP 用戶端用的 `<AppImage> --cli mcp`），
或建立一個指向 AppImage、名為 `craft-anything-cli` 的連結再執行它。

### FreeBSD

| 版本 | 檔案 |
|---|---|
| x86_64 | `craft-anything-<ver>-freebsd-x86_64.tar.gz` |

### 網頁版（WebAssembly）

| 版本 | 檔案 | 說明 |
|---|---|---|
| 瀏覽器檢視器 | `craft-anything-web-<ver>.zip` | 在瀏覽器中判斷檔案是什麼（檔案不會離開瀏覽器）；把資料夾放到任何靜態網頁伺服器即可（[packaging/web/README.md](packaging/web/README.md)） |

<br>

<p align="center">
  <a href="https://discord.gg/artcraft"><img alt="加入 ArtCraft 的 Discord 社群" src="https://img.shields.io/badge/Join%20us%20on%20Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white" height="40"></a>
</p>

<h3 align="center">一起來創作吧</h3>

<p align="center">
  我們的 Discord 聚集了各種創作者：畫畫的、拍照的、畫插圖的、剪片的、排版的，
  還有仍在摸索自己想做什麼的人。分享你正在做的東西、尋求協助、告訴我們哪裡壞了，
  或告訴我們你希望這些工具能做到什麼。不論你的媒材是什麼、做了多久，這裡都歡迎你。
</p>

<p align="center">
  <a href="https://discord.gg/artcraft"><b>discord.gg/artcraft</b></a> ·
  <a href="https://getartcraft.com/">getartcraft.com</a> ·
  <a href="https://getartcraft.com/apps">Crafting App 系列</a> ·
  <a href="https://github.com/storytold/craft-anything">Craft Anything</a>
</p>

---

## Craft App 系列

| | App | 用途 | 原始碼 | 了解更多 |
|:-:|---|---|---|---|
| <img src="https://raw.githubusercontent.com/storytold/photocraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.photocraft.png" alt="" width="32" height="32"> | **PhotoCraft** | 影像編輯：圖層、遮色片、文字與真正的 PSD 檔 | [GitHub](https://github.com/storytold/photocraft) | [網站](https://getartcraft.com/apps/photocraft) |
| <img src="https://raw.githubusercontent.com/storytold/vectorcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.vectorcraft.png" alt="" width="32" height="32"> | **VectorCraft** | 向量插畫 | [GitHub](https://github.com/storytold/vectorcraft) | [網站](https://getartcraft.com/apps/vectorcraft) |
| <img src="https://raw.githubusercontent.com/storytold/filmcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.filmcraft.png" alt="" width="32" height="32"> | **FilmCraft** | 影片剪輯、調色與音效 | [GitHub](https://github.com/storytold/filmcraft) | [網站](https://getartcraft.com/apps/filmcraft) |
| <img src="https://raw.githubusercontent.com/storytold/lightcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.lightcraft.png" alt="" width="32" height="32"> | **LightCraft** | 相片圖庫與 RAW 顯像 | [GitHub](https://github.com/storytold/lightcraft) | [網站](https://getartcraft.com/apps/lightcraft) |
| <img src="https://raw.githubusercontent.com/storytold/pdfcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.pdfcraft.png" alt="" width="32" height="32"> | **PdfCraft** | 閱讀、整理與保護 PDF | [GitHub](https://github.com/storytold/pdfcraft) | [網站](https://getartcraft.com/apps/pdfcraft) |
| <img src="https://raw.githubusercontent.com/storytold/effectcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.effectcraft.png" alt="" width="32" height="32"> | **EffectCraft** | 動態圖像與視覺特效 | [GitHub](https://github.com/storytold/effectcraft) | [網站](https://getartcraft.com/apps/effectcraft) |
| <img src="https://raw.githubusercontent.com/storytold/designcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.designcraft.png" alt="" width="32" height="32"> | **DesignCraft** | 版面編排與出版 | [GitHub](https://github.com/storytold/designcraft) | [網站](https://getartcraft.com/apps/designcraft) |
| <img src="https://raw.githubusercontent.com/storytold/figcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.figcraft.png" alt="" width="32" height="32"> | **FigCraft** | 介面設計與原型製作 | [GitHub](https://github.com/storytold/figcraft) | [網站](https://getartcraft.com/apps/figcraft) |
| <img src="https://raw.githubusercontent.com/storytold/wordcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.wordcraft.png" alt="" width="32" height="32"> | **WordCraft** | 文書處理與文件 | [GitHub](https://github.com/storytold/wordcraft) | [網站](https://getartcraft.com/apps/wordcraft) |
| <img src="https://raw.githubusercontent.com/storytold/soundcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.soundcraft.png" alt="" width="32" height="32"> | **SoundCraft** | 錄音與音訊編輯 | [GitHub](https://github.com/storytold/soundcraft) | [網站](https://getartcraft.com/apps/soundcraft) |
| <img src="https://raw.githubusercontent.com/storytold/deckcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.deckcraft.png" alt="" width="32" height="32"> | **DeckCraft** | 簡報與投影片 | [GitHub](https://github.com/storytold/deckcraft) | [網站](https://getartcraft.com/apps/deckcraft) |
| <img src="https://raw.githubusercontent.com/storytold/cadcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.cadcraft.png" alt="" width="32" height="32"> | **CADCraft** | 電腦輔助設計 | [GitHub](https://github.com/storytold/cadcraft) | [網站](https://getartcraft.com/apps/cadcraft) |
| <img src="https://raw.githubusercontent.com/storytold/gridcraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.gridcraft.png" alt="" width="32" height="32"> | **GridCraft** | 試算表與資料 | [GitHub](https://github.com/storytold/gridcraft) | [網站](https://getartcraft.com/apps/gridcraft) |
| <img src="https://raw.githubusercontent.com/storytold/texturecraft/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.texturecraft.png" alt="" width="32" height="32"> | **TextureCraft** | 3D 材質繪製 | [GitHub](https://github.com/storytold/texturecraft) | [網站](https://getartcraft.com/apps/texturecraft) |
| <img src="https://raw.githubusercontent.com/storytold/craft-anything/main/assets/app-icon/hicolor/64x64/apps/ai.storyteller.craft-anything.png" alt="" width="32" height="32"> | **Craft Anything** | 看懂任何軟體如何運作，再做出你自己的版本 | [GitHub](https://github.com/storytold/craft-anything) | [網站](https://getartcraft.com/apps/craft-anything) |
| <img src="https://raw.githubusercontent.com/tangency-labs/solidcraft/main/assets/icons/app/linux/exports/hicolor/64x64/apps/io.tangencylabs.solidcraft.png" alt="" width="32" height="32"> | **SolidCraft** | Tangency Labs 開發的參數式 3D CAD（社群 App） | [GitHub](https://github.com/tangency-labs/solidcraft) | |


## 授權與致謝

Craft Anything 採 [MIT](LICENSE-MIT) 或 [Apache-2.0](LICENSE-APACHE) 雙重授權，由你擇一適用。
Copyright (c) 2026 ArtCraft Team and the Craft Anything contributors. 必要的聲明見 [NOTICE](NOTICE)。

隨附的字型、圖示、圖片與其他素材保有各自的開放授權；每一項的作者、來源與授權都列在 [ATTRIBUTION.md](ATTRIBUTION.md)。

螢幕截圖中的範例專案是為截圖而做的：example.com、一款貪食蛇遊戲，以及一個小型計算機程式。

[`docs/brand/`](docs/brand/) 中的 ArtCraft 名稱、文字標誌與標誌是 ArtCraft Team 的商標，不在本授權範圍內。
它們只能以未經修改的形式、且僅作為本 repository 與 Craft Anything 的一部分使用，依
[`docs/brand/LICENSE-brand.txt`](docs/brand/LICENSE-brand.txt) 規範。分支（fork）與修改版必須移除它們。

<p align="center">
  <a href="https://getartcraft.com/"><img alt="ArtCraft" src="docs/brand/artcraft-mark.svg" width="28"></a><br>
  <sub>由 <a href="https://getartcraft.com/">ArtCraft</a> 團隊與社群打造。</sub>
</p>

---

<sub>本頁是 [README.md](README.md) 的繁體中文（台灣）翻譯。如有出入，以英文版為準。</sub>
