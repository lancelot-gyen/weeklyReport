# 橘焱週報 — index.html 系統架構文件

> 此文件由 Claude 自動產生，每次修改 index.html 後同步更新。
> 讀取此文件取代直接讀取完整 index.html，節省 token。

---

## 一、基本資訊

| 項目 | 內容 |
|------|------|
| 檔案 | `D:\claude\claude\週報\index.html` |
| 部署 | https://lancelot-gyen.github.io/weeklyReport/ |
| Repo | https://github.com/lancelot-gyen/weeklyReport |
| 後端 | Supabase (`https://zunactakynxzgvzoazul.supabase.co`) |
| 認證 | Google OAuth via Supabase |
| AI | Anthropic Claude API（`ANTHROPIC_KEY` 常數，目前為空） |

---

## 二、CSS 架構

### 設計 Token（`:root`）
```css
--bg:#f4f4f2   --sf:#fff   --sf2:#f8f8f7
--bd:rgba(0,0,0,.08)   --bdm:rgba(0,0,0,.13)
--t:#1a1a1a   --t2:#4a4a4a   --t3:#999
--ac:#c8691a   --al:#fff7f2   --ab:#f5d5b8   /* 主色（橘）*/
--pj:#2858a0   --pjl:#eef3fc   --pjb:rgba(40,88,160,.2)  /* 跨群色（藍）*/
--sb-w:224px   --ai-w:360px
--tb-h:50px   --bb-h:56px   --mn-h:52px
--safe-b:env(safe-area-inset-bottom,0px)
```

### RWD 斷點
- `≤1024px` (tablet)：AI 面板縮至 300px，grid 單欄
- `≤768px` (mobile)：側邊欄變抽屜、底部導覽列出現、AI 面板為 bottom sheet
- `≤390px`：小手機微調

### 主要 CSS class 對照

| Class | 用途 |
|-------|------|
| `.tb` | 頂部列（黑底） |
| `.upill` | 右上角使用者膠囊 |
| `.sidebar` | 左側導覽 |
| `.sbsc` | sidebar section 容器 |
| `.sbi` | sidebar 項目（`.active`、`.done` 狀態） |
| `.sbd` | sidebar 狀態圓點（`.d-done`、`.d-act`、`.d-pj`、`.d-empty`、`.d-warn`） |
| `.sb-ai-dot` | sidebar AI 生成 badge（`✦ AI`，橘色小標籤，有 AI 報告時才顯示） |
| `.sbft` | sidebar footer（進度列） |
| `.sbi-adm` | 管理後台 sidebar 按鈕（橘色樣式） |
| `.main` | 主內容區（`.po` 時右推開 AI 面板） |
| `.page` | 各填報頁面（`.active` 顯示） |
| `.dv` / `.ev` | done view / edit view（`.h`=隱藏） |
| `.ph` | 頁面 header |
| `.scard` | section 卡片（`.xpc` 為跨群藍色） |
| `.dnc` | 已完成卡片 |
| `.dc` | 決策/呈報卡片（黃色邊） |
| `.vc` | 本週一句話卡片 |
| `.fld` | 表單欄位容器（`.s2` 跨兩欄） |
| `.cbr` | radio button 群組 |
| `.aip` | AI 面板（`.open` 顯示） |
| `.bbar` | 底部列 |
| `.mob-nav` | 手機底部導覽列 |
| `.adm-ov` | 管理後台 overlay |
| `.adm-wrap` | 管理後台容器 |
| `.adm-tbl` | 管理後台資料表格 |
| `.hist-ov` | 週報查詢 overlay（`.open` 顯示） |
| `.hist-wrap` | 週報查詢容器 |
| `.hist-ctrl` | 週次導航列 |
| `.hist-psel` | persona 選擇器（多人可見時顯示） |
| `.hist-pcard` | 單一 persona 卡片（`.on` 選中） |
| `.hist-prog` | 完成進度區塊 |
| `.hist-section` | 各 section 唯讀卡片 |
| `.hist-badge` | 完成狀態 badge（`.done`/`.empty`） |
| `.hist-radio` | radio 值顯示（`.g/.y/.r/.a/.b/.c`） |
| `.hist-brand` | 品牌資料區塊 |
| `.hist-goto` | 前往填報按鈕（僅本人本週顯示） |
| `.sbi-hist` | sidebar 週報查詢按鈕（藍色） |
| `.sbi-air` | sidebar AI 週報條目（橘色 icon，名稱，生成時間） |
| `.sbi-air-icon/.name/.time` | AI 週報條目子元素 |
| `#sbAISection` | sidebar AI 週報動態區塊（`updateSidebarAISection()` 填入） |
| `.ai-send-preview` | AI 發送 UI — 報告預覽（max-height 160px） |
| `.ai-send-opts` | AI 發送 UI — 群組勾選清單 |
| `.ai-send-opt` | 單一群組選項（`.on` 已勾選） |
| `.ai-send-btns` | 發送按鈕列（全部發送 + 發送選取） |
| `.submit-ov` | 送出週報預覽 overlay（`z:8100`，`.open` 顯示） |
| `.submit-wrap` | 送出預覽容器（max-width 900px） |
| `.submit-foot` | *(已移至 `.aifo` 行內，含取消 + 確認送出按鈕)* |
| `.submit-cancel` | 取消按鈕 |
| `.submit-confirm` | 確認送出按鈕（橘色，disabled 時灰化） |

---

## 三、HTML 結構

```
body
├── #loadingApp          載入畫面（fixed, z:8888）
├── #loginScreen         登入畫面（fixed, z:9999）
├── .tb                  頂部列
│   ├── .tbl
│   │   ├── #hamBtn     漢堡選單（手機才顯示）
│   │   ├── .logo       橘焱週報
│   │   └── #weekLabel  週次標籤
│   └── .tbr
│       ├── #admBadgeTop ⚙ 管理後台（僅管理員顯示）
│       ├── #upill       使用者膠囊（#topAv, #topName, #topRole）
│       └── #saveStatus  儲存狀態 badge
├── #ov                  Persona 切換 overlay
│   └── .pmw > #pmList  persona 清單
├── #sbOverlay           sidebar 遮罩（手機）
├── .abody               主體 flex 容器
│   ├── #sidebar         左側導覽（動態產生）
│   └── .mwrap
│       ├── #mainArea    主內容（動態產生 .page）
│       └── #aiPanel     AI 週報面板
│           ├── #aiTabs  section 分頁
│           ├── #aiGenBtn 生成按鈕
│           └── #aiBody  AI 內容
├── .bbar                底部列
│   ├── .botl: #pvb(AI預覽), #autoSaveNote
│   └── .botr: #rnote(提示文字), #nxtBtn(下一項按鈕)
├── #admOv               管理後台 overlay
│   └── .adm-wrap
│       ├── .adm-hd
│       ├── #admTabs     分頁（6個）
│       └── #admBody     動態內容
├── #submitOv            送出週報預覽 overlay（z:8100）
│   └── .submit-wrap（flex column，重用 AI 面板視覺）
│       ├── .aih（黑底）  標頭 + #submitProgBadge 進度文字
│       ├── #submitTabs  group tabs（重用 .ai-tab / .ai-section-tabs）
│       ├── #submitBody  AI 報告內容（重用 .aibl/.aitx）
│       └── .aifo         說明文字 + 取消 / 確認送出按鈕
└── #mobNav              手機底部導覽
    ├── #mn-shared       共用
    ├── #mn-groups       群組
    ├── #mn-ai           AI
    └── #mn-menu         選單
```

---

## 四、JavaScript 架構

### 常數
```javascript
const SUPA_URL = 'https://zunactakynxzgvzoazul.supabase.co';
const SUPA_KEY = 'eyJ...'; // anon key
// AI 設定不再寫死，改由 Supabase app_settings 表動態載入
const WEEK_KEY = getWeekKey(); // 格式：'2026-W15'（固定，app 生命週期內不變）
```

