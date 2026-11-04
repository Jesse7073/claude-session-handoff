---
name: handoff
description: 結束工作前更新專案的 docs/handoff.md，讓下個 session 不靠舊對話也能接手。只在使用者輸入 /handoff 時執行。
argument-hint: "[下個 session 要做的事]"
disable-model-invocation: true
---

# Handoff

更新 `docs/handoff.md`，讓沒看過這段對話的新 session 讀完就能開始工作。

使用者指定的下個 session 重點（可能是空的）：$ARGUMENTS

這個 skill 只改 `docs/handoff.md`。不要 commit、push，也不要改 `.gitignore`；這些動作留給使用者決定。

## 1. 找到文件

在 repo 根目錄工作（`git rev-parse --show-toplevel`；不是 git repo 就用目前目錄）。

- 已有 `docs/handoff.md`：先完整讀過，沿用它的章節結構和語言。
- 沒有：用下方範本建立。語言跟著使用者平常的對話。

接著確認這份文件會不會被公開：

```bash
git check-ignore -q docs/handoff.md && echo ignored || echo not-ignored
git ls-files --error-unmatch docs/handoff.md 2>/dev/null && echo tracked || echo not-tracked
```

顯示 `not-ignored` 或 `tracked` 時，照常更新，但在回報時提醒使用者。交接文件常有未公開的想法，放進公開 repo 前要由使用者判斷。

## 2. 查實際狀態

對話裡的「做完了」可能已經過時，所以進度要以現在查到的結果為準。

```bash
git status --short --branch
git log --oneline -10
git branch -vv
```

有遠端時，也查進度有提到的 PR、issue 和 CI。優先用 `gh`；沒有 `gh` 而 repo 是公開的，就用 GitHub API 的 GET 查詢。

查到的結果和對話或舊文件不同時，寫查到的結果。查不到的事項寫成「未確認」，並附上確認方法。不要把它寫成已完成。

## 3. 整理這次 session

從對話找出下個 session 需要、但從 repo 看不出來的內容：

- **進度**：完成了什麼、做到哪裡，以及怎麼驗證的
- **決定**：決定了什麼、為什麼，以及不再考慮的做法
- **未決定**：還沒定案的問題和選項
- **踩過的坑**：下次可能再犯，而且 repo 或文件沒有記錄的事
- **待處理**：還要做的事，依順序排列
- **協作偏好**：使用者這次明確說過的工作方式

要分清楚誰說的。AI 的建議在使用者同意前都算「未決定」，寫成「建議 X，使用者尚未決定」。新 session 會把文件當成事實，寫錯就等於替使用者做了決定。

## 4. 更新文件

這份文件描述「現在的狀態」，不是工作日誌，所以要直接改寫，不要往後追加：

- 更新「最後更新」日期，用系統提供的今天日期
- 把過時的內容改掉或刪除，已完成的待處理項目移到進度
- 和這次工作無關的章節保持原樣，別為了改寫而改寫
- repo 已有的內容只放路徑，不要整段複製，否則兩邊會逐漸不一致
- 寫清楚第一步：下個 session 打開就知道要做哪件事、從哪個檔案開始

使用者有指定下個 session 的重點時，「下一步」就以它為主，只留下和它相關的背景。沒有指定時，從待處理事項判斷。

## 5. 檢查機密

不要寫入密碼、token、API key、`.env` 的值，或含密碼的連線字串。需要提到時只寫變數名稱，例如 `DB_PASSWORD`。

寫完後掃一次：

```bash
grep -nEi 'password|passwd|secret|token|api[_-]?key|://[^/ ]+:[^@ ]+@' docs/handoff.md
```

逐筆確認命中的內容只是名稱，不是實際的值。是實際的值就刪掉，回報時不要重複該值。

## 6. 回報

用幾行回報：

- 這次改了哪些重點
- 還有哪些事是「未確認」
- 需要使用者處理的提醒，例如文件沒有被 ignore

最後給下個 session 的開場句，讓使用者直接貼上：

> 先讀 `docs/handoff.md`，然後從「下一步」開始。

## 新文件範本

```markdown
# 交接說明：<專案名稱>

> 給下個 session 讀取。先讀完這份，再開始處理「下一步」。
> 最後更新：YYYY-MM-DD。

## 專案是什麼

## 目前進度

## 已定的決定

## 尚未決定

## 未確認

## 踩過的坑

## 待處理

## 下一步

## 協作偏好

## 相關文件
```

沒有內容的章節就刪除。
