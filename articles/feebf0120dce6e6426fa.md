---
title: "（2021年版）iOSアプリをAppStoreで公開する手順まとめ"
emoji: "✨"
type: "tech"
topics: ["iOS", "iPhone", "AppStore", "アプリ開発"]
published: true
---
# はじめに

以下の点に注意して読み進めてください。

1. macOS 10.15.7 / Xcode 12.4で作業しました。
2. Apple Developerの登録方法は説明しません。
3. 無料アプリを前提に説明します。有料アプリの公開方法は説明しません。
4. Apple DeveloperやAppStore Connectサイトの見た目は頻繁に変わります。そのため、あえてスクリーンショットは添付していません。

それでは、手順1から手順10まで順番に作業を進めてください。

# （手順1）アプリのスクリーンショットを撮影する

1. Xcodeを起動してProducts→Destinationを選択します。
2. iPhone 8 Plusを選択します。
3. シミュレーターが起動したらスクリーンショットを撮影します。ショートカットキー`Command + S`で撮影できます。
4. 同じ手順でiPhone 12 Proのスクリーンショット を撮影します。
5. おわり

## （補足）スクリーンショットの画面サイズについて

アプリの申請には5.5インチと6.5インチ、2種類のスクリーンショットが必要になります。枚数はそれぞれ1枚以上必要です。

画面のサイズが5.5インチまたは6.5インチであれば、シミュレーターはどの機種を選んでも構いません。iPhoneの機種ごとの画面サイズについて、詳細は以下を確認してください。

参考：[スクリーンショットのサイズ要件 - Apple](https://help.apple.com/app-store-connect/?lang=ja-jp#/devd274dd925)

## （補足）シミュレーターの言語設定について

日本語のアプリを開発している場合、まずはシミュレーターの言語を設定してください。手順は以下のとおりです。

1. シミュレーターが起動したら`Command + Shift + H`を押してホーム画面に移動します。
2. Settingsアプリを起動してGeneral→Languageを選択します。
3. Japaneseを選択します。
4. シミュレーターが再起動して日本語表示に切り替わります。
5. スクリーンショット を撮影してください。

# （手順2）App IDを作成する

1. Safariを起動して[Apple Developer](https://developer.apple.com/account/#/welcome)を開きます。
2. Certificates, IDs & Profilesを選択します。
3. Identifiersを選択します。
4. 「+」ボタンを押して新規作成します。
5. App IDsを選択してContinueボタンを押します。
6. Select a typeと表示されたらAppを選択してContinueボタンを押します。
7. DescriptionにApp IDの説明を入力します。（例：`App ID for My App`）
8. Bundle IDとしてExplicitを選択します。
9. テキストボックスにアプリ作成の際に設定したBundle IDを入力します。
10. Capabilities（アプリ実行に必要な権限）一覧の中に必要なものがあればチェックを入れます。
11. Continueボタンを押します。
12. Registerボタンを押します。
13. おわり

# （手順3）アプリをAppStore Connectに登録する

1. [AppStore Connect](https://appstoreconnect.apple.com)を開きます。
2. My Appを選択します。
3. Appsの右側にある「+」ボタンを押して新規作成します。
4. プラットフォームとしてiOSにチェックを入れます。
5. Bundle IDとして手順2で作成したApp IDを選択します。
6. アプリの名前や対応言語、SKUなどを入力します。
7. User AccessとしてFull Accessを選択します。
8. Createボタンを押すと、アプリの詳細情報を編集する画面が表示されます。
9. おわり

## （補足）App ID・Bundle ID・SKUについて

App IDはBundle IDの別名です。Apple DeveloperではApp IDと表示されていたり、AppStore ConnectではBundle IDと表示されていたり、表記方法が統一されていないようです。

App ID（Bundle ID）はアプリの識別子です。一方、SKUはAppStoreでアプリを管理するための識別子です。売上などの管理用に使われるものであり、SKUはユーザーに表示されません。

SKUに命名規則はありません。ただし、`App001`のように適当な名前は紛らわしいので、おすすめしません。私の場合、Bundle IDが`com.example.MyApp`ならSKUは`ComExampleMyApp`のように設定しています。

# （手順4）アプリの詳細情報を編集する

1. 手順1で作成したスクリーンショット や説明文、プライバシー情報や対象年齢などを設定します。
2. Buildは設定せず空欄のままにします。
3. 入力が終わったらSaveボタンを押します。
4. Safariを閉じます。
5. おわり

## （補足）iPadのスクリーンショットについて

アプリの詳細情報を編集する画面にはiPadのスクリーンショットを設定する欄があります。この部分は設定しなくてもアプリの申請が可能です。

もしiPhone用アプリを開発しているのにiPadのスクリーンショットが未設定と表示されたら、Xcodeの設定を見直してください。手順は以下のとおりです。

1. Xcodeを起動します。
2. 画面左側のファイル一覧から`.xcodeproj`を選択します。
3. General→Deployment Info→iPadのチェックを外します。

# （手順5）Certificateを作成する

1. Xcodeを起動してPreferences→Accountを選択します。
2. もしログインしていなければApple Developerのアカウントでログインします。
3. Manage Certificatesボタンを押します。
4. Create a certificateボタンを押します。
5. Apple Distributionを選択するとCertificateが追加されます。
6. おわり

# （手順6）Provisioning Profileを作成する

1. Safariを起動して[Apple Developer](https://developer.apple.com/account/#/welcome)を開きます。
2. Certificates, IDs & Profilesを選択します。
3. Profilesを選択します。
4. 「＋」ボタンを押して新規作成します。
5. AppStoreを選択してContinueボタンを押します。
6. App IDとして手順2で作成したものを選択します。
7. Select Certificatesと表示されたら手順5で作成したCertificateを選択します。
8. Provisioning Profile Nameに適当な名前を入力します。（例：`Provisioning Profile for My App`）
9. Generateボタンを押します。
10. Downloadボタンを押して`.mobileprovision`ファイルをダウンロードします。
11. おわり

# （手順7）アプリを検証する

1. Xcodeを起動してProducts→Archiveを選択します。
2. しばらく待ちます。
3. アーカイブ一覧画面が表示されたらValidate Appボタンを押します。
4. Include bitcode for iOS contentにチェックを入れます。
5. Upload your app's symbols to receive symbolicated reports from Appleにチェックを入れます。
6. Nextボタンを押します。
7. Manually manage signingを選択してNextボタンを押します。
8. Distribution certificateとして手順5で作成したCertificateを選択します。
9. Select Profile→Importを選択して、手順6で作成した`.mobileprovision`ファイルを選択します。
10. Macのパスワードを入力します。
11. アーカイブの詳細情報が表示されたらValidateボタンを押します。
12 しばらく待ちます。
13. 「App "MyApp" successfully validated.」のように表示されたら成功です。
14. Doneボタンを押してダイアログを閉じます。
15. おわり

## （補足）Bit codeとシンボルについて

Bit codeはiOS 9から導入されたバイナリの中間表現です。この機能はアプリ配信の最適化に使われます。通常はチェックを入れたままで構いません。

シンボルとはソースコードに含まれる変数やメソッドなどの名前のことです。シンボルとソースコードのファイル名や行番号の対応づけを行う場合、Upload your app's symbols ...にチェックを入れます。チェックを入れなければ不具合が発生したときにデバッグが困難になります。チェックを外すのはお勧めしません。

# （手順8）アプリをアップロードする

1. 手順7が成功するとアーカイブ一覧画面に戻ります。
2. Distribute Appボタンを押します。
3. Select a destinationの中からUploadを選択してNextボタンを押します。
4. Include bitcode for iOS contentにチェックを入れます。
5. Upload your app's symbols to receive symbolicated reports from Appleにチェックを入れます。
6. Nextボタンを押します。
7. Manually manage signingを選択してNextボタンを押します。
8. Distribution certificateとして手順5で作成したCertificateを選択します。
9. Select Profile→Importを選択して、手順6で作成した`.mobileprovision`ファイルを選択します。
10. Macのパスワードを入力します。
11. アーカイブの詳細情報が表示されたらUploadボタンを押します。
12 しばらく待ちます。
13. 「App "MyApp" successfully uploaded.」のように表示されたら成功です。
14. Doneボタンを押してXcodeを閉じます。
15. おわり

## （補足）アップロードをやり直したい

どんなに気をつけていても、メール送信した直後に誤字脱字を発見することがあります。アプリ開発も同じです。完成したと思っていたら、アップロード直後に不具合を発見することがあります。そんなときは焦らずアップロードをやり直しましょう。

一度アップロードした内容を削除したり上書きすることはできません。ビルド番号を変更して再アップロードを行ってください。手順は以下のとおりです。

1. アーカイブ一覧画面を閉じます。
2. 画面左側のファイル一覧から`.xcodeproj`を選択します。
3. General→Identity→Buildを編集します。
4. 現在のビルド番号が1であれば2に書き換えます。
5. 手順8にしたがって、もう一度アップロードを行います。

# （手順9）アプリのビルド情報を編集する

1. 手順8が完了したら、しばらく待ちます。
2. 「App Store Connect: Version 1.0 (1) for MyApp has completed processing.」のような件名のメールが届きます。
3. メールが届いたら、Safariを起動して[AppStore Connect](https://appstoreconnect.apple.com)を開きます。
4. My Appを選択します。
5. 手順5で作成した編集途中のアプリを選択します。
6. Buildの右側にある「+」ボタンを押します。
7. アップロード済みアプリの一覧が表示されます。
8. バージョン番号とビルド番号を確認して、アプリを選択したらDoneボタンを押します。
9. Export Compliance Informationに回答します。通常はNOを選択します。
10. Doneボタンを押します。
11. おわり

## （補足）Export Compliance Informationについて

アプリがHTTPSなどの暗号化通信を行っている場合はYES、そうでなければNOを選択します。しかし、通常はNOを選択すれば問題ないはずです。なぜなら他のプラットフォーム、例えばGoogle Play Storeの場合は暗号化通信についての申告は必要ないためです。

文字どおり解釈するのであれば、画像をHTTPSのGETリクエストで取得するだけでも輸出規制の暗号化通信に該当します。Appleが厳しいのか、Googleが緩いのか、実際のところはわかりません。

なお、回答としてNOを選択した結果、不都合が生じても私は一切責任を負いません。回答は各自の責任で判断してください。

参考：[iOSアプリ提出の輸出コンプライアンスで、通信にHTTPSを使っているだけの場合の解釈 - Qiita](https://qiita.com/osamu1203/items/35df25d93e9d12b11222)

# （手順10）アプリを申請する

1. 手順9が完了したら、Submit for Reviewボタンを押します。
2. 「MyApp is now "Waiting for Review", ...」というメールが届きます。
3. しばらく待ちます。審査が開始されるまで数時間から数日かかる場合があります。
4. 「MyApp is now "In Review" ...」というメールが届きます。
5. しばらく待ちます。審査結果のメールが届くまで数時間から数日かかる場合があります。
6. 「MyApp is now "Ready for Sale", ...」のような内容のメールが届きます。
7. AppStoreでアプリが公開されていることを確認してください。
8. おめでとうございます

# おわりに

内容の不備や誤字脱字などがあれば知らせていただけると助かります。