### 全域狀態變數
```javascript
var G = {};           // { [groupId]: { name, mgr, brands[] } }
var BRANDS = {};      // { [groupId]: string[] }
var PERSONAS = [];    // 見下方結構
var RQ = {};          // 各職能的問題模板（'營運'、'品牌策略'...）
var curPId = '';      // 目前選中的 persona ID
var ST = {};          // { [sectionId]: 'empty'|'done'|'active' }
var curPage = '';     // 目前顯示的 section ID
var savedData = {};   // { `${personaId}__${sectionId}__${fieldKey}`: value }
var aiPrompts = {};   // { [promptId]: template }
var aiReportCache = {}; // { [groupId|'xp']: ai_reports row } — 本週 AI 報告快取
var saveTimer = null;
var currentUser = null; // Supabase auth user
var appLoaded = false;  // 防止 onAuthStateChange 重複載入
var admPGEditPersonaId = null; // 管理後台群組對應：目前編輯的 persona ID
var appSettings = {};          // 從 app_settings 表載入：ai_provider/gemini_key/gemini_model/anthropic_key/anthropic_model
```

### PERSONAS 結構
```javascript
// 每個 persona 物件（從 personas 表 + persona_groups 表合併）
{
  id, name, init, color, role, note,
  has_cross_group, is_admin, email,
  xp: p.has_cross_group,     // 別名
  groups: [groupId, ...]     // 從 persona_groups 過濾
}
```

### savedData key 格式
```
`${personaId}__${sectionId}__${fieldKey}`
特殊 fieldKey:
  '__done__'         → value='1' 表示該 section 已主動完成
  'radio_st'         → 整體狀態 radio 值 (g/y/r)
  'radio_mp'         → 工作量 radio 值 (a/b/c)
  `${gid}_${brand}_q0..q3`   → 各品牌問題
  `${gid}_esc1/esc2`         → 各群決策呈報
  xp_q1..xp_q4, xp_esc1, xp_esc2 → 跨群進度
```

### Section ID 對照
```
ss    → 本週整體狀態（radio:st, textarea:note）
sm    → 人力狀態（radio:mp, textarea:pressure, conflict）
sc    → 跨群議題（textarea:cross_issue）
sv    → 本週一句話（textarea:voice）
xp    → 跨群工作進度（xp_q1~xp_q4, xp_esc1, xp_esc2）
[groupId] → 各事業群（每品牌 q0~q3, esc1, esc2）
```

---

## 五、主要函式

### 認證流程
```javascript
initAuth()              // 頁面入口：getSession → loadApp 或顯示 login
signInGoogle()          // OAuth 登入
onAuthStateChange()     // SIGNED_IN → loadApp（若 !appLoaded）; SIGNED_OUT → reset
```

### 資料載入
```javascript
async loadApp()
  // 1. groups → G{}
  // 2. brands → BRANDS{}, G[].brands
  // 3. personas → PERSONAS[]（含 pgLinks 合併）
  //    - String() 轉型比對 persona_id === p.id
  //    - 小寫比對 email 找登入者對應 persona → curPId
  // 4. ai_prompts → aiPrompts{}
  // 5. weekly_reports（本週）→ savedData{}
  // 6. buildUI()

async reloadPersonas()
  // pgSave 後重新載入 personas + persona_groups
  // 更新 PERSONAS[]，重新匹配 curPId，呼叫 buildUI()
```

### UI 建構
```javascript
buildUI()
  // 設定 topbar（name, role, groups count）
  // 初始化 ST{}
  // buildSidebar(p), buildPages(p)
  // restorePageData, restoreDoneStates, updateProg

buildSidebar(p)
  // 共用欄位 → 跨群進度（if xp）→ 我服務的群（p.groups）
  // 若 isAdmin() 加「⚙ 管理後台」按鈕

buildPages(p)
  // ss, sm, sc, sv → if(xp) buildXPPage → p.groups.forEach buildGroupPage
  // 動態寫入 #mainArea

buildGroupPage(p, gid, idx)
  // 依 G[gid].brands 建立各品牌填報欄位
  // 問題模板：getQs(p.role) → RQ[role] || RQ.default
```

### 導覽
```javascript
showPage(sid)         // 切換 .page.active，更新 sidebar 狀態
clickSb(sid)          // sidebar 點擊 → showPage
getOrderFor(p)        // ['ss','sm','sc','sv', (xp), ...groups]
goNext()              // 儲存 → 寫 __done__ → markDone → 找下一未完成 section
editSec(sid)          // 刪除 __done__ → 回編輯模式
```

