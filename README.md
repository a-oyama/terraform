## Terraformの設定方法・備忘録

- AWSアカウント登録
- AWS CLI インストール
- GitBashコマンドライン インストール

をし事前にインストールしておくこと。

## 自分が行った設定手順

- AWS CLI インストール後、以下のコマンドを実行し、configが設定されているか確認する。

```console
aws configure list
```
```console
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                <not set>             None    None
access_key     ****************CVME shared-credentials-file
access_key     ****************THfG shared-credentials-file
    region           ap-northeast-1      config-file    ~/.aws/config
```

- access_key, access_key, region が not set の場合、設定するために下記コマンドを実行

```console
aws configure
```

各キーがない場合は、以下の方法で取得する。

- AWS Access Key ID
→AWSコンソール右上のユーザー→セキュリティ認証情報→アクセスキーIDをコピー、または作成し取得

- AWS Secret Access Key
→アクセスキーとセットでコピーする(アクセスキーを作成した際にCSVをダウンロードしておくとよい)

- region
→日本人の場合は ap-northest-1 にすることがほとんど

- output format(ファイルの保存形式)
→.jsonなど何でもよいが、自分はスペースで飛ばした。

S3バケットを作成する

- AWSでS3サービスを開く。 "バケットを作成"でバケットユーザーを作成する。

Terraformをインストールする

- https://github.com/tfutils/tfenv
からリポジトリをクローンする。

- リポジトリを保存したフォルダに".bashrc"とファイルを作成する。

- ファイルを編集し
```console
export PATH=$PATH:/＜.tfenv配下のbinフォルダ絶対パス＞
```
を記述する。.tfenv/bin までのディレクトリを記述すること。

- ファイル編集が完了したら、そのフォルダで右クリックし、[Git Bash Here]を押下し再起動する。

- GitBash上で
```console
tfenv
```
とコマンドを実行し、パスの結果が返ってくることを確認する。

- Terraformをインストールする前に、インストール可能なバージョンを確認する。
```console
tfenv list-remote
```

- リストを確認し、最新バージョンをインストール
```console
tfenv install 1.9.8
```

- インストール完了後、以下のコマンドでTerraformのバージョンを指定する。
```console
tfenv use 1.9.8
```

- Terraformが使用可能であるかをバージョンチェックし確認する。
```console
terraform version
```

以上でTerraformのインストールが完了です。


### Terraformを使ってAWSリソースを構築

はじめに、VSCodeの拡張機能にて[HashiCorp Terraform]をインストールし、
Terraformコマンドを使用できるようにする。

任意のディレクトリに[Terraform]フォルダを作成し、[main.tf]ファイルを作成する。

- [main.tf]に下記のテンプレートを張り付ける。
amiについては、EC2のインスタンスを作成し、AMI-IDを取得し記述する。

```console
provider "aws" {
  profile = "terraform"
  region  = "ap-northeast-1"
}

resource "aws_instance" "test_ec2" {
  ami           = "ami-?????????????????"
  instance_type = "t2.micro"
}
```

- VSCodeのターミナルを実行し、
```console
terraform init
```
と初期化コマンドを実行し、[Terraform has been successfully initialized!]
と表示されることを確認する。

ついでに
```console
terraform fmt
```
も実行しコードの体裁を整えます。

- リソースを作成する前に、
```console
terraform plan
```
を実行し、作成物や削除物を事前に確認する。
また構文チェックを行ってくれる
```console
terraform validate
```
もやっておくとよい。

- 確認を終えたら、
```console
terraform apply -auto-approve
```
を実行する。
-auto-approveは実行確認を求められないオプションです。

AWSのGUI側でEC2のインスタンスやS3にファイルが作成されたことが確認できれば,
コード管理化できたことになります。

また[Terraform]フォルダ下にこのように

![スクリーンショット 2024-11-23 215512](https://github.com/user-attachments/assets/317e1ac2-e9f7-4b01-9611-361eccd750f9)

tfstateファイルとbackupが生成されていることも確認できればOK

- 不必要なインスタンスは、[terraform destroy]やGUI上で削除する。




