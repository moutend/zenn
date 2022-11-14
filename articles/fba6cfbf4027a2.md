---
title: （iOS）HealthKitを使って体重記録アプリを作ろう
emoji: "👌"
type: "tech"
topics: [iOS HealthKit SwiftUI ヘルスケア]
published: false
---
# はじめに

HealthKitの使い方を学習するための足がかりとして、簡単な体重記録アプリを作ることにしました。この記事ではその実装手順を説明します。

この記事で作成するのは体重をピッカーで選び、記録ボタンを押すとヘルスケアに記録される、というアプリです。本当はBluetooth体重計と連携したいのですが、まずはヘルスケアに体重を記録する処理にフォーカスして実装します。

なお、この記事で説明している内容については以下の環境で動作検証しました。

- Xcode 14.0.1 (14A400)
- iOS 15.7

# （Step 1.）プロジェクトの作成

プロジェクトの作成手順は普段と変わりません。Xcodeを起動してFile→New→Projectを選択してください。

# （Step 2.）HealthKit Entitlementの設定

たかが体重の記録とあなどるなかれ、ここから面倒になります。

1. プロジェクトのSigning & Capabilitiesタブを選択します。
2. Addボタンを押すと検索フィールドが表示されます。「healthkit」と入力してリターンキーを押してください。
3. Capabilities一覧にHealthKitが追加されました。今回は体重の記録のみ行うため「Clinical Health Records」と「Background Delivery」のチェックは不要です。

# （Step 3.）Information Property Listの設定

Xcode 13からInfo.plistが廃止されてプロジェクト画面から設定するようになりました。その点だけ注意してください。

1. プロジェクトのInfoタブを選択します。
2. Custom iOS Target Propertiesの表があります。どこでも構わないので、表の上で右クリック→Add rowを選択して行を追加してください。
3. キーは`NSHealthUpdateUsageDescription`と入力してリターンキーを押します。するとキーの名前が「Privacy - Health Update Usage Description」に自動補完されます。
5. キーの値としてヘルスケアのデータの利用目的を入力すれば設定完了です。これでHealthKitが利用できるようになりました。

## いい加減な利用目的を設定するとアプリがクラッシュする

利用目的として「for testing」のようにいい加減な説明文を設定するとアプリがクラッシュします。私の環境では以下のようなエラーメッセージが表示されました。

```
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'The string "for testing" is an invalid value for NSHealthUpdateUsageDescription'
```

上記のエラーはデバッグ用のビルドでも発生します。エラーを回避するにはリリースを想定した説明文、例えば「体重の変化を記録するためヘルスケアの利用を許可してください」といった説明文を設定してください。

Apple Developerのドキュメントでは言及されていませんし、どのような内容がいい加減な説明文として判定されるのかは不明です。Xcodeのバージョンによって挙動が変化するかもしれませんし、もし同様のエラーが発生した場合は各自で説明文を見直してみてください。

# （Step 4.）アプリの実装

ようやくアプリの実装に着手できます。ヘルスケアに体重を記録する流れは以下のようになります。

1. HKHealthStoreのインスタンスを作成する。
2. HKHealthStoreのrequestAuthorizationメソッドを呼び出してヘルスケアの利用許可を得る。
3. 体重が記録されたサンプルを作成する
4. HKHealthStoreのsaveメソッドにサンプルを渡してヘルスケアに保存する。

ヘルスケアについて軽く説明します。ヘルスケアに保存できるデータは4種類あります。

1. Category Samples ... 血液型や生年月日など基本的に変化しないデータ
2. Quantity Samples ... 体重や心拍数などの時系列データ
3. Correlations ... 1つ以上のサンプルが含まれた混合データ
4. Workouts ... ランニングや水泳などの運動データ

ここでのサンプルは時系列における標本を意味します。

体重や心拍数はともかく血液型などもサンプルとして扱われているのはやや違和感がありますが、例えば血液型であれば骨髄移植で変化する可能性があるためサンプルとして扱われているようです。

