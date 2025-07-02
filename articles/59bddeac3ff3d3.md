---
title: "（2024年版）iPhoneアプリをApp Storeで公開する手順まとめ"
emoji: "🍎"
type: "tech"
topics: [iOS, iPhone, Apple, App Store]
published: true
---
## はじめに

iPhoneアプリをApp Storeで公開する手順をまとめました。アプリ開発初心者の頃に知りたかった点を中心に説明しますので、お役に立てたら幸いです。

### 注意事項

1. Swiftプログラミングやアプリの開発方法については説明しません。
2. Apple Developerの登録手順は説明しません。登録が完了した後に本記事をお読みください。
3. iOSネイティブアプリを前提に説明します。FlutterやReact Nativeなどフレームワーク固有の説明は行いません。
4. 無料アプリを前提として説明します。有料アプリの公開手順については説明しません。
5. 個人でのアプリ開発を前提としています。Xcode Cloudを利用したCI / CDパイプラインの構築など、大規模チーム開発の手法については説明しません。
6. 記事の公開後にApple Developer / App Store Connectの外観が変化する可能性があります。そのためスクリーンショットは掲載せず、操作手順を文章で説明しています。

### 環境

記事の投稿にあたり、動作確認は以下の環境で行いました。

- macOS Sonoma 14.5
- Xcode Version 15.4 (15F31d)

### （補足）証明書とプロファイルの管理について

本記事はXcode Managed Profileを利用する想定で説明を進めます。証明書とプロファイルの管理はXcodeがすべて自動で行うため、通常は手作業で設定を行う必要はありません。  
はじめてアプリを開発される場合、最初からXcode Managed Profileが利用可能なはずです。以下の手順で確認してください。  

1. Xcodeでプロジェクトを開き、Signing & Capabilitiesタブを選択します。
2. Automatically manage signingにチェックがついていることを確認します。
3. Provisioning Profileが「Xcode Managed Profile」と表示されていることを確認します。
4. Signing Certificateが「Apple Development: Taro Yamada」のように表示されていることを確認します。名前は仮の値ですので各自の名前に読み替えてください。
5. Profile Infoボタンを押します。警告やエラーが表示されていなければ問題ありません。

証明書やプロファイルの管理でトラブルが発生している場合、申し訳ありませんが解説の範囲外です。各自で対処した後、次の章に進んでください。

## App Storeでアプリを公開するための作業手順

それでは、手順1から手順10まで作業を進めましょう。

## （手順1）App Store掲載用のスクリーンショットを撮影する

1. Xcodeでプロジェクトを開いてProducts→Destinationを選択します。
2. iPhone 15 Pro Maxを選択します。選択肢にない場合、メニューの最後にある「Add Additional Simulators …」からデバイスを追加してください。
3. シミュレーターが起動したらスクリーンショットを撮影します。ショートカットキー`Command + S`で撮影できます。
4. おわり

### （補足）スクリーンショットの解像度と枚数

アプリの申請には画面サイズ6.9インチまたは6.5インチのスクリーンショットが必須です。一方を省略すると、もう一方のスクリーンショットが拡大または縮小されてApp Storeに表示されます。

スクリーンショットの解像度が一致していれば、シミュレーターはどの機種を選んでも構いません。枚数はそれぞれ1枚以上必要です。

機種と画面サイズの例

- 6.5インチ
  - iPhone 14 Plus
  - iPhone 13 Pro Max
  - iPhone 12 Pro Max
  - iPhone 11 Pro Max
- 6.9インチ
  - iPhone 16 Pro Max
  - iPhone 16 Plus
  - iPhone 15 Pro Max
  - iPhone 15 Plus

スクリーンショットの要件について、詳細は公式ドキュメントを参照してください。

