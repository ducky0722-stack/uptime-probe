# uptime-probe

達基資訊的對外服務外部監控。**跑在 GitHub 的機器上，不在自己家。**

為什麼需要：本機的 watchdog 只能檢查 localhost，Mac 斷網／停電／休眠時，
本機的通知也一起死掉。這一層從外部打真實網址，才是唯一能叫醒人的。

## 檢查對象（每 5 分鐘）

| 服務 | 網址 | 判定 |
|---|---|---|
| 叫貨系統 | https://orders.grownstudio.net/healthz | 必須 200 |
| ERP | https://app.grownstudio.net/login | 必須 200 |
| LINE 通道 | https://line.grownstudio.net/line/webhook | 不得 5xx、不得連不上 |

每個網址重試 3 次（間隔 10 秒）才判定失敗，避免單次抖動誤報。

## 通知規則

- 失敗：開一張 label `incident` 的 issue，並發 Telegram；**未結案前不重複發**
- 恢復：自動關閉 issue，發一則「恢復正常」
- 沒有事件時完全安靜

## 測試

Actions → uptime-probe → Run workflow → `force_fail` 打勾，會送一則測試通知。

## 注意

- 這是公開 repo，裡面只有網址與檢查邏輯；Telegram token 放在 repo secrets，不會外洩。
- GitHub 的排程在尖峰時可能延遲幾分鐘，這是免費服務的特性，不是故障。
- 這層只確認「外面連得到」，不確認「功能正確」。功能面的驗證仍靠本機測試。
