---
title: （iOS）HealthKitを使って体重記録アプリを作ろう
emoji: "👌"
type: "tech"
topics: [iOS HealthKit SwiftUI ヘルスケア]
published: false
---
# はじめに

可能ならばBluetooth体重計と連携する機能も実装したいところですが、HealthKitの話題から脱線することになるので今回は体重を入力する部分は手作業で行うことにします。

なお、この記事で説明している内容については以下の環境で動作検証しました。

- Xcode 14.0.1 (14A400)
- iOS 15.7

# （Step 1.）プロジェクトを作成する

# （Step 2.）HealthKit Entitlementを設定する

# （Step 3.）Information Property ListにNSHealthUpdateUsageDescriptionを設定する

###
「for testing」のように適当な説明文を設定すると
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'The string "for testing" is an invalid value for NSHealthUpdateUsageDescription'

# 参考資料

- [Getting started with HealthKit - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10664/)
- [About the HealthKit Framework - Apple Developer](https://developer.apple.com/documentation/healthkit/about_the_healthkit_framework)
- [Setting Up HealthKit - Apple Developer](https://developer.apple.com/documentation/healthkit/setting_up_healthkit)
