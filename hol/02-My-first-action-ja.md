# 🔨 Hands-on: はじめてのアクション

このハンズオン ラボでは、dockerアクションの作成について、パラメータの渡し方やワークフローへの値の返却の仕方を学びます。そして、CIビルドを利用してアクションをローカルでテストする方法についても学びます。

このハンズオン ラボは、以下の手順で構成されています:
- [Creating the action](#creating-the-action)
- [Testing the action](#testing-the-action)
- Optional: [Release and use the action](#optional-release-and-use-the-action)


## Creating the action

1. [`hello-world-docker-action/action.yml`](/../../new/main?filename=hello-world-docker-action%2Faction.yml)という新しいファイルを作成します:
<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174234628-14f58066-3188-42a6-9204-99c577558c08.png">

2. `action.yml`ファイルに内容を追加します（[template](https://github.com/actions/hello-world-docker-action)と
[help](https://github.com/actions/hello-world-docker-action)をご参考ください）。
`Dockerfile`を起動し、初期値を`world`としてパラメータ`who-to-greet`を渡します。また、（実行時の）現在時刻の出力を返却します。


<details>
  <summary>答え</summary>

```YAML
name: 'Hello World Docker Action'
description: 'Say hello to a user or the world.'
inputs:
  who-to-greet:
    description: 'Who to greet'
    required: true
    default: 'world'
outputs:
  time:
    description: 'The time we said hello.'
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.who-to-greet }}
```

</details>

3. このファイルをコミットします（ビルドをまだ実行させないように`[skip ci]`を記述します）。
4. `hello-world-docker-action`ディレクトリの中で[`Dockerfile`](/../../new/main?filename=hello-world-docker-action%2FDockerfile)を作成します。このコンテナは`FROM alpine:3.10`を継承し、`entrypoint.sh`ファイルをコピーして実行するようにします。`RUN chmod +x entrypoint.sh`を記述して、スクリプトを実行可能にすることを忘れないでください。

<details>
  <summary>答え</summary>

```dockerfile
FROM alpine:3.10

COPY entrypoint.sh /entrypoint.sh

RUN chmod +x entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

</details>

5. このファイルをコミットします（この時点では、ビルドをまだ実行させないように`[skip ci]`を記述します）。
6. そのディレクトリに[`entrypoint.sh`](/../../new/main?filename=hello-world-docker-action%2Fentrypoint.sh)ファイルを作成します。これは、コンソールにメッセージを表示して出力パラメータを設定するシンプルなbashスクリプトです。

<details>
  <summary>答え</summary>

```bash
#!/bin/sh -l

echo "hello $1"

echo "time=$(date)" >> $GITHUB_OUTPUT
```

</details>

7. このファイルをコミットします（ビルドをまだ実行させないように`[skip ci]`を記述します）。

## Testing the action

1. このアクションをテストするために、新しいワークフローファイル[`.github/workflows/hello-world-docker-ci.yml`](/../../new/main?filename=.github%2Fworkflows%2Fhello-world-docker-ci.yml&workflow_template=blank)を作成します。
2. アクションが変更された契機で実行させるため、このワークフローは全てのpushで実行されるようにします。また、手動でビルドを実行できるように、手動のトリガーも追加します。
   git参照なしでアクションをローカルで参照できるように、リポジトリをチェックアウトします。

<details>
  <summary>答え</summary>

```YAML
name: CI Build for Docker Action
on:
  push:
    branches: [ main ]
    paths: [ hello-world-docker-action/** ]
  workflow_dispatch:

jobs:
  test-action:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3.3.0

      - name: Run my own container action
        id: hello-action
        uses: ./hello-world-docker-action
        with:
          who-to-greet: '@wulfland'

      - name: Output time set in the container
        run: echo "The time was ${{ steps.hello-action.outputs.time }} when the action said hello"

```

</details>

3. ワークフローを実行し、パラメータがアクションにどう渡され、何がワークフローに返却されているかを確認しましょう。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174239255-262a8014-4b66-40df-aa17-6f043f948342.png">

## Optional: Release and use the action

もし時間に余裕があるなら、リリースを作成し、それから、他のリポジトリのワークフローからアクションを利用できることを試してもよいでしょう。

> **Note**
> リポジトリのルートにあるGitHub Actionsのみ公開できます。

1. 新しいパブリックリポジトリ`hello-world-docker-action`を作成し、[hello-world-docker-action](../hello-world-docker-action)からすべてのファイルをコピーし配置します。

2. [新しいリリース](/../..releases/new)を、タグに`v1`を指定しタイトルに`v1`を指定して作成します。`Generate release notes`ボタンをクリックし、そのリリースを公開します。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174241482-6d3d0c34-9d55-4e3d-86fa-8ac28055cea8.png">

3. 新しいリポジトリを作成するか他に存在する物を利用して、`v1`バージョンとして作成したそのアクションを呼び出す簡単なワークフローを作成します。

<details>
  <summary>答え</summary>

```YAML
name: Test
on: [workflow_dispatch]

jobs:
  test-action:
    runs-on: ubuntu-latest
    steps:
      - name: Say hello
        uses: <your-github-username>/hello-world-docker-action@v1
        with:
          who-to-greet: '@octocat'
```

</details>

## まとめ

このハンズオン ラボでは、パラーメータを渡したりワークフローに値を返却したりするdockerアクションを作成し、CIビルドを用いてアクションをローカルでテストする方法を学びました。

引き続き、[段階的なデプロイメント](03-Staged-deployments-ja.md)に進みましょう。
