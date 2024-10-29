# 🔨 Hands-on: はじめてのワークフロー

このハンズオン ラボでは、最初のGitHub Actionsワークフローを作成し、ソフトウェア開発サイクルの中で、タスクを自動化するためにどうActionsを使うかを学びます。もしより詳しい情報を知りたい場合は、[GitHub Actions](https://docs.github.com/ja/actions/learn-github-actions/understanding-github-actions)のページをご覧ください。グッドラック！👍

このハンズオン ラボは、以下の手順で構成されています:
- [Creating a repository](#creating-a-repository)
- [Creating the workflow](#creating-the-workflow)
- [Viewing your workflow results](#viewing-your-workflow-results)

## Creating a repository

このリポジトリの[Code](/../../)タブを開き、`Use this template`ボタンをクリックします:

<img width="400" alt="2022-09-18_11-24-58" src="https://user-images.githubusercontent.com/5276337/190895393-6fa0fad9-e05c-4fea-8126-a291b087d663.png">

ownerにあなたのGitHubアカウントを選択し、リポジトリの名前を入力します。GitHub Actionsの無料枠を利用するため（公開範囲の設定は）パブリックのままとします:

<img width="400" alt="2022-09-18_11-25-57" src="https://user-images.githubusercontent.com/5276337/190895398-751a1ec9-c1cf-497f-beb7-a6b53d4d911e.png">

以降、新しいリポジトリで進めます。

## Creating the workflow

**Actions**タブ | [New Workflow](/../../actions/new)に移動し、[set up a workflow yourself](/../../new/main?filename=.github%2Fworkflows%2Fmain.yml&workflow_template=blank)をクリックします。

1. `.github/workflows` ディレクトリ配下の`main.yml`のファイル名を`github-actions-demo.yml`に変更します。
<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174096754-4e2d7219-9caf-42e8-bfd9-c190762886d3.png">

2. 一からワークフローを作成するため、テンプレートの内容を削除します。
3. <kbd>Ctrl</kbd>+<kbd>Space</kbd>を押下し、最初の要素としてnameを選択します:

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174097468-8be92e37-7948-4895-b5ed-20a22c5773bc.png">

4. ワークフローの名前に`GitHub Actions Demo`と設定します:

```YAML
name: GitHub Actions Demo
```

5. <kbd>Ctrl</kbd>+<kbd>Space</kbd>とドキュメンテーションを利用してワークフローにトリガーを追加します。私たちは、以下のような契機でワークフローを実行したいです:
  - `main`ブランチに対するすべてのpush、ただし`.github`ディレクトリの変更は除く
  - `main`ブランチに対する全てのプルリクエスト
  - 毎日曜日の6:15 UTC
  - 手動実行

<details>
  <summary>答え</summary>

```YAML
on:
  push:
    branches: [ main ]
    paths-ignore: [.github/**]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '15 6 * * 0'
  workflow_dispatch:
```

</details>

6. GitHubホステッドランナーで最新のUbuntuイメージで実行するように`Build`ジョブを作成します。どのラベルを使うべきか、どのバージョンかについては[virtual environments](https://github.com/actions/virtual-environments/)のドキュメンテーションをご確認ください。このジョブは、以下が実行されるようにします:
  - ワークフローを実行したイベントの名前を出力する
  - リポジトリが参照しているブランチの名前を出力する
  - リポジトリのすべてのファイルを一覧表示する

<details>
  <summary>答え</summary>

```YAML
jobs:
  Build:
    runs-on: ubuntu-latest
    steps:
      - run: |
          echo "🎉 The job was triggered by event: ${{ github.event_name }}"
          echo "🔎 The name of your branch is ${{ github.ref }} and your repository is ."

      - uses: actions/checkout@v3.3.0

      - name: List files in the repository
        run: |
          echo "The repository ${{ github.repository }} contains the following files:"
          tree
```

</details>

7. そのワークフローをコミットし、手動で実行します。pathフィルタが正しく機能していれば、自動的に実行されないはずです。[Action](/../../Actions)タブへ移動し,[GitHub Actions Demo](/../../actions/workflows/github-actions-demo.yml)と`Run workflow`をクリックします:

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174105162-19f33fd1-8533-4860-9279-88fabec84451.png">


## Viewing your workflow results

1. 実行したワークフローをクリックします:

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174105747-0e205e0d-37cc-464c-905b-5b29be74fc75.png">

2. 'Build'ジョブをクリックします:

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174105990-a1c204c6-fb7d-44a4-9343-6982899edb25.png">

3. `Set up job`を開き、行番号とログを記録します（行番号はリンクです）。ソフトウェアを含む仮想環境に関する情報を確認します:

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174106759-c2a8f933-74cf-42a4-899b-b29dc67eccd7.png">

4. 作成したジョブを開き、その出力が正しいことを確認します。

<img width="400" alt="image" src="https://user-images.githubusercontent.com/5276337/174107136-af9187c1-dbee-4109-9ddc-f2abd4830282.png">

5. [README.md](/README.md)ファイルを編集して他のトリガーが実行されることを確認します:
  - 編集してコミットする: トリガ―が実行される（`push`）
  - 編集してExtended descriptionに`[skip ci]`を書き加える（ワークフローは実行されない）:
  <img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174110845-93d4a38a-9c8a-4336-9b6a-9089ea9a1cfd.png">

  - 編集してプルリクエストを作成する（`pull_request`がトリガーされる）

## まとめ

このハンズオンでは、トリガー、ジョブ、ステップ、条件式を含む最初のワークフローの作成を学びました。次は、[はじめてのアクション](02-My-first-action-ja.md)を書いてみましょう.
