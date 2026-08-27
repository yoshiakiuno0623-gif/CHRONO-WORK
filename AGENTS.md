# Codex Instructions

このリポジトリは複数のAIエージェント（Codex / Claude Code）が同じworking treeを扱う可能性がある。同時編集を防ぐため、以下に従うこと。

## 編集ロック

working treeまたはGitの状態を変更する直前に、Gitルートで `.ai-edit.lock/` を取得する。

```bash
mkdir .ai-edit.lock && printf 'agent=%s\nstarted_at=%s\n' "自分の識別名" "$(date +%Y-%m-%dT%H:%M:%S%z)" > .ai-edit.lock/owner
```

`mkdir` が失敗した場合は、他のエージェントが編集中とみなし、ファイルの変更やGit状態を変更する操作を一切行わず、read-only操作（読み取り・検索・`git status`・`git diff`）に限定する。待機ループやリトライでロックを奪わない。

作業が終わったら速やかに解放する。

```bash
rm -f .ai-edit.lock/owner && rmdir .ai-edit.lock
```

残っているロックを独断で削除しない。古く見える場合も、解除の可否はユーザーが判断する。

## 変更の安全性

- 他のエージェントやユーザーの未コミット変更を、独断で削除・revert・上書きしない。意図不明の差分も「不要」と判断して消さない。
- `git reset --hard`、広範囲の削除、revertなどの破壊的操作は、対象と必要性を確認してから行う。
- 秘密情報をコミットしない。
