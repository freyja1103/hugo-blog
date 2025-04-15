+++
date = '2025-04-15T17:55:55+09:00'
title = 'Github ActionsでPull Requestを自動作成した初回はpull_requestイベントは起きない'
+++

意外な落とし穴で気づかなかったのでメモ

タイトルは少し語弊で、`GITHUB_TOKEN`によって起きたイベントではワークフローが実行されないということです。

例えば、以下のような Workflow があるとします。

- Workflow A: main への push 時に実行

```yaml
# Workflow A
name: Create Pull Request (main -> release)
run: |
  gh pr create -b release -H main --title "Pull request title" --body "Pull request body"
```

- Workflow B

```yaml
# Workflow B
on:
    push:
    pull_request:
...
job:
    steps:
        name: Deploy Hugo site to Pages
        if: github.event == 'pull_request'
        run: |
            # なにかがデプロイされる

        name: Notif to discord
        if: github.event == 'push'
        run: |
            # 通知が行く
```

main へ push された際の挙動は以下になる。

1. Workflow A により Pull Request が作成される
2. `Deploy Hugo site to Pages`は実行されない
3. `Notif to discord`は実行される

というのも、2 のトリガーは Actions による自動作成された PR で、
3 のトリガーは人による push によるものだからでした。

おわり

参考: [Github Docs - ワークフローでの GITHUB_TOKEN の使用](https://docs.github.com/ja/actions/security-for-github-actions/security-guides/automatic-token-authentication#using-the-github_token-in-a-workflow)
