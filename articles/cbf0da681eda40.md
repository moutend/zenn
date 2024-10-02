---
title: "App Storeのレビューを通過したのにRemoved from App Storeと表示される場合の対処法"
emoji: "📘"
type: "tech"
topics: [Apple, iOS, "App Store"]
published: true
---
## 状況

以下は私の個人アカウントでApp Store Connectのアプリ一覧画面を撮影したスクリーンショットです。「Sonic Motion」アプリのステータスに注目してください。「Removed from App Store」と表示されています。もちろん削除した覚えはありません。

![画像1 App Store Connectのスクリーンショット。Removed from App Storeと表示されている。](https://storage.googleapis.com/zenn-user-upload/b16b8c7a3c84-20241002.png)

画像1 App Store Connectのスクリーンショット。Removed from App Storeと表示されている。

このアプリはレビューを通過しています。アプリ詳細ページに移動すると「Ready for Distribution」と表示されます。ちぐはぐな状況です。

![画像2 アプリ詳細ページのスクリーンショット。こちらはステータスとして「Ready for Distribution」が表示されている。](https://storage.googleapis.com/zenn-user-upload/2db58eed541c-20241002.png)

画像2 アプリ詳細ページのスクリーンショット。こちらはステータスとして「Ready for Distribution」が表示されている。

## 原因

App Availabilityの設定が漏れていました。今まで気がついていなかったのですが、この項目が未設定のままでもレビューの申請が可能なようです。

未設定でレビューを通過すると「Removed from App Store」と表示される状態になるようです。紛らわしいので別の文言で表示してほしいところです。

## 対処法

あえて説明するほどでもありませんが念のため。App Availabilityの設定手順は以下のとおりです。

1. Pricing and Availability画面に移動する。
2. App Availability欄にあるSet Up Availabilityボタンを押す。
3. 「Select where your app will be available to purchase or download.」というダイアログが表示される。
4. 今回は地域を限定せず公開したいので「All Countries or Regions」を選ぶ。
5. 「Make app available in all 175 countries or regions after releasing it?」という確認ダイアログが表示される。
6. Confirmボタンを押す。
7. 終わり
