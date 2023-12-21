---
title: TencentCloud  の Cloud Object Storage を利用して Static Website をホスティングする
tags:
  - CloudObjectStorage
  - ホスティング
  - TencentCloud
  - StaticWeb
private: false
updated_at: '2023-12-22T01:46:06+09:00'
id: e3e2f79d7f1a7f9fd7bb
organization_url_name: qiita-inc
slide: false
ignorePublish: false
---

## はじめに

この記事は [むちゃぶりにも応える開発技術があるって？！〜3つのテーマで記事を募集〜 by V-CUBEのカレンダー | Advent Calendar 2023 - Qiita](https://qiita.com/advent-calendar/2023/v-cube) の 3 日目の記事です。

今回は、 TencentCloud の Cloud Object Storage を利用して Static Website をホスティングしてみようと思います。

最近は、 HTML や CSS, JS 等のファイルを出力し Static Website として公開する方も多いのではないでしょうか?

実際に Qiita の一部で Static Website として動いている部分がいくつか存在します。
普段 Tencent Cloud は利用していませんが、 Tencent Cloud で Static Website をホスティングしたい! となったときどのような構成にすべきか調べ実際に設定してみました。

## Cloud Object Storage について

> Cloud Object Storage（COS）は、Tencent Cloudが打ち出す、ディレクトリ階層がなく、データフォーマットの制限もない、大容量データに対応でき、HTTP/HTTPSによるアクセスもサポート可能な分散型ストレージサービスです。

https://www.tencentcloud.com/jp/products/cos

AWS で言う Amazon S3 、 Google Cloud で言う Cloud Storage に該当するサービスです。
ドキュメントを読むと Cloud Object Storage は COS と略されていることが多いようです。

料金は ↓ ページから確認することができます。費用は「ストレージ費用」「リクエスト費用」「データ取得費用」「トラフィック費用」「管理機能費用」で構成されているそうです。
コストも他クラウドサービスと大きな差はありませんでした。

https://intl.cloud.tencent.com/pricing/cos?lang=jp

## Bucket を作成する

まずはじめに、Cloud Object Storage の Bucket を作成します。

[https://console.tencentcloud.com/](https://console.tencentcloud.com/) にアクセスし、 「Cloud Object Storage」 を選択するか、 [https://console.tencentcloud.com/cos/bucket](https://console.tencentcloud.com/cos/bucket) に直接アクセスします。

| Cosnoel Home page                                                                                                        | Bucket List page                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/c9b8054b-edc0-0cc0-68b7-ef7bb0c5b808.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/6d96a68f-726a-5312-3acd-5f654eaac574.png) |

アクセスしたら、 Bucket を作成します。
今回作成した Bucket は以下設定としました。

### Step 1 ... Information

- Region: Asia-Pacific Tokyo
- Name: adcal-2023-mziyut
- Access Permission: Public Read/Private Write
- Default Alarm: Enable

### Step 2 ... Advenced optional configration

Access Permission を 「Public Read/Private Write」 または 「Public Read/Write」 へ変更した時に警告が表示されていたのが親切でした。 (小さな心配り良いですね、)

次の設定画面に遷移すると Versionning や Logging 、 Server-Side Encryption の設定を求められました。
今回は簡単な検証を行う目的だったので Versionning や Logging 、 Server-Side Encryption といった設定は有効にしませんでした。

最後、今まで選択・入力した内容を確認するページが表示されます。「Create」 を押せば Bucket が作成されるようです。

| Information                                                                                                              | Advenced optional configration                                                                                           | Complete                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/e5c3d042-276a-0df1-f2ce-d8ea74685ab8.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/1f3b444c-8a9b-13c4-d8b1-d60890ba29a8.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/e8f9b0b3-2e05-c7d1-97cc-d06228269c96.png) |

Bucket が無事作成されました。 :tada:

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/e9ffbc32-c880-5498-3763-266116dfe979.png)

## Bucket にファイルをアップロードする

先程作成した Bucket に HTML ファイルをアップロードしてみます。
簡単な Hello World と表示させるだけの HTML を用意したのでこのファイルをアップロードしてみます。

```html
<!doctype html>
<html>
  <head>
    <title>TencentCloud Cloud Object Storage - Test</title>
  </head>
  <body>
    <p>Hello World.</p>
  </body>
</html>
```

| Upload Files をクリック                                                                                                  | ファイルを選択                                                                                                           | アップロード完了                                                                                                         |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/9101ba7e-c548-b0ff-d629-620c7b8fc647.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/8d324f0a-1d6e-c00d-2688-a09bde427ec3.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/27810905-f08e-0cff-e637-d74742f6916b.png) |

アップロードが完了したら、実際にアクセスしてみましょう。
Overview 内の下部に Endpoint が記載されています。 記載されている Endpoint にアップロードしたファイル名を追加しアクセスします。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/2dd7c685-bdd2-3833-b74c-89ab332e70f4.png)

私の場合は、 `https://adcal-2023-mziyut-1323308177.cos.ap-tokyo.myqcloud.com/index.html` となります。
これで、作成した Bucket に Upload したファイルへアクセスすることができるようになりました。

## Static Website を有効にする

続いて、Static Website を有効にして `/` へアクセスした際に `/index.html` の内容が表示されるように設定してみます。
Static Website の設定画面に行き有効に変更いただくと、 専用の Endpoint や HTTP から HTTPS へのリダイレクト強制 Index document の設定等が行えるようになります。

| Before                                                                                                                   | After                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/671a4d5b-4d0c-5617-eb29-fdcba0ce7e03.png) | ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/0674a22a-797f-a752-0401-1082c2c81b69.png) |

今回は設定しませんでしたが Redirection rules 等も (ドキュメントを読む限り) 便利に使えそうな機能でした。
他にも Custom Domain Name や CDN 等一通り機能は揃っているので十分活用できそうでした。

[Hosting a Static Website | Tencent Cloud](https://www.tencentcloud.com/document/product/436/30958#redirection-rules)

## おわりに

今回は、TencentCloud の Cloud Object Storage を利用して Static Website をホスティングするまでを試してみました。
TencentCloud は触るのが初めてでしたが、ドキュメントも準備・整理されており、他クラウドインフラを触ったことがある人なら簡単に試すことができました。
アカウントもせっかく作ったことですし、様々なサービスをもう少し触ってみようと思います。

### 付記

検証が終わったので Cloud Object Storage の Bucket を削除しようとしたら、メール認証を求められました。
Access Permission の設定はじめ TencentCloud 1つ1つが丁寧だなと改めて感じました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/e7309931-4a2f-e3db-bb33-69fc7cba22ee.png)

## References

- [Setting Static Website | Tencent Cloud](https://www.tencentcloud.com/document/product/436/14984?has_map=1)
- [Hosting a Static Website | Tencent Cloud](https://www.tencentcloud.com/document/product/436/30958)
