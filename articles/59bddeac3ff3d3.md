---
title: "（2024年版）iOSアプリをAppStoreで公開する手順まとめ"
emoji: "✨"
type: "tech"
topics: [iOS, iPhone, アプリ開発, Apple, AppStore]
published: true
---
## はじめに

iOSアプリをAppStoreで公開する手順をまとめました。以下の点に注意して読み進めてください。

1. Apple Developerの登録手順については説明しません。登録が終わった後にこの記事を読み進めてください。
2. 無料アプリについて説明します。有料アプリの公開手順については説明しません。
3. 記事の公開後にApple DeveloperやAppStore Connectサイトの外観が変わるかもしれません。そのため、あえてスクリーンショットは掲載していません。

動作確認は以下の環境で行いました。

- macOS Sonoma 14.5
- Xcode Version 15.4 (15F31d)

それでは、手順1から手順10まで順番に作業を進めましょう。

### （補足）証明書やプロファイルの管理について

この記事はXcode Managed Profileを利用する想定で説明を進めます。証明書やプロファイルの管理はXcodeがすべて自動で行うため、手作業で設定を行う必要はありません。

アプリ作成未経験で、今回はじめてアプリを作成する場合、最初からXcode Managed Profileが利用できる状態になっているはずです。確認方法は次のとおりです。

1. Xcodeでプロジェクトを開き、Signing & Capabilitiesタブを選択します。Signingパネルが閉じている場合はクリックすると開きます。
2. Automatically manage signingにチェックがついていることを確認します。
3. Provisioning Profileが「Xcode Managed Profile」と表示されていることを確認します。
4. Signing Certificateが「Apple Development: Taro Yamada」のように表示されていることを確認します。名前は仮の値ですので各自の名前に読み替えてください。
5. Profile Infoボタンを押します。警告やエラーが表示されていなければ問題ありません。

証明書やプロファイルの管理でトラブルが発生している場合、申し訳ありませんが解説の範囲外ですので各自で対処してください。ChatGPTで訪ねたり検索すれば何らかの解決策が見つかるはずです。

## （手順1）アプリのスクリーンショットを撮影する

1. Xcodeでプロジェクトを開いてProducts→Destinationを選択します。
2. iPhone 8 Plusを選択します。選択肢にない場合、メニューの最後にある「Add Additional Simulators …」からデバイスを追加してください。
3. シミュレーターが起動したらスクリーンショットを撮影します。ショートカットキー`Command + S`で撮影できます。
4. 同じ手順でiPhone 14 PlusとiPhone 14 Pro Maxのスクリーンショットを撮影します。
5. おわり

### （補足）スクリーンショットの解像度と枚数について

アプリの申請には画面サイズ5.5インチ・6.5インチ・6.7インチのスクリーンショットが必要になります。5.5インチは必須、6.5インチと6.7インチはどちらか片方が必須です。

機種と画面サイズの例

- 5.5インチ: iPhone 8 Plus
- 6.5インチ: iPhone 14 Plus
- 6.7インチ: iPhone 14 Pro Max

スクリーンショットの解像度が一致していれば、シミュレーターはどの機種を選んでも構いません。枚数はそれぞれ1枚以上必要です。

6.5インチと6.7インチのスクリーンショットは、片方を省略した場合、もう片方のスクリーンショットが拡大または縮小されてAppStoreに表示されます。詳細については以下の公式ドキュメントを参照してください。

