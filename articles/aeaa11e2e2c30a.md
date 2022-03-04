---
title: "[SwiftUI] accessibilityActionで設定したカスタムアクションをVoiceOverが読み上げる順番について"
emoji: "📌"
type: "tech"
topics: [iOS VoiceOver SwiftUI a11y]
published: true
---
# はじめに

以下の環境で動作検証しました。

- Xcode 13.2.1 (13C100)
- iOS 15.3.1

# カスタムアクションとは

カスタムアクションとはVoiceOverの上下スワイプで項目を選択して、その後にダブルタップで実行する機能です。

例えばホーム画面でアイコンにカーソルが当たっているとき、下スワイプすると「編集モード」と読み上げされます。その状態でダブルタップするとホーム画面上のアイコンが編集モードに切り替わります。

カスタムアクションは複数割り当てることが可能です。例えばiOS版のGmailアプリはカスタムアクションとして「アーカイブ」「スターをつける・外す」「選択と未選択を切り替える」の3つが設定されています。

# カスタムアクションは最後に定義したものから順番に読み上げされる

ここから本題です。カスタムアクションはSwiftUIのAccessibility Modifierとして定義されている`.accessibilityAction`を利用して設定することが可能です。

以下に実装例を示します。

```swift
struct ContentView: View {
  var body: some View {
    Button(action: {
      // Do nothing.
    }) {
      Text("Button")
    }
    .accessibilityAction(named: "One") {
      // Do nothing.
    }
    .accessibilityAction(named: "Two") {
      // Do nothing.
    }
    .accessibilityAction(named: "Three") {
      // Do nothing.
    }
  }
}
```

現状、カスタムアクションは最後に定義したものから順番に読み上げされます。上記の実装例では下スワイプすると「Three」「Two」「One」と読み上げされます。

ただし、Apple Developerのドキュメントにはカスタムアクションの読み上げ順についての説明がありません。従って、現状の挙動が将来に渡って保証されるとは限りません。

# 余談

カスタムアクションは便利です。しかし、大量のカスタムアクションを設定すると目的の機能に到達するまでひたすら上下スワイプすることになります。

音声プレーヤーのシークバーの実装を例に考えてみましょう。「1秒スキップ」「2秒スキップ」「3秒スキップ」「4秒スキップ」「5秒スキップ」「...」のように大量のカスタムアクションを設定するのは良い実装ではありません。

この場合、Accessibility Modifierの`.accessibilityAdjustableAction()`を利用するべきです。VoiceOverカーソルが当たっている状態で上下スワイプすると即座に任意の処理が実行できます。さらに、`.accessibilityValue()`を組み合わせることでスワイプに対する結果を即座に読み上げさせることも可能です。

# 参考資料

1. [Accessibility Modifiers - Apple Developer](https://developer.apple.com/documentation/swiftui/view-accessibility)
