# oshi-timeline — 推し活情報の集約フィード

通知ツール4兄弟が拾った新商品・ニュースを1本の `feed.json` にまとめて配信する。
**おし蔵（`oshi-goods-manager`）がこれを読んでタイムライン表示する。**

## ステータス

- **現在**: 稼働中。**200エントリ**・最終更新 2026-09-18（2026-09-21実測）
- **検証方法**: `git pull` 後に
  `node -p "JSON.parse(require('fs').readFileSync('feed.json','utf8')).length"`

## 仕組み

```
holo-shop-notify   ┐
suisei-shop-notify ├→ GitHub API に TIMELINE_GITHUB_TOKEN で直接書き込み → feed.json
vspo-shop-notify   │                                                          ↓
merch-notify       ┘                                      oshi-goods-manager が読む
```

**書き込みはGitHub API経由**（`api.github.com/repos/bibimib/oshi-timeline/contents/feed.json`）。
ローカルのクローンを経由しないので、**手元のfeed.jsonは放っておくと古くなる**。

`ui-shigure-notify` はこの連携に入っていない（BOOTHが相手）。

## エントリの形

```json
{
  "id": "vspo-8523978834107",
  "sourceKey": "vspo",
  "sourceLabel": "ぶいすぽっ！",
  "sourceColor": "#FF69B4",
  "title": "藍沢エマ　5周年記念2026",
  "url": "https://store.vspo.jp/products/...",
  "price": "14900",
  "image": "https://cdn.shopify.com/...",
  "discoveredAt": "2026-09-18T12:29:18.916Z",
  "type": "shop"
}
```

`sourceKey` / `sourceLabel` / `sourceColor` は書き込む側（各通知ツール）が決めている。
**新しい通知ツールを足すときは、この3つを決めて既存とぶつからないようにすること。**

## 構成

このリポジトリは **`feed.json` 1枚だけ**。コードは持たない。
書き込む側（4兄弟）と読む側（おし蔵）のあいだの受け渡し場所。

## ⚠️ 手元のクローンは黙って古くなる

2026-09-21に確認したとき、**ローカルは6か月前の初期コミット（空配列）のまま**だった。
リモートは正常に200件まで育っていたので、実害はなかった。

**ここを見るときは必ず `git pull` してから。** `[]` を見て「壊れてる」と早とちりしない。
ぺこらは実際に一度やった。

## 未実装候補

1. 古いエントリを落とす保持期間のルールを決めて明文化する（いま200件でどこまで増えるか未定）
2. `bot-health` の監視に「feed.jsonの最終更新が古い」を足す
   （いまは各ツールのワークフロー成功しか見ていないので、**書き込みだけ失敗しても気づけない**）
