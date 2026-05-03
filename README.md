# 美珊與野狼的碩班之旅 — 部署指南

## 檔案結構

```
website/
├── index.html          ← 首頁（背景照片 + 兩個入口按鈕）
├── assets/
│   └── background.jpg  ← ⚠️ 需手動加入你們的照片
├── wolf/
│   └── index.html      ← 野狼的日記頁面
└── meishan/
    └── index.html      ← 美珊的日記頁面
```

---

## Step 1：加入背景照片

將你們的合照存為 `assets/background.jpg`（建議寬度 1920px 以上，橫向或直向均可）。

---

## Step 2：建立 Firebase 專案

網站使用 Firebase Firestore 儲存日記、行事曆、每日問答，需先完成設定。

1. 前往 [https://console.firebase.google.com](https://console.firebase.google.com)
2. 點選「新增專案」，輸入名稱（例如 `meishan-wolf-blog`）
3. 建立完成後，點選「</> 網頁應用程式」，取得設定碼
4. 複製如下格式的設定物件：

```js
const firebaseConfig = {
  apiKey:            "AIza...",
  authDomain:        "meishan-wolf-blog.firebaseapp.com",
  projectId:         "meishan-wolf-blog",
  storageBucket:     "meishan-wolf-blog.appspot.com",
  messagingSenderId: "1234567890",
  appId:             "1:1234...:web:abc123"
};
```

5. 將上述資訊分別貼入 `wolf/index.html` 與 `meishan/index.html` 頂部的 `firebaseConfig` 區塊（兩個檔案填入**相同**的設定）

### 開啟 Firestore 資料庫

1. 在 Firebase 控制台左側選「Firestore Database」
2. 點「建立資料庫」→ 選「以測試模式啟動」（方便個人使用）
3. 選擇離你最近的區域（建議 `europe-west3` 法蘭克福，較接近瑞典）

> 測試模式的規則允許任何人讀寫 30 天。30 天後可在 Firestore「規則」頁面更新為：
> ```
> rules_version = '2';
> service cloud.firestore {
>   match /databases/{database}/documents {
>     match /{document=**} {
>       allow read, write: if true;
>     }
>   }
> }
> ```

---

## Step 3：部署到 GitHub Pages

```bash
# 1. 在 GitHub 建立一個新的 repository（例如 our-story）

# 2. 在本機初始化
cd /Users/jim41103/Desktop/Claude-agent/website
git init
git add .
git commit -m "init: 美珊與野狼的碩班之旅"

# 3. 推上 GitHub
git remote add origin https://github.com/你的帳號/our-story.git
git push -u origin main

# 4. 在 GitHub repository 頁面：
#    Settings → Pages → Source: Deploy from a branch → Branch: main / (root)
#    儲存後等 1-2 分鐘，網址即為 https://你的帳號.github.io/our-story/
```

---

## 功能說明

| 功能 | 說明 |
|------|------|
| 日記新增 / 搜尋 | 各自獨立，支援關鍵字搜尋標題與內容 |
| 心情標籤 | 8 種心情選項，顯示於列表卡片 |
| 共同行事曆 | 兩人共用，任一方新增皆可在對方頁面看到 |
| 斯德哥爾摩天氣 | Open-Meteo API，每 10 分鐘自動更新，不需 API Key |
| 每日問答 | 30 道題目輪流，兩人回答儲存後可互看 |

---

## 資料結構（Firestore Collections）

| Collection | 用途 |
|-----------|------|
| `diary_wolf` | 野狼的日記條目 |
| `diary_meishan` | 美珊的日記條目 |
| `calendar_events` | 共同行事曆（兩頁面共用） |
| `daily_qa` | 每日問答回答，以日期為文件 ID |
