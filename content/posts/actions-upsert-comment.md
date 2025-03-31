+++
date = '2025-03-31T19:38:48+09:00'
title = 'Github Actions + Github CLIでコメントを更新する'
+++

Github Actions でコメントを更新する方法として、
[marocchino/sticky-pull-request-comment](https://github.com/marocchino/sticky-pull-request-comment)を使う方法があります。
が、Github CLI を使えば自前でできるのでそのメモ
（ちょっとめんどくさいけど）

### 仕組み

- update したいコメントに invisible な footer を仕込む
- 仕込んだ footer を探す
- あれば更新

みたいな感じです。

雰囲気: https://github.com/freyja1103/ci-upsert-comment/pull/1

#### コメントをさがす

gh api repos/repoName/issues/number/comments で JSON を拾いつつ、jq でいい感じにフィルタリングする

- select だけだと存在しない場合に空の配列が代入されるので`map() | first // ""`で空文字にする

```yaml
- name: Find existing comment
    id: existing_comment

    run: |
        COMMENT_ID=$(gh api repos/${{ github.repository }}/issues/${{ github.event.pull_request.number }}/comments | jq -r --arg footer "$FOOTER" 'map(select(.body | contains($footer)) | .id) | first // ""' | head -n1)

        if [[ -n "$COMMENT_ID" ]]; then
        echo "COMMENT_ID=$COMMENT_ID" >> $GITHUB_ENV
        fi
```

### コメントを更新する

空文字でなければ PATCH で更新

```yaml
- name: Update existing comment
    if: env.COMMENT_ID != ''
    run: |
        gh api --method PATCH repos/${{ github.repository }}/issues/comments/"$COMMENT_ID" \
        -f body="
        test (updated)
        ${FOOTER}
        "
```

#### 普通に投稿する

```yaml
- name: Post new comment if none exists
    if: env.COMMENT_ID == ''
    run: |
        gh pr comment ${{ github.event.pull_request.number }} --body "
        test
        ${FOOTER}
        "
```
