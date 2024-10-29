# 🔨 Hands-on: 段階的なデプロイメント

このハンズオン ラボでは、enviornmentsの作成と、approvalによる段階的なデプロイメントを学びます。
> Note: 無料アカウントについては、パブリックリポジトリでのみ利用できます。`private`リポジトリに対するenvironmentsの追加は、Teamかそれ以上のプランによるOrganization配下、もしくはGitHub Proアカウントのユーザーのみ利用できます。

このハンズオン ラボは[はじめてのワークフロー](01-My-first-workflow.md)をもとに、下記の手順を追加します:
- [Creating and protecting environments](#creating-and-protecting-environments)
- [Adding a input for picking environments to manual workflow trigger](#adding-a-input-for-picking-environments-to-manual-workflow-trigger)
- [Chaining workflow steps and conditional execution](#chaining-workflow-steps-and-conditional-execution)
- [Reviewing and approving deployments](#reviewing-and-approving-deployments)

## Creating and protecting environments

1. [Settings](/../../settings) | [Environments](/../../settings/environments)に移動し、[New environment](/../../settings/environments/new)をクリックします。
2. 名前に`Production`を入力し、`Configure environment`ボタンをクリックします。
3. 作成したenvironmentに、自身を`Required reviewer`として追加します:

<img width="349" alt="image" src="https://user-images.githubusercontent.com/5276337/174113475-967127de-45a7-4dc9-8477-4de4df62c7e6.png">

4. `main`ブランチにのみ、このenvironmentに対してデプロイされることを許可します:

<img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174113782-70a1b18a-0ab9-49fd-a53e-cb2ea78916e1.png">

5.2つ以上のenvironmentを作成しましょう。たとえば、`Test`と`Load-Test`とし、何の制限もかけません。

## Adding a input for picking environments to manual workflow trigger

environmentの入力型を`workflow_dispatch`トリガーに追加します。

<details>
  <summary>答え</summary>

```YAML
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy to'
        type: environment
        required: true
```

</details>

## Chaining workflow steps and conditional execution

1. 3つのジョブをワークフローファイルに追加します:
  - Test: `Build`のあとに`ubuntu-latest`上で実行します。ワークフローが手動実行されたときだけ実行します。`Test`というenvironment上で実行します。このジョブはワークフローのログに`Testing...`と書き出します。
  - Load-Test: `Build`のあとに`ubuntu-latest`上で実行します。ワークフローが手動実行されたときだけ実行します。 `Load-Test`というenvironment上で実行します。このジョブはワークフローのログに`Testing...`と書き出し、15秒待機します。
  - Production: `Test`と`Load-Test`のあとに`ubuntu-latest`上で実行します。入力パラメータで`Production`が選ばれた場合にだけ、`Production`にデプロイします。そのenvironmentは`https://writeabout.net`というURLを持ちます。デプロイメントをシミュレートするために、ジョブは5つのステップを実行します。それぞれのステップはワークフローのログに`Step x deploying...`と書き出し、10秒待機します。

<details>
  <summary>答え</summary>

```YAML
  Test:
    runs-on: ubuntu-latest
    if: github.event_name == 'workflow_dispatch'
    needs: Build
    environment: Test
    steps:
      - run: echo "🧪 Testing..."

  Load-Test:
    runs-on: ubuntu-latest
    if: github.event_name == 'workflow_dispatch'
    needs: Build
    environment: Load-Test
    steps:
      - run: |
          echo "🧪 Testing..."
          sleep 15

  Production:
    runs-on: ubuntu-latest
    needs: [Test, Load-Test]
    environment:
      name: Production
      url: https://writeabout.net
    if: github.event.inputs.environment == 'Production'
    steps:
      - run: |
          echo "🚀 Step 1..."
          sleep 10
      - run: |
          echo "🚀 Step 2..."
          sleep 10
      - run: |
          echo "🚀 Step 3..."
          sleep 10
      - run: |
          echo "🚀 Step 4..."
          sleep 10
      - run: |
          echo "🚀 Step 5..."
          sleep 10
```

</details>

2. ワークフローを実行し、environmentに`Production`を選択します:

<img width="212" alt="image" src="https://user-images.githubusercontent.com/5276337/174119722-9b76d479-e355-414b-a534-03d8634536ef.png">

## Reviewing and approving deployments

1. ワークフローの実行を開き、レビューを始めましょう。

<img width="600" alt="image" src="https://user-images.githubusercontent.com/5276337/174120029-f395e8ec-5e6e-4350-94c5-130caefaafc2.png">

2. コメントをつけて承認（approve）します:

<img width="350" alt="image" src="https://user-images.githubusercontent.com/5276337/174120086-fed98feb-2d7f-476b-a997-1aa099de7d0e.png">

3. デプロイを行う間のプログレスバーを確認しておきましょう...

<img width="200" alt="image" src="https://user-images.githubusercontent.com/5276337/174120314-c900585c-6b94-4fc2-8fe9-92452b0cf187.png">

4. 結果はこのようになり、承認（approval）の状況や本番環境のURLが含まれます:

<img width="800" alt="image" src="https://user-images.githubusercontent.com/5276337/174120381-cef48594-6663-481a-aadd-1ef0dbd50b0a.png">

## まとめ

このラボでは、GitHubでのenvironmentの作成及び保護、そしてワークフローでの使い方を学びました。また、条件分岐による
ジョブとステップの実行や`needs`キーワードを用いてジョブのチェーンを構成する方法も学びました。

引き続き、[再利用可能なワークフロー](04-Reusable-workflows-ja.md)に進みましょう。