[スクリーンショットのサイズ要件 - Apple](https://help.apple.com/app-store-connect/?lang=ja-jp#/devd274dd925)

### （補足）シミュレーターの言語設定

シミュレーターが英語で表示される場合は言語を変更しましょう。

1. シミュレーターが起動したら`Command + Shift + H`を押してホーム画面に移動します。
2. シミュレーター状のSettingsアプリを起動してGeneral→Languageを選択します。
3. Japaneseを選択するとシミュレーターが再起動して日本語に切り替わります。

## （手順2）App IDがApple Developerサイトに同期されていることを確認する

1. Safariを起動して[Apple Developerのサイト](https://developer.apple.com/account/#/welcome)を開きます。
2. Identifiersを選択します。App Storeに提出するアプリのBundle IDが一覧の中に表示されていることを確認します。
3. おわり

### （補足）Apple DeveloperのIdentifiersにBundle IDが表示されない場合

以下の記事が解決の役に立つかもしれません。

[Xcode Managed Profileを利用しているときBundle IDがApple Developerに表示されない場合の対処法 - Zenn]()

### （補足）Bundle IDとApp ID

Bundle IDは`com.example.MyApp`のような書式の文字列であり、アプリの識別に使用されます。App Storeに公開するBundle IDは世界中でただひとつ存在し、重複は許されません。

App IDは「My Cool App」のようなBundle IDに紐づく文字列です。Apple DeveloperとApp Store Connectのサイト上でアプリを識別するために使用されます。

Bundle IDとApp IDはIPアドレスとドメイン名のような関係だと考えてください。App IDはApp Storeに表示されないため、ユーザーが知ることはありません。

Xcodeでプロファイルを自動管理している場合、App IDの初期値として「XC com example MyApp」のような文字列が設定されます。この文字列はApple DeveloperのIdentifiersページでいつでも自由に変更できます。

Bundle IDは原則として、一度アプリがApp Storeに公開されると変更できません。希望の名前がすでに使われているからと言って、`com.example.tmp12345`のように適当な値を設定すると、後々紛らわしいので慎重に設定することをお勧めします。

## （手順3）アプリをApp Store Connectに登録する

1. Safariで[App Store Connect](https://appstoreconnect.apple.com)を開きます。
2. My Appを選択します。
3. Appsの右側にある「+」ボタンを押すと新規作成ダイアログが表示されます。
4. プラットフォームとしてiOSにチェックをつけます。
5. アプリの名前を入力します。すでに使われている名前を指定することはできません。
6. Bundle IDとして手順2で確認したApp IDを選択します。
7. SKUを入力します。詳細は次の補足を読んでください。
8. 言語を設定します。User AccessとしてFull Accessを選択します。
9. Createボタンを押します。成功すると、アプリの一般情報を編集する画面に移動します。

この手順は以上で完了です。Safariを開いたまま、手順4へ進んでください。

### （補足）EUのデジタルサービス法（DSA：Digital Services Act）コンプライアンス」の回答について

2024年の3月以降、App Store Connectサイトを開くとEUのDSAコンプライアンスに解凍するようメッセージが表示されます。おそらく、解凍しなければアプリ作成画面に進めません。

この記事を読まれている方は日本のApp Storeにアプリを公開しようと考えているはずです。その場合は「I'm not a trader under the DSA or I don't plan to distribute in the EU」を選択すれば問題ありません。

なお、コンプライアンスの同意状況はApp Store ConnectのBusinessリンクを開くと確認できます。

参考資料

- [Manage European Union Digital Services Act trader requirements - App Store Connect - Help - Apple Developer](https://developer.apple.com/help/app-store-connect/manage-compliance-information/manage-european-union-digital-services-act-trader-requirements/)
- [EUのデジタルサービス法(DSA)による「デジタルサービス法のコンプライアンス」の選択について #DSA法 - Qiita](https://qiita.com/y-matsumoto/items/75fe8f734b9d0c0b5355)

### （補足）SKUについて

SKU（Stock Keeping Unit：在庫管理単位）はApple社がアプリの売り上げを管理するとき内部的に使用する名前です。SKUがユーザーに表示されることはありません。アプリが無料化有料化に関わらず、SKUの設定は必須です。

SKUに命名規則はありません。自由に名前を決めることができます。ただし、`App001`のように適当な名前をつけると紛らわしいので、おすすめしません。私の場合、Bundle IDが`com.example.MyApp`ならSKUは`ComExampleMyApp`のように設定しています。

## （手順4）アプリの一般情報を編集する

1. 手順1で作成したスクリーンショットを設定してください。iPhoneアプリの場合、iPadのスクリーンショットは設定不要です。
2. Promotional Text（アプリのキャッチコピー）を入力します。
3. Description（アプリの説明）を入力します。
4. Keywords（App Storeの検索キーワード）を入力します。「aaa,bbb,ccc」のようにカンマ区切りで入力します。
5. Support URL（アプリの公式サイトURL）を入力します。私の場合はGitHub Pagesを利用してアプリの説明ページを作成しました。X（旧twitterアカウントのURLなどでも構わないようです。
6. Buildは設定せず空欄のままにします。後で設定します。
7. Sign-In Informationを設定します。ログイン不要で使えるアプリの場合は「Sign-in required」のチェックを外します。
8. Notesにレビュー担当者の方へのコメントを記入します。なお連絡先の電話番号を設定する欄がありますが、私の場合、今まで一度も電話連絡が届いたことがありません。
9. Saveボタンを押して内容を保存したら手順5へ進んでください。

私の場合、Notesにはアプリの概要と簡単な操作説明を記入しています。Notesは日本語で記入して問題ないはずですが、念のため英語に翻訳した内容も合わせて記入しています。そして、レビューありがとうございます、といった挨拶を加えるとレビューの進みがスムーズになる気がします。

### （補足）iPadのスクリーンショットについて

アプリの詳細情報を編集する画面にはiPadやApple Watchのスクリーンショットを設定する欄が表示されているかもしれません。iPhone用アプリの場合、設定しなくてもアプリの申請が可能です。

iPhone用アプリにもかかわらず、iPadのスクリーンショットが求められる場合、XcodeのSupported DestinationsにipadOSが含まれている可能性があります。削除手順は次のとおりです。

1. Xcodeでプロジェクトを開きます。
2. General→Supported Destinationsを開きます。
3. リストの中のiPad iOSを削除します。クリックした後にDeleteキーを押すと削除されます。
4. もしmacOSなど他のOSもサポート対象として表示されていたら、同様に削除してください。

## （手順5）App Informationを設定する

1. 画面左側のメニューからApp Informationを選択します。
2. Subtitleを設定します。アプリのキャッチコピーと同じ内容を設定しても構いません。
3. Content Rightsを設定します。第三者の著作物を利用しないアプリであれば「This app does not contain, show, or access third-party content.」を設定します。
4. Age Rating（アプリの対象年齢）を設定します。Editボタンを押すと暴力や性描写に関連する質問が表示されます。質問の回答が完了すると自動的に対象年齢が設定されます。
5. アプリのカテゴリーを設定します。
6. Saveボタンを押して内容を保存したら手順6へ進んでください。

## （手順6）Pricing and Availabilityを設定する

1. 画面左側のメニューからPricing and Availabilityを選択します。
2. Priceとして「JPY 0 (Free)」を選択します。
3. Saveボタンを押して内容を保存したら手順7へ進んでください。

## （手順7）App Privacyを設定する

1. 画面左側のメニューからApp Privacyを選択します。
2. アプリのプライバシーポリシーを記載したページのURLを設定します。個人情報を収集しないアプリの場合もその旨を記載したページを作成しましょう。
3. Get Startedボタンを押して個人情報の収集に関連する質問に回答します。
4. 「Do you or your third-party partners collect data from this app?」について、個人情報を収集して第三者に渡すことがなければ「No , we do not collect data from this app」を選択します。
5. Publishボタンを押して入力内容を確定させます。ここまでの作業が完了したら一度Safariを閉じてください。
6. おわり

## （手順8）アプリをアップロードする

1. Xcodeでプロジェクトを開いてProducts→Archiveを選択します。アーカイブ完了までしばらく待ちます。
2. アーカイブ一覧画面が表示されたらDistribute Appボタンを押します。
3. Select a method for distributionダイアログが表示されます。App Store Connectを選択してDistributeボタンを押します。
4. App upload completeのように表示されたら成功です。Doneボタンを押してXcodeを閉じてください。
5. おわり

アップロードが完了すると数分後に「MyApp has completed processing」という件名のメール通知が届きます。メールの文面はやや異なるかもしれません。

### （補足）アップロードをやり直す

アップロードが完了した後にアプリの不備に気づくことがあります。そんなときは焦らずアップロードをやり直しましょう。手順は以下のとおりです。

1. アプリの修正をします。
2. 修正が終わったら画面左側のファイル一覧から`.xcodeproj`を選択します。
3. General→Identity→Buildを確認します。
4. ビルド番号を書き換えます。例えば現在のビルド番号が1であれば2に書き換えます。
5. 手順8にしたがって、もう一度アップロードを行います。
6. おわり

### （補足）アイコンの背景色について

アプリをアップロードすると「ITMS-90717:" Invalid App Store Icon. The app store icon in the asset catalog in can't be transparent nor contain an alpha channel."」と表示される場合があります。

App Storeに表示されるPNG画像（1024 x 1024）については透明色が許可されていません。そのため、1ピクセルでも透明色が使われていると上記のエラーが発生します。

なお、アプリのアイコン画像については透明色が使われていてもエラーは発生しません。

## （手順9）アプリのビルド情報を編集する

1. 手順8が完了したら、しばらく待ちます。
2. 「App Store Connect: Version 1.0 (1) for MyApp has completed processing.」という件名のメールが届きます。
3. メールが届いたら、Safariを起動して[App Store Connect](https://appstoreconnect.apple.com)を開きます。
4. My Appを選択します。
5. 編集途中のアプリを選択します。
6. Buildの右側にある「+」ボタンを押します。
7. アップロード済みアプリの一覧が表示されます。
8. バージョン番号とビルド番号を確認して、アプリを選択したらDoneボタンを押します。
9. Export Compliance Informationに回答します。通常はNOを選択します。
10. Doneボタンを押します。
11. おわり

### （補足）Export Compliance Informationについて

アプリが暗号化通信を行っている場合はYES、そうでなければNOを選択します。通常はNOを選択します。

参考：[iOSアプリ提出の輸出コンプライアンスで、通信にHTTPSを使っているだけの場合の解釈 - Qiita](https://qiita.com/osamu1203/items/35df25d93e9d12b11222)

## （手順10）アプリを申請する

1. ここまでの手順がすべて完了したら、Add for Reviewボタンを押します。なお、入力の漏れや不備がある場合はその旨が表示されます。
2. ページが遷移すると同時に「MyApp is now "Ready for Review", ...」という件名のメールが届きます。
3. ページ内のSubmit to App Reviewボタンを押します。直後に「MyApp is now "Waiting for Review", ...」という件名のメールが届きます。
4. しばらく待ちます。審査が開始されるまで数時間から数日かかる場合があります。
5. 「MyApp is now "In Review" ...」という件名のメールが届きます。
6. しばらく待ちます。審査結果のメールが届くまで数時間から数日かかる場合があります。
7. 審査を通過すると「Your submission was accepted...」という件名のメールが届きます。今回はアプリの公開日を指定していないため、通常は直後に「MyApp is now "Ready for Sale", ...」という件名のメールが届きます。

お疲れ様でした！以上でアプリ公開の手順は完了です。App Storeでアプリが公開されていることを確認してください。

### （補足）App Storeの検索に表示されない？

レビューを通過してApp Storeにアプリが登録されても、数時間はApp Storeの検索に表示されません。この待機期間もアプリの共有リンクは取得可能ですので、SNSなどでシェアすることは可能です。

1. Safariで[App Store Connect](https://appstoreconnect.apple.com)を開きます。
2. 画面左側のメニューからApp Informationを選択します。
3. View on App Storeボタンを押します。App Storeの画面に移動したら、Shareボタンを押して共有リンクを取得します。

## おわりに

この記事がアプリ開発のお役に立てたら幸いです。内容の不備や誤字脱字などがあれば知らせていただけると助かります。
