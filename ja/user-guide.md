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
docker tag ubuntu:18.04 example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
```

> [参考]
> コンテナイメージ名には、英語小文字、数字、一部特殊文字(-, _, /, .)の組み合わせのみ許可されます。リポジトリ名はレジストリアドレスを含めて最大255文字、タグ名は最大129文字に制限されます。イメージ名が長いと使いにくい場合があります。適切な長さの名前を使用してください。

Dockerコマンドラインツールの**push**コマンドを使用してコンテナイメージをユーザーレジストリに保存できます。

```bash
docker push {ユーザーレジストリアドレス}/{イメージ名}:{タグ名}
```

* 例
```bash
$ docker push example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.nhncloud.com/registry/ubuntu]
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
$ docker pull example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
18.04: Pulling from ubuntu
5bed26d33875: Pull complete
f11b29a9c730: Pull complete
930bda195c84: Pull complete
78bf9a5ad49e: Pull complete
Digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320
Status: Downloaded newer image for example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04

$ docker images
REPOSITORY                                                     TAG     IMAGE ID        CREATED         SIZE
example-kr1-registry.container.nhncloud.com/registry/ubuntu   18.04   4e5021d210f6    12 days ago     64.2MB
```

### Helm chart使用

NCRでHelm chartを管理することができます。Helm chartを保存したり、希望の環境にインポートするには、Helmコマンドラインツールを利用する必要があります。Helmコマンドラインツールのバージョンは最低3.8.0以上でなければなりません。

#### ユーザーレジストリログイン

Helmコマンドラインツールを利用してユーザーレジストリにアクセスするにはログインが必要です。helm registry login`コマンドを使用した後、 `Username`にはNHN CloudユーザーアカウントのUser Access Keyを、 `Password`にはSecret Keyをそれぞれ入力します。

```
$ helm registry login {ユーザーレジストリアドレス}
Username: {NHN CloudユーザーアカウントUser Access Key}
Password: {NHN CloudユーザーアカウントUser Secret Key}
Login Succeeded
```

#### Helm chart保存(Push)

Helm chartをレジストリに保存するには、アップロードするチャートを圧縮してローカルに保存する必要があります。Helm chartのrootディレクトリにディレクトリを変更した後、**package**コマンドを使ってチャートをローカルに保存します。`Chart.yaml`に指定された名前とバージョンで保存されます。

