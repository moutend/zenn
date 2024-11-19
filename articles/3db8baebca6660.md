---
title: "（AWS）S3とCloudFrontを利用して独自ドメインが設定された性的なWebサイトを構築する"
emoji: "😺"
type: "tech"
topics: [AWS, S3, CloudFront, Route53, ACM]
published: false
---
## はじめに

この記事ではAWSのインフラを利用して独自ドメインが設定された静的なWebサイトを構築する手順を説明します。作業の流れを把握するためCloudFormationやTerraformによる自動化は行わず、AWSマネージメントコンソールから手動で作業を行います。

### この記事で説明しないこと

以下の内容については記事中で説明しません。

- AWSアカウントの作成方法や作業用ユーザーのIAM設定方法
- HTMLやCSSの書き方

### この記事の目標

`<h1>It works!</h1>`と書かれたHTMLファイルをデプロイします。その後、ブラウザとcurlコマンドを利用してWebサイトにアクセスし、デプロイされたHTMLファイルが返されることを確認します。期待どおりのレスポンスが返されたら目標達成とします。

### 利用するサービス一覧

構築にあたり、以下のサービスを利用します。

- Route 53: ドメインの購入とDNSレコードの設定に利用します。
- ACM: TLS証明書の発行に利用します。
- S3: Webサイトのアセット（.htmlファイルや.cssファイル）を格納するストレージとして利用します。
- CloudFront: S3をオリジンとして設定可能なCDNとして利用します。

いわゆるサーバーレスの構成です。CloudFrontが実質的なWebサーバーの役割を担当します。

### 費用について

独自ドメインの購入費用を除き、Webサイトを構築直後に削除すれば費用は数十円ほどに抑えられます。ただし、構築したWebサイトをそのまま放置するとアクセス数に応じて意図せず課金される恐れがあります。学習後は各自の責任で削除してください。

## 作業手順

それでは、以下の手順で作業を進めましょう。

1. Route 53でドメインを購入する
2. ACMで証明書を発行する
3. S3バケットを作成する
4. CloudFront Distributionを作成する
5. Route 53でレコードを追加する

### （手順1）Route 53でドメインを購入する

以下の公式ドキュメントに従ってドメインを購入してください。ここでは仮に`example.com`ドメインを購入したものとして以降の説明を続けます。

- [Registering a new domain - Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html)

なお、ドメイン購入時は以下の点に注意してください。

1. Privacy protectionをONに設定してください。OFFの場合、ドメイン購入時の住所氏名がドメイン管理者の連絡先としてインターネット上に公開されます。
2. 今回のドメイン購入は学習目的ですので、ドメインのXXX（有効期間）を1年、Renewal（自動更新）をOFFに設定してください。
3. ドメインごとに費用が異なります。例えば`.com`ドメインの場合、記事を投稿した時点で価格は1年につき$15（約2,400円）でした。
4. 一度購入したドメインをキャンセルして返金を受けることはできません。
5. Checkoutボタンを押すと購入が完了します。その場で支払いされるのではなく、当月分のAWS利用料として請求されます。
6. ドメインの購入が完了すると数分後に確認メールが届きます。そのメール本文に記載されたリンクを開いてください。確認が完了するまではドメインが利用できません。

## （手順2）ACMで証明書を発行する

以下の手順に従って作業を進めてください。

1. ACMのコンソール（[https://console.aws.amazon.com/acm/home](https://console.aws.amazon.com/acm/home)）を開きます。
2. 画面上部のメニューでリージョンを`us-east-1 (N. Virginia)`に変更します。
3. 「Request certificate」を選択します。その後、「Request a public certificate」を選び「Next」ボタンを押します。
4. 「Fully qualified domain name」に`example.com`を入力します。
5. 「Add another name to this certificate」を押します。
6. 追加された「Fully qualified domain name」に`*.example.com`を入力します。
7. 「Validation method」として「DNS validation」を選択します。
8. 「Key algorithm」として「RSA 2048」を選択します。
9. 「Request」ボタンを押します。

証明書発行の作業は以上で完了です。Route 53で購入したドメインに対して証明書を発行する場合、自動的にDNSレコードが作成されて証明書のバリデーションが完了します。

## （手順3）S3バケットを作成する

以下の手順に従って作業を進めてください。

1. S3のコンソール（[https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/])）を開きます。
2. 画面上部のメニューでリージョンを`ap-northeast-1 (Tokyo)`に変更します。
3. 「Create bucket」ボタンを押します。
4. バケットの名前はお好きに決めて構いません。ここでは`static-website-bucket`を入力したと仮定します。
5. バケットの名前の他は入力せず、そのままにしてください。
6. 「Create bucket」ボタンを押します。

以上でバケット作成の作業は完了です。

## （手順4）S3バケットにHTMLファイルを配置する

以下の手順に従って作業を進めてください。

まずは`index.html`ファイルを作成します。内容は次のとおりです。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>It works!</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />
  </head>
  <body>
    <h1>It works!</h1>
  </body>
</html>
```

ファイルが作成できたら以下の手順を進めてください。

1. S3のコンソール（[https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/])）を開きます。
2. Bucketを選択します。
3. 手順3で作成したバケットを選びます。
4. Uploadボタンを押します。
5. Add filesボタンを押します。ファイル選択のダイアログが表示されるので、先ほど作成したindex.htmlファイルを選びます。
6. Uploadボタンを押します。

以上でS3にHTMLファイルを配置する作業は完了です。

## （手順5）CloudFront Distributionを作成する

以下の手順に従って作業を進めてください。

1. CloudFrontのコンソール（[https://console.aws.amazon.com/cloudfront/v4/home](https://console.aws.amazon.com/cloudfront/v4/home)）を開きます。
2. 「Create distribution」を選択します。
3. Origin domainのコンボボックスをクリックすると、候補一覧が表示されます。
    - 手順3で作成したS3バケットを指定してください。
    - 候補は上下カーソルキーでも選択可能です。
    - 選択すると、自動的に`static-website-bucket.s3.ap-northeast-1.amazonaws.com`のようなテキストが入力されます。
4. Origin accessとしてOrigin access control settings (recommended)」を選択します。
5. Create new OACボタンを押します。
6. Create new OACダイアログが開きます。すでにデフォルト値が入力されているはずですので、そのままCreateボタンを押します。
7. Web Application Firewall (WAF)については「Do not enable security protections」を選択します。
    - 今回は静的なWebサイトを構築しています。Webサイト内に動的な要素がなく、攻撃される恐れがないためWAFは設定していません。
8. 他の項目については入力不要です。デフォルト値のままにしてください。
9. Create distributionボタンを押します。

以上でCloudFront Distributionの作成作業は完了です。反映されるまで5、10分ほど時間を要します。

## （手順6）S3にバケットポリシーを設定する

以下の手順に従って作業を進めてください。

1. CloudFrontのコンソール（[https://console.aws.amazon.com/cloudfront/v4/home](https://console.aws.amazon.com/cloudfront/v4/home)）を開きます。
2. Distributionsを選択します。
3. 手順5で作成したDistributionを選び、ARNをメモします。
    - ARNの形式は`arn:aws:cloudfront::アカウントID:distribution/DistributionのID`です。
4. S3のコンソール（[https://console.aws.amazon.com/s3/](https://console.aws.amazon.com/s3/])）を開きます。
5. 手順3で作成したバケットを選びます。
6. Propertiesタブを選び、ARNをメモします。
    - ARNの形式は`arn:aws:s3:::バケットの名前`です。
7. Permissionsタブを選びます。
8. Bucket policyのEditボタンを押します。その後、以下の内容を入力します。

注意

1. `"arn:aws:s3:::static-website-bucket/*"`を先ほどメモしたS3のARNに置き換えてください。末尾の`/*`は残してください。
2. `"arn:aws:cloudfront::123456789012:distribution/xxxxxxxxxxxxxxxx"`を先ほどメモしたCloudFront DistributionのARNに置き換えてください。

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudfront.amazonaws.com"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::static-website-bucket/*",
      "Condition": {
        "StringEquals": {
          "AWS:SourceArn": "arn:aws:cloudfront::123456789012:distribution/xxxxxxxxxxxxxxxx"
        }
      }
    }
  ]
}
```

入力が完了したらSave changesボタンを押してください。以上でS3バケットポリシーの設定作業は完了です。