話が長くなりました。今回の目的である体重の記録は2番目のQuantity Samplesに該当します。詳細についてはApple Developerの[About the HealthKit Framework](https://developer.apple.com/documentation/healthkit/about_the_healthkit_framework)を参照してください。

## 実装例

以下にアプリの実装例を示します。ContentView.swiftを以下のコードに置き換えてください。

```swift
import HealthKit
import SwiftUI

class BodyScale {
  private var healthStore: HKHealthStore?

  init() {
    // guard HKHealthStore.isHealthDataAvailable() else {
    //  fatalError("This app requires a device that supports HealthKit")
    // }

    self.healthStore = HKHealthStore()
  }
  func requestAuthorization(completion: @escaping (Bool, Error?) -> Void) {
    let sampleTypes = Set([
      HKObjectType.quantityType(forIdentifier: .bodyMass)!
    ])

    self.healthStore?.requestAuthorization(toShare: sampleTypes, read: nil, completion: completion)
  }
  func authorizationStatus() -> HKAuthorizationStatus {
    guard
      let status = self.healthStore?.authorizationStatus(
        for: HKObjectType.quantityType(forIdentifier: .bodyMass)!)
    else {
      return .notDetermined
    }

    return status
  }
  func save(kilogram weight: Double, completion: @escaping (Bool, Error?) -> Void) {
    let now = Date.now
    let bodyMassType = HKObjectType.quantityType(forIdentifier: .bodyMass)!
    let bodyMassQuantity = HKQuantity(unit: .gram(), doubleValue: weight * 1000.0)

    let sample = HKQuantitySample(
      type: bodyMassType,
      quantity: bodyMassQuantity,
      start: now,
      end: now)

    self.healthStore?.save(sample, withCompletion: completion)
  }
}

struct ContentView: View {
  var bodyScale = BodyScale()

  @State var status: HKAuthorizationStatus = .notDetermined
  @State var weight = 50

  @State var isShowingAlert = false
  @State var alertTitle = ""

  var body: some View {
    VStack {
      weightIndicator
      weightPicker
      if self.status == .notDetermined {
        notDetermined
      } else {
        if self.status == .sharingAuthorized {
          sharingAuthorized
        } else {
          sharingDenied
        }
      }
    }
    .onAppear {
      self.status = self.bodyScale.authorizationStatus()
    }
  }
  var weightIndicator: some View {
    Text("体重は\(self.weight) kgです。")
      .padding()
  }
  var weightPicker: some View {
    Picker(selection: $weight, label: Text("\(self.weight) kg")) {
      ForEach(0..<100) {
        Text("\($0) kg").tag($0)
      }
    }
    .accessibilityElement(children: .combine)
    .accessibilityLabel("体重を選択")
    .accessibilityValue("\(self.weight) kg")
  }
  var notDetermined: some View {
    Button(action: self.requestAuthorization) {
      Text("ヘルスケアの設定に進む")
        .padding()
    }
  }
  var sharingDenied: some View {
    VStack {
      Text("再設定してください")
        .padding()
      Button(action: self.updateAuthorizationStatus) {
        Text("再設定")
          .padding()
      }
    }
  }
  var sharingAuthorized: some View {
    Button(action: self.save) {
      Text("体重を記録")
        .padding()
    }
    .alert(self.alertTitle, isPresented: $isShowingAlert) {
      Button("OK") {}
    }
  }
  func updateAuthorizationStatus() {
    self.status = self.bodyScale.authorizationStatus()
  }
  func requestAuthorization() {
    self.bodyScale.requestAuthorization { (success, error) in
      self.updateAuthorizationStatus()
    }
  }
  func save() {
    self.bodyScale.save(kilogram: Double(self.weight)) { (success, error) in
      if success {
        self.alertTitle = "体重を記録しました"
        self.isShowingAlert = true
      }

      self.updateAuthorizationStatus()
    }
  }
}

struct ContentView_Previews: PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
```

## 動作確認

上記のコードを保存して`Command + R`で実行します。

実機で確認が終わったらヘルスケアのアプリを開いて、体重のデータおを削除してください。

## 実装の注意点

# まとめ

体重を記録するだけなのに

# 参考資料

- [Getting started with HealthKit - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10664/)
- [About the HealthKit Framework - Apple Developer](https://developer.apple.com/documentation/healthkit/about_the_healthkit_framework)
- [Setting Up HealthKit - Apple Developer](https://developer.apple.com/documentation/healthkit/setting_up_healthkit)
