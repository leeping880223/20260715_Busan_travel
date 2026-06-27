# 綺綺生日快樂之旅 — 釜山 4 天 3 夜行程網頁

純 HTML + CSS 製作的單頁行程時間軸，無 JavaScript、無建置流程，直接用瀏覽器開 `index.html` 即可。

## 檔案結構

```
index.html   行程內容（結構 + 資料）
style.css    視覺樣式（顏色、版面、RWD）
```

`index.html` 只透過 `<link rel="stylesheet" href="style.css">` 引用樣式，拆開兩檔是為了改樣式時不用整份 HTML 一起讀取/編輯。

## 頁面邏輯／資料結構

整頁由上到下分五個區塊：

1. **`header.hero`** — 標題、副標、`pills`（行程重點摘要：抵達日、起飛時間、人數、住宿）
2. **`div.legend`** — 圖例說明，對應後面每個行程點的顏色點與圖示意義
3. **`main.main`** 內的主要行程，由 4 張 `div.card`（每天一張，7/12～7/15）組成
4. **`div.backup-all`** — 整本行程共用的候補清單，依區域分組（見下方說明）
5. **`div.tips` + `footer`** — 出發前注意事項、頁尾來源說明

### 一張 `card`（一天行程）的結構

```
div.card
├─ input.rm-cb           隱藏的 checkbox（控制路徑圖開關，預設 checked＝展開）
├─ div.card-head        當天標題列
│   ├─ span.badge       日期徽章（b0~b4 對應不同顏色）
│   ├─ div.card-info    當天主題 + 一句話摘要
│   └─ label.rm-btn     🗺️ 路徑圖按鈕（for 對應上面的 checkbox）
├─ p.note               當天提醒（⚡ 開頭）
├─ div.routemap         當天路徑圖（見下方說明）
└─ div.row（多個）       時間軸上的每一個行程點
    ├─ div.rtime         時間
    ├─ div.rdots         時間軸圓點 + 連接線
    │   └─ div.rdot      顏色點：df 餐飲／ds 景點／dt 交通／dh 住宿機場
    └─ div.rc            內容
        ├─ div.rn        地點名稱
        ├─ div.rs        說明文字
        └─ div.tags      標籤列
            ├─ span.tag   分類標籤（tf 美食／ts 景點／tw 提醒／tm 交通）
            ├─ a.yt       YouTube 影片時間點連結
            ├─ a.mp       Google 地圖連結
            ├─ a.nv       Naver 地圖連結
            ├─ a.ac       住宿／訂房連結（紅色，目前只用在 Airbnb）
            └─ a.bl       部落格／小紅書等圖文參考連結（橙色）
```

`.row:last-child` 會自動隱藏連接線（`.rline`），讓時間軸在每天最後一筆收尾乾淨，不需要額外標記。

### 路徑圖（`div.routemap`）

每天 `p.note` 下方有一條橫向、會自動換行的「路徑圖」，把當天行程點濃縮成一排小卡片＋箭頭文字，方便不展開細項也能掃過整天動線：

```
div.routemap
├─ div.rm-node（多個）   一個停留點
│   ├─ span.rm-ico       emoji 圖示
│   └─ div.rm-meta
│       ├─ span.rm-time  時間
│       └─ span.rm-name  地點短名
└─ span.rm-edge（多個）  夾在兩個 rm-node 之間，文字＝交通方式＋所需時間
```

`rm-node` 依停留點性質加 `n-df`／`n-ds`／`n-dh`／`n-dt` 其中一個 class，顏色對應跟 `rdot` 同一套（左側色條，餐飲綠／景點藍／住宿機場紅／交通灰）。沒有實際移動資訊（例如同一帶步行可達）時，`rm-edge` 就只放交通方式文字（如「🚶 步行可達」），不硬湊時間。

**開關機制**：`input.rm-cb` 是一個 `display:none` 的 checkbox，`label.rm-btn` 透過 `for` 屬性連到它；CSS 用 `.rm-cb:checked~.routemap{display:flex}` 控制顯示，純 HTML/CSS，沒有用到 JavaScript。checkbox 預設帶 `checked`，所以路徑圖預設展開，點一下 🗺️ 路徑圖 按鈕即可收起/展開。`id`／`for` 用日期命名（`rm-712`、`rm-713`、`rm-714`、`rm-715`），新增一天時記得換一個不重複的 id。

### 候補清單（`div.backup-all`）

每天卡片裡曾經各自放過候補清單，後來整合成頁面最下方（在第 4 天卡片之後、TIPS 之前）**一個共用區塊**，依地理區域分組，而不是依日期分組：

