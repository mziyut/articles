<!--
title:   Terraformer を docker で気軽に実行できる環境を用意する
tags:    Docker,Terraform,docker-compose,terraformer
id:      e84d3e9f3646507c0b2a
private: false
-->
## はじめに

Terraformer は簡単に設定、利用できますが、Terraformer だけでなく Terraform もある程度理解することが求められます。
Terraformer だけを docker で気軽に実行できる環境を用意したかったためその手順と内容をまとめました。

## Terraformer について

改めて、 Terraformer とは 構築済みのインフラを tf ファイルに変更してくれるツールです。

https://github.com/GoogleCloudPlatform/terraformer

Terraformer の実行方法については以下記事を参照ください :information_desk_person:

https://qiita.com/mziyut/items/5416dca4614f181d9468

## `Dockerfile` を用意する

README.md に用意されている `Installation > From Releases: > Linux` を参照し用意されている Shell を Dockerfile 内に記載します。

```sh
export PROVIDER={all,google,aws,kubernetes}
curl -LO https://github.com/GoogleCloudPlatform/terraformer/releases/download/$(curl -s https://api.github.com/repos/GoogleCloudPlatform/terraformer/releases/latest | grep tag_name | cut -d '"' -f 4)/terraformer-${PROVIDER}-linux-amd64
chmod +x terraformer-${PROVIDER}-linux-amd64
sudo mv terraformer-${PROVIDER}-linux-amd64 /usr/local/bin/terraformer
```

README.md 内のサンプルだと `export PROVIDER={all,google,aws,kubernetes}` を実行し AWS や GCP k8s 等すべての実行ファイルを取得していますが `all` のみ取得すれば問題ありません

作成した Dockerfile は以下のようになります。

```dockerfile:Dockerfile
FROM hashicorp/terraform:0.13.7

RUN apk update --no-cache && \
    apk add curl bash

RUN curl -LO https://github.com/GoogleCloudPlatform/terraformer/releases/download/$(curl -s https://api.github.com/repos/GoogleCloudPlatform/terraformer/releases/latest | grep tag_name | cut -d '"' -f 4)/terraformer-all-linux-amd64
RUN chmod +x terraformer-all-linux-amd64 && \
    mv terraformer-all-linux-amd64 /usr/local/bin/terraformer

WORKDIR /app/terraform

ENTRYPOINT ["/usr/local/bin/terraformer"]
CMD ["--help"]
```

## `docker-compose.yml` を用意する

次に実行を簡単にするために `docker-compose.yml` を作成します

```yml:docker-compose.yml
version: "3"

services:
  terraformer:
    build:
      context: .
      dockerfile: ./Dockerfile
    volumes:
      - ./:/app/terraform
```

## コンテナを実行する

`Dockerfile` と `docker-compose.yml` を用意したので コンテナを作成し terraformer の --help を実行します
以下のように Terraformer の help が表示されればOKです。

```zsh
% docker-compose run --rm terraformer
Creating terraformer_terraformer_run ... done
Usage:
   [command]

Available Commands:
  help        Help about any command
  import      Import current state to Terraform configuration
  plan        Plan to import current state to Terraform configuration
  version     Print the version number of Terraformer

Flags:
  -h, --help      help for this command
  -v, --version   version for this command

Use " [command] --help" for more information about a command.
```

また、 Terraformer のコンテナに用いたベースイメージは Terraform を用いたため entrypoint を上書きすると terraform コマンドも実行することができます。

```zsh
mziyut% docker-compose run --rm --entrypoint terraform terraformer
Creating terraformer_terraformer_run ... done
Usage: terraform [-version] [-help] <command> [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.

Common commands:
    apply              Builds or changes infrastructure
    console            Interactive console for Terraform interpolations
    destroy            Destroy Terraform-managed infrastructure
    env                Workspace management
    fmt                Rewrites config files to canonical format
    get                Download and install modules for the configuration
    graph              Create a visual graph of Terraform resources
    import             Import existing infrastructure into Terraform
    init               Initialize a Terraform working directory
    login              Obtain and save credentials for a remote host
    logout             Remove locally-stored credentials for a remote host
    output             Read an output from a state file
    plan               Generate and show an execution plan
    providers          Prints a tree of the providers used in the configuration
    refresh            Update local state file against real resources
    show               Inspect Terraform state or plan
    taint              Manually mark a resource for recreation
    untaint            Manually unmark a resource as tainted
    validate           Validates the Terraform files
    version            Prints the Terraform version
    workspace          Workspace management

All other commands:
    0.12upgrade        Rewrites pre-0.12 module source code for v0.12
    0.13upgrade        Rewrites pre-0.13 module source code for v0.13
    debug              Debug output management (experimental)
    force-unlock       Manually unlock the terraform state
    push               Obsolete command for Terraform Enterprise legacy (v1)
    state              Advanced state management
```

## 最後に

`Dockerfile` と `docker-compose.yml` を用意し気軽に `Terraformer` を実行できるようになりました。
`Terraformer` の実行には terraform 設定等必要になりますが公式ドキュメントを参考に設定してみてください。