<!--
title:   GitHub Actionsを使ってNext.jsで作られているアプリケーションをfirebase deployをするまで
tags:    Firebase,GitHubActions,React,next.js
id:      2e125a55945f991b0af4
private: false
-->
[Increments × cyma (Ateam Inc.) Advent Calendar 2020](https://qiita.com/advent-calendar/2020/increments-cyma) の17日目は、
Increments株式会社の @mziyut が担当します！

## はじめに
+ 最近ハッカソンに参加した際にGitHub Actionsを利用し、master(main)ブランチにmerge等新規コミットが行われたことをトリガーに、本番環境に反映されるように設定しました。
+ この記事では、最短で`firebase deploy`するまでの手順を記載します。


### 今回用いる環境

```
% sw_vers
ProductName:	Mac OS X
ProductVersion:	10.15.7
BuildVersion:	19H15
% node -v
v12.16.0
```

また、firebaseのプロジェクト作成や、クレジットカード登録[^1] などはこの記事では記載しません。

[^1]: 使用するNodeバージョンによって異なります。

## Projectの準備
+ 今回はFirebaseにデプロイすることがゴールなのでGitHub上で公開されている Next.js with Firebase Hosting exampleを利用します。

https://github.com/vercel/next.js/tree/canary/examples/with-firebase-hosting

```zsh
npx create-next-app --example with-firebase-hosting mziyut-advent-calendar-2020
```

### 起動確認
+ 最低限作成したProjectが動作することを確認しておきましょう。

```zsh
cd mziyut-advent-calendar-2020 && yarn dev
```

![localhost_3000_(Laptop with touch).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/a8159378-015d-8c06-2648-8300cb536819.png)

### `.firebaserc` の設定

+ [Next.js with Firebase Hosting example](https://github.com/vercel/next.js/tree/canary/examples/with-firebase-hosting) でプロジェクトを作成した場合 `.firebaserc` のプロジェクト設定が `<project-name-here>` となっているので適当なプロジェクト名を設定しましょう。

```diff:.firebaserc
 {
   "projects": {
-    "default": "<project-name-here>"
+    "default": "mziyut-advent-calendar-2020"
   }
 }
```

## GitHub Actionsを用いてデプロイする

[GitHub Actions](https://github.co.jp/features/actions) についてリンク先を確認してください。
デプロイするために[Firebase token]()が必要となります。

### デプロイ用トークン生成とSecretsに登録

[Firebase CLI リファレンス - CI システムで CLI を使用する](https://firebase.google.com/docs/cli?hl=ja#cli-ci-systems) を参照しTokenを生成しましょう

生成出来たら `https://github.com/<YOUR_NAME>/<YOUR_REPO_NAME>/settings/secrets/actions`にアクセスしトークンを登録しましょう

今回は`FIREBASE_TOKEN`と登録しました。

![github.com_mziyut_mziyut-advent-calendar-2020_settings_secrets_actions(Laptop with touch).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/8ab19760-9747-5779-5b29-769f265f36a9.png)


### `package.json`にCI用のデプロイタスクを定義

デプロイ時に実行するコマンドは以下です。

```zsh
firebase deploy --token "$FIREBASE_TOKEN"
```

そのままでも使用することは出来ますがworkflowに簡単に乗せることができるように`package.json`に定義していきましょう。


```diff_json:package.json
     "serve": "npm run build && firebase emulators:start --only functions,hosting",
     "shell": "npm run build && firebase functions:shell",
     "deploy": "firebase deploy --only functions,hosting",
+    "deploy:token": "firebase deploy --only functions,hosting --token ${FIREBASE_TOKEN}",
     "logs": "firebase functions:log"
   },
   "dependencies": {
```

### workflowsの定義

「デプロイ用トークン生成とSecretsに登録」と「`package.json`にCI用のデプロイタスクを定義」で設定した内容を元にworkflowsを定義していきます。

```yml:.github/workflows/deploy.yml
name: Deploy to firebase

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [10.x]
    steps:
      - uses: actions/checkout@v2
      - name: yarn install
        uses: borales/actions-yarn@v2.0.0
        with:
          cmd: install
      - name: yarn run deploy:token
        uses: borales/actions-yarn@v2.0.0
        with:
          cmd: deploy:token
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```

上記内容を記述したらGitHubのmaster(main)ブランチにPushしましょう。
Actionsの画面からworkflowの状態を確認することが出来ます。

![github.com_mziyut_mziyut-advent-calendar-2020_runs_1551507367_check_suite_focus=true(Laptop with touch).png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/d0e8c8e2-d902-35fb-1159-5ef4560f5680.png)

## まとめ

今回はサンプルプロジェクトをGitHub Actionsを使いFirebaseにデプロイするまでを記載しました。
実際に使ったコードは、mziyut/nextjs-github-action-deploy-sampleにPushしておきました。

https://github.com/mziyut/nextjs-github-action-deploy-sample

## 最後に

[Increments × cyma (Ateam Inc.) Advent Calendar 2020](https://qiita.com/advent-calendar/2020/increments-cyma)の18日目は@mi-noがお送りします！！