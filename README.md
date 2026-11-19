# Raco 跟單包（raco-trader-pack）

給 Claude Code 用的跟單工具包。美國國會議員一申報買股票、大戶一交出季度持股，Claude 一下就整理成表，再照你的預算分好每一檔要買多少。

裡面是 [Trader Skill](https://github.com/Jakeschincariol/trader-skill)（Jake Schincariol 作，MIT 授權）的十一個 skill，Raco 幫你設定好了：

- **設定檔填好了**：`trader/config.json` 預算 5000 美元、留一成備用現金、單一檔最多 25%、模擬交易
- **中文 CLAUDE.md**：Claude 一律用繁體中文回答、數字整理成表格、什麼事該叫哪個 skill
- **中文說明.md**：十一個 skill 各用一句話說它做什麼、怎麼叫它

下載下來、用 Claude Code 打開就能用。

---

## 需要什麼

- **Claude Code**：幫你安裝、幫你跑 skill 的 AI
- **Python 3.8 以上**：不用另外裝任何套件
- **Git**：下載這個專案用
- **網路**：讀美國證交會、眾議院和新聞網站的公開資料，不用註冊、不用 API 金鑰

Python 和 Git 沒有的話，Claude 安裝時會幫你裝。

## 安裝

在電腦上開一個新資料夾，用 Claude Code 打開，貼上這段話：

```
請幫我安裝 Raco 跟單包：https://github.com/ShabiSung/raco-trader-pack
照 README 裡「給 AI 的安裝步驟」做，裝好後跑一次確認能用，再教我怎麼用。
```

想自己動手的話，下載下來直接用 Claude Code 打開：

```bash
git clone https://github.com/ShabiSung/raco-trader-pack.git
cd raco-trader-pack
claude
```

## 使用方式

直接用自然的話跟 Claude 說，或打 `/trader-copycat` 這樣的指令。十一個 skill 會接力：

```
跟大戶：    copycat → budget → risk → broker
盯盤：      news、pelosi、alerts（可以排程固定跑）
自己選股：  analyst → screener → budget → risk → broker
定期維護：  rebalance、tax → risk → broker
```

| skill | 做什麼 |
| --- | --- |
| `trader-copycat` | 讀大戶的 13F 持股申報，算出每一檔的比例，跟上一季比 |
| `trader-pelosi` | 讀美國眾議員的股票交易申報，PDF 整理成表 |
| `trader-budget` | 照你的預算、備用現金、單檔上限分配每一檔 |
| `trader-news` | 盯持股的新聞和 8-K 公告 |
| `trader-analyst` | 抓財報數字，跟去年同一季比 |
| `trader-screener` | 照你的條件篩股票、排名 |
| `trader-risk` | 每張單送出前檢查有沒有超過上限 |
| `trader-broker` | 一張一張給你看，你打 yes 才送出 |
| `trader-tax` | 記成本、算損益、找節稅部位 |
| `trader-alerts` | 價格到了、開盤了就提醒你 |
| `trader-rebalance` | 比例跑掉時算出怎麼拉回來 |

每一個怎麼叫、設定檔每個欄位的意思，都在 [中文說明.md](中文說明.md)。想改預算或規則，直接跟 Claude 說「把預算改成 3000 美元」。

預設是模擬交易、沒有接任何券商。想接真的券商帳戶，跟 Claude 說「用 trader-broker 帶我接 Alpaca 模擬帳戶」，它會一步一步帶你做。

## 五個常用跟單指令

裝好之後，直接貼這五句。

**1. 國會議員最近買了什麼**

```
用 trader-pelosi 列出最近兩週美國眾議員申報的股票交易，整理成表：議員、股票、買或賣、金額區間、交易日、申報日
```

**2. 跟巴菲特買一樣的股票**

```
用 trader-copycat 抓波克夏最新一季的 13F，把前五大持股算成比例，再跟上一季比，列出新買進和賣掉的
```

**3. 照你的預算，分好每一檔**

```
用 trader-budget 幫我把 5000 美元照剛剛的比例分配，留一成備用現金，單一檔不超過 25%
```

**4. 持股一有新聞就標出來**

```
用 trader-news 看 AAPL AXP GOOGL KO BAC 過去 24 小時的新聞和 8-K 公告，跟我持股有關的標出來
```

**5. 你說好，才下單**

```
用 trader-risk 檢查這幾張單，過了再用 trader-broker 一張一張給我看，我說 yes 才用模擬帳戶送出
```

---

## 給 AI 的安裝步驟

> 這一節寫給幫使用者安裝的 AI 助理（例如 Claude Code）。請依序執行，每一步確認成功再繼續。安裝位置是使用者目前打開的資料夾。

### 1. 檢查環境

```bash
python3 --version   # Windows 用 python --version 或 py --version，要 3.8 以上
git --version
```

- 沒有 Python：Windows 用 `winget install Python.Python.3.12`，macOS 用 `brew install python`，或請使用者到 python.org 下載。
- 沒有 Git：Windows 用 `winget install Git.Git`，macOS 用 `xcode-select --install`。
- 裝完要重開終端機才找得到指令。

### 2. 把跟單包放進目前的資料夾（擇一）

**方式 A：複製進目前的資料夾（推薦）**

macOS／Linux：

```bash
rm -rf /tmp/raco-trader-pack
git clone --depth 1 https://github.com/ShabiSung/raco-trader-pack.git /tmp/raco-trader-pack
mkdir -p .claude/skills trader
cp -R /tmp/raco-trader-pack/.claude/skills/trader-* .claude/skills/
[ -f trader/config.json ] || cp /tmp/raco-trader-pack/trader/config.json trader/
[ -f CLAUDE.md ] || cp /tmp/raco-trader-pack/CLAUDE.md .
cp /tmp/raco-trader-pack/中文說明.md .
```

Windows（PowerShell）：

```powershell
Remove-Item -Recurse -Force "$env:TEMP\raco-trader-pack" -ErrorAction SilentlyContinue
git clone --depth 1 https://github.com/ShabiSung/raco-trader-pack.git "$env:TEMP\raco-trader-pack"
New-Item -ItemType Directory -Force ".claude\skills", "trader" | Out-Null
Copy-Item -Recurse -Force "$env:TEMP\raco-trader-pack\.claude\skills\trader-*" ".claude\skills\"
if (-not (Test-Path "trader\config.json")) { Copy-Item "$env:TEMP\raco-trader-pack\trader\config.json" "trader\" }
if (-not (Test-Path "CLAUDE.md")) { Copy-Item "$env:TEMP\raco-trader-pack\CLAUDE.md" "." }
Copy-Item "$env:TEMP\raco-trader-pack\中文說明.md" "."
```

- 資料夾裡本來就有 `CLAUDE.md` 的話不要覆蓋，把跟單包 `CLAUDE.md` 的內容接在原本檔案的最後面（用 UTF-8 存）。
- 本來就有 `trader/config.json` 的話保留使用者原本的設定，不要蓋掉。

**方式 B：Claude Code 外掛**（想在每個資料夾都能叫到這些 skill 時）

```bash
claude plugin marketplace add ShabiSung/raco-trader-pack
claude plugin install raco-trader-pack@raco-trader
```

- 已經加過這個 marketplace 的話，先執行 `claude plugin marketplace update raco-trader`。
- 外掛裝的 skill 名稱會變成 `/raco-trader-pack:trader-copycat` 這樣。
- 設定檔還是要放在使用者工作的資料夾：照方式 A 的最後三行，把 `trader/config.json`、`CLAUDE.md`、`中文說明.md` 複製過來。

### 3. 跑一次確認能用

在安裝的資料夾執行（Windows 先設 `$env:PYTHONIOENCODING="utf-8"`，`python3` 換成 `python` 或 `py`）：

```bash
python3 .claude/skills/trader-copycat/edgar13f.py filings 1067983
python3 .claude/skills/trader-alerts/alerts.py clock
```

- 第一行會連美國證交會，看到波克夏（BERKSHIRE HATHAWAY INC）的 13F-HR 申報清單就是成功。
- 第二行會印出紐約時間和美股現在有沒有開盤。
- 證交會回 403 的話：問使用者的英文名字和 Email，寫進 `trader/config.json` 的 `sec_user_agent`（格式 `"Name name@example.com"`），再跑一次。
- 用方式 B 安裝的話，腳本在 `~/.claude/plugins/` 底下，用 `find ~/.claude -name edgar13f.py` 找到路徑再跑。

### 4. 告訴使用者

- 請在這個資料夾重新開啟 Claude Code，skill 才會載入。
- 先試「五個常用跟單指令」的第一句：`用 trader-pelosi 列出最近兩週美國眾議員申報的股票交易，整理成表`。
- 十一個 skill 一句話的說明在 `中文說明.md`；預設是模擬交易、沒有接任何券商。

---

## 檔案結構

```
.claude/skills/                  十一個 skill（Trader Skill 原檔，沒有改動）
  trader-copycat/                13F 持股反推（edgar13f.py、filers.json…）
  trader-pelosi/                 國會議員股票申報（ptr.py）
  trader-budget/                 預算分配（size.py）
  trader-news/                   持股新聞（news.py）
  trader-analyst/                財報數字（facts.py）
  trader-screener/               選股條件（screen.py）
  trader-risk/                   下單前風控（check.py）
  trader-broker/                 下單確認（ticket.py）
  trader-tax/                    成本與損益（lots.py）
  trader-alerts/                 價格提醒（alerts.py）
  trader-rebalance/              再平衡（rebalance.py）
trader/config.json               設定好的設定檔
CLAUDE.md                        給 Claude 的中文規則
中文說明.md                      十一個 skill 的中文說明與設定欄位
.claude-plugin/plugin.json       外掛設定
.claude-plugin/marketplace.json  外掛市集設定（讓 claude plugin 指令可以直接安裝）
LICENSE                          MIT 授權原文
```

## 出處與授權

十一個 skill 改自 [Jakeschincariol/trader-skill](https://github.com/Jakeschincariol/trader-skill)，作者 [Jake Schincariol](https://opusjake.ai)，以 MIT 授權釋出，授權原文見 [LICENSE](LICENSE)。`.claude/skills/` 裡的檔案保持原樣；Raco 加上的設定檔、`CLAUDE.md`、中文說明與這份 README 同樣以 MIT 授權釋出。

資料來自美國證交會 EDGAR、美國眾議院書記處、新聞 RSS 與 Yahoo 財經的公開資料，使用時請遵守各網站的使用規範。

## 免責聲明

這是工具教學，不是投資建議。這些 skill 讀公開資料、做計算，不知道你的財務狀況；跟著大戶或議員買，不代表會有一樣的結果。任何交易前請自己評估風險。
