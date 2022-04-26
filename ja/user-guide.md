## Container > NHN Container Registry(NCR) > 使用ガイド

## 事前準備
### Dockerインストール
NHN Container Registry（以下、NCR) サービスは、Dockerコンテナイメージを保存して配布するためのサービスです。コンテナイメージを扱うにはまずユーザーの環境にDockerをインストールする必要があります。

#### Windows
Docker Hubから[Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)をダウンロードしてインストールします。

#### macOS
Docker Hubから[Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)をダウンロードしてインストールします。

#### Linux
Linuxディストリビューションによってインストールプロセスが異なります。CentOS 7、Ubuntu以外のディストリビューションを使用している場合は[Install Docker Engine](https://docs.docker.com/engine/install)を確認してください。

* CentOS 7
```
// Dockerのインストールに必要なパッケージのインストール
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2

// Dockerリポジトリの追加
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

// Dockerのインストール
$ sudo yum install -y docker-ce docker-ce-cli containerd.io

// Dockerサービスの起動
$ sudo systemctl start docker
```

* Ubuntu
```
// aptインデックスの更新
$ sudo apt-get update

// repository over HTTPSを使用するためのパッケージのインストール
$ sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common

// GPG Keyを追加して確認
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

// リポジトリを追加してaptインデックスを更新
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt-get update

// Dockerのインストール
$ sudo apt-get install docker-ce docker-ce-cli containerd.io

// Dockerサービスの起動
$ sudo systemctl start docker
```



### User Access Key/Secret Keyの確認

Dockerコマンドラインツールを利用してユーザーレジストリにログインするには、User Access KeyとSecret Keyが必要です。 User Access KeyとSecret KeyはNHN Cloud Consoleでアカウント > **APIセキュリティ設定**で作成できます。

* User Access Key IDはNHN Cloud ID1つ当たり5個、 IAM ID1当たり5個まで作成できます。

* User Secret Keyは二度と確認できないため、、紛失した場合は再作成する必要があります。セキュリティのために発行されたキーは安全な場所に保管してください。

* User Access Keyは90日ごとに変更することを推奨します。



## コンテナレジストリの使用

> [参考]
> メンバー権限のユーザーはコンテナイメージの保存、削除機能は使用できません。

### ユーザーレジストリの作成

レジストリサービスを初めて使用するには、まずNHN Cloud Consoleでレジストリを作成する必要があります。**Container > NHN Container Registry(NCR) > Management**サービスページに移動して**レジストリ作成**ボタンをクリックします。作成するレジストリの名前を入力したら、確認ボタンをクリックしてレジストリを作成できます。

### ユーザーレジストリアドレスの確認
作成したレジストリのアドレスは**Container > NHN Container Registry(NCR) > Management**サービスページのレジストリリストで確認できます。

### ユーザーレジストリログイン
コンテナイメージを保存したり、任意の環境にインポートするには、Dockerコマンドラインツールを利用する必要があります。Dockerコマンドラインツールを利用してユーザーレジストリにアクセスするにはログインする必要があります。 `docker login`コマンドを使用した後、`Username`にはNHN CloudユーザーアカウントのUser Access Keyを、`Password`にはSecert Keyをそれぞれ入力します。

```bash
$ docker login {ユーザーレジストリアドレス}
Username: {NHN CloudユーザーアカウントUser Access Key}
Password: {NHN CloudユーザーアカウントUser Secret Key}
Login Succeeded
```

> [参考]
> `docker login`コマンドで取得したトークンの最大有効時間は12時間です。



### コンテナイメージの保存(Push)

コンテナイメージをユーザーレジストリに保存するには、アップロードするイメージの名前をユーザーレジストリアドレスを含むイメージ(Image)名とタグ(Tag)形式に設定する必要があります。Dockerコマンドラインツールの**tag**コマンドを利用して指定できます。

```bash
docker tag {イメージ名}:{タグ} {ユーザーレジストリアドレス}/{イメージ名}:{タグ}
```

* 例

```bash
docker tag ubuntu:18.04 example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
```

> [参考]
> コンテナイメージ名には、英語小文字、数字、一部特殊文字(-, _, /, .)の組み合わせのみ許可されます。リポジトリ名はレジストリアドレスを含めて最大255文字、タグ名は最大129文字に制限されます。イメージ名が長いと使いにくい場合があります。適切な長さの名前を使用してください。

Dockerコマンドラインツールの**push**コマンドを使用してコンテナイメージをユーザーレジストリに保存できます。

```bash
docker push {ユーザーレジストリアドレス}/{イメージ名}:{タグ名}
```

* 例
```bash
$ docker push example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.cloud.toast.com/ubuntu]
16542a8fc3be: Pushed
6597da2e2e52: Pushed
977183d4e999: Pushed
c8be1b8f4d60: Pushed
18.04: digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320 size: 1152
```

### コンテナイメージ照会
保存されたコンテナイメージは、NHN Cloud Consoleで照会できます。

* イメージリスト
**Container > NHN Container Registry(NCR) > Management**サービスページのレジストリリストからアップロードしたレジストリの**イメージ表示**ボタンをクリックすると、コンテナイメージリストが表示されます。

* アーティファクトリスト

 イメージリストから任意のイメージの**アーティファクト表示**ボタンをクリックすると、そのイメージのアーティファクトリストを表示できます。アーティファクトリストから特定のアーティファクトを選択して詳細情報とタグリストを確認できます。

* タグリスト
アーティファクトリストでアーティファクトをクリックすると、選択したアーティファクトに指定されたタグリストを照会できます。新しくタグを作成したり、タグを検索して削除できます。

### コンテナイメージのインポート(Pull)
Dockerコマンドラインツールの**pull**コマンドを使用してイメージをインポートできます。これを行うには、NHN Cloud Consoleでインポートするイメージの情報を確認する必要があります。

```bash
docker pull {ユーザーレジストリアドレス}/{イメージ名}:{タグ名}
```

* 例
```bash
$ docker pull example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
18.04: Pulling from ubuntu
5bed26d33875: Pull complete
f11b29a9c730: Pull complete
930bda195c84: Pull complete
78bf9a5ad49e: Pull complete
Digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320
Status: Downloaded newer image for example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
example-kr1-registry.container.cloud.toast.com/ubuntu:18.04

$ docker images
REPOSITORY                                              TAG     IMAGE ID        CREATED         SIZE
example-kr1-registry.container.cloud.toast.com/ubuntu   18.04   4e5021d210f6    12 days ago     64.2MB
```



## コンテナレジストリ管理

### コンテナイメージおよびアーティファクト削除

レジストリに保存されたイメージを今後使用しない場合は、NHN Cloud Consoleから削除できます。イメージを削除するには、イメージリスト表示画面で削除するイメージを選択した後、**イメージ削除**ボタンをクリックします。同様にアーティファクトを削除するにはアーティファクトリスト表示画面で削除するアーティファクトを選択した後、**アーティファクト削除**ボタンをクリックします。

### コンテナイメージタグの作成

Dockerコマンドラインツールを使わずにNHN Cloud Consoleからタグを作成できます。アーティファクトリスト表示画面でタグを追加するアーティファクトを選択した後、下部の詳細情報表示画面で**タグ**タブを選択します。すると現在選択したアーティファクトに指定されたタグリストを表示できます。ここで**タグ作成**ボタンをクリックすると**タグ作成ポップアップ**ウィンドウが表示され、タグ名を入力して新しいタグを作成できます。

### コンテナイメージタグの削除

使用しないタグがある場合は、同様にNHN Cloud Consoleでタグを削除できます。タグを作成する時と同様に、タグリスト表示画面に移動して削除するタグを選択します。タグが多くてタグリストにすぐに表示されない場合は、タグ検索機能を活用して削除したいタグを検索できます。削除するタグを選択して**タグ削除**ボタンをクリックすると選択したタグを削除できます。



### Garbage Collection

レジストリ、イメージまたはアーティファクトを削除する場合、メタデータのみ削除されるため、ストレージはすぐに解放されません。そのためリソースを削除した後、ストレージ容量を確保するにはGarbage Collection機能を使用する必要があります。NHN Cloud ConsoleでGarbage Collectionを使用するには**Container > NHN Container Registry(NCR) > Garbage Collection**ページに移動します。

Garbage Collectionを本格的に実行する前に**テスト実行**を通して削除対象リストとストレージ容量の確保予想値を確認できます。**Garbage Collection**ページで**今すぐ実行**または**テスト実行**ボタンをクリックすると実行できます。実行結果に応じて**実行状態**項目に**成功**または**失敗**と表示されます。ログ表示ボタンをクリックするとGarbage Collectionの結果を確認できます。

> [参考]
>
> アップロード中のイメージやアーティファクトが損傷しないように、Garbage Collectionを実行しても、直近2時間以内にアップロードされたイメージやアーティファクトはクリーンアップしません。