> [参考]
> Helm chartを作る方法についての詳しい情報は[Chart Template Developer's Guide](https://helm.sh/docs/chart_template_guide/)を参照してください。
> [Artifact Hub](https://artifacthub.io/)で公開されたHelm chartを取得できます。

```
$ helm package .
Successfully packaged chart and saved it to: /path/helm-0.1.0.tgz
```

これでHelmコマンドラインツールの**push**コマンドを使ってチャートをユーザーレジストリに保存できます。

```
helm push {チャート圧縮ファイル} oci://{ユーザーレジストリアドレス}
```

* 例

```shell
$ helm push helm-0.1.0.tgz oci://example-kr1-registry.container.nhncloud.com/registry
Pushed: example-kr1-registry.container.nhncloud.com/registry/helm:0.1.0
Digest: sha256:628760743a9642f0edd5f4dc30b598827c2c4cde4976ebe9eeb2d3e827ca7e99
```

#### Helm chartインストール(Install)

Helmコマンドラインツールの**install**コマンドを使ってチャートをKubernetes環境に配布できます。このため、NCR Consoleでインストールするチャートの情報を確認する必要があります。

```
helm install {配布名} oci://{ユーザーレジストリアドレス}/{チャート名} --version {チャートバージョン}
```

* 例

```
$ helm install myrelease oci://example-kr1-registry.container.nhncloud.com/registry/helm --version 0.1.0
```

#### Helm chart取得(Pull)

Helmコマンドラインツールの**pull**コマンドを使ってチャートを圧縮ファイルでインポートできます。このため、NCR Consoleでインポートするチャートの情報を確認する必要があります。

```
helm pull oci://{ユーザーレジストリアドレス}/{チャート名} --version {チャートバージョン}
```

* 例

```
$ helm pull oci://example-kr1-registry.container.nhncloud.com/registry/helm --version 0.1.0
Pulled: example-kr1-registry.container.nhncloud.com/registry/helm:0.1.0
Digest: sha256:628760743a9642f0edd5f4dc30b598827c2c4cde4976ebe9eeb2d3e827ca7e99
```

### OCI Artifact使用

ORASコマンドラインツールを使用して任意のファイルをOCI Artifactとしてレジストリに保存できます。
[ORAS installation](https://oras.land/docs/installation)を参考にしてORASコマンドラインツールをインストールします。ORASコマンドラインツールの詳しい使い方は[ORAS docs](https://oras.land/docs/)を参照してください。

#### ユーザーレジストリログイン

ORASコマンドラインツールを使ってユーザーレジストリにアクセスするにはログインが必要です。oras login`コマンドを使用した後、 `Username`にはNHN CloudユーザーアカウントのUser Access Keyを、`Password`にはSecret Keyをそれぞれ入力します。

```
$ oras login {ユーザーレジストリアドレス}
Username: {NHN CloudユーザーアカウントUser Access Key}
Password: {NHN CloudユーザーアカウントUser Secret Key}
Login Succeeded
```

#### OCI Artifact保存(Push)

レジストリに保存する任意のファイルを作成します。

```
$ echo "hello world" > artifact.txt
```

ORASコマンドラインツールの**push**コマンドを使ってファイルをユーザーレジストリに保存できます。

```
oras push {ユーザーレジストリアドレス}/{アーティファクト名}:{アーティファクトバージョン} {ファイル名}
```

* 例

```
$ oras push example-kr1-registry.container.nhncloud.com/registry/hello-artifact:v1 artifact.txt
Uploading 6001d106f8ef artifact.txt
Uploaded  6001d106f8ef artifact.txt
Pushed [registry] example-kr1-registry.container.nhncloud.com/registry/hello-artifact:v1
Digest: sha256:fbd2f5fd108cc75e7a805d9f21ab3c2ad8810c55c4e6581b1e1b3f3ea111d4fc
```

#### OCI Artifact取得(Pull)

ORASコマンドラインツールの**pull**コマンドを使用してファイルをインポートできます。このため、NCR Consoleでインポートするチャートの情報を確認する必要があります。

```
oras pull {ユーザーレジストリアドレス}/{アーティファクト名}:{アーティファクトバージョン}
```

* 例

```
$ oras pull example-kr1-registry.container.nhncloud.com/registry/hello-artifact:v1
Downloading a948904f2f0f artifact.txt
Downloaded  a948904f2f0f artifact.txt
Pulled [registry] example-kr1-registry.container.nhncloud.com/registry/hello-artifact:v1
Digest: sha256:a6886dfd78cfee5412d410d5ad09129efea9fe7da9c911dd976e8e77808a95b0
```

## コンテナレジストリ管理

### コンテナイメージおよびアーティファクト削除

レジストリに保存されたイメージを今後使用しない場合は、NHN Cloud Consoleから削除できます。イメージを削除するには、イメージリスト表示画面で削除するイメージを選択した後、**イメージ削除**ボタンをクリックします。同様にアーティファクトを削除するにはアーティファクトリスト表示画面で削除するアーティファクトを選択した後、**アーティファクト削除**ボタンをクリックします。

### コンテナイメージタグの作成

Dockerコマンドラインツールを使わずにNHN Cloud Consoleからタグを作成できます。アーティファクトリスト表示画面でタグを追加するアーティファクトを選択した後、下部の詳細情報表示画面で**タグ**タブを選択します。すると現在選択したアーティファクトに指定されたタグリストを表示できます。ここで**タグ作成**ボタンをクリックすると**タグ作成ポップアップ**ウィンドウが表示され、タグ名を入力して新しいタグを作成できます。

### コンテナイメージタグの削除

使用しないタグがある場合は、同様にNHN Cloud Consoleでタグを削除できます。タグを作成する時と同様に、タグリスト表示画面に移動して削除するタグを選択します。タグが多くてタグリストにすぐに表示されない場合は、タグ検索機能を活用して削除したいタグを検索できます。削除するタグを選択して**タグ削除**ボタンをクリックすると選択したタグを削除できます。

### レジストリWebフック設定

イメージ変更事項通知を受け取るには、NCR ConsoleでWebフック設定を登録します。 Webフックを設定するレジストリを選択し、下部の詳細情報表示画面で**Webフック**タブを選択します。**Webフック作成**ボタンをクリックします。**Webフック作成**ダイアログボックスが現れたらプロパティを指定した後、**確認**ボタンをクリックします。現在はHTTP(S)呼び出しとSlackメッセンジャーを利用した通知設定をサポートします。


### コンテナイメージ整理

レジストリに保存されたイメージとアーティファクトをポリシーに基づいて整理(削除)するようにNCR Consoleで設定できます。イメージ整理ポリシーを使用するには設定するレジストリを選択し、下部の詳細情報表示画面で**イメージ整理**タブをクリックします。
イメージ整理ポリシーは最大15個まで登録できます。

> [注意]
> イメージ整理と保護に同じポリシーが設定されている場合、イメージ保護ポリシーが優先的に処理され、イメージ整理機能が正常に動作しない可能性があります。
#### イメージ整理ポリシー設定

**ポリシー設定**タブで**整理ポリシー追加**ボタンをクリックすると**整理ポリシー追加**ダイアログボックスが表示され、イメージ、タグ、ポリシーを入力して新しい整理ポリシーを設定できます。
イメージ整理ポリシーは有効になって作成されます。一時的にポリシーの適用をやめるには**有効化/無効化機能**を使用できます。

* イメージ整理の動作中に整理ポリシー追加/削除が行われる場合、次のイメージ整理実行から反映されます。

イメージ、タグ、整理ポリシーはAND条件で処理されます。

* 全てのアーティファクト削除例：`全てのイメージ + 使用しないタグ名 + 該当アーティファクトすべて除外して、整理`

> [参考]
> レジストリにイメージ、タグ、ポリシーがすべて同じ整理ポリシーを重複して追加できません。
>
> `Push/Pullした日付がNのアーティファクトを除外して整理`ポリシーはPush/Pullを行ってからN日を超過したアーティファクトを削除します。
#### イメージ整理ポリシー削除

**ポリシー設定**タブ下部で削除する整理ポリシーを選択し、**整理ポリシー削除**ボタンをクリックして削除できます。

#### テスト実行

**ポリシー設定**タブで**テスト実行**ボタンをクリックして設定したイメージ整理ポリシーをテストできます。
テスト実行結果は**ヒストリー照会**タブで確認できます。

#### いま実行

**ポリシー設定**タブで**いま実行** ボタンをクリックして設定したイメージ整理ポリシーを手動で実行します。
実行結果は **ヒストリー照会** タブで確認できます。

#### 整理サイクル設定

イメージ整理ポリシーを定期的に自動実行するように設定できます。
繰り返し周期はCron式(\* \* \* \* \*)を利用します。各フィールドの意味は次のとおりです。

| フィールド名 | 値の許容範囲 | 許可された特殊文字 |
| --- | --- | --- |
| 分 | 0-59 | `*` `/` `,` `-` |
| 時間 | 0-59 | `*` `/` `,` `-` |
| 日 | 1-31 | `*` `/` `,` `?` |
| 月 | 1-12<br>JAN-DEC | `*` `/` `,` `-` |
| 曜日 | 0-6<br>SUN-SAT | `*` `/` `,` `?` |

> [参考]
> Cron式で使用する時間帯は協定世界時(UTC)です。

#### ヒストリー照会

**ヒストリー照会** タブでイメージ整理ヒストリーを照会できます。
下部の照会された情報をクリックしてヒストリー詳細情報を確認できます。

### コンテナイメージ保護

レジストリに保存されたイメージとアーティファクトが削除/変更されないようにNCR Consoleでイメージ保護機能を設定できます。イメージ保護機能を使用するには設定するレジストリを選択し、下部の詳細情報表示画面で **イメージ保護** タブをクリックします。
イメージ保護ポリシーは最大15個まで登録できます。

#### イメージ保護ポリシー追加

**保護ポリシー追加**ボタンをクリックすると **保護ポリシー追加**ダイアログボックスが表示され、保護ポリシーを入力して新しい保護ポリシーを設定できます。
イメージ保護ポリシーは有効になって作成されます。一時的にポリシーの適用をやめるには**有効化/無効化機能**を使用できます。

> [参考]
> レジストリにイメージとタグが同じ保護ポリシーを重複して追加できません。
#### イメージ保護ポリシー削除

**イメージ保護** タブ下部で削除する保護ポリシーを選択し、**保護ポリシー削除**ボタンをクリックして削除できます。

<span id="public-uri"></span>
## Public URIの使用
Public URIは外部からNCRにアクセスできるように提供するアドレスです。レジストリを作成する時、Public URIの使用有無を設定することで、レジストリへのアクセスを制御できます。Public URIを使用することで、外部システムやサービスでレジストリのイメージを活用できるようになります。

Public URIを使用しないように設定すると、レジストリは外部からアクセスできないように制限されます。この設定は、レジストリへのアクセスをNHN CloudのVPCネットワークに制限し、外部からの無断ダウンロードやアップロードの試みを防ぎ、セキュリティを強化します。

> [参考]
> Public URIを使用しない設定にすると、外部からレジストリに保存された画像にアクセスできなくなります。設定を変更する際は、周辺システムやサービスに影響を与える可能性があるため、変更前に十分な検証とテストを行う必要があります。

<span id="private-uri"></span>
## Private URI使用
Private URIはNHN CloudのVPCネットワーク内で使用できるNCRアドレスです。セキュリティを強化するためにインターネットゲートウェイに接続せずに外部ネットワークを切断したインスタンスでNCRサービスを使用したい場合はPrivate URI機能を活用できます。

> [参考]
> Private URIは韓国(ピョンチョン)リージョンでのみ使用可能です。

> [参考]
> インターネットゲートウェイに接続していないインスタンスでPrivate URIを利用するには、NCRとObject Storageサービスゲートウェイを作成する必要があります。

> [参考]
> インスタンス、サービスゲートウェイ、 Object StorageとNCRはすべて同じリージョンを使用する必要があります。

### NCRサービスゲートウェイの作成
**Network > Service Gateway**ページに移動し、**サービスゲートウェイの作成**をクリックします。作成したいサービスゲートウェイの**名前**、**VPC**、**サブネット**を入力し、**サービス**を**NCR**に選択して**確認**をクリックするとNCRサービスゲートウェイが作成されます。
![ncr_c001_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c001.png)

### Object Storageサービスゲートウェイの作成
NCRでPrivate URIを利用してイメージをインポートするには、Object Storageのサービスゲートウェイを作成する必要があります。NCRはObject Storageを使用してイメージ階層を保存するため、サービスゲートウェイが必要です。イメージをダウンロードする時もNCRにアクセスしてイメージマニフェストをインポートし、Object Storageにアクセスして実際のイメージレイヤーをダウンロードします。

**Network > Service Gateway**ページに移動して**サービスゲートウェイの作成**をクリックします。作成したいサービスゲートウェイの**名前**、**VPC**、**サブネット**を入力し、**サービス**を**Object Storage**に選択して**確認**をクリックするとObject Storageサービスゲートウェイが作成されます。
![ncr_c002_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c005.png)

### ホスト登録
インターネットゲートウェイに接続していないインスタンスでPrivate URIを利用してNCRレジストリを使用できるように、ホストファイルにドメインとIPを設定する必要があります。
インスタンスでPrivate EndpointのIPを見つけるためにホストファイルにNCRサービスゲートウェイIPアドレスとNCR Private Endpoint, Object StorageサービスゲートウェイIPアドレスとObject Storageドメインを入力します。

NCRとObject StorageサービスゲートウェイのIPアドレスは**Network > Service Gateway**ページで確認できます。
![ncr_c003_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c003.png)

NCR Private URIは**Container > NHN Container Registry(NCR) > 管理**ページでレジストリを選択した後、下部の**基本情報**タブで確認できます。Private EndpointはPrivate URIからレジストリ名を除いたパスです。
![ncr_c004_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c004.png)

* 例
```
Private URI: private-example-kr1-registry.container.nhncloud.com/hello-world
Private Endpoint: private-example-kr1-registry.container.nhncloud.com
```

**Windows**
`C:\Windows\System32\drivers\etc\hosts`ファイルを開き、次の内容を追加します。
```
{NCRサービスゲートウェイIPアドレス} {NCR Private Endpoint}
{Object StorageサービスゲートウェイIPアドレス} {Object Storageドメイン}
```

**Linux**
`/etc/hosts`ファイルを開き、次の内容を追加します。
```
{NCRサービスゲートウェイIPアドレス} {NCR Private Endpoint}
{Object StorageサービスゲートウェイIPアドレス} {Object Storageドメイン}
```


### Private URIによるレジストリ作業
インスタンスに接続し、`docker login`コマンドを実行してレジストリにログインします。インスタンス構成によっては、次のコマンドに`sudo`を付ける必要があります。
```shell
$ docker login {ユーザーPrivateレジストリアドレス}
Username: {NHN CloudユーザーアカウントUser Access Key}
Password: {NHN CloudユーザーアカウントUser Secret Key}
Login Succeeded
```

* 例
```shell
$ docker login private-example-kr1-registry.container.nhncloud.com
Username: hello-world
Password:
Login Succeeded
```

`docker pull`などのレジストリ作業を実行し、レジストリからサンプルイメージをダウンロードします。 NCR Consoleでインポートするイメージの情報を確認します。
```shell
$ docker pull {ユーザーPrivate URI}/{イメージ名}:{タグ名}
```

* 例
```
$ docker pull private-example-kr1-registry.container.nhncloud.com/hello-world/ubuntu:18.04
18.04: Pulling from ubuntu
5bed26d33875: Pull complete
f11b29a9c730: Pull complete
930bda195c84: Pull complete
78bf9a5ad49e: Pull complete
Digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320
Status: Downloaded newer image for private-example-kr1-registry.container.nhncloud.com/hello-world/ubuntu:18.04
private-example-kr1-registry.container.nhncloud.com/hello-world/ubuntu:18.04

$ docker images
REPOSITORY                                                        TAG     IMAGE ID        CREATED         SIZE
example-kr1-registry.container.nhncloud.com/hello-world/ubuntu   18.04   4e5021d210f6    12 days ago     64.2MB
```


## コンテナイメージの複製

NCRで提供する複製機能はリージョン間イメージを複製します。複製機能の具体的な特徴は次のとおりです。

* リージョン間の双方向イメージ複製を行います。
* ユーザーが複製実行方式を選択できます。
* 対象リージョンにすでに同じイメージが存在する場合は複製しません。
* 現在リージョン(A)から複製イメージの原本が削除されても対象リージョン(B)に複製されたイメージは削除しません。

イメージ複製機能を使用するにはNCR Consoleで**複製**タブをクリックします。

### 複製構成設定

**複製作成**をクリックし、**複製作成**ダイアログボックスで複製構成に必要な情報を入力します。

> [参考]
> 複製作成直後は状態が**無効**と表示される場合があります。複製準備が完了すると**有効**と表示されます。
> 数分経っても状態が変更されない場合は**更新**をクリックします。

### 複製対象フィルタ

* **ソースイメージ名フィルタ**：イメージ名または一部を入力して指定された対象を複製します。
* **ソースタグフィルタ**：タグ名または一部を入力して指定された対象を複製します。このフィルタに対して一致/除外を指定できます。

フィルタは次のようなパターンをサポートします。
| Pattern | Description | String(Match or not) |
| --- | --- | --- |
| * : path/* | 区分記号`/`を除くすべての文字と一致します。 | path/hello-world(Y) <br> path/my/hello-world(N) |
| ** : path/** | すべての文字と一致します。 | path/hello-world(Y) <br> path/my/hello-world(Y) |
| ? : 1.? | 区分記号`/`を除く任意の1文字と一致します。 | 1.0(Y) <br> 1.01(N) |
| {} : {path,ncr}/** | カンマで区切られた項目のいずれかに一致する文字と一致します。 | path/hello-world(Y) <br> ncr/hello-world(Y) <br> nhn/hello-world(N) |

### 自動複製

* **イベントベース**:現在リージョンにイメージがアップロードされる時、自動的に対象リージョンに複製が実行されます。
* **ユーザー設定**：ユーザーが設定した周期に基づいて複製が実行されます。

> [注意]
> **イベントベース**方式はPushタイプの複製でのみ使用できます。
> 新たにアップロードされるイメージのみ自動複製が実行されます。
> 複製構成前にアップロードされたイメージを複製するには**手動複製**機能を利用します。

### 手動複製

**複製実行**をクリックした後、**複製実行**ダイアログボックスで**確認**をクリックすると複製が実行されます。

> [注意]
> Garbage Collection機能が実行される前に複製を実行する場合、対象リージョン(B)に複製されたイメージの容量が原本よりも小さくなる可能性があります。
> 一定時間後に原本イメージの容量が小さくなります。

### 複製ヒストリー

複製ヒストリーで複製進行状況および履歴を確認できます。複製ヒストリーを確認するには構成した複製をクリックし、下部の**詳細情報表示**画面で**複製ヒストリー**タブをクリックします。
下部の照会された情報をクリックしてヒストリー詳細情報を確認できます。

## イメージキャッシュ使用

ソースレジストリ(他の遠隔レジストリ)からイメージをダウンロードしてキャッシュする機能を提供します。
イメージキャッシュタイプのレジストリでイメージPullリクエストが行われると、次のように区分してイメージの提供を決定します。

| 区分 | イメージ提供 |
| --- | --- |
| キャッシュされたイメージとソースレジストリのイメージが異なる | ソースレジストリのイメージをダウンロードしてイメージを提供 |
| キャッシュされたイメージとソースレジストリのイメージが同じ | キャッシュされたイメージを提供 |
| ソースレジストリが接続されていない | キャッシュされたイメージを提供 |
| ソースレジストリからイメージが削除 | イメージを提供しない |

イメージキャッシュタイプのレジストリは、次のようにリクエストしたイメージがない場合、ソースレジストリのイメージをダウンロードして提供します。
![D-NCR_imagecache_01](https://static.toastoven.net/prod_ncr/20221129/D-NCR_imagecache_01.png)

### イメージキャッシュの作成

イメージキャッシュを使用するにはソースレジストリを登録する必要があります。 NCR Consoleで**イメージキャッシュ**タブをクリックし、**イメージキャッシュ作成**をクリックします。**イメージキャッシュ作成**ダイアログボックスでソースレジストリの情報を入力します。
サポートされるソースレジストリタイプとURL、Access ID、Access Secretは次のとおりです。

| ソースレジストリタイプ | URL | Access ID | Access Secret |
| --- | --- | --- | --- |
| NHN Container Registry(NCR) | `https://xxxxxxxx-$REGION-registry.container.nhncloud.com` | User Access Key ID | Secret Access Key |
| Amazon Elastic Container Registry | `https://$AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazoneaws.com` | IAMアクセスキーID | IAM秘密アクセスキー |
| Azure Container Registry | `https://$REGISTRY_NAME.azurecr.io` | アクセスキーユーザー名 | アクセスキー暗号 |
| Google Cloud Container Registry | `https://$REGION` | \_json\_key | サービスアカウントの秘密鍵(JSONタイプ) |
| Docker Hub | `https://hub.docker.com` | Username | Password |
| Docker Registry | `docker-registryアドレス` | Username | Password |
| Harbor | `Harborアドレス` | Username | Password |
| Quay | `https://quay.io` | json\_file | {<br>"account\_name": "$ユーザーアカウント"、<br>"docker\_cli\_password": "$Quayで作成した暗号化されたPassword"<br>} |

> [参考]許可されるソースレジストリのポートは80、443です。
### イメージキャッシュの修正

ソースレジストリを変更できます。 NCR Consoleで**イメージキャッシュ**  >  **イメージキャッシュ修正**をクリックし、**イメージキャッシュ修正**ダイアログボックスでソースレジストリの情報を入力します。

### イメージキャッシュの削除

廃止予定のイメージキャッシュを削除できます。 NCR Consoleの**イメージキャッシュ**で削除したいイメージキャッシュを選択し、**イメージキャッシュ削除**ボタンをクリックします。

> [参考]
> 当該イメージキャッシュを対象に指定したレジストリが存在する場合、イメージキャッシュを削除できません。
### イメージキャッシュタイプのレジストリ作成

イメージキャッシュタイプのレジストリを作成するにはNCR Consoleの**管理**タブで**レジストリ作成**をクリックします。**レジストリ作成**ダイアログボックスで使用用途に**イメージキャッシュ**を選択し、イメージキャッシュ対象を選択します。

> [参考]
> イメージキャッシュタイプのレジストリにはImage Pushができません。
> イメージキャッシュタイプのレジストリには自動的に`Pullした日付が7日以内のアーティファクトは除外して整理`イメージ整理ポリシーが追加されます。
> * イメージ整理/保護ポリシーを追加して、レジストリごとにキャッシュされたイメージ管理ポリシーを設定できます。
> コンテナイメージ複製機能を使用すると、イメージキャッシュタイプのレジストリは一般タイプのレジストリに変更されて複製されます。
### イメージキャッシュタイプのレジストリからイメージを取得する(Pull)

Dockerコマンドラインツールのpullコマンドを使用してイメージキャッシュタイプのレジストリからイメージをインポートできます。

```
docker pull {ユーザーイメージキャッシュタイプのレジストリアドレス}/{ソースレジストリのレジストリ名}/{イメージ名}:{タグ名}
```

* 例

```
$ docker pull example-kr1-registry.container.nhncloud.com/harbor/test/nginx:latest
latest: Pulling from harbor/test/nginx
7a6db449b51b: Already exists
ca1981974b58: Already exists
d4019c921e20: Already exists
7cb804d746d4: Already exists
e7a561826262: Already exists
7247f6e5c182: Already exists
Digest: sha256:89020cd33be2767f3f894484b8dd77bc2e5a1ccc864350b92c53262213257dfc
Status: Downloaded newer image for example-kr1-registry.container.nhncloud.com/harbor/test/nginx:latest
example-kr1-registry.container.nhncloud.com/harbor/test/nginx:latest
$ docker images
REPOSITORY                                                                   TAG             IMAGE ID       CREATED         SIZE
example-kr1-registry.container.nhncloud.com/harbor/test/nginx                latest          2b7d6430f78d   2 months ago    142MB
```

公式イメージまたは単一レベルリポジトリからイメージをインポートする場合、`ソースレジストリのレジストリ名をlibrary`にして使用する必要があります。

```
docker pull {ユーザーイメージキャッシュタイプのレジストリアドレス}/library/hello-world:latest
```

* 公式イメージ例

```
$ docker pull example-kr1-registry.container.nhncloud.com/docker/library/hello-world:latest
$ docker images
REPOSITORY                                                                   TAG             IMAGE ID       CREATED         SIZE
example-kr1-registry.container.nhncloud.com/docker/library/hello-world       latest          feb5d9fea6a5   14 months ago   13.3kB
```

> [注意]イメージキャッシュ処理の最大時間は200秒です。ソースレジストリからイメージをダウンロードするのに200秒以上の時間がかかる場合はイメージキャッシュが処理されません。
>
> 同じイメージのインポートを再実行する場合、続きから行われます。
>
> イメージキャッシュが処理されていないのに容量が増加した場合、増加した容量は2時間後に元の状態に戻ります。
### イメージキャッシュタイプのレジストリ削除

廃止予定のイメージキャッシュタイプのレジストリを削除できます。NCR Consoleの**管理**で削除したいレジストリを選択し、**レジストリ削除**ボタンをクリックします。

## イメージ脆弱性スキャン

NCRサービスを有効にすると、デフォルトでイメージ脆弱性スキャン機能を使用できます。
イメージ脆弱性スキャンを使用すると、選択したアーティファクトの手動スキャンを開始できます。また、特定の間隔でNCR内のすべてのアーティファクトを自動的にスキャンするように周期を設定することもできます。

### アーティファクトスキャン

**Container** > **NHN Container Registry(NCR)**ページのレジストリリストでアップロードしたレジストリの**イメージ表示**ボタンをクリックすると、コンテナイメージリストを表示できます。
イメージリストでイメージの**アーティファクト表示**ボタンをクリックすると、そのイメージのアーティファクトリストを表示できます。
アーティファクトリストでアーティファクトを選択した後、**スキャン**をクリックして脆弱性スキャンを実行します。

* アーティファクトの**脆弱性**列には次の状態が表示されます。
    * Not Scanned(-)：アーティファクトがスキャンされたことがありません。
    * Scanning(進行中)：スキャン作業が進行中です。
    * Stopped(停止)：**スキャン停止**リクエストによりスキャン作業がキャンセルされました。
    * アーティファクトスキャンが正常に完了すると、発見された脆弱性の総数と修正できる脆弱性の数、そして全体の重大度レベルが表示されます。

> [参考]
> 状態が**Scanning**でなければ、いつでもスキャンを開始できます。
### 脆弱性情報照会

アーティファクトリストでアーティファクトを選択した後、**イメージスキャン**タブで詳細な脆弱性情報を確認できます。

### スキャン設定

自動的に脆弱性をスキャンする周期を設定できます。
**Container** > **NHN Container Registry(NCR)** > **スキャン設定**をクリックし、**自動スキャン**で設定する周期を選択します。

| 周期 | 説明 |
| --- | --- |
| None | 自動スキャンが設定されていません。 |
| Hourly | 毎時間、起動時にスキャンを実行します。 |
| Daily | 毎日0時にスキャンを実行します。UTC時間を使用するため、韓国時間基準で毎日午前9時に実行します。|
| Weekly | 毎週土曜日0時にスキャンを実行します。UTC時間を使用するため、韓国時間基準で日曜日午前9時に実行します。|
| Custom | ユーザーが設定したcron jobに基づいてスキャンを実行します。 |

> [参考]
> **Container** > **NHN Container Registry(NCR)**ページで**スキャン設定**を行うと、NCR内のすべてのレジストリに適用されます。
### CVE許可リスト

スキャンを実行すると、イメージに含まれるCVE(common vulnerabilities and exposures、共通脆弱性および漏洩)が識別されます。CVEの重要度によってはイメージの実行を許可しない場合があります。
この場合、ユーザーはCVE許可リストを作成して特定CVEを無視できます。
NCR全体に共通CVE許可リストを設定したり、レジストリごとに個別CVE許可リストを設定したりできます。

**NCR全体の共通CVE許可リスト設定**
</br>**Container** > **NHN Container Registry(NCR)**ページで**スキャン設定**をクリックします。**共通レジストリCVE許可** > **許可リスト**に無視するCVE IDリストを入力します。カンマまたは改行を使用して複数のCVE IDを追加できます。

**レジストリ別の個別CVE許可リスト設定**
</br>**Container** > **NHN Container Registry(NCR)**ページのレジストリリストでレジストリを選択した後、**イメージスキャン**タブの**イメージスキャン設定**をクリックします。**CVE許可** > **許可リスト**に無視するCVE IDリストを入力します。カンマまたは改行を使用して複数のCVE IDを追加できます。

> [参考]
> 個別レジストリに許可リストを設定すると、共通レジストリ許可リストは使用されなくなります。
### レジストリ設定

レジストリごとに脆弱性関連設定を行うことができます。

**Push時の自動スキャン**
</br>ユーザーがイメージをpushすると、自動的にスキャンを実行します。
</br>**Container** > **NHN Container Registry(NCR)**ページで**レジストリ作成**をクリックして**Push時に自動スキャン**を設定します。

**イメージ配布防止**
</br>脆弱性の重大度によっては、イメージの実行を許可しない場合があります。設定した重大度以上の重大度が検出されたイメージはpullできません。CVE許可リストに追加された脆弱性は無視されます。
</br>**Container** > **NHN Container Registry(NCR)**ページのレジストリリストからレジストリを選択した後、**イメージスキャン**タブの**イメージスキャン設定**をクリックします。**Pull防止**に許可しない重大度を選択します。

> [参考]
> イメージキャッシュレジストリに配布防止設定を行って初めてイメージをpullする場合にはイメージキャッシュレジストリにまだイメージの脆弱性情報が存在しないため、配布防止設定が適用されません。   

## イメージ信頼機能

NCRにあるイメージに署名し、署名を検証してイメージの整合性を確認できます。

### 事前準備

NCRはsigstore/cosignソリューションを利用してイメージ署名機能を提供します。イメージ信頼機能を使用するにはsigstore/cosignクライアントがインストールされている必要があります。
[sigstore/cosign](https://docs.sigstore.dev/cosign/installation/)を参照してインストールします。

**Windows**
[Cosign for Windows](https://github.com/sigstore/cosign/releases/download/v2.0.0/cosign-windows-amd64.exe)をダウンロードしてインストールします。

### キーペアの作成

アーティファクトを署名し、検証するためのキーペアをローカルに作成します。
コマンドを実行したパスにPrivate/Publicキーファイルが作成されます。

```bash
$ cosign generate-key-pair
Enter password for private key: 
Enter password for private key again: 
Private key written to cosign.key
Public key written to cosign.pub
```

### アーティファクト署名

Privateキーを利用して署名し、signatureをNCRに保存します。

```bash
$ cosign sign --key {private key file} {ユーザーレジストリアドレス}/{イメージ名}:{タグ名}
```

* 例

```bash
$ cosign sign --key cosign.key 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest
Enter password for private key: 
WARNING: Image reference 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest uses a tag, not a digest, to identify the image to sign.
    This can lead you to sign a different image than the intended one. Please use a
    digest (example.com/ubuntu@sha256:abc123...) rather than tag
    (example.com/ubuntu:latest) for the input to cosign. The ability to refer to
    images by tag will be removed in a future release.

WARNING: "517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox" appears to be a private repository, please confirm uploading to the transparency log at "https://rekor.sigstore.dev"
Are you sure you would like to continue? [y/N] y

	Note that there may be personally identifiable information associated with this signed artifact.
	This may include the email address associated with the account with which you authenticate.
	This information will be used for signing this artifact and will be stored in public transparency logs and cannot be removed later.

By typing 'y', you attest that you grant (or have permission to grant) and agree to have this information stored permanently in transparency logs.
Are you sure you would like to continue? [y/N] y
tlog entry created with index: 16394784
Pushing signature to: f579cc3e-kr2-registry.container.nhncloud.com/hy/busybox
```

> [参考]
> イメージキャッシュタイプのレジストリにあるイメージは署名ができません。
**アーティファクトの署名有無確認**
アーティファクトリストの**認証**列でアーティファクトの署名有無を確認できます。

### アーティファクトの署名検証

Publicキーを利用して偽造・改ざんを検証します。

```bash
$ cosign sign --key {public key file} {ユーザーレジストリアドレス}/{イメージ名}:{タグ名}
```

* 例

```bash
$ cosign verify --key cosign.pub 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest
Verification for 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The signatures were verified against the specified public key

[{"critical":{"identity":{"docker-reference":"517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox"},"image":{"docker-manifest-digest":"sha256:5e42fbc46b177f10319e8937dd39702e7891ce6d8a42d60c1b4f433f94200bd2"},"type":"cosign container image signature"},"optional":{"Bundle":{"SignedEntryTimestamp":"MEYCIQDw+fMYgkFqzoAT2LOJaLogLyGKMzhcz31RZEcdc1+84wIhAJ46+JqazStGtkqaJWRcmRkk97/nJ4L0wrNXhj1JCifO","Payload":{"body":"eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiI1YzMxNmNjYjRmMTBjMDhkNmY4ODVjMDVkNzhlZjMyODliZDMxYjhhMTliMDAwZTAwOTkwMzcxM2Y0ZGRmYTViIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FWUNJUUR5WEtYSEpzamZNZDJIMjRxWkliTDFSOE1XTFV3K0pKdHg3Z1J5T1JEYTd3SWhBSXAvalNWQkhMdm5NczY4ZWVSMFBVUkZtQjI0b0VFRVN5NmZKVHhXT0JXVyIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGWTBWcEsyMU9UR05wYzBaSWQyVXlOamRhUzJsc01ERkNRMk0xTWdwTlowTkdVWGhDYkRsa2NGTlJOakowVmtrMFNsQmxLMGxRVjJ0VFFXc3JNRzlKWmtZMU9GRjJVMUpxTm1OcU1XSm1SWEpKUVhOVGNWVm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=","integratedTime":1679898462,"logIndex":16394784,"logID":"c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"}}}}]
```

> [参考]
> 異なるキーで複数回署名する場合、すべてのキーで検証が可能です。

## サービス利用権限

サービス利用権限を利用してユーザーごとにNCRの使用を制御できます。

### 権限別使用機能

NCRサービスの利用権限は次のとおりです。

| 権限 | 説明 |
| --- | --- |
| Project Admin<br>Project Member<br>Service Admin | NHN Container Registry(NCR)サービスCreate(作成)、Read(読み取り)、Update(更新)、Delete(削除) |
| Service Viewer | NHN Container Registry(NCR)サービスRead(読み取り) |
| Image Uploader | NHN Container Registry(NCR)サービスRead(読み取り)、イメージCreate(作成)) |

NCRサービス利用権限により使用できる機能は次のとおりです。

| Action | Project Admin<br>Project Member<br>Service Admin | Service Viewer | Image Uploader |
| --- | --- | --- | --- |
| レジストリの照会 | ✓ | ✓ | ✓ |
| レジストリの作成/削除 | ✓ |  |  |
| レジストリ複製リストの照会 | ✓ | ✓ | ✓ |
| レジストリ複製の作成/修正/削除/実行 | ✓ |  |  |
| レジストリ複製ヒストリーの照会 | ✓ | ✓ | ✓ |
| イメージの表示 | ✓ | ✓ | ✓ |
| イメージPull | ✓ | ✓ |  |
| イメージPush | ✓ |  | ✓ |
| イメージの削除 | ✓ |  |  |
| アーティファクトの表示 | ✓ | ✓ | ✓ |
| アーティファクトの削除 | ✓ |  |  |
| アーティファクトタグの照会 | ✓ | ✓ | ✓ |
| アーティファクトタグの作成/削除 | ✓ |  |  |
| Webフックの照会 | ✓ | ✓ | ✓ |
| Webフックの作成/修正/削除 | ✓ |  |  |
| イメージ 整理 ポリシーの照会 | ✓ | ✓ | ✓ |
| イメージ 整理 ヒストリーの照会 | ✓ | ✓ | ✓ |
| イメージ 整理 ポリシーの作成/削除/実行 | ✓ |  |  |
| イメージ 整理 ルールの有効化/無効化 | ✓ |  |  |
| イメージ保護ポリシーの照会 | ✓ | ✓ | ✓ |
| イメージ保護ポリシーの追加/削除 | ✓ |  |  |
