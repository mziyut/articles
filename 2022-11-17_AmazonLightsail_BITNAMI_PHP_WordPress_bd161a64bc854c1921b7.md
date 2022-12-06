<!--
title:   Bitnami が提供する WordPress 用のイメージは PHP を更新することはできません
tags:    AmazonLightsail,BITNAMI,PHP,WordPress
id:      bd161a64bc854c1921b7
private: false
-->
## はじめに

Amazon Lightsail for Bitnami WordPress を利用し作成されたサーバーの OS やミドルウェアの更新を検討していたが思わぬ所に躓いたので記事にしました

## 結論

タイトルの通り、 Bitnami が提供する WordPress 用のイメージを用いてサーバーを構築している場合
旧サーバー内のデータやコンテンツをエクスポートし、新サーバーにインポートする必要があります

## どうすると良いか

- [Bitnami Documentation] に記載されているエクスポート方法として、 [All-in-One WP Migration] を利用する方法が記載されていました

https://docs.bitnami.com/aws/how-to/migrate-wordpress/

https://wordpress.org/plugins/all-in-one-wp-migration/

手順はとても簡単で

1. 旧サーバーで...
    1. WordPress Plugin [All-in-One WP Migration] をインストールし有効化する
    1. WordPress のコンテンツ/データをエクスポートする
1. 新サーバーで...
    1. 旧サーバーからエクスポートしたデータをインポートする

新しい WordPress サーバーの構築は [Bitnami Documentation] のドキュメントを参考にしてください

https://docs.bitnami.com/aws/how-to/get-started-wordpress-aws-marketplace-beginner/

[Bitnami Documentation]: https://docs.bitnami.com/
[All-in-One WP Migration]: https://wordpress.org/plugins/all-in-one-wp-migration/

## References

https://docs.bitnami.com/aws/how-to/migrate-wordpress/

https://github.com/bitnami/vms/issues/207#issuecomment-1205022832

https://stackoverflow.com/questions/73947826/how-can-i-upgrade-php-in-bitnami-wordpress-stack

https://blog.duaneleem.com/upgrade-aws-lightsail-bitnami-wordpress-using-all-in-one-wp-migration-plugin/