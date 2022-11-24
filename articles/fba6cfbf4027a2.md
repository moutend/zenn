---
title: （iOS）HealthKitを使って体重記録アプリを作る
emoji: "👌"
type: "tech"
topics: [iOS HealthKit SwiftUI ヘルスケア]
published: true
---
# はじめに

HealthKitの使い方を学習するための足がかりとして、簡単な体重記録アプリを作ることにしました。体重をピッカーで選び、記録ボタンを押すとヘルスケアにデータが記録される、というものです。

# 環境

この記事で説明する内容は以下の環境で開発と動作検証を行いました。

- macOS Monterey 12.6.1
- Xcode 14.0.1 (14A400)
- iOS 15.7

# （Step 1.）プロジェクトの作成

プロジェクトの作成手順は普段と変わりません。Xcodeを起動してFile→New→Projectを選択してください。インターフェースはSwiftUIを選んでください。

# （Step 2.）HealthKit Entitlementの設定

1. プロジェクトのSigning & Capabilitiesタブを選択します。
2. Addボタンを押すと検索フィールドが表示されます。「healthkit」と入力してリターンキーを押してください。
3. Capabilities一覧にHealthKitが追加されました。今回は体重の記録のみ行うため「Clinical Health Records」と「Background Delivery」のチェックは不要です。

# （Step 3.）Information Property Listの設定

1. プロジェクトのInfoタブを選択します。
2. Custom iOS Target Propertiesの表があります。どこでも構わないので、表の上で右クリック→Add rowを選択して行を追加してください。
3. キーに`NSHealthUpdateUsageDescription`と入力してリターンキーを押します。するとキーの名前が「Privacy - Health Update Usage Description」に自動補完されます。
4. キーの値としてヘルスケアのデータの利用目的を入力すれば設定完了です。これでHealthKitが利用できるようになりました。

## いい加減な利用目的を設定するとアプリがクラッシュする

「for testing」のようにいい加減な利用目的を設定するとアプリがクラッシュします。私の環境では以下のようなエラーメッセージが表示されてアプリがクラッシュしました。

```
Terminating app due to uncaught exception 'NSInvalidArgumentException', reason: 'The string "for testing" is an invalid value for NSHealthUpdateUsageDescription'
```

上記のエラーはデバッグ用のビルドでも発生します。エラーを回避するにはリリースを想定した説明文、例えば「体重の変化を記録するためヘルスケアの利用を許可してください」といった内容を設定してください。

この挙動について、Apple Developerのドキュメントには説明が見当たりません。そのため、どのような内容がいい加減な説明文として判定されるのかは不明です。もし同様のエラーが発生した場合は説明文の内容を見直してみてください。

（追記 2022-11-24）iOS 15.7.1にアップデートしたところ上記のエラーは発生しなくなりました。説明文の判定ロジックはSDKではなくランタイムに含まれているようです。

# （Step 4.）アプリの実装

ヘルスケアに体重データを記録する流れは以下のようになります。

1. HKHealthStoreのインスタンスを作成する。
2. HKHealthStoreのrequestAuthorizationメソッドを呼び出してヘルスケアの利用許可を得る。
3. 体重が記録されたサンプルを作成する
4. HKHealthStoreのsaveメソッドにサンプルを渡してヘルスケアに保存する。

HealthKitで扱うデータは時系列データです。そのため用語としてサンプルを使っています。詳しくはこの記事の最後にまとめているApple Developerのドキュメントを参照してください。

といっても難しいことはありません。HealthKitで扱うのは測定値・測定開始日時・測定終了日時を格納するだけのデータ構造です。

測定の日時が開始日時と終了日時に分かれているのは、心拍数のようにある一定の時間測定を続ける必要があるものに対応するためです。体重のように一瞬で測定できるものは開始日時と終了日時に同じ日時を格納します。

## 実装例

以下にアプリの実装例を示します。ContentView.swiftを以下のコードに置き換えてください。

HealthKitを利用したロジックが実装されているのはBodyScaleクラスです。そのほかはSwiftUIのコードですのでおまけ程度に考えてください。

```swift
import HealthKit
import SwiftUI

class BodyScale {
  private var healthStore: HKHealthStore

  init() {
    self.healthStore = HKHealthStore()
  }
  func requestAuthorization(completion: @escaping (Bool, Error?) -> Void) {
    let sampleTypes = Set([
      HKObjectType.quantityType(forIdentifier: .bodyMass)!
    ])

    self.healthStore.requestAuthorization(toShare: sampleTypes, read: nil, completion: completion)
  }
  func authorizationStatus() -> HKAuthorizationStatus {
    return self.healthStore.authorizationStatus(
      for: HKObjectType.quantityType(forIdentifier: .bodyMass)!)
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

    self.healthStore.save(sample, withCompletion: completion)
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

# （Step 5.）動作確認

準備は整いました。それでは`Command + R`でアプリを実行しましょう。アプリの操作を迷うことはないかもしれませんが、手順を示します。

1. 「ヘルスケアの設定に進む」ボタンを押すと許可を求めるダイアログが表示されます。体重の許可をONにしてください。
2. ピッカーで体重を選びます。
3. 「体重を記録」ボタンを押します。
4. ヘルスケアに体重のデータが記録されます。成功するとアラートが表示されます。

実機で動作確認する場合、体重データの削除をお忘れなく。ヘルスケアアプリを開いてブラウズ→身体測定値→体重→すべてのデータから該当のデータを削除してください。

## 実装の注意点

HealthKitに限った話ではありませんが、ヘルスケアのアクセス許可はいつでも取り消すことができます。saveメソッドが失敗した場合に備えて、アクセス許可の状態を毎回確認する必要があります。

また、requestAuthorizationメソッドのコールバックに渡されるsuccessはあくまでダイアログが正しく表示できたかを示すパラメータです。アクセスを拒否してもsuccessはtrueになります。

# 参考資料

- [Getting started with HealthKit - WWDC20 - Videos - Apple Developer](https://developer.apple.com/videos/play/wwdc2020/10664/)
- [About the HealthKit Framework - Apple Developer](https://developer.apple.com/documentation/healthkit/about_the_healthkit_framework)
- [Setting Up HealthKit - Apple Developer](https://developer.apple.com/documentation/healthkit/setting_up_healthkit)
