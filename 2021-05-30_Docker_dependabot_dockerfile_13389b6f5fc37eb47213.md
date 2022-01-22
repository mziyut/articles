<!--
title:   dependabot を使って Dockerfile の定義を最新化しよう
tags:    Docker,dependabot,dockerfile
id:      13389b6f5fc37eb47213
private: false
-->
## これはなに
Docker Hubなどで公開されている Docker Imageをベースに用途に応じて、パッケージや設定の変更、定義の追加を行っているがBase imageの更新を忘れることが多いので設定を行った際のメモ。

## やること
+ dependabot.yml を記述する
+ main（master） branch にpush する

## dependabot.yml を記述する

今回は、必須となる `package-ecosystem` `directory` `schedule.interval` の例を記載。
Pull requestを作成するときに誰をassignするかなども設定出来ます。

```yml:.github/dependabot.yml
version: 2
updates:
- package-ecosystem: docker
  directory: "/"
  schedule:
    interval: "daily"
```

## main（master） branch にpush する

pushするだけなので省略します 🙇

## (数日後) Pull request が作成される

指定した時刻にチェックが走り、更新出来ると判断されると Pull request が作成されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/3f5389cf-67ba-5b6d-6510-005a6602cfb4.png)

## Reference

https://docs.github.com/ja/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/configuration-options-for-dependency-updates