<!--
title:   Google Apps Script を使って Quill に「hello world!」を送信する
tags:    GoogleAppsScript,HelloWorld,QuillChat,incoming-webhook
id:      97eeaeb0ce8dfbd72157
private: false
-->
## これはなに
スレッド重視のチャットツール「Quill」を使ってみてincoming webhookが使えることが解ったので Google Apps Script からメッセージの送信を試してみました。

https://quill.chat/

また、「Quill」と検索するとリッチテキストエディタが多く出てきますがエディタの情報ではありません。

https://quilljs.com/

## Google Apps Script を作成する

以下URLから Google Apps Script を作成してください

https://script.google.com/home/start

## Scriptを記述する

今回は、`hello world!` のメッセージを送信するだけなのでとてもシンプルです


```typescript:code.gs
const sendToQuill = () => {
  const body = {
    "text": "hello world!",
    "title": "title here!",
  }
  
  const options = {
    "method" : "post",
    "contentType" : "application/json",
    "payload" : JSON.stringify(body)
  }

  // Quillの設定画面から発行した `webhooks` URLを指定
  const url = "https://api.quill.chat/webhooks/incoming?channel=dummy&webhook=dummy&nonce=dummy"

  UrlFetchApp.fetch(url, options);
}
```

## 実行してみる

今回作成した `sendToQuill` が選択されていることを確認し「実行」をクリックして下さい

![Screen Shot 2021-05-25 at 9.54.24.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/1d430b6b-f13d-3fa6-2465-2d168cf986ad.png)

実行すると、指定したURLに対してリクエストが送信されメッセージが届きます。
今回は、`Message + Thread` タイプのチャンネルのため Thread内にメッセージが送信されました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/2d16ed72-40f3-3581-b4a3-21eb4aa47df4.png)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/2ed4150b-cc91-b9e5-cdb4-3d4efc520ed2.png)


## 最後に

今回は、「hello world!」を送信するだけだったが、Referenceを読む限り Slack 同様に「attachments」「blocks」等設定出来るようなので、後日試します。

## Reference

https://docs.quill.chat/docs/specification

https://developers.google.com/apps-script/reference/url-fetch/url-fetch-app