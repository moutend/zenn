---
title: "（スクリーンリーダーのユーザー対象）Google Forms送信時にSlackへ通知するApps Scriptの設定方法"
emoji: "🤖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: []
published: true
---
## はじめに

本記事ではフォームを送信すると、記入された項目がSlackに通知される仕組みの設定方法を説明します。Slackの通知にはIncoming Webhookを利用する想定です。  
また、本記事はスクリーンリーダーで操作する前提の説明をしています。画面のレイアウトについては2025年6月の時点で確認した内容になります。  

## 1. SlackのIncoming Webhook設定手順

以下の手順で「Incoming Webhook」を作成すると、指定したSlackチャネルにHTTP POSTでメッセージを送信できるようになります。

### 1.1. Slack APIサイトにアクセスしてアプリを作成

1. ブラウザで[api.slack.com/apps](https://api.slack.com/apps)を開きます。
2. Create New Appボタンをします。
3. ダイアログが表示されます。選択肢として以下2つのボタンがあることを確認してください。
  - From a manifest
  - From scratch
4. From scratchを選択します。
5. App Nameを入力します。名前は適当で構いません。
6. Workspaceを選択します。ご自身が複数のWorkspaceに所属している場合、操作対象を間違えないように注意してください。
7. Create Appボタンを押します。

操作が成功すると画面が切り替わります。次の手順に進んでください。  

### 1.2. Incoming Webhooksを有効化

1. ページ内のIncoming Webhooksリンクをクリックします。リンクは「Features」という見出しの近くにあります。  
2. Activate Incoming WebhooksのチェックボックスをONにします。
3. 「Webhook URLs for Your Workspace」という見出しの項目が出現します。また、以下のようなcurlコマンドのサンプルが表示されます。この時点では、まだWebhookのURLは発行されていません。  

```console
curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' YOUR_WEBHOOK_URL_HERE
```

### 1.3. Webhookを追加

1. Add New Webhookリンクを押します。curlコマンドのサンプルの近くにリンクが配置されています。
2. 権限の確認画面に切り替わります。Select a channelをクリックして、上下カーソルキーでWebhook送信対象のチャンネルを選んでください。
3. Allowボタンを押します。
4. 操作が完了するとIncoming Webhooksの画面に切り替わります。Webhook URLが一覧に追加されていれば成功です。  

以上でSlackの設定は完了です。

## 2. Google Formsの設定手順

続いてフォームの設定を行います。既存のフォームに設定しても構いませんが、テスト用に適当なフォームを作って試されることをお勧めします。

### 2.1. フォームに紐づいたApps Scriptプロジェクトを作成する

1. ブラウザで[forms.google.com](https://forms.google.com)を開きます。
2. 編集対象のフォームを選択します。
3. フォームの詳細設定に切り替わったら「その他」ボタンを押します。
4. メニューのポップアップが表示されるので「Apps Script」を押します。
5. 別タブまたは別ウィンドウでApps Script設定画面が開きます。

以降の説明では開いた直後のApps Script設定画面を「Apps Scriptのホーム画面」とよびます。

### 2.2. Apps Scriptプロジェクト名を変更する

初期状態はプロジェクト名として「無題のプロジェクト」が設定されています。名前の変更は必須ではありませんが、紛らわしいので変更をお勧めします。

1. Apps Scriptホーム画面で名前を変更ボタンを押します。
2. ダイアログが表示されます。テキストフィールドにプロジェクト名を入力してください。
3. 名前を変更ボタンを押すとプロジェクト名の変更が反映されます。

以降の説明では、プロジェクト名として`Slack Notification Script`を設定したものと想定します。

### 2.3. スクリプトプロパティにWebhook URLを保存する

1. Apps Scriptホーム画面で「プロジェクトの設定」リンクを押します。
2. 画面が切り替わったら「スクリプトプロパティ」という見出しを探します。その近くにある「編集」ボタンを押します。
3. プロパティと値の組み合わせを入力して「保存」ボタンを押します。ここでは以下の組み合わせで保存します。
  - プロパティ: `SLACK_WEBHOOK_URL`
  - 値: WebhookのURL

### 2.4. フォーム送信イベント処理用の関数を登録する

Apps Scriptホーム画面にはコードエディタが表示されています。その内容を以下のJavaScriptで置き換えます。

```js
function onFormSubmit(e) {
  const props = PropertiesService.getScriptProperties();
  const webhookUrl = props.getProperty("SLACK_WEBHOOK_URL");

  // フォーム回答を "質問: 回答" 形式で改行区切りに整形
  const text = e.response
    .getItemResponses()
    .map((r) => `${r.getItem().getTitle()}: ${r.getResponse()}`)
    .join("\n");

  const payload = { text: "お問い合わせがありました。\n\n" + text };
  const options = {
    method: "post",
    contentType: "application/json",
    payload: JSON.stringify(payload),
    muteHttpExceptions: true, // Slack 側エラーでもスクリプトを止めない
  };

  UrlFetchApp.fetch(webhookUrl, options);
}
```

コードを置き換えたら「ドライブにプロジェクトを保存」ボタンを押します。

### 2.5. フォーム送信トリガーを追加する

1. Apps Scriptホーム画面で「トリガー」リンクを押します。
2. トリガーを追加ボタンを押します。
3. 実行する関数として「onFormSubmit」を選択します。通常は選択済みになっているはずです。
4. 項目名「デプロイ」の値として「Head」を選択します。
5. 項目名「イベントのソースを選択」の値として「フォームから」を選択します。
6. 項目名「イベントの種類を選択」の値として「フォーム送信時」を選択します。
7. エラー通知など、その他の項目は初期値のままで構いません。
8. 保存ボタンを押します。

**補足事項**

保存ボタンを押した直後に「保存が失敗しました」と読み上げされる場合があります。これは正しい挙動です。  
はじめてApps Scriptを利用する場合、別タブまたは別ウィンドウで権限承認画面が開きます。ブラウザがポップアップの表示をブロックしている場合は自動で画面が切り替わらないため失敗したと通知されます。手動でブロックを解除してください。  
権限承認画面が開いたら、現在開いているApps Script画面は閉じず、手順6に進んでください。  

### 2.6. トリガーの権限を設定する

手順2.4の続きです。画面には以下のような文言が表示されていることを確認してください。  

> The app is requesting access to sensitive info in your Google Account.
> Until the developer (yourname@gmail.com) verifies this app with Google, you shouldn't use it.

続いて以下の操作を行います。

1. 画面内にあるAdvancedリンクを押します。
2. 「Go to Slack Notification Script (unsafe)」というリンクを押します。
3. 「Make sure you trust Slack Notification Script」という見出しのある画面に切り替わったことを確認します。
4. Allowボタンを押します。
5. 「Slack Notification Script: Has been successfully authorized to access your data.」という文言の画面に切り替わったら成功です。
6. この画面（タブまたはウィンドウ）を閉じて、Apps Scriptホーム画面に戻ります。
7. Apps Script画面に戻ったら、トリガーの設定画面が表示されていることを確認します。
8. 保存ボタンを押します。
9. 画面が切り替わり、トリガーの一覧が表示されたら成功です。

以上でGoogle Formsの設定は完了です。

## おわりに

記事の内容がお役に立てれば幸いです。もし内容に不備がありましたらご指摘ください。