### 儲存
```javascript
saveCurrentPage()     // 掃描 #p-{curPage} 的 input/textarea/radio → upsert weekly_reports
startAutoSave()       // 每 30 秒呼叫 saveCurrentPage
```

### 進度
```javascript
updateProg()          // 計算 done/total → 更新 sidebar 進度列和 #ptxt
updateBot()           // 更新底部列按鈕文字和 #rnote
markDone(sid)         // ST[sid]='done'，更新 sidebar dot，showDone
```

### 管理後台
```javascript
isAdmin()
  // 硬編碼：['lancelot@gyen.com.tw']
  // 或 persona.is_admin && email 相符

openAdmin() / closeAdmin()

loadAdmTab(tab)
  // 載入 admRelated（外鍵來源）→ 查詢資料表 → renderAdmTab
  // persona_groups 特例 → renderPGTab()

renderAdmTab(tab)     // 表單 + 表格（colRelMap 解析外鍵顯示名稱）
admStartEdit(tab,idx) // 設 admEditIdx，填報人時自動產生 note（群組名稱）
admSubmit(tab)        // insert / update
admDelete(tab,idx)    // delete

renderPGTab()         // 群組對應特製 UI（persona 下拉 + group checkboxes）
pgSave()              // delete + insert persona_groups → reloadPersonas()
pgEdit(pId)           // 設 admPGEditPersonaId → renderPGTab
pgSelectPersona(pId)  // 動態更新 checkbox 勾選狀態
pgCbChange(cb)        // 更新 checkbox label 樣式
pgClearAll(pId,pName) // 清除某 persona 的所有群組對應
```

### AI 面板
```javascript
toggleAI() / closeAI()
updateAIPanel(sid)    // 建立 group tabs，載入快取結果
generateAIReport()
  // 讀取 appSettings.ai_provider 決定呼叫哪個 API
  // provider='gemini':
  //   URL: generativelanguage.googleapis.com/v1beta/models/${model}:generateContent?key=${key}
  //   body: { contents: [{ parts: [{ text: promptText }] }] }
  //   解析: data.candidates[0].content.parts[0].text
  // provider='anthropic':
  //   URL: api.anthropic.com/v1/messages
  //   headers: x-api-key, anthropic-version, anthropic-dangerous-direct-browser-access
  //   body: { model, max_tokens:1500, messages:[{role:'user',content:promptText}] }
  //   解析: data.content[0].text
  // 結果儲存至 ai_reports 表
collectFields(sid,p)    // 組合填報資料為 AI prompt 輸入（產生 promptText）
loadCachedAIReport(sid) // 從 ai_reports 表查詢上次生成結果
```

### 系統設定（管理後台）
```javascript
renderSettingsTab()   // 自訂渲染：provider radio + key/model 輸入欄
stProvChange(radio)   // radio 切換時更新 label 樣式
settingsSave()
  // upsert app_settings 表（ai_provider/gemini_key/gemini_model/anthropic_key/anthropic_model）
  // 同步更新 appSettings{} 記憶體
  // re-render renderSettingsTab() 顯示最新狀態
```

### 送出週報預覽
```javascript
var aiReportCache = {};   // { [gid|'xp']: ai_reports row }
var submitCurTab = null;  // 目前選中的 submit tab

async loadAIReportCache()
  // 查詢 ai_reports 表（本週 + 本 persona），最新一筆 per group
  // 結果存至 aiReportCache{}

updateSidebarAIBadges()
  // 遍歷 p.groups (+ xp) 的 sidebar 元素
  // 若 aiReportCache[sid] 存在 → 插入 .sb-ai-dot（'✦ AI'）badge，badge 有 onclick → openAIFor(sid)
  // 否則移除已存在的 badge
  // 同時呼叫 updateSidebarAISection()

updateSidebarAISection()
  // 填入 #sbAISection：有任何 AI 報告時顯示「✦ AI 週報」section
  // 每份報告各自一個 .sbi-air 條目，顯示群名 + 生成時間（MM/DD HH:mm）
  // 點擊 → openAIFor(sid)

openAIFor(sid)
  // showPage(sid) → 打開 AI 面板(aiPanel.classList.add('open')) → setAITab(sid)
  // 從 sidebar +AI badge 點擊觸發

async openSubmitPreview()
  // saveCurrentPage() → loadAIReportCache()
  // 計算完成進度 → 更新 #submitProgBadge
  // 建立 #submitTabs（xp + groups）
  // 開啟 #submitOv → submitSetTab(firstTab)

submitSetTab(sid)
  // 切換 #submitTabs 選中狀態
  // 若 aiReportCache[sid] 存在：顯示報告內容（.aibl.result + .aitx）+ 複製按鈕
  // 否則：顯示「尚未生成 AI 週報」提示

submitCopyAI()         // 複製 #submitAiText 文字到剪貼簿
closeSubmitPreview()   // 關閉 #submitOv
submitOvClick(e)       // 點背景關閉

showAISendUI(content, isXP, p, gr)
  // AI 生成成功後取代 #aiBody 顯示：
  //   - 報告預覽（.ai-send-preview）
  //   - 群組勾選（isXP → 固定「Jerry」；否則顯示 p.groups 的 checkboxes，預選 currentAISection）
  //   - 「全部發送」→ aiSendAll() / 「發送選取」→ aiSendSelected()

aiSendAll()           // 全選 checkboxes → aiSendSelected()
aiSendSelected()      // 讀取勾選群組 → confirmAISend(content, false, p, groupIds)

async confirmAISend(content, isXP, p, groupIds)
  // isXP: insert 一筆 group_id=null
  // !isXP: insert 多筆，每個 groupId 一筆 ai_reports（同一 content, 同一 generated_at）
  // 成功 → renderAIResult, toast, loadAIReportCache, updateSidebarAIBadges

async confirmSubmit()
  // saveCurrentPage() → upsert weekly_reports(section_id='_submit_', field_key='submitted')
  // 關閉 modal → toast '🎉 週報已送出！' → 更新 #nxtBtn 為「週報已送出　✓」
  // 例外處理：toast 錯誤，按鈕恢復可點
```

### 其他
```javascript
getP()                // PERSONAS.find(p => p.id === curPId)
getQs(role)           // RQ[role] || RQ.default（各職能問題模板）
switchP(id)           // 切換 curPId → buildUI
toggleOv() / closeOv() // Persona 切換 modal
toast(msg, color)     // 2.6s 浮動通知
startIdleTracking()   // 10 分鐘閒置自動登出
reloadPersonas()      // 重新從 DB 載入 PERSONAS 並 buildUI
escapeHtml(t)         // XSS 防護
```

### 週報查詢（Hist）
```javascript
// 狀態
var histState = { weekOffset: 0, personaId: null };
// weekOffset: 0=本週, -1=上週, -2=兩週前...

// 權限
getViewablePersonaIds()
  // 回傳可查看的 persona ID 陣列
  // 自己: 永遠包含
  // 管理者(isAdmin): 所有人
  // 事業群負責人(名字在 G[gid].mgr): 該群所有成員

canEditHist(pid)
  // 僅本人(email 比對) 可修改

// 週次工具
getWeekKeyByOffset(offset)   // offset → '2026-W15' 格式
getWeekLabelByOffset(offset) // offset → '2026 W15・2026.04.13' 顯示

// 入口/控制
openHist()            // 開啟 overlay，初始化狀態，呼叫 renderHistCtrl + loadAndRenderHist
closeHist()           // 關閉 overlay
histOvClick(e)        // 點背景關閉
histShiftWeek(dir)    // +1/-1 週，不可超過本週
histGoToday()         // 回到 weekOffset=0
renderHistCtrl()      // 更新週次標籤、上下週按鈕、persona 選擇器

// 資料
histSelectP(pid)      // 選擇 persona → loadAndRenderHist
async loadAndRenderHist()
  // 從 DB 載入 weekly_reports → hd{} → renderHistReport
  // hd 格式：{ `${section_id}__${field_key}`: value }

// 渲染
renderHistReport(pid, weekKey, hd)
  // 進度條 → ss/sm/sc/sv → xp（if p.xp）→ 各群
  // 若 canEditHist && weekOffset=0：附加「前往填報」按鈕

histSec(title, sid, hd, contentFn)
  // 渲染單一 section 卡片（含完成 badge）

histF(label, valHtml)
  // 渲染單一欄位（label + value）
```

---

## 六、Supabase 資料表

### `groups`
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | text PK | e.g. 'g1' |
| name | text | 事業群名稱 |
| manager | text | 負責人（顯示用，e.g. 'Kyle / 小美'） |
| sort_order | int | 排序 |

### `brands`
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | text PK | |
| group_id | text FK→groups | |
| name | text | 品牌名稱 |
| sort_order | int | |

### `personas`
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | text PK | |
| name | text | 姓名 |
| init | text | 頭像文字（1-2字） |
| color | text | 頭像顏色（hex） |
| role | text | 職能 |
| note | text | 備注（自動產生） |
| has_cross_group | bool | 是否有跨群進度 |
| is_admin | bool | 管理者 |
| email | text | Google 登入 email |
| supervisor_id | text FK→personas \| null | 部門主管（非必填，自參照） |
> ⚠ `supervisor_id` 需手動在 Supabase 執行 SQL：
> ```sql
> ALTER TABLE personas ADD COLUMN IF NOT EXISTS supervisor_id text REFERENCES personas(id);
> ```

### `persona_groups`（多對多）
| 欄位 | 型別 | 說明 |
|------|------|------|
| persona_id | text FK→personas | |
| group_id | text FK→groups | |
> PK = (persona_id, group_id)

### `weekly_reports`（主要資料表）
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | uuid PK | |
| persona_id | text FK→personas | |
| week_key | text | e.g. '2026-W15' |
| section_id | text | ss/sm/sc/sv/xp/{groupId}/**`_submit_`** |
| field_key | text | 欄位名（含 __done__）/**`submitted`** |
| value | text | 填報值；送出時 value=ISO timestamp |
| updated_at | timestamptz | |
> unique: (persona_id, week_key, section_id, field_key)
>
> 特殊紀錄：`section_id='_submit_'`, `field_key='submitted'`, `value=ISO timestamp` — 代表週報已正式送出

### `ai_reports`
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | uuid PK | |
| persona_id | text | |
| week_key | text | |
| group_id | text | null=xp |
| content | text | 生成的 AI 報告 |
| generated_at | timestamptz | |

### `ai_prompts`
| 欄位 | 型別 | 說明 |
|------|------|------|
| id | text PK | 'group_report'/'cross_group_report' |
| label | text | |
| prompt_template | text | 含 {{persona_name}}/{{role}}/{{week}}/{{manager}}/{{fields}} |

---

## 七、管理後台 ADM_SCHEMA

```javascript
ADM_SCHEMA = {
  groups:        { label:'事業群', pk:'id', sortBy:'sort_order', cols:[...], fields:[...] }
  brands:        { label:'品牌', pk:'id', sortBy:'sort_order', ... }
  personas:      { label:'填報人', pk:'id', ... }
  persona_groups:{ label:'群組對應', pk:['persona_id','group_id'], 特製 renderPGTab() }
  ai_prompts:    { label:'AI Prompt', pk:'id', ... }
  weekly_reports:{ label:'週報記錄', pk:'id', readonly:true, ... }
}
```

---

## 八、職能問題模板（RQ）

```javascript
RQ = {
  '營運': [{lbl:'門市現場本週狀況',...}, ...],
  '品牌策略': [...],
  '展店': [...],
  '央廚': [...],
  '人資': [...],
  '人發': [...],
  '財務': [...],
  '採購': [...],
  'default': [{lbl:'本週完成',...}, {lbl:'進行中',...}, {lbl:'卡關或待處理',...}, {lbl:'下週計畫',...}]
}
// 每組 4 題，每題含 {lbl, ph}
```

---

## 九、關鍵程式碼行號對照

| 項目 | 約略行號 |
|------|---------|
| CSS 開始 | L13 |
| CSS 結束 / HTML body | L502 |
| `<script>` 開始 | L630 |
| 常數（SUPA_URL/KEY, ANTHROPIC_KEY） | L634–637 |
| Fallback AI Prompt（群組） | L640–675 |
| Fallback AI Prompt（跨群） | L677–699 |
| WEEK_KEY / getWeekKey() | L707–722 |
| 全域變數宣告 | L727–739 |
| initAuth / signInGoogle | L744–778 |
| loadApp() | L783–841 |
| buildUI() | L946–974 |
| buildSidebar(p) | L981–1012 |
| buildPages(p) | L1015–1063 |
| buildGroupPage(p,gid,idx) | L1092–1140 |
| getOrderFor(p) | L1201–1210 |
| showPage(sid) | L1211–1222 |
| AI 面板函式 | L1242–1390 |
| collectFields(sid,p) | L1392–1454 |
| markDone / editSec / goNext | L1456–1520 |
| updateBot / updateProg / toast | L1521–1538 |
| isAdmin() | L1543–1549 |
| Admin 變數宣告 | L1551–1556 |
| 閒置登出 | L1558–1575 |
| ADM_SCHEMA | L1578–1642 |
| openAdmin / closeAdmin | L1644–1650 |
| loadAdmTab(tab) | L1652–1688 |
| renderAdmTab(tab) | L1690–1778 |
| admStartEdit / admSubmit / admDelete | L1780–1860 |
| renderPGTab / pgSave 等 | L1862–1983 |
| reloadPersonas() | L1968–1984 |
| toggleSidebar / closeSidebar | L2007–2022 |
| mobNavGo / updateMobNav | L2027–2059 |
| initAuth() 呼叫 | L2062 |

---

## 十、版本修改記錄

| 日期 | 修改內容 |
|------|---------|
| 2026-04-15 | 初始架構，Google OAuth、Supabase 資料載入、週報填報流程 |
| 2026-04-15 | 修正 top bar「0 個群」：加 reloadPersonas() 在 pgSave 後執行 |
| 2026-04-15 | 修正 sidebar 群組不顯示：null 保護、String() ID 比對、email 不分大小寫 |
| 2026-04-15 | 新增週報查詢功能：sidebar 按鈕、overlay UI、週次導航、權限控管、各 section 唯讀顯示 |
| 2026-04-15 | AI 從 Anthropic 切換至 Gemini（gemini-2.0-flash-lite），保留 ANTHROPIC_KEY 供正式上線用 |
| 2026-04-15 | AI key/provider 改存 app_settings 表，管理後台新增「系統設定」tab 可切換 Gemini/Anthropic |
| 2026-04-15 | 新增送出週報預覽 Modal（`#submitOv`）：全週資料唯讀確認 + AI 報告展示，確認後寫 `_submit_` marker |
| 2026-04-15 | 新增 Sidebar AI badge（`.sb-ai-dot`）：各群/xp 有 AI 報告時顯示「✦ AI」小標籤 |
| 2026-04-15 | 重構送出週報預覽：改為 AI 面板同款視覺（tabs + aibl content）；移除底部列「預覽 AI 生成週報」按鈕；`+AI` badge 可點擊開啟 AI 面板；多群報告各自獨立 tab |
| 2026-04-16 | 新增：personas.supervisor_id 部門主管欄位（需 Supabase SQL migration）；AI 生成後顯示發送群組選擇 UI（可選多群或全部發送同一份）；Sidebar 獨立 AI 週報區塊（#sbAISection，每份報告各一條目） |

---

## 十一、待實作功能（Pending）

- [x] **週報查詢功能**：已完成。sidebar 📋 按鈕開啟，週次導航（最遠回查一年），persona 選擇器，各 section 唯讀顯示含完成進度。
- [x] **送出週報預覽 Modal**：已完成。「送出週報」按鈕開啟確認 modal，唯讀預覽本週所有填報資料 + AI 報告，確認送出後寫 `_submit_` marker 至 weekly_reports。
- [x] **Sidebar AI badge**：已完成。各群組/xp 項目在有 AI 報告時顯示「✦ AI」橘色小標籤，generateAIReport 成功後自動更新。
