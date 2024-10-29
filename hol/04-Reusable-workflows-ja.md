# 🔨 Hands-on: 再利用可能なワークフロー

このハンズオン ラボでは、[reusable workflow](https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow)とそれを利用するワークフローの作成について学びます。reusable workflowに対してパラメータを渡し、呼び出す側のワークフローで出力パラメータを利用する方法も学びます。

このハンズオン ラボは、以下の手順で構成されています:
- [Creating a reusable workflow](#creating-a-reusable-workflow)
- [Adding an output parameter](#adding-an-output-parameter)
- [Consuming the reusable workflow](#consuming-the-reusable-workflow)

## Creating a reusable workflow

1. [新しいファイル](/../../new/main)として`.github/workflows/reusable.yml`を作成します（入力欄にこのパスを含むファイル名を貼りつけます）。
2. 名前に`Reusable workflow`を指定します。

<details>
  <summary>答え</summary>

```YAML
name: Reusable workflow
```

</details>

3. [入力パラメータ](https://docs.github.com/en/enterprise-cloud@latest/actions/using-workflows/workflow-syntax-for-github-actions#onworkflow_call)に`string`型の必須項目として`who-to-greet`を構成し、`workflow_call`トリガーを追加します。既定の値に`World`を設定します。

<details>
  <summary>答え</summary>

```YAML
on:
  workflow_call:
    inputs:
      who-to-greet:
        description: 'The person to greet'
        type: string
        required: true
        default: World
```

</details>

4. `ubuntu-latest`上で実行され、コンソールに"Hello \<input parameter\>"と表示する`reusable-job`という名前のジョブを追加します。 

<details>
  <summary>答え</summary>

```YAML
jobs:
  reusable-job:
    runs-on: ubuntu-latest
    steps:
      - name: Greet someone
        run: echo "Hello ${{ inputs.who-to-greet }}"
```

</details>

## Adding an output parameter

1. さらに、idに`time`を指定したステップを追加します。
このステップでは、ワークフロー コマンドを使用して、実行時点の日付と時間を`current-time`という名前の出力パラメータとして設定します。（このために`$(date)`を使います）。

<details>
  <summary>答え</summary>

```YAML
      - name: Set time
        id: time
        run: echo "time=$(date)" >> $GITHUB_OUTPUT
```

</details>

2. `current-time`という出力を`reusable-job`に追加します。

<details>
  <summary>答え</summary>

```YAML
   outputs:
      current-time: ${{ steps.time.outputs.time }}
```

</details>

3. `current-time`というパラメータを`workflow_call`に追加し、それをワークフロー コマンドの出力に設定します。

<details>
  <summary>答え</summary>

```YAML
    outputs:
      current-time:
        description: 'The time when greeting.'
        value: ${{ jobs.reusable-job.outputs.current-time }}
```

</details>


<details>
  <summary>完全な答え</summary>

```YAML
name: Reusable workflow

on:
  workflow_call:
    inputs:
      who-to-greet:
        description: 'The person to greet'
        type: string
        required: true
        default: World
    outputs:
      current-time:
        description: 'The time when greeting.'
        value: ${{ jobs.reusable-job.outputs.current-time }}

jobs:
  reusable-job:
    runs-on: ubuntu-latest
    outputs:
      current-time: ${{ steps.time.outputs.time }}
    steps:
      - name: Greet someone
        run: echo "Hello ${{ inputs.who-to-greet }}"
      - name: Set time
        id: time
        run: echo "time=$(date)" >> $GITHUB_OUTPUT
```

</details>

## Consuming the reusable workflow

1. [新しいファイル](/../../new/main)として`.github/workflows/reuse.yml`を作成します（入力欄にこのパスを含むファイル名を貼りつけます）。
2. 名前に`Reuse other workflow`を指定し、手動のトリガーを追加します。

<details>
  <summary>答え</summary>

```YAML
name: Reuse other workflow

on: [workflow_dispatch]
```

</details>

3. 作成したreusable workflowを使用し、自身の名前を入力パラメータとして渡す`call-workflow`というジョブを追加します。 

<details>
  <summary>答え</summary>

```YAML
jobs:
  call-workflow:
    uses: ./.github/workflows/reusable.yml
    with:
      who-to-greet: '@octocat'
```

</details>

4. もう一つ、`current-time`という出力パラメータをコンソールに表示する`use-output`というジョブを追加します。（ヒント: 出力にアクセスするには、needsコンテキストを使います）

<details>
  <summary>答え</summary>

```YAML
  use-output:
    runs-on: ubuntu-latest
    needs: [call-workflow]
    steps:
      - run: echo "Time was ${{ needs.call-workflow.outputs.current-time }}"
```

</details>

5. ワークフローを実行し、出力を確認しましょう。

## Summary

このラボでは、reusable workflowとそれを利用するワークフローのつくり方を学びました。また、reusable workflowにパラメータを渡したり、利用する側のワークフローで出力パラメータを使用する方法も学びました。

それでは、[README](../README-ja.md)に戻りましょう.
