<!--
title:   arm64 向け terraform 実行環境を Docker で用意する
tags:    Docker,QiitaEngineerFesta2022,Terraform,arm64,docker-compose
id:      a71a300ea8d4ebb69e14
private: false
-->
## はじめに

Terraform の実行を手元の環境を汚さず容易に実行できるよう、 Docker を利用し実行できる環境を構築していました
しかし、現在 HashiCorp 社が用意する Terraform の Docker image は `x86_64` (`amd64`) のみ実行が可能となっています

調べると Alpine Linux を利用する記事が沢山出てきますが、 Terraform の最新バージョンを利用するためには Alpine Linux Edge バージョンを利用する必要があったり、新しいバージョンを Install するまで時間を要したりと様々コントロール出来ないため自身で Dockerfile を記述し実行環境を整えました

https://pkgs.alpinelinux.org/packages?name=terraform&branch=edge

https://pkgs.alpinelinux.org/packages?name=terraform&branch=v3.15


## PGP Public Keys を取得する

まずはじめに、 HashCorp 社のWeb ページから PGP Public Keys を取得します。
取得した PGP Public Key は Dockerfile と同じ場所 (今回の場合は レポジトリ root) に配置します

https://www.hashicorp.com/security


## `Dockerfile` を作成する

以下のような `Dockerfile` を用意します。


```dockerfile:Dockerfile
FROM alpine:3.15.0

ENV TERRAFORM_VERSION=1.2.4

RUN apk add --no-cache curl gnupg

WORKDIR /tmp

COPY hashicorp.asc /tmp/hashicorp.asc
RUN gpg --import hashicorp.asc && \
    rm hashicorp.asc

RUN case $(uname -m) in \
         "x86_64")  TERRAFORM_CPU_ARCH=amd64 ;; \
         "aarch64")  TERRAFORM_CPU_ARCH=arm64 ;; \
    esac && \
    curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${TERRAFORM_CPU_ARCH}.zip && \
    curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS && \
    curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS.sig && \
    gpg --verify terraform_${TERRAFORM_VERSION}_SHA256SUMS.sig terraform_${TERRAFORM_VERSION}_SHA256SUMS && \
    grep terraform_${TERRAFORM_VERSION}_linux_${TERRAFORM_CPU_ARCH}.zip terraform_${TERRAFORM_VERSION}_SHA256SUMS | sha256sum -c && \
    unzip terraform_${TERRAFORM_VERSION}_linux_${TERRAFORM_CPU_ARCH}.zip && \
    mv terraform /usr/local/bin/ && \
    chmod +x /usr/local/bin/terraform && \
    rm -f terraform_${TERRAFORM_VERSION}_linux_${TERRAFORM_CPU_ARCH}.zip && \
    terraform version

WORKDIR /app

ENTRYPOINT ["/usr/local/bin/terraform"]
```

ポイントは、`case $(uname -m) in` としているところです
Docker build を実行する Host マシンの CPU architecture を判定し、取得する ファイルを動的に切り替えます。

```dockerfile:Dockerfile
RUN case $(uname -m) in \
         "x86_64")  TERRAFORM_CPU_ARCH=amd64 ;; \
         "aarch64")  TERRAFORM_CPU_ARCH=arm64 ;; \
    esac
```

事前に Docker image を build し Docker Hub 等に push し利用いただく環境の場合は、
以下のように `${TARGETPLATFORM}` から ターゲットとなる CPU architecture を取得し `docker build` ではなく `docker buildx` を利用し Build を行うようにしてください

```dockerfile:Dockerfile
RUN case ${TARGETPLATFORM} in \
         "linux/amd64")  TINI_ARCH=amd64  ;; \
         "linux/arm64")  TINI_ARCH=arm64  ;; \
         "linux/arm/v7") TINI_ARCH=armhf  ;; \
         "linux/arm/v6") TINI_ARCH=armel  ;; \
         "linux/386")    TINI_ARCH=i386   ;; \
    esac
```

## `docker-compose.yml` を作成する

実行の度に docker コマンドを打つのはとても面倒なので `docker-compose.yml` を作成します
作成後は `docker compose run --rm terraform` で実行することができます

```yml:docker-compose.yml
version: "3"
services:
  terraform:
    build: .
    volumes:
      - ./:/app/terraform
```

## Reference

- https://www.terraform.io/downloads
- https://github.com/BretFisher/multi-platform-docker-build
- https://docs.docker.com/engine/reference/builder/#automatic-platform-args-in-the-global-scope