# Atlas — Discord AI 公司總部

> 把 Discord 從「聊天室」升級成多 channel × 多 agent × 多 context 的 AI 公司辦公室。自寫 edit-streaming 解 rate-limit、自寫 button parser 把 message 轉互動 UI、自寫 channel-context awareness 讓 agent 知道在哪裡說話。

**Status**: Live, 2026-04 ~ 2026-05 主迭代
**Stack**: Python, discord.py, custom rate-limit-handling, multi-channel state design

---

## 為什麼做這個

Telegram 已經是 ductor 主介面，但 Telegram 在「**多人 + 多 channel + 結構化資訊**」場景比較弱。Discord 的 server / channel / role / category / thread 結構天然適合做「AI 公司辦公室」：

- 不同 channel = 不同 role / 不同 agent / 不同 context
- 公開 channel + 私有 channel + DM 三層權限
- 多人加入時自動 onboarding（welcome DM + FAQ）

目標：讓 Atlas (Discord 上的主 agent) 在不同 channel 表現出對的 persona、用對的 context、follow 對的規則。

---

## 三個關鍵自寫模組

### 1. `DiscordEditStreamer` — 解 Discord rate-limit 40062

**症狀**：Atlas 開始上線後常「壞掉」— chairman 訊息 14 分鐘後才慢慢 stream 出來，或乾脆完全無回應。

**根因診斷**：Discord rate-limit 40062 (service-resource throttle on `/typing`)。Claude Opus 一個 reply 要 30-60s，期間 `async with channel.typing()` 連續 POST `/typing` 每 5-9s × 4 個 auto-respond channel concurrent → discord.py 重試風暴。

**修補（3 次迭代）**：
- iter 1: 移除 typing → 還是慢
- iter 2: one-shot typing `async with: pass` 1 次/訊息 → 改善但不夠
- iter 3 ✅ **final**: 仿 Telegram edit_streaming 寫 `messenger/discord/edit_streaming.py`（~225 行 `DiscordEditStreamer` class）：
  - placeholder send (一個空 message 占位)
  - 每 1.5s edit 一次（throttle 避免再撞 rate limit）
  - 1900-char split (Discord message 上限 2000)
  - 3-fail fallback (連 3 次 edit 失敗就 send 新 message)
  - handle_message_streaming callbacks（跟既有 framework hook）

**結果**：3 guilds 同時穩定 stream Opus 30-60s reply，0 rate-limit 錯誤。

### 2. `buttons.py` — 把 message 轉成互動 UI

**問題**：framework 既有的 `[button:Label]` syntax 只在 Telegram 有 inline keyboard 對應。Discord 也想要相同抽象。

**設計**：寫 `messenger/discord/buttons.py` (~120 行):

- 解析 message text 中的 `[button:Label]` markers
- 建 `discord.ui.View` + `Button`（max 5×5 button matrix / 24h timeout）
- 按下時透過 `_ClickedMessage` proxy mask author 為 `interaction.user`
- 走 `_dispatch_channel_message` 重新走完整 agent context

**設計取捨**：button click 等於 user 重新傳訊息 — 統一 message dispatch path，不開 special case branch。

### 3. Channel-context awareness（Phase 6）— 讓 agent 知道在哪

**chairman 說的話**：「bot 在哪裡說話都不知道」+「不要冗餘 / 完整記憶」。

**設計**：每次 agent 處理訊息前，從三個來源抓 context block 塞進 system prompt：

```
[CHANNEL] meta block
  ↓ from kb/channel_rules.json (per-channel JSON: persona, formatting, etc.)
[FAQ] block
  ↓ from kb/faq/<guild_id>.md (per-guild knowledge base)
[CHANNEL MEMORY]
  ↓ from kb/channel_memory/<channel_id>.md
    含 4 section schema:
    - Identity (LLM 寫)
    - Persistent facts (LLM 寫)
    - Open threads (LLM 寫)
    - Auto-summary (cron 寫，nightly in-place ≤600 字)
```

**設計取捨**：4 section 中前 3 段是 LLM 自己 maintain（會踩寫）、最後 1 段是 cron 寫（nightly digest）。**Daily digest cron 改寫只更新 Auto-summary 不踩 LLM 寫的部份** — 避免破壞 agent 自己累積的記憶。

---

## 其他工程細節

- **Auto-respond channels**：runtime-reload 不需重啟（每訊息 re-read `auto_respond_channels.txt`），特定 channel 跳過 `@mention` 要求
- **Boardroom round-robin**：`boardroom_round.py` 在 #boardroom dispatch C-suite agent 並 tag `[ROLE agent — Round N]`，讓 agent 之間能在公開 channel 開「會議」
- **Welcome DM + FAQ injection**：新人加入自動 DM、Atlas 收到 FAQ 範圍問題時自動注入答案
- **Spam / slur auto-mod**：基本內容過濾
- **`/grant-private` / `/revoke-private`**：privatize live-feed 等敏感 channel
- **Live-feed audit**：199/200 cron noise 自動 privatize 進 🔒 Private category
- **檔案上傳**：`<file:/absolute/path>` syntax 在 streaming finalize 階段解析，自動上傳到 Discord（25MB 上限 + 路徑 whitelist）

---

## 規模

- **3 guilds** 含台大論文獨立 guild
- **5 slash commands** global synced
- **14 channel** bootstrap on AI LAB guild
- **3 個 ADR** 處理架構決策（Atlas single-relay vs 多 bot / Gemini 工具化路線 / persona 修正）

---

## Trade-offs

| 我選的 | 我沒選的 | 為什麼 |
|---|---|---|
| Edit-streaming over typing indicator | 純 typing 通知 | typing API 限速、edit 沒限速這麼狠 |
| `_ClickedMessage` proxy 走完整 dispatch | 開 button-handler special branch | 統一 path，避免 button 漏吃 channel context |
| 4-section channel memory + cron split | 純 LLM 寫 / 純 cron 寫 | 兩者各有優勢，分工避免互踩 |
| Single-relay (Atlas 一隻) | 每 agent 一隻 Discord bot | 維運成本 + Discord rate-limit pool 共享問題 |

---

## 工作模式

跟 Ductor 同：架構決策 + framework-level 介入是我親手做的；具體 implementation 大量由 AI agent 完成。

最有 craft 感的是**3 次 iter rate-limit 修補** — 每次都要看 Discord SDK 內部行為、找出根因、設計新抽象。這層只能我做。
