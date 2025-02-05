---
title: "AlibabaのDifyみたいなサービス。Alibaba Model StudioでWorkflowアプリを作ってみた"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["alibaba", "cloud", "dify", "model", "workflow"]
published: true
---

AlibabaにもDifyと似たような機能があるという噂を聞いたので、触ってみました。

Difyはノーコードの生成AIアプリケーション開発ツールなのですが、簡単に独自の生成AIアプリを作ることができるので、検証などでよく使っております。

今回構築するアプリは、記事を要約するアプリにしたいと思います。

# Workflowアプリで使える要素

- LLM
- Knowledge
- API
- Intent Classification
- Text Conversion
- Script Conversion
- Conditional Judgement
- Function Compute

## LLM

プロンプトを記述して、選択したモデルに指示を与えることができます。変数やContextなどの機能もあるため、より細かく制御することもできます。

![CleanShot 0006-12-19 at 19.31.29.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.31.29.png)

## Knowledge

いわゆるRAGを利用することもできます。Model Studioの画面からRAGを作ることができ、ローカルのファイルをアップロードして、独自のRAG構築が可能です。

![CleanShot 0006-12-19 at 19.33.29.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.33.29.png)

![CleanShot 0006-12-19 at 19.34.31.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.34.31.png)

## API

APIブロックは外部のAPIを利用するためのブロックです。GETやPOSTなど指定してあげれば、Workflowの処理中に外部APIを叩いた結果を使用することができます。

![CleanShot 0006-12-19 at 19.36.48.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.36.48.png)

## Intent Classification

Intent Classificationブロックは、ユーザーが入力したqueryを生成AIモデルを使用して分類してくれます。分類する種別をそれぞれ作成して、分類結果に合わせて後続の処理を分岐させることができます。

![CleanShot 0006-12-19 at 19.38.26.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.38.26.png)

## Text Conversion

Text Conversionブロックは、LLMブロックやその他のブロックの出力結果などの変数を参照して特定のテンプレートまたは形式に基づいて変換できます。

![CleanShot 0006-12-19 at 19.46.21.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.46.21.png)

## Script Conversion

Script Conversionブロックは、PythonもしくはJavascriptのコードを記述することで、より複雑なデータ変換処理などを行うことができます。

Json形式のデータ変換などが用途として多いのではないかと思います。

![CleanShot 0006-12-19 at 19.47.39.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-19_at_19.47.39.png)

## Conditional Judgement

変数の値を元にIF/ELSEで条件分岐させることができます。

![CleanShot 0006-12-20 at 12.12.00.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-20_at_12.12.00.png)

## Function Compute

Alibabaのサーバーレス関数のFunction Computeを呼び出すことができます。

Function Computeを使うことができるので、より柔軟に処理を行えます。独自のリソースへのアクセスや複雑な処理を行えるので非常に便利だと思います。

![CleanShot 0006-12-20 at 12.14.51.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-20_at_12.14.51.png)

　　　　Function Computeのアクティベート

![CleanShot 0006-12-20 at 12.15.25.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-20_at_12.15.25.png)

# アプリ作成手順

1. アプリタイプを選択
2. モデルのアクティベート
3. Workflowタイプでブロックを繋げて構築

# アプリの構築手順

1. **Applicationの作成**
Alibaba Model Studioにログイン後、「My Application」から新しいアプリを作成します。アプリタイプ「Workflow」を選択し、会話形式のWorkflow「Create Dialog Workflow」を作成します。

    
    ![CleanShot 0006-12-20 at 12.27.20.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-20_at_12.27.20.png)
    
2. **ワークフローの設計**
ワークフローエディタで、アプリのフローを構築します。今回は、記事の要約を実現するために以下のステップを設定しました。
    - **Intent Classification**：入力されたテキストが要約対象であることを確認。
    - **LLM**：テキストを要約する処理を実行。
    - **Text Conversion**：生成された要約を整形して出力フォーマットに適用。
    
    ![CleanShot 0006-12-20 at 12.32.25.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-12-20_at_12.32.25.png)
    
3. **LLMの設定**
LLMノードを追加し、適切なモデルを選択します。Alibaba Cloudが提供するモデルから、要約に適したものを使用します。モデルのパラメータ（例: 要約の長さ、トーン）をカスタマイズします。
4. **条件分岐の設定**
Intent Classificationノードを使用して、入力データに応じた処理を分岐させます。テキストの内容によって記事がAIの内容かどうかを判別させて、要約の処理を行う設定を行いました。
AIの内容以外の場合は、記事の内容がどのようなトピックなのか、を判定するようにLLMを設定しました。
5. **テストとデプロイ**
ワークフローをテストし、期待通りに動作することを確認します。その後、アプリケーションをデプロイして利用可能にします。

# 完成したアプリの動作

完成したアプリは、任意のテキストを入力すると、要約結果を数秒で生成します。例えば、長いニュース記事を簡潔に要約し、主要なポイントを抽出することができます。

# 使用してみた感想

Alibaba Model Studioは、直感的なインターフェースと多機能なノードが揃っており、ノーコードで高度なAIアプリを構築するのに適していると感じました。特に、LLMやIntent ClassificationなどのAI機能が簡単に統合できる点が素晴らしいです。

Difyと比較すると、Model StudioはFunction Computeが使えて、より複雑なワークフローを構築するのに適しており、大規模なプロジェクトにも対応可能だと感じました。

今後、Knowledgeノードを活用して、特定のドメインに特化した要約アプリを構築してみたいと思います。また、Function Computeを使ったカスタム処理にも挑戦してみたいです。

---

## 画像集

- アプリ作成ページ
    
    ![CleanShot 0006-11-27 at 13.55.25.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_13.55.25.png)
    
- アプリタイプ選択
    
    ![CleanShot 0006-11-27 at 13.58.07.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_13.58.07.png)
    
- Workflow初期ページ
    
    ![CleanShot 0006-11-27 at 13.59.01.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_13.59.01.png)
    
- LLMの作成
    
    ![CleanShot 0006-11-27 at 13.59.56.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_13.59.56.png)
    
- モデルのアクティベート
    
    ![CleanShot 0006-11-27 at 14.00.53.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_14.00.53.png)
    
- アクティベートの同意
    
    ![CleanShot 0006-11-27 at 14.01.16.png](/images/alibaba-dify-alibaba-model-studio-workflow/CleanShot_0006-11-27_at_14.01.16.png)
    

- プロンプト
    - AI
    
    ```
    #Background#
    あなたの強みである記事の要約について、以下の要件に基づいて作業をお願いします。
    
    #Style#
    与えられた記事の内容を詳細に分析し、その核心を捉えた要約を作成してください。
    要約は日本語で行い、以下のポイントを確保すること：
     1. 記事の主要なポイントと情報が正確かつ完全に含まれていること。
     2. 要約が元記事の意味や調子を損なわず、流通性と理解の容易さを兼ね備えていること。
     3. 文脈の連続性と論理的な構成が保たれていること。 
     4. 要約結果の文字数が200字以内に収まること。
    
    #Purpose#
    記事のテキストを提供していただき、それを基に日本語の要約を作成いたします。
    
    ```
    
    - その他
    
    ```
    #Background#
    記事の内容をもとに、該当するジャンルを特定してください。
    
    #Style#
    判断の根拠として記事中にみられるキーワードやトピック、筆者の手法、目標読者層などを考慮して説明をお願いします。
    
    ```