- [スクリーンショットのサイズ要件 - Apple](https://help.apple.com/app-store-connect/?lang=ja-jp#/devd274dd925)

### （補足）シミュレーターの言語設定について

日本語のアプリを開発しているのにシミュレーターが英語で表示されるときは言語を変更してください。手順は以下のとおりです。

1. シミュレーターが起動したら`Command + Shift + H`を押してホーム画面に移動します。
2. Settingsアプリを起動してGeneral→Languageを選択します。
3. Japaneseを選択するとシミュレーターが再起動して日本語に切り替わります。

## （手順2）App IDがApple Developerサイトに同期されていることを確認する

1. Safariを起動して[Apple Developerのサイト](https://developer.apple.com/account/#/welcome)を開きます。
2. Identifiersを選択します。AppStoreに提出するアプリのBundle IDが一覧の中に表示されていることを確認します。
3. おわり

### （補足）Automatically manage signingを使っているのにApple DeveloperのIdentifiersにBundle IDが表示されない場合

以下の記事が解決の役に立つかもしれません。

- [Xcode Managed Profileを利用しているときBundle IDがApple Developerに表示されない場合の対処法 - Zenn]()

### （補足）Bundle IDとApp IDについて

Xcodeでプロジェクトを作成するときBundle IDが設定されます。Bundle IDは`com.example.MyApp`のような書式の文字列であり、アプリの識別に使用されます。AppStoreに公開するBundle IDは世界中でただひとつ存在し、重複は許されません。

App IDは「My first very cool app」のようなBundle IDに紐づく文字列です。Apple DeveloperとAppStore Connectのサイト上でアプリを識別するのに使用されます。

Xcodeでプロファイルを自動管理している場合、App IDの初期値として「XC com example MyApp」のような文字列が設定されます。この文字列はApple DeveloperのIdentifiersページで自由に変更できます。

Bundle IDとApp IDはIPアドレスとドメイン名のような関係だと考えてください。なおApp IDはAppStoreに表示されないため、ユーザーが知ることはありません。

## （手順3）アプリをAppStore Connectに登録する

1. Safariで[AppStore Connect](https://appstoreconnect.apple.com)を開きます。
2. My Appを選択します。
3. Appsの右側にある「+」ボタンを押すと新規作成ダイアログが表示されます。
4. プラットフォームとしてiOSにチェックをつけます。
5. アプリの名前を入力します。すでにAppStoreで使われている名前を指定することはできません。
6. Bundle IDとして手順2で確認したApp IDを選択します。
7. SKUを入力します。詳細は次の補足を読んでください。
8. 言語を設定します。User AccessとしてFull Accessを選択します。
9. Createボタンを押します。成功すると、アプリの一般情報を編集する画面に移動します。

この手順は以上で終わりです。Safariを開いたまま、手順4へ進んでください。

### （補足）EUのデジタルサービス法（DSA：Digital Services Act）コンプライアンス」の回答について

はじめてAppStore Connectサイトを開くと、EUのDSAコンプライアンスに解凍するようメッセージが表示されます。おそらく、解凍しなければアプリ作成画面に進めないはずです。

この記事を読まれている方は日本のAppStoreにアプリを公開しようと考えているはずです。通常は「I'm not a trader under the DSA or I don't plan to distribute in the EU」を選択すれば問題ありません。

なお、コンプライアンスの同意状況はAppStore ConnectのBusinessリンクを開くと確認できます。

参考資料

- [Manage European Union Digital Services Act trader requirements - App Store Connect - Help - Apple Developer](https://developer.apple.com/help/app-store-connect/manage-compliance-information/manage-european-union-digital-services-act-trader-requirements/)
- [EUのデジタルサービス法(DSA)による「デジタルサービス法のコンプライアンス」の選択について #DSA法 - Qiita](https://qiita.com/y-matsumoto/items/75fe8f734b9d0c0b5355)

### （補足）SKUについて

SKU（Stock Keeping Unit：在庫管理単位）はApple社がアプリの売り上げを管理するとき内部的に使用する名前です。SKUがユーザーに表示されることはありません。アプリが無料化有料化に関わらず、SKUの設定は必須です。

SKUに命名規則はありません。自由に名前を決めることができます。ただし、`App001`のように適当な名前をつけると紛らわしいので、おすすめしません。私の場合、Bundle IDが`com.example.MyApp`ならSKUは`ComExampleMyApp`のように設定しています。

## （手順4）アプリの一般情報を編集する

1. 手順1で作成したスクリーンショットを設定してください。iPadのスクリーンショットは設定不要です。
2. Promotional Text（アプリのキャッチコピー）を入力します。
3. Description（アプリの説明）を入力します。
4. Keywords（AppStoreの検索キーワード）を入力します。「aaa,bbb,ccc」のようにカンマ区切りで入力します。
5. Support URL（アプリの公式サイトURL）を入力します。私の場合はGitHub Pagesを利用してアプリの説明ページを作成しました。X（旧twitterアカウントのURLなどでも構わないようです。
6. Buildは設定せず空欄のままにします。
7. Sign-In Informationを設定します。ログイン不要で使えるアプリの場合は「Sign-in required」のチェックを外します。
8. Saveボタンを押して内容を保存したら手順5へ進んでください。

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

AppStoreに表示されるPNG画像（1024 x 1024）については透明色が許可されていません。そのため、1ピクセルでも透明色が使われていると上記のエラーが発生します。

なお、アプリのアイコン画像については透明色が使われていてもエラーは発生しません。

### （補足）iPadのスクリーンショットについて

アプリの詳細情報を編集する画面にはiPadのスクリーンショットを設定する欄があります。iPhone用アプリの場合、設定しなくてもアプリの申請が可能です。

もしアプリの申請ができない場合はXcodeの設定を見直してください。手順は以下のとおりです。

1. Xcodeでプロジェクトを開きます。
2. 画面左側のファイル一覧から`.xcodeproj`を選択します。
3. General→Deployment Info→iPadのチェックを外します。
4. 手順8に従ってアプリをアップロードしてください。

## （手順9）アプリのビルド情報を編集する

1. 手順8が完了したら、しばらく待ちます。
2. 「App Store Connect: Version 1.0 (1) for MyApp has completed processing.」という件名のメールが届きます。
3. メールが届いたら、Safariを起動して[AppStore Connect](https://appstoreconnect.apple.com)を開きます。
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

1. ここまでの手順がすべて完了したら、Add for Reviewボタンを押します。
2. 数分後に「MyApp is now "Ready for Review", ...」という件名のメールが届きます。
3. SafariをリロードするとAdd for Reviewボタンがリンクに切り替わります。クリックするとアプリの内容確認ダイアログが表示されるので内容に間違いがなければSubmitボタンを押します。
4. 「MyApp is now "Waiting for Review", ...」という件名のメールが届きます。
5. しばらく待ちます。審査が開始されるまで数時間から数日かかる場合があります。
6. 「MyApp is now "In Review" ...」という件名のメールが届きます。
7. しばらく待ちます。審査結果のメールが届くまで数時間から数日かかる場合があります。
8. 審査を通過すると「Your submission was accepted...」という件名のメールが届きます。その直後に「MyApp is now "Ready for Sale", ...」という件名のメールが届きます。

お疲れ様でした！以上でアプリ公開の手順は完了です。AppStoreでアプリが公開されていることを確認してください。

## おわりに

この記事がアプリ開発のお役に立てたら幸いです。もし内容の不備や誤字脱字などがあれば知らせていただけると助かります。
