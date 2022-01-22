<!--
title:   Pulumi (TypeScript) で操作中の AWS アカウント ID を取得する 
tags:    AWS,AdventCalendar2021,Infrastructure_as_code,Pulumi,TypeScript
id:      4abe7a67b0f81f0ba952
private: false
-->
## はじめに

AWS のリソースへのアクセス権限 （bucket policy 等） や、ログ出力先の設定などを行うために、アカウント ID を取得する機会があり調査したメモです。


### AWS Classic (`@pulumi/aws`) の場合

AWS Classic (`@pulumi/aws`) は terraform の AWS Provider を元に作成されています


terraform の場合は `aws_caller_identity` を利用しますが Pulumi でも同様の data source を利用します
terraform で AWS アカウント ID を取得する場合は以下記事を参考ください。

https://qiita.com/gongo/items/a2b83d7402b97ef43574


AWS Classic (`@pulumi/aws`) の場合は `getCallerIdentity` を利用し取得
`getCallerIdentity` terraform 同様、 アカウント ID 以外にも ユーザーID やその ARN を取得することが出来ます

```ts:index.ts
import * as pulumi from "@pulumi/pulumi"
import * as aws from "@pulumi/aws"

const callerIdentity = aws.getCallerIdentity({})
export const accountId = callerIdentity.then((callerIdentity) => callerIdentity.accountId)
```

`getCallerIdentity` に渡すことが出来るオプションなどは以下ドキュメントを参照してください

https://www.pulumi.com/registry/packages/aws/api-docs/getcalleridentity/


#### `pulumi preview` を実行する

以下 AWS Classic package で AWS アカウント ID を取得した結果です。

```zsh
% pulumi preview
Previewing update (xxxxxx)

View Live: https://app.pulumi.com/mziyut/xxxxxx/xxxxxx/xxxxxx/xxxxxx-xxxxxx-xxxxxx-xxxxxx-xxxxxx

     Type                 Name                       Plan
     xxxxxx               xxxxxx

Outputs:
  + accountId    : "xxxxxxxxxxxx"

Resources:
    xxxxxx unchanged
```

### AWS Native の場合

AWS Native Packege を利用している場合は
`getAccountId` を利用することで アカウント ID を取得することができます


```ts:index.ts
import * as pulumi from "@pulumi/pulumi"
import * as awsNative from "@pulumi/aws-native"

export const accountId = awsNative.getAccountId()
```

https://www.pulumi.com/registry/packages/aws-native/api-docs/getaccountid/


#### `pulumi preview` を実行する

以下 AWS Native package で AWS アカウント ID を取得した結果です。

```zsh
% pulumi preview
Previewing update (xxxxxx)

View Live: https://app.pulumi.com/mziyut/xxxxxx/xxxxxx/xxxxxx/xxxxxx-xxxxxx-xxxxxx-xxxxxx-xxxxxx

     Type                 Name                       Plan
     xxxxxx               xxxxxx

Outputs:
  + accountId    : {
      + accountId: "xxxxxxxxxxxx"
    }

Resources:
    xxxxxx unchanged
```


## 最後に

AWS Classic / AWS Native それぞれで AWS アカウント ID を取得する方法を記載しました。
AWS のリソースへのアクセス権限 （bucket policy 等） や SNS Topic の設定などもハードコーディングせず設定できるようになるはずです。