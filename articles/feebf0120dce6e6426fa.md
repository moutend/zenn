---
title: "（2021年版）iOSアプリをAppStoreで公開する手順まとめ"
emoji: "✨"
type: "tech"
topics: ["iOS", "iPhone", "AppStore", "アプリ開発"]
published: true
---
# はじめに

以下の点に注意して記事を読み進めてください。

1. この記事ではApple Developerの登録手順について説明しません。
1. 無料アプリを前提に説明します。有料アプリの公開手順は説明しません。
1. Apple DeveloperやAppStore Connectサイトの見た目は頻繁に変わります。そのため、あえてスクリーンショットは掲載していません。

動作検証については以下の組み合わせで行いました。

1. macOS 10.15.7 / Xcode 12.4
1. macOS 11.3 / Xcode 12.5

それでは、手順1から手順10まで順番に作業を進めましょう。

# （手順1）アプリのスクリーンショットを撮影する

1. Xcodeを起動してProducts→Destinationを選択します。
2. iPhone 8 Plusを選択します。
3. シミュレーターが起動したらスクリーンショットを撮影します。ショートカットキー`Command + S`で撮影できます。
4. 同じ手順でiPhone 12 Proのスクリーンショット を撮影します。
5. おわり

## （補足）スクリーンショットの画面サイズについて

アプリの申請には6.5インチと5.5インチ、2種類のスクリーンショットが必要になります。枚数はそれぞれ1枚以上必要です。

画面のサイズが6.5インチまたは5.5インチであれば、シミュレーターはどの機種を選んでも構いません。iPhoneの機種ごとの画面サイズについて、詳細は以下を確認してください。

参考：[スクリーンショットのサイズ要件 - Apple](https://help.apple.com/app-store-connect/?lang=ja-jp#/devd274dd925)

## （補足）シミュレーターの言語設定について

シミュレーターが英語で表示されるときは言語の変更をしてください。手順は以下のとおりです。

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

## （補足）SKU・App ID・Bundle IDについて

SKUはApple社がアプリの売り上げを管理するときに使用する名前です。SKUがユーザーに表示されることはありません。

SKUに命名規則はありません。ただし、`App001`のように適当な名前をつけると紛らわしいので、おすすめしません。私の場合、Bundle IDが`com.example.MyApp`ならSKUは`ComExampleMyApp`のように設定しています。

App IDとBundle IDは同じものです。Apple DeveloperではApp IDと表示されていたり、AppStore ConnectではBundle IDと表示されていたり、表記が統一されていないようです。

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
7. Select Certificatesと表示されたら手順5で作成したCertificateを選択します。通常は有効期限として1年後の日付が表示されます。
8. Provisioning Profile Nameに適当な名前を入力します。（例：`Provisioning Profile for My App`）
9. Generateボタンを押します。
10. Downloadボタンを押して`.mobileprovision`ファイルをダウンロードします。
11. おわり

# （手順7）アプリをアップロードする

1. Xcodeを起動してProducts→Archiveを選択します。アーカイブ完了までしばらく待ちます。
2. アーカイブ一覧画面が表示されたらDistribute Appボタンを押します。
3. Select a destinationの中からUploadを選択してNextボタンを押します。
4. 「Include bitcode for iOS content」と「Upload your app's symbols to receive symbolicated reports from Apple」にチェックを入れてNextボタンを押します。
5. Manually manage signingを選択してNextボタンを押します。
6. Distribution certificateとして手順5で作成したCertificateを選択します。
7. Select Profile→Importを選択して、手順6で作成した`.mobileprovision`ファイルを選択します。
8. Macのパスワードを入力します。
9. アーカイブの詳細情報が表示されたらUploadボタンを押します。
10 しばらく待ちます。
11. 「App "MyApp" successfully uploaded.」のように表示されたら成功です。
12. Doneボタンを押してXcodeを閉じます。
13. おわり

## （補足）Bit codeとシンボルについて

Bit codeはiOS 9から導入されたバイナリの中間表現です。この機能はアプリ配信の最適化に使われます。通常はチェックを入れたままでOKです。

シンボルとはソースコードに含まれる変数やメソッドなどの名前のことです。シンボルとソースコードのファイル名や行番号の対応づけを行う場合、Upload your app's symbols ...にチェックを入れます。チェックを入れなければ不具合が発生したときのデバッグが困難になります。チェックを外すのはお勧めしません。

## （補足）アップロードをやり直す

アップロードが完了した後にアプリの不備に気づくことがあります。そんなときは焦らずアップロードをやり直しましょう。手順は以下のとおりです。

1. アーカイブ一覧画面が表示されている場合は閉じてください。
2. アプリの修正作業をします。
3. 修正が終わったら画面左側のファイル一覧から`.xcodeproj`を選択します。
4. General→Identity→Buildを確認します。
5. 現在のビルド番号が1であれば2に書き換えます。
6. 手順7にしたがって、もう一度アップロードを行います。
7. おわり

## アイコンの背景色について

アプリをアップロードすると「ITMS-90717:" Invalid App Store Icon. The app store icon in the asset catalog in can't be transparent nor contain an alpha channel."」と表示される場合があります。

AppStoreに表示されるPNG画像（1024 x 1024）については透明な色が許可されていません。そのため、1ピクセルでも透明な色が使われていると上記のエラーが発生します。

なお、アプリのアイコン画像については透明な色が使われていてもエラーは発生しません。

# （手順8）アプリのビルド情報を編集する

1. 手順7が完了したら、しばらく待ちます。
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

アプリが暗号化通信を行っている場合はYES、そうでなければNOを選択します。通常はNOを選択します。

[参考：iOSアプリ提出の輸出コンプライアンスで、通信にHTTPSを使っているだけの場合の解釈 - Qiita](https://qiita.com/osamu1203/items/35df25d93e9d12b11222)

# （手順9）アプリを申請する

1. 手順8が完了したら、Submit for Reviewボタンを押します。
2. 「MyApp is now "Waiting for Review", ...」というメールが届きます。
3. しばらく待ちます。審査が開始されるまで数時間から数日かかる場合があります。
4. 「MyApp is now "In Review" ...」というメールが届きます。
5. しばらく待ちます。審査結果のメールが届くまで数時間から数日かかる場合があります。
6. 「MyApp is now "Ready for Sale", ...」のような内容のメールが届きます。
7. AppStoreでアプリが公開されていることを確認してください。
8. おめでとうございます

# おわりに

内容の不備や誤字脱字などがあれば知らせていただけると助かります。
