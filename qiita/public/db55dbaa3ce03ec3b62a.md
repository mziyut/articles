---
title: GitLab Runner のジョブ実行中に Slack 通知を行う
tags:
  - GitLab
  - GitLab-CI
  - GitLab-CI-Runner
  - gitlab-runner
private: false
updated_at: '2023-06-16T14:34:11+09:00'
id: db55dbaa3ce03ec3b62a
organization_url_name: qiita-inc
slide: false
ignorePublish: false
---

## はじめに

普段は GitHub を利用していますが、 GitLab + GitLab Runner を触る機会がありました。
GitLab Runner の Job 実行中に Slack 通知を送信しようと設定したことがあり、その手順、方法についてまとめました。

## 環境

- GitLab
  - SaaS/Cloud (gitlab.com)
  - レポジトリは作成済み
- GitLab Runners
  - Shared runners を利用

## GitLab Runner から Slack 通知送信の設定

Slack に通知を送信するために以下を行います。

- GitLab Runner の Variables 設定
- `.gitlab-ci.yml` を追加

### GitLab Runner の設定

まず、GitLab Runner の Variables を設定します
レポジトリ Top > Settings > CI/CD に進み、Variables を設定します。

`https://gitlab.com/<YOUR_USER_OR_ORG_NAME>/<REPOSITORY_NAME>/-/settings/ci_cd`

設定する値は、以下に記載の Slack の Webhook URL です
「Add Variable」 をクリック モーダル内に必要な情報を入力していきます。

- Key: `SLACK_WEBHOOK_URL`
- Value: `https://hooks.slack.com/services/xxxxxxxx/xxxxxxxx/xxxxxxxx`
  - 一部文字を置き換えています
- Type: Variable
- Environment scope: All
- Flags
  - Protect variable: (off)
  - Mask variable: (on)

![Screen Shot 0004-05-05 at 13.52.02.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/b2dec0c7-695e-8c75-a01a-0ec164798849.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/788104c9-c34f-9ac0-efcc-7af3b5555276.png)

### `.gitlab-ci.yml` を追加

今回は、 `Test` とだけ送信する Job を定義しました。
`variables.MESSAGE` 部分を変更いただければ、通知する チャンネルや icon 等も変更いただけます。

```yml:.gitlab-ci.yml
stages:
  - test

test-slack-notify:
  stage: test
  image: alpine
  variables:
    MESSAGE: '{"text":"Test"}'
  script:
    - apk add curl
    - 'curl -X POST --data-urlencode "payload=${MESSAGE}" --url ${SLACK_WEBHOOK_URL}'
```

ファイルを追加しコミットすると GitLab Runner が実行されます。

```console
Running with gitlab-runner 14.10.0~beta.50.g1f2fe53e (1f2fe53e)
  on blue-1.shared.runners-manager.gitlab.com/default j1aLDqxS
Preparing the "docker+machine" executor
00:06
Using Docker executor with image alpine ...
Pulling docker image alpine ...
Using docker image sha256:0ac33e5f5afa79e084075e8698a22d574816eea8d7b7d480586835657c3e1c8b for alpine with digest alpine@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454 ...
Preparing environment
00:01
Running on runner-j1aldqxs-project-35898685-concurrent-0 via runner-j1aldqxs-shared-1651726213-66f07ad3...
Getting source from Git repository
00:01
$ eval "$CI_PRE_CLONE_SCRIPT"
Fetching changes with git depth set to 20...
Initialized empty Git repository in /builds/mziyut/gitlab-ci-test/.git/
Created fresh repository.
Checking out 5953f134 as add-slack-notification...
Skipping Git submodules setup
Executing "step_script" stage of the job script
00:02
Using docker image sha256:0ac33e5f5afa79e084075e8698a22d574816eea8d7b7d480586835657c3e1c8b for alpine with digest alpine@sha256:4edbd2beb5f78b1014028f4fbb99f3237d9561100b6881aabbf5acce2c4f9454 ...
$ apk add curl
fetch https://dl-cdn.alpinelinux.org/alpine/v3.15/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.15/community/x86_64/APKINDEX.tar.gz
(1/5) Installing ca-certificates (20211220-r0)
(2/5) Installing brotli-libs (1.0.9-r5)
(3/5) Installing nghttp2-libs (1.46.0-r0)
(4/5) Installing libcurl (7.80.0-r1)
(5/5) Installing curl (7.80.0-r1)
Executing busybox-1.34.1-r5.trigger
Executing ca-certificates-20211220-r0.trigger
OK: 8 MiB in 19 packages
$ curl -X POST --data-urlencode "payload=${MESSAGE}" --url ${SLACK_WEBHOOK_URL}
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    39    0     2  100    37     13    258 --:--:-- --:--:-- --:--:--   2ok74
Cleaning up project directory and file based variables
00:01
Job succeeded
```

https://gitlab.com/mziyut/gitlab-ci-slack-notify-test/-/jobs/2415749798

Job が走りきると Slack に設定したテストメッセージが送信されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/55950/fabed8f9-8889-1668-beb4-76d41d7e3590.png)

## Reference

https://gitlab.com/mziyut/gitlab-ci-slack-notify-test

https://docs.gitlab.com/ee/ci/

https://docs.gitlab.com/ee/ci/variables/index.html#add-a-cicd-variable-to-a-project

https://docs.gitlab.com/ee/ci/yaml/gitlab_ci_yaml.html
