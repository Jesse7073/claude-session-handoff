# claude-session-handoff

Claude Code 的 `/claude-session-handoff` skill。結束工作前輸入 `/claude-session-handoff`，它會更新專案的 `docs/handoff.md`，讓新的 session 不靠舊對話也能接手。

## 它做什麼

- 持續更新同一份 `docs/handoff.md`，不另外產生新檔案
- 先查 git、PR 和 CI 的實際狀態，不憑對話印象寫進度
- 分清楚使用者的決定和 AI 的建議
- repo 已有的內容只放路徑，不重複寫
- 寫完掃描機密，避免把密碼或 token 寫進文件
- 不會 commit、push，也不會改 `.gitignore`

## 安裝

個人使用（所有專案都能用）：

```bash
git clone https://github.com/Jesse7073/claude-session-handoff.git ~/.claude/skills/claude-session-handoff
```

只給單一專案使用時，改放在專案的 `.claude/skills/claude-session-handoff`。

## 使用

```text
/claude-session-handoff
/claude-session-handoff 下次從 XX step 開始
```

開新 session 後輸入：

```text
先讀 docs/handoff.md，然後從「下一步」開始。
```

交接文件可能包含未公開的想法。公開 repo 建議把 `docs/handoff.md` 加進 `.gitignore`。

## 致謝

設計參考 [mattpocock/skills](https://github.com/mattpocock/skills) 的 `handoff` skill。

## License

[MIT](LICENSE)
