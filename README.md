# 綺綺生日快樂之旅 — 釜山 4 天 3 夜行程網頁

純 HTML + CSS 製作的單頁行程時間軸，無 JavaScript、無建置流程，直接用瀏覽器開 `index.html` 即可。

## 檔案結構

```
index.html   行程內容（結構 + 資料）
style.css    視覺樣式（顏色、版面、RWD）
```

`index.html` 只透過 `<link rel="stylesheet" href="style.css">` 引用樣式，拆開兩檔是為了改樣式時不用整份 HTML 一起讀取/編輯。

## 頁面邏輯／資料結構

整頁由上到下分四個區塊：

1. **`header.hero`** — 標題、副標、`pills`（行程重點摘要：抵達日、起飛時間、人數、住宿）
2. **`div.legend`** — 圖例說明，對應後面每個行程點的顏色點與圖示意義
3. **`main.main`** — 主要行程，由多張 `div.card`（每天一張）組成
4. **`div.tips` + `footer`** — 出發前注意事項、頁尾來源說明

### 一張 `card`（一天行程）的結構

```
div.card
├─ div.card-head        當天標題列
│   ├─ span.badge       日期徽章（b0~b4 對應不同顏色）
│   ├─ div.card-info    當天主題 + 一句話摘要
│   └─ a.rt             「整天路線」按鈕 → Google Maps 多點路線連結
├─ p.note               當天提醒（⚡ 開頭）
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
            └─ a.nv       Naver 地圖連結
```

`.row:last-child` 會自動隱藏連接線（`.rline`），讓時間軸在每天最後一筆收尾乾淨，不需要額外標記。

### 顏色／class 對應邏輯

CSS 用三組獨立的顏色 class 對應同一種分類，分別用在不同元素上：

| 分類 | 時間軸點 (`rdot`) | 標籤 (`tag`) | 日期徽章 (`badge`) |
|---|---|---|---|
| 餐飲 | `df` | `tf` | — |
| 景點／體驗 | `ds` | `ts` | — |
| 交通 | `dt` | `tm` | — |
| 住宿／機場 | `dh` | — | — |
| 每日主色（依天數輪替） | — | — | `b0`～`b4` |

顏色本身定義在 `style.css` 的 `:root` CSS 變數（`--teal`、`--blue`、`--coral`、`--amber`…），改主題色只需改變數，不用逐個改 class。

### 「整天路線」連結邏輯

每張卡片標題列右側的 `a.rt`「整天路線」按鈕，連到 Google Maps 多點路線網址：

```
https://www.google.com/maps/dir/地點1/地點2/.../地點N
```

地點依當天時間軸順序、用韓文店名/地標名稱串接（空格以 `+` 取代），讓 Google 自動定位、不需要手動查座標。

> 沒有採用 Naver 地圖做多點路線，因為 Naver 的多點路線只能透過手機 App 專用的 `nmap://` deep link 開啟，電腦瀏覽器或未安裝 App 時無法使用，且官方文件未公開 waypoint 串接的確切格式，無法保證手動拼出的連結正確。個別地點仍保留 `a.nv` 的 Naver 搜尋連結。

### 外部連結三種類型

每個行程點視資料來源附上 0～3 種連結：

- `a.yt`（紅）：來源 YouTuber 影片的對應時間點，方便對照影片畫面
- `a.mp`（藍）：該地點的 Google 地圖連結
- `a.nv`（綠）：該地點的 Naver 地圖搜尋連結（部分地點適用，因 Google 地圖在韓國收錄/評論較不完整）

## RWD

`style.css` 在 `@media(max-width:600px)` 內縮小版面間距、把 `.row` 的格線欄寬從 `52px 14px 1fr` 改為 `42px 12px 1fr`，並讓 `.card-head` 允許換行，避免「整天路線」按鈕在小螢幕被擠出版面。

## 如何新增一天 / 一個行程點

- 新增一天：複製一張 `div.card` 區塊，改 `badge` 文字與顏色 class（`b0`~`b4` 任選不重複的）、改 `card-info`、改 `a.rt` 的路線連結，內部 `row` 依序增刪。
- 新增一個行程點：複製一個 `div.row` 區塊，改 `rtime`、`rdot` 顏色 class、`rn`/`rs` 文字、`tags` 內的標籤與連結。