```
div.backup-all
├─ div.backup-all-h     區塊標題
└─ div.region（多個）    一個地理區域
    ├─ div.region-h      區域標題（📍 開頭，藍字）
    └─ div.backup-item（多個）
        ├─ div.rn         地點名稱（跟 row 共用同一套樣式）
        ├─ div.rs         說明文字
        └─ div.tags       標籤列（跟 row 共用同一套連結 class）
```

目前分三個區域：廣安里・水營・南川（住宿周邊）、西面・南浦・甘川洞（西區一帶）、海雲台・청사포（東部）。新增候補地點時，先判斷它離哪個區域最近，加進對應的 `div.region`；如果出現新的地理範圍，就複製一份 `div.region` 區塊。

主行程裡換掉的店家（公休、評價差、改用更好的替代方案等）通常會移進這裡保留備查，而不是直接刪除資料。

### 顏色／class 對應邏輯

CSS 用三組獨立的顏色 class 對應同一種分類，分別用在不同元素上：

| 分類 | 時間軸點 (`rdot`) | 路徑圖節點 (`rm-node`) | 標籤 (`tag`) | 日期徽章 (`badge`) |
|---|---|---|---|---|
| 餐飲 | `df` | `n-df` | `tf` | — |
| 景點／體驗 | `ds` | `n-ds` | `ts` | — |
| 交通 | `dt` | `n-dt` | `tm` | — |
| 住宿／機場 | `dh` | `n-dh` | — | — |
| 每日主色（依天數輪替） | — | — | — | `b0`～`b4` |

顏色本身定義在 `style.css` 的 `:root` CSS 變數（`--teal`、`--blue`、`--coral`、`--amber`…），改主題色只需改變數，不用逐個改 class。

### 外部連結五種類型

每個行程點／候補項目視資料來源附上連結：

- `a.yt`（紅）：來源 YouTuber 影片的對應時間點，方便對照影片畫面
- `a.mp`（藍）：該地點的 Google 地圖連結
- `a.nv`（綠）：該地點的 Naver 地圖搜尋連結（部分地點適用，因 Google 地圖在韓國收錄/評論較不完整）
- `a.ac`（紅）：住宿／訂房類連結，跟 `a.mp` 區分開讓住宿項目一眼可辨識
- `a.bl`（橙）：部落格、小紅書等圖文筆記類參考連結，非地圖也非影片

> 小紅書（`xhslink.com`）連結目前工具端會因 TLS 憑證問題讀不到內容，只能放連結本身，註明「需用手機開」，無法在此自動補上店名說明。

### 多點路線連結（已移除）

之前每張卡片標題列右側有一個 `a.rt`「整天路線」按鈕，串接當天所有地點成 Google Maps 多點路線網址。後來整本行程改動頻繁、地點常常增刪調順序，路線連結很容易跟著過期，所以全部移除了。`style.css` 裡的 `.rt` 樣式還留著沒刪，之後如果要重新加回某天的路線按鈕，可以直接複用。

## RWD

`style.css` 在 `@media(max-width:600px)` 內縮小版面間距、把 `.row` 的格線欄寬從 `52px 14px 1fr` 改為 `42px 12px 1fr`，並讓 `.card-head` 允許換行；候補清單區塊（`.backup-all-h`、`.region-h`、`.region .backup-item`）也同步縮邊距。

## 如何新增 / 修改

- **新增一天**：複製一張 `div.card` 區塊，改 `input.rm-cb` 的 `id`（換成新日期、不重複）、`label.rm-btn` 的 `for` 要跟著改、改 `badge` 文字與顏色 class（`b0`~`b4` 任選不重複的）、改 `card-info`，內部 `row` 依序增刪，並重新整理 `routemap`。
- **新增一個行程點**：複製一個 `div.row` 區塊，改 `rtime`、`rdot` 顏色 class、`rn`/`rs` 文字、`tags` 內的標籤與連結；同時在 `div.routemap` 裡加一個對應的 `rm-node`（時間＋短名＋圖示），並補上前後的 `rm-edge`（交通方式＋所需時間）。
- **換掉某個地點**：把原本的 `div.row` 改成新地點的內容，`routemap` 裡對應的 `rm-node` 也要同步改名；如果想保留舊地點當備案，把舊內容剪到候補清單對應的 `div.region` 裡，包成一個 `div.backup-item`（候補清單不需要出現在 `routemap`）。
- **新增候補地點**：判斷地理區域，加進 `div.backup-all` 裡對應的 `div.region`；沒有合適區域就新開一個。
