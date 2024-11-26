---
title: Vitest を GitHub Actions 上で実行するときに設定したいオプションや composite action - カバレッジレポート編
tags:
  - GitHub
  - GitHubActions
  - vite
  - Vitest
private: true
updated_at: '2024-11-17T23:41:45+09:00'
id: ab642c7d9c3efcc9cffd
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Vitest をプロジェクトに設定しローカル環境はもちろん CI/CD 環境でも動かす場面は少なくありません。
今回は GitHub Actions で Vitest を実行する際に設定したい内容や composite actions をまとめました。

## レポーター設定

Vitest は GitHub Actinos の workflow command に対応しています。
なお、この設定は `process.env.GITHUB_ACTIONS` が `true` である場合自動的に有効になります。

https://vitest.dev/guide/reporters.html#github-actions-reporter

もし、レポーター設定をデフォルト値以外で使用している場合は、 `github-actions` を追加することで利用可能です。

```ts:vite.config.ts
export default defineConfig({
   test: {
     coverage: {
       reporter: ["dot", "github-actions"],
```

## カバレッジレポート

こちらの記事でも紹介されていましたが、 `vitest-coverage-report-action` を導入することで作成した Pull request にコメントでテストカバレッジを追加してくれます。

https://qiita.com/shun198/items/f640b3d3bf73d2cc3510

https://github.com/marketplace/actions/vitest-coverage-report

`vitest-coverage-report-action` を使用するためには、 `reporter` に下記値を定義する必要があります。

- json-summary (必須)
- json

```ts:vite.config.ts
export default defineConfig({
   test: {
     coverage: {
       reporter: ["json-summary", "json"],
```

## 上記内容適用した設定ファイル

上記内容適用した設定ファイルは以下のようになります。

```ts:vite.config.ts
export default defineConfig({
   test: {
     coverage: {
       reporter: ["dot", "github-actions", "json-summary", "json"],
```

## Ref

https://github.com/davelosert/vitest-coverage-report-action

https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/workflow-commands-for-github-actions#setting-an-error-message
