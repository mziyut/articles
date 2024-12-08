---
title: Docusaurus でインライン TOC (Table of Contents) を表示させる
tags:
  - React
  - MDX
  - Docusaurus
private: true
updated_at: '2024-12-09T00:15:05+09:00'
id: bde37ec077aeb71c64ed
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

Meta社が管理する React ベースのドキュメント管理ツール Docusaurus 。

Docusaurus は Markdown (`.md`) に加えて MDX (`.mdx`) も表示可能です。
MDX でドキュメントを書くことで ドキュメント内に React Component も表示可能になります。

今回は、標準で用意されている Inline TOC (Table of Contents)を表示させる方法を記載します。

## ドキュメントの用意

インライン TOC を表示させたいドキュメントを用意します。
すでに Markdown 形式でドキュメントを書いていた場合は拡張子を `.md` から `.mdx` に変更してください。

```mdx:docs/test.md
# 見出し1

これは Docusaurus の Inline TOC のテストのためのドキュメントです。

## 見出し2_1

見出し2_1 のテキスト

## 見出し2_2

見出し2_2 のテキスト

## 見出し2_3

見出し2_3 のテキスト

## 見出し2_4

見出し2_4 のテキスト
```

## Inline TOC を表示させる定義の追加

用意済みのドキュメント (`docs/test.md`) に `TOCInline` 表示用の Component を import します。

```mdx
import TOCInline from '@theme/TOCInline';

<TOCInline toc={toc} />
```

```mdx:docs/test.md
import TOCInline from '@theme/TOCInline';

# 見出し1

これは Docusaurus の Inline TOC のテストのためのドキュメントです。

<TOCInline toc={toc} />

## 見出し2_1

見出し2_1 のテキスト

## 見出し2_2

見出し2_2 のテキスト

## 見出し2_3

見出し2_3 のテキスト

## 見出し2_4

見出し2_4 のテキスト
```

下記スクリーンショットのように Inline TOC が表示されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/59156a8a-ff12-aa43-3255-1ba1a3ba3eb5.png)

## さいごに

今回は Inline TOC の説明だけに留めましたが、自身で React Component を定義することでドキュメントをよりリッチにすることも可能です。

https://docusaurus.io/docs/markdown-features/react

## Ref

https://docusaurus.io/docs/markdown-features/toc#inline-table-of-contents
