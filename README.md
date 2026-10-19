# 愛心任務島｜協會參訪團隊合作闖關

## 操作方式

1. 解壓縮，雙擊 `index.html`，使用 Chrome 或 Edge 開啟。
2. 選「管理者後台」：查看隊伍、開始關卡、揭曉答案、推進下一關。
3. 同一瀏覽器另外開一個分頁進入「主持人大螢幕」，可全螢幕投影（F11）。
4. 另開分頁選「參訪者手機」，用暱稱加入其中一隊，點選答案。可再多開分頁模擬多位隊友。
5. 主持人揭曉答案後，每個選對的隊伍得 100 分；共四關。

## 重要：不是正式多人連線網站

- 原型使用瀏覽器 localStorage / BroadcastChannel，只可在**相同網站來源的瀏覽器分頁**之間同步。不同手機不會同步。從檔案 `file://` 開啟時，各瀏覽器對 localStorage 的行為可能不同，建議用本機 HTTP 伺服器測試。
- 要讓 20 支手機掃 QR Code 真正同步，還需要部署到 HTTPS 網站，新增 Firebase Authentication 匿名登入、Firestore 房間／隊伍／玩家／回合資料、嚴格的 Firestore 安全規則、主持人身分驗證與伺服器端計分。
- 管理者畫面**沒有安全認證**，不能當作正式網站發布給參訪者使用。
- 作答邏輯和分數在瀏覽器端演示，不防止修改資料或惡意操作。

## Windows 本機 HTTP 測試（推薦）

在解壓縮資料夾執行：

```powershell
py -m http.server 8000
```

用 Chrome / Edge 打開 `http://localhost:8000`；若沒有 Python，可在 VS Code 安裝 Live Server 延伸模組。

## 正式版規劃（Firebase）

建議資料結構：

```
rooms/{roomId} {
  hostUid, status, currentRound, createdAt, joinCode
}
rooms/{roomId}/teams/{teamId} {name, score}
rooms/{roomId}/players/{playerUid} {displayName, teamId, joinedAt}
rooms/{roomId}/rounds/{roundId}/answers/{teamId} {choiceIndex, answeredBy, submittedAt}
```

安全要點：
- 只有取得授權的主持人能開始、揭曉和切換關卡。
- 玩家只能讀取自己所在房間允許公開的資訊，加入需限制人數。
- 防止重複作答，隊伍答案只能提交一次；在揭曉前不能暴露正確答案。
- 計分必須由可信任的 Cloud Functions 執行，不能由手機自訂分數。
- 正式版使用 20 人上限與隊伍上限的交易或後端驗證。
- 不蒐集真實姓名及敏感資料，測試用暱稱即可。

## 後續可擴充

- QR Code（由正式網域產生，不用手打 URL）
- 自訂關卡題目／提示與隊名
- 限時倒數、排行榜動畫、音效
- 隊友合作解鎖、多階段線索與回饋
- 離場回饋問卷與活動成果報表

## V2：參加人數與分組
主持人在「管理者後台」開場前，可先選擇總人數（2–20 人）與分組數（2、3、4 組，但不可多於總人數）。系統會將名額平均分到各隊，例如 11 人分成 3 隊就是 4、4、3 人。已有玩家加入後會鎖定設定，若要修改請重設遊戲房間（會清空玩家與分數）。
