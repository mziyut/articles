---
title: WordPress の設定ファイルに $schema JSON Schema を追加しエディタで補完する
tags:
  - WordPress
  - JSON
  - jsonschema
  - wp-env
private: true
updated_at: '2024-12-18T00:14:08+09:00'
id: c9a0ba113eb8a6f44ecc
organization_url_name: null
slide: false
ignorePublish: false
---

## はじめに

WordPress には設定ファイル (`theme.json` や `wp-env.json` 等)が複数存在します。
これらのファイルの定義は多数あり完璧に覚えることは困難です。
設定ファイル (`theme.json` や `wp-env.json` 等) に対して $schema JSON Schema の定義を追加することでエディタに補完してもらいスムーズに開発を進めることができます。

## JSON Schema とは

JSON Schema については以下記事等を参考にしてください。

https://qiita.com/g0e/items/9a4f886897fd46f107a8

https://json-schema.org

## JSON Schema の定義を追加する。

今回は WordPress の開発環境ツール `wp-env` の設定ファイル `wp-env.json` を例に記載します。

https://ja.wordpress.org/team/handbook/block-editor/reference-guides/packages/packages-env/

`wp-env.json` の schema 定義は以下URLで公開されています。

`https://schemas.wp.org/trunk/wp-env.json`

`wp-env.json` 内に `"$schema": "https://schemas.wp.org/trunk/wp-env.json"` を定義すれば OKです。

```json:wp-env.json
{
    "$schema": "https://schemas.wp.org/trunk/wp-env.json"
}
```

ただし、ドキュメントにも記載がありますがバージョンを指定して JSON Schema を読み込めない点には注意が必要です。

## その他 JSON Schema 定義

今回は、 `wp-env.json` を例に記載しましたが以下設定用の schema ファイルも用意されています。

- theme.json
- block.json
- font-collection.json
- wp-env.json
- blueprint.json
- WordPress REST API

ファイル毎に参照先が異なりますので詳しくは下記URLを確認して設定してください。

https://developer.wordpress.org/news/2024/07/json-schema-in-wordpress/

## Ref

https://github.com/WordPress/gutenberg/pull/36276

https://developer.wordpress.org/news/2024/07/json-schema-in-wordpress/

https://json-schema.org

https://code.visualstudio.com/docs/languages/json#_json-schemas-and-settings
