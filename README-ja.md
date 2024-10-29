# GitHub Actions Fundamentals

`GitHub Actions Fundamentals`トレーニングのリポジトリです。

## 👉 ゴール

- __基礎的なコンポーネント__ とGitHub Actionsの用語を理解する
- __YAML__ の記法を理解する（scalar types, quotes, literal blocks, maps, and sequences）
- __ワークフローの記法__ とインテリセンスを使ったワークフローの記述の仕方を理解する
- ワークフローを __実行（trigger）__ するイベントについて理解する
- __コンテキストと式の記法__ およびワークフロー コマンドを学ぶ
- __アクション__ の異なる形式と作成/公開の仕方について学ぶ
- __ランナー__ の異なるホスティングオプションについて理解する
- 段階的なデプロイメントにおける __シークレット__ と __Environments__ を利用する
- ワークフロー テンプレートと __reusable workflows__

## Getting ready

[こちらの手順](GettingReady.md) に従い、[前提条件](#-prerequisites)をすべて満たすようにセットアップしてください。

## 📆 アジェンダ

### Day 1: 🚀 Getting started

- [ ] 導入とアイスブレイク
- [ ] GitHub Actionsの基礎
- [ ] YAMLの記法
- [ ] 基本的なワークフローの記法
- [ ] ワークフローを実行（trigger）するイベント
- [ ] ジョブとステップ
- [ ] :mag: Demo: ワークフローを作成する
- [ ] 🔨 Hands-on: [はじめてのワークフロー](hol/01-My-first-workflow-ja.md)
- [ ] [ワークフローの記法](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions) 応用編
- [ ] :coffee: 休憩
- [ ] GitHub Actions
- [ ] Actionsの形式
- [ ] 🔨 Hands-on: [はじめてのアクション](hol/02-My-first-action-ja.md)
- [ ] Typescriptアクションとcomposite actions
- [ ] アクションの共有とリリース
- [ ] GitHub APIおよびOctokitの利用
- [ ] Job descriptions

### Day 2
- [ ] 導入
- [ ] CI/CDのためのアクション
- [ ] GitHubトークンのパーミッション
- [ ] Environmentsとシークレット
- [ ] environments、approvalsおよびdeployment gatesを利用した段階的なデプロイメント
- [ ] 🔨 Hands-on: [段階的なデプロイメント](hol/03-Staged-deployments-ja.md)
- [ ] :coffee: 休憩
- [ ] アクションのポリシー
- [ ] ワークフローの実行
- [ ] ワークフローの共有
- [ ] 🔨 Hands-on: [再利用可能なワークフロー](hol/04-Reusable-workflows-ja.md)
- [ ] ベストプラクティスとセキュリティ


## ⚡ Prerequisites

このワークショップは、Azure DevOpsやGitLab、Bitbucketなどの他のプラットフォームを利用したことがあり、GitHubに切替えたいと考える開発者に向けて構成されています。また、gitやCI/CD、DevOpsなどのトピックに初めて触れる方にも適しています。

このワークショップのために、下記をご用意ください:

- ラップトップPC（Windows、MacまたはLinux）
- https://github.com で取得した無料アカウント
- ハンズオン ラボを実施する際、もう一つディスプレイがあると望ましい

## Cheat Sheet
一般的な用語については、[こちら](./CheatSheet.md)をご参照ください。

