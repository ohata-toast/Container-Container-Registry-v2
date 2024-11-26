## Container > NHN Container Registry(NCR) > APIガイド

コンテナレジストリを構成するためのAPIを記述します。
APIを使用するには、User Access KeyとSecret Access Keyが必要です。 User Access KeyとSecret Access KeyはNHN Cloudコンソールのアカウント > **APIセキュリティ設定** ページで作成します。

### APIリクエスト共通情報

APIを使用するにはサービスAppKeyが必要です。サービスAppkeyはコンソール上部の<strong>URL & Appkey</strong> メニューで確認が可能です。
APIドメインは次のとおりです。

| リージョン | ドメイン |
| --- | --- |
| 韓国(パンギョ)リージョン | https://kr1-ncr.api.nhncloudservice.com |
| 韓国(ピョンチョン)リージョン | https://kr2-ncr.api.nhncloudservice.com |

APIヘッダは次のとおりです。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-TC-AUTHENTICATION-ID | Header | String | O | User Access Key |
| X-TC-AUTHENTICATION-SECRET | Header | String | X (作成した場合は必須) | Secret Access Key |

### APIレスポンス共通情報

すべてのAPIリクエストに <strong>200 OK</strong>を返します。詳細なレスポンス結果はレスポンス本文ヘッダを参照します。

| 名前 | 種類 | 形式 | 説明 |
| --- | --- | --- | --- |
| header | Body | Object |  |
| header.isSuccessful | Body | Boolean | true:正常<br>false:エラー |
| header.resultCode | Body | Integer | 200:正常<br>10000以上:エラー |
| header.resultMessage | Body | String | "SUCCESS":正常<br>その他：エラー原因メッセージ |

> 注意] APIレスポンスにガイドに記載されていないフィールドが表示される場合があります。このようなフィールドはNHN Cloudの内部用途で使用され、事前告知なしに変更される可能性があるため、使用しないでください。<br>
> 注意]リソースを作成する際に入力しなかったオプションフィールドは、照会時にレスポンス本文に表示されない場合があります。

## コンテナレジストリ

### コンテナレジストリリスト表示

コンテナレジストリリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| registries | Body | Array | O | コンテナレジストリリスト |
| registries.project\_id | Body | Integer | O | コンテナレジストリID |
| registries.name | Body | String | O | コンテナレジストリ名 |
| registries.creation\_time | Body | String | O | 作成時間 |
| registries.update\_time | Body | String | O | 変更時間 |
| registries.repo\_count | Body | Integer | O | コンテナレジストリにあるイメージ数 |
| registries.metadata | Body | Object map[string]string | O | コンテナレジストリ設定 |
| registries.metadata.auto\_scan | Body | String | O | イメージをpushする時、自動的にスキャンするかどうか: true/false |
| registries.metadata.severity | Body | String | X | 脆弱性の深刻度: critical/medium/high/low/none |
| registries.metadata.prevent\_vul | Body | String | O | イメージの脆弱性深刻度に応じてpull防止設定を行うかどうか：true/false |
| registries.metadata.reuse\_sys\_cve\_allowlist | Body | String | O | 共通CVE許可リストを使用するかどうか：true/false |
| registries.cve\_allowlist | Body | Object | O | 脆弱性許可リスト |
| registries.cve\_allowlist.id | Body | Integer | O | 脆弱性許可リストID |
| registries.cve\_allowlist.items | Body | Object List | O | CVEリスト |
| registries.cve\_allowlist.items.cve\_id | Body | String | X | CVE ID |
| registries.registry\_id | Body | Integer | O | 0でなければイメージキャッシュタイプ。0は一般タイプ |
| registries.usage | Body | Integer | O | コンテナレジストリ使用量 |
| registries.uri | Body | String | O | コンテナレジストリアクセスURI |
| registries.private\_uri | Body | String | O | コンテナレジストリアクセスPrivate URI |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "registries": [
        {
            "project_id": 2,
            "name": "hy",
            "creation_time": "2023-03-14T02:41:10.525Z",
            "update_time": "2023-03-14T02:41:10.525Z",
            "repo_count": 2,
            "metadata": {
                "auto_scan": "true",
                "prevent_vul": "true",
                "public": "false",
                "retention_id": "1",
                "severity": "critical"
            },
            "cve_allowlist": {
                "id": 2,
                "project_id": 2,
                "items": []
            },
            "registry_id": 0,
            "usage": 57464288,
            "uri": "63ae9964-alp-kr1-registry.container.cloud.toastoven.net/hy",
            "private_uri": "private-63ae9964-alp-kr1-registry.container.cloud.toastoven.net/hy"
        }
    ]
}
```

### コンテナレジストリ表示

コンテナレジストリ情報を照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID、名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値の名前かどうか、true/false |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| registry | Body | Object | O | コンテナレジストリ情報 |
| registry.project\_id | Body | Integer | O | コンテナレジストリID |
| registry.name | Body | String | O | コンテナレジストリ名 |
| registry.creation\_time | Body | String | O | 作成時間 |
| registry.update\_time | Body | String | O | 変更時間 |
| registry.repo\_count | Body | Integer | O | コンテナレジストリにあるイメージ数 |
| registry.metadata | Body | Object map[string]string | O | コンテナレジストリ設定 |
| registry.metadata.auto\_scan | Body | String | O | イメージをpushする時、自動的にスキャンするかどうか：true/false |
| registry.metadata.severity | Body | String | X | 脆弱性の深刻度: critical/medium/high/low/none |
| registry.metadata.prevent\_vul | Body | String | O | イメージの脆弱性の深刻度に応じてpull防止設定を行うかどうか：true/false |
| registry.metadata.reuse\_sys\_cve\_allowlist | Body | String | O | 共通CVE許可リストを使用するかどうか：true/false |
| registry.cve\_allowlist | Body | Object | O | 脆弱性許可リスト |
| registry.cve\_allowlist.id | Body | Integer | O | 脆弱性許可リストID |
| registry.cve\_allowlist.items | Body | Object List | O | CVEリスト |
| registry.cve\_allowlist.items.cve\_id | Body | String | X | CVE ID |
| registry.registry\_id | Body | Integer | O | 0でなければイメージキャッシュタイプ。0は一般タイプ |
| registry.usage | Body | Integer | O | コンテナレジストリ使用量 |
| registry.uri | Body | String | O | コンテナレジストリアクセスURI |
| registry.private\_uri | Body | String | O | コンテナレジストリアクセスPrivate URI |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "registry": {
        "project_id": 2,
        "name": "hy",
        "creation_time": "2023-03-14T02:41:10.525Z",
        "update_time": "2023-03-14T02:41:10.525Z",
        "repo_count": 2,
        "metadata": {
            "auto_scan": "true",
            "prevent_vul": "true",
            "public": "false",
            "retention_id": "1",
            "severity": "critical"
        },
        "cve_allowlist": {
            "id": 2,
            "project_id": 2,
            "items": []
        },
        "registry_id": 0,
        "usage": 57464288,
        "uri": "63ae9964-alp-kr1-registry.container.cloud.toastoven.net/hy",
        "private_uri": "private-63ae9964-alp-kr1-registry.container.cloud.toastoven.net/hy"
    }
}
```

### コンテナレジストリを作成する

コンテナレジストリを作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| project\_name | Body | String | O | コンテナレジストリ名 |
| metadata | Body | Object map[string]string | X | コンテナレジストリ設定 |
| metadata.auto\_scan | Body | String | X | イメージをpushする時、自動的にスキャンするかどうか：true/false |
| metadata.severity | Body | String | X | 脆弱性の深刻度：critical/medium/high/low/none |
| metadata.prevent\_vul | Body | String | X | イメージの脆弱性の深刻度に応じてpull防止設定を行うかどうか：true/false |
| metadata.reuse\_sys\_cve\_allowlist | Body | String | X | 共通CVE許可リストを使用するかどうか：true/false |
| registry\_id | Body | Integer | X | イメージキャッシュタイプのレジストリ作成時に必要なイメージキャッシュID |

例

```json
{
    "project_name": "registry"
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### コンテナレジストリを削除する

コンテナレジストリを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID、名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値の名前かどうか、true/false |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### コンテナレジストリを変更する

コンテナレジストリを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID、名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値の名前かどうか、true/false |
| metadata | Body | Object map[string]string | X | コンテナレジストリ設定 |
| metadata.auto\_scan | Body | String | X | イメージをpushする時、自動的にスキャンするかどうか：true/false |
| metadata.severity | Body | String | X | 脆弱性の深刻度：critical/medium/high/low/none |
| metadata.prevent\_vul | Body | String | X | イメージの脆弱性の深刻度に応じてpull防止設定を行うかどうか：true/false |
| metadata.reuse\_sys\_cve\_allowlist | Body | String | X | 共通CVE許可リストを使用するかどうか：true/false |
| cve\_allowlist | Body | Object | 設定されている時は必須 | 脆弱性許可リスト |
| cve\_allowlist.items | Body | Object List | 設定されている時は必須 | CVEリスト |
| cve\_allowlist.items.cve\_id | Body | String | 設定されている時は必須 | CVE ID |

例

```json
{
    "metadata": {
        "auto_scan": "true"
    },
    "cve_allowlist": {
        "items": [
            {
                "cve_id": "CVE-2011-3374"
            }
        ]
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージ保護ポリシーリストを表示

イメージ保護ポリシーリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId  | URL | String | O | レジストリID |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| immutable\_tag\_rules | Body | Array | O | イメージ保護ポリシーリスト |
| immutable\_tag\_rules.id | Body | Integer | O | イメージ保護ポリシーID |
| immutable\_tag\_rules.disabled | Body | Boolean | O | イメージ保護ポリシーを無効にするかどうか |
| immutable\_tag\_rules.scope | Body | Object | O | イメージに対する保護ポリシー情報 |
| immutable\_tag\_rules.scope.include | Body | Boolean | O | イメージに対する保護ポリシーの設定有無 |
| immutable\_tag\_rules.scope.pattern | Body | String | O | 保護対象イメージ |
| immutable\_tag\_rules.tag | Body | Object | O | タグに対する保護ポリシー情報 |
| immutable\_tag\_rules.tag.include | Body | Boolean | O | タグに対する保護ポリシー設定の有無 |
| immutable\_tag\_rules.tag.pattern | Body | String | O | 保護対象タグ |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "immutable_tag_rules": [
        {
            "id": 1,
            "disabled": false,
            "scope": {
                "include": true,
                "pattern": "**"
            },
            "tag": {
                "include": true,
                "pattern": "**"
            }
        }
    ]
}
```

### イメージ保護ポリシーを作成する

イメージ保護ポリシーを作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/immutabletagrules
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| registryId | URL | String | O | レジストリID |
| scope | Body | Object | X | イメージに対する保護ポリシー情報 |
| scope.include | Body | Boolean | X | イメージに対する保護ポリシー設定の有無 |
| scope.pattern | Body | String | X | 保護対象イメージ<br>全体イメージ対象: \*\* 入力 |
| tag | Body | Object | X | タグに対する保護ポリシー情報 |
| tag.include | Body | Boolean | X | タグに対する保護ポリシー設定の有無 |
| tag.pattern | Body | String | X | 保護対象タグ<br>全体タグ対象：\*\* 入力 |

例

```json
{
    "scope": {
        "include": true,
        "pattern": "test"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージ保護ポリシーを削除する

イメージ保護ポリシーを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId  | URL | String | O | レジストリID |
| rule\_id | URL | String | O | イメージ保護ポリシーID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージ保護ポリシーを変更する

イメージ保護ポリシーを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId  | URL | String | O | レジストリID |
| rule\_id | URL | String | O | イメージ保護ポリシーID |
| disabled | Body | Boolean | X | イメージ保護ポリシーを無効化にするかどうか |
| scope | Body | Object | O | イメージに対する保護ポリシー情報 |
| scope.include | Body | Boolean | O | イメージに対する保護ポリシー設定の有無 |
| scope.pattern | Body | String | O | 保護対象イメージ |
| tag | Body | Object | O | タグに対する保護ポリシー情報 |
| tag.include | Body | Boolean | O | タグに対する保護ポリシー設定の有無 |
| tag.pattern | Body | String | O | 保護対象タグ |

例

```json
{
    "disabled": true,
    "scope": {
        "include": true,
        "pattern": "test"
    },
    "tag": {
        "include": false,
        "pattern": "**"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージクリーンアップポリシーリストを表示

イメージクリーンアップポリシーリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| retention\_rules | Body | Array | O | イメージクリーンアップポリシーリスト |
| retention\_rules.id | Body | Integer | O | イメージクリーンアップポリシーID |
| retention\_rules.disabled | Body | Boolean | O | イメージクリーンアップポリシーを無効にするかどうか |
| retention\_rules.parameters | Body | Object map | O | クリーンアップポリシー設定 |
| retention\_rules.parameters.always | Body | Integer | X | 常にn個のアーティファクトのみ保管 |
| retention\_rules.parameters.latest\_pulled\_n\_images | Body | Integer | X | 直近にPullしたn個のアーティファクトのみ保管 |
| retention\_rules.parameters.latest\_pushed\_n\_images | Body | Integer | X | 直近にPushしたn個のアーティファクトのみ保管 |
| retention\_rules.parameters.n\_days\_since\_last\_pull | Body | Integer | X | Pushした日付がn日のアーティファクトのみ保管 |
| retention\_rules.parameters.n\_days\_since\_last\_push | Body | Integer | X | Pullした日付がnのアーティファクトのみ保管 |
| retention\_rules.tag | Body | Object | O | タグに対するクリーンアップポリシー情報 |
| retention\_rules.tag.include | Body | Boolean | O | タグに対するクリーンアップポリシー設定の有無 |
| retention\_rules.tag.pattern | Body | String | O | クリーンアップ対象タグ |
| retention\_rules.tag.untagged | Body | Boolean | O | タグが付けられていないアーティファクトを含めるかどうか |
| retention\_rules.scope | Body | Object | O | イメージに対するクリーンアップポリシー情報 |
| retention\_rules.scope.include | Body | Boolean | O | イメージに対するクリーンアップポリシー設定の有無 |
| retention\_rules.scope.pattern | Body | String | O | クリーンアップ対象イメージ |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "retention_rules": [
        {
            "id": 1,
            "disabled": false,
            "parameters": {
                "always": -1
            },
            "tag": {
                "include": true,
                "pattern": "**",
                "untagged": false
            },
            "scope": {
                "include": true,
                "pattern": "**"
            }
        }
    ]
}
```

### イメージクリーンアップポリシーを作成する

イメージクリーンアップポリシーを作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| tag | Body | Object | X | タグに対するクリーンアップポリシー情報 |
| tag.include | Body | Boolean | X | タグに対するクリーンアップポリシー設定の有無 |
| tag.pattern | Body | String | X | クリーンアップ対象タグ<br>全体タグ対象：\*\* 入力 |
| tag.untagged | Body | Boolean | X | タグが付けられていないアーティファクトを含めるかどうか |
| scope | Body | Object | X | イメージに対するクリーンアップポリシー情報 |
| scope.include | Body | Boolean | X | イメージに対するクリーンアップポリシー設定の有無 |
| scope.pattern | Body | String | X | クリーンアップ対象イメージ<br>全体イメージ対象：\*\* 入力 |
| disabled | Body | Boolean | X | イメージクリーンアップポリシーを無効にするかどうか<br>未入力時は無効(false)設定 |
| parameters | Body | Object map | O | クリーンアップポリシー設定 |
| parameters.always | Body | Integer | X | 常にn個のアーティファクトのみ保管 |
| parameters.latest\_pulled\_n\_images | Body | Integer | X | 直近にPullしたn個のアーティファクトのみ保管 |
| parameters.latest\_pushed\_n\_images | Body | Integer | X | 直近にPushしたn個のアーティファクトのみ保管 |
| parameters.n\_days\_since\_last\_pull | Body | Integer | X | Pushした日付がn日のアーティファクトのみ保管 |
| parameters.n\_days\_since\_last\_push | Body | Integer | X | Pullした日付がn日のアーティファクトのみ保管 |

例

```json
{
    "scope": {
        "include": true,
        "pattern": "test"
    },
    "parameters": {
        "latest_pulled_n_images": 2
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージクリーンアップポリシーを削除する

イメージ保護ポリシーを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| id | Query | Integer | O | イメージクリーンアップポリシーID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージクリーンアップポリシーを変更する

イメージ保護ポリシーを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/{rule_id}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| rule\_id | URL | String | O | イメージクリーンアップポリシーID |
| disabled | Body | Boolean | X | イメージクリーンアップポリシーを無効にするかどうか |
| tag | Body | Object | O | タグに対するクリーンアップポリシー情報 |
| tag.include | Body | Boolean | O | タグに対するクリーンアップポリシー設定の有無 |
| tag.pattern | Body | String | O | クリーンアップ対象タグ |
| tag.untagged | Body | Boolean | O | タグが付けられていないアーティファクト含めるかどうか |
| scope | Body | Object | O | イメージに対するクリーンアップポリシー情報 |
| scope.include | Body | Boolean | O | イメージに対するクリーンアップポリシー設定の有無 |
| scope.pattern | Body | String | O | クリーンアップ対象イメージ |
| parameters | Body | Object map | O | クリーンアップポリシー設定 |
| parameters.always | Body | Integer | 設定されている場合は必須 | 常にn個のアーティファクトのみ保管 |
| parameters.latest\_pulled\_n\_images | Body | Integer | 設定されている場合は必須 | 直近にPullしたn個のアーティファクトのみ保管 |
| parameters.latest\_pushed\_n\_images | Body | Integer | 設定されている場合は必須 | 直近にPushしたn個のアーティファクトのみ保管 |
| parameters.n\_days\_since\_last\_pull | Body | Integer | 設定されている場合は必須 | Pushした日付がn日のアーティファクトのみ保管 |
| parameters.n\_days\_since\_last\_push | Body | Integer | 設定されている場合は必須 | Pullした日付がn日のアーティファクトのみ保管 |

例

```json
{
    "disabled": true,
    "scope": {
        "include": true,
        "pattern": "test"
    },
    "tag": {
        "include": false,
        "pattern": "**",
        "untagged": false
    },
    "parameters": {
        "latest_pulled_n_images": 2
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージクリーンアップヒストリーリスト表示

イメージクリーンアップヒストリーリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| executions | Body | Array | O | イメージクリーンアップヒストリーリスト |
| executions.id | Body | Integer | O | イメージクリーンアップヒストリーID |
| executions.dry\_run | Body | Boolean | O | テスト実行を行うかどうか |
| executions.trigger | Body | String | O | イメージクリーンアップ実行方式 |
| executions.start\_time | Body | String | O | イメージクリーンアップ開始時間 |
| executions.end\_time | Body | String | O | イメージクリーンアップ終了時間 |
| executions.status | Body | String | O | イメージクリーンアップ状態 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "executions": [
        {
            "id": 350,
            "dry_run": false,
            "trigger": "MANUAL",
            "start_time": "2023-03-27T17:34:53Z",
            "end_time": "2023-03-27T17:34:54Z",
            "status": "Success"
        }
    ]
}
```

### イメージクリーンアップヒストリー詳細表示

イメージクリーンアップヒストリーを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| executionId | URL | String | O | イメージクリーンアップヒストリーID |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tasks | Body | Array | O | タスクリスト |
| tasks.id | Body | Integer | O | タスクID |
| tasks.repository | Body | String | O | クリーンアップされたイメージ名 |
| tasks.retained | Body | Integer | O | イメージ保護設定の有無 |
| tasks.total | Body | Integer | O | クリーンアップされた数 |
| tasks.start\_time | Body | String | O | クリーンアップ開始時間 |
| tasks.end\_time | Body | String | O | クリーンアップ終了時間 |
| tasks.status | Body | String | O | タスク状態 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "tasks": [
        {
            "id": 350,
            "repository": "busybox",
            "retained": 1,
            "total": 1,
            "start_time": "2023-03-27T17:34:53Z",
            "end_time": "2023-03-27T17:34:54Z",
            "status": "Success"
        }
    ]
}
```

### イメージクリーンアップヒストリーログ表示

イメージクリーンアップヒストリーログを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks/{taskId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| executionId | URL | String | O | イメージクリーンアップヒストリーID |
| taskId | URL | String | O | イメージクリーンアップタスクID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tasks | Body | String | O | イメージクリーンアップログ |

### イメージクリーンアップポリシー実行

イメージクリーンアップポリシーを実行します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| dry\_run | Body | Boolean | X | テスト実行有無 |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージクリーンアップ周期表示

イメージクリーンアップ周期を照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| trigger | Body | Object | O | イメージクリーンアップ周期情報 |
| trigger.kind | Body | String | O | イメージクリーンアップタイプ |
| trigger.settings | Body | Object | O | イメージクリーンアップ周期設定 |
| trigger.settings.cron | Body | String | O | イメージクリーンアップ周期 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "trigger": {
        "kind": "Schedule",
        "settings": {
            "cron": ""
        }
    }
}
```

### イメージクリーンアップ周期を作成する

イメージクリーンアップ周期を作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryId | URL | String | O | レジストリID |
| settings | Body | Object | O | イメージクリーンアップ周期設定 |
| settings.cron | Body | String | O | イメージクリーンアップ周期 |

例

```json
{
    "settings": {
        "cron": "0 0 * * 0"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### Webフックリスト表示

Webフックリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID。名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値が名前かどうか。true/false |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| policies | Body | Array | O | Webフックリスト |
| policies.creation\_time | Body | String | O | Webフック作成時間 |
| policies.enabled | Body | Boolean | O | Webフックが有効かどうか |
| policies.event\_types | Body | String List | O | Webフックイベントタイプ、PUSH\_ARTIFACT/PULL\_ARTIFACT/DELETE\_ARTIFACT |
| policies.id | Body | Integer | O | WebフックID |
| policies.name | Body | String | O | Webフック名 |
| policies.project\_id | Body | Integer | O | レジストリID |
| policies.targets | Body | Object List | O | Webフック情報 |
| policies.targets.address | Body | String | O | Webフックエンドポイント |
| policies.targets.type | Body | String | O | Webフック通知タイプ、http/slack |
| policies.targets.auth\_header | Body | String | X | 認証ヘッダ |
| policies.targets.skip\_cert\_verify | Body | Boolean | X | 証明書検証有無 |
| policies.update\_time | Body | String | O | Webフック変更時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "policies": [
        {
            "creation_time": "2023-03-27T22:53:16.719Z",
            "enabled": true,
            "event_types": [
                "PUSH_ARTIFACT",
                "PULL_ARTIFACT",
                "DELETE_ARTIFACT"
            ],
            "id": 1,
            "name": "test",
            "project_id": 2,
            "targets": [
                {
                    "address": "https://192.168.0.1",
                    "skip_cert_verify": true,
                    "type": "http"
                }
            ],
            "update_time": "2023-03-27T22:53:16.719Z"
        }
    ]
}
```

### Webフック表示

Webフックを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID。名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値が名前かどうか。true/false |
| policyId | URL | String | O | WebフックID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| policy | Body | Object | O | Webフック情報 |
| policy.creation\_time | Body | String | O | Webフック作成時間 |
| policy.enabled | Body | Boolean | O | Webフックが有効かどうか |
| policy.event\_types | Body | String List | O | Webフックイベントタイプ、PUSH\_ARTIFACT/PULL\_ARTIFACT/DELETE\_ARTIFACT |
| policy.id | Body | Integer | O | WebフックID |
| policy.name | Body | String | O | Webフック名 |
| policy.project\_id | Body | Integer | O | レジストリID |
| policy.targets | Body | Object List | O | Webフック情報 |
| policy.targets.address | Body | String | O | Webフックエンドポイント |
| policy.targets.type | Body | String | O | Webフック通知タイプ、http/slack |
| policy.targets.auth\_header | Body | String | X | 認証ヘッダ |
| policy.targets.skip\_cert\_verify | Body | Boolean | X | 証明書検証有無 |
| policy.update\_time | Body | String | O | Webフック変更時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "policy": {
        "creation_time": "2023-03-27T22:53:16.719Z",
        "enabled": true,
        "event_types": [
            "PUSH_ARTIFACT",
            "PULL_ARTIFACT",
            "DELETE_ARTIFACT"
        ],
        "id": 1,
        "name": "test",
        "project_id": 2,
        "targets": [
            {
                "address": "https://192.168.0.1",
                "skip_cert_verify": true,
                "type": "http"
            }
        ],
        "update_time": "2023-03-27T22:53:16.719Z"
    }
}
```

### Webフックを作成する

Webフックを作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID。名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値が名前かどうか。true/false |
| enabled | Body | Boolean | X | Webフックが有効かどうか<br>未入力時false設定 |
| event\_types | Body | String List | O | Webフックイベントタイプ、PUSH\_ARTIFACT/PULL\_ARTIFACT/DELETE\_ARTIFACT |
| name | Body | String | X | Webフック名 |
| targets | Body | Object List | O | Webフック情報 |
| targets.address | Body | String | O | Webフックエンドポイント |
| targets.type | Body | String | O | Webフック通知タイプ、http/slack |
| targets.auth\_header | Body | String | X | 認証ヘッダ |
| targets.skip\_cert\_verify | Body | Boolean | X | 証明書検証有無 |

例

```json
{
    "event_types": [
        "PUSH_ARTIFACT",
        "PULL_ARTIFACT",
        "DELETE_ARTIFACT"
    ],
    "name": "test",
    "targets": [
        {
            "address":"https://192.168.1.1:8080",
            "skip_cert_verify":true,
            "type":"http"
        }
    ]
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### Webフックを削除する

Webフックを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID。名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値が名前かどうか。true/false |
| policyId | URL | String | O | WebフックID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### Webフックを変更する

Webフックを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID。名前が数字だけの場合はX-Is-Resource-Name値をtrueに設定 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId値が名前かどうか。true/false |
| policyId | URL | String | O | WebフックID |
| enabled | Body | Boolean | X | Webフックが有効かどうか |
| event\_types | Body | String List | O | Webフックイベントタイプ、PUSH\_ARTIFACT/PULL\_ARTIFACT/DELETE\_ARTIFACT |
| name | Body | String | X | Webフック名 |
| targets | Body | Object List | O | Webフック情報 |
| targets.address | Body | String | O | Webフックエンドポイント |
| targets.type | Body | String | O | Webフック通知タイプ、http/slack |
| targets.auth\_header | Body | String | X | 認証ヘッダ |
| targets.skip\_cert\_verify | Body | Boolean | X | 証明書検証有無 |

例

```json
{
    "event_types": [
        "PUSH_ARTIFACT"
    ],
    "targets": [
        {
            "address":"https://192.168.1.1:8080",
            "skip_cert_verify":true,
            "type":"http"
        }
    ]
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

## コンテナイメージ

### コンテナイメージリスト表示

コンテナイメージリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| images | Body | Array | O | コンテナイメージリスト |
| images.id | Body | Integer | O | コンテナイメージID |
| images.name | Body | String | O | コンテナイメージ名 |
| images.project\_id | Body | Integer | O | レジストリID |
| images.pull\_count | Body | Integer | O | コンテナイメージpull回数 |
| images.artifact\_count | Body | Integer | O | コンテナイメージアーティファクト数 |
| images.creation\_time | Body | String | O | コンテナイメージ作成時間 |
| images.update\_time | Body | String | O | コンテナイメージ変更時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "images": [
        {
            "id": 1,
            "name": "hy/busybox",
            "project_id": 2,
            "description": "",
            "pull_count": 8,
            "artifact_count": 1,
            "creation_time": "2023-03-27T01:55:13.176Z",
            "update_time": "2023-03-27T02:02:07.764Z"
        }
    ]
}
```

### コンテナイメージ表示

コンテナイメージを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID |
| imageName | URL | String | O | コンテナイメージ名 |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| image | Body | Object | O | コンテナイメージリスト |
| image.id | Body | Integer | O | コンテナイメージID |
| image.name | Body | String | O | コンテナイメージ名 |
| image.project\_id | Body | Integer | O | レジストリID |
| image.pull\_count | Body | Integer | O | コンテナイメージpull回数 |
| image.artifact\_count | Body | Integer | O | コンテナイメージアーティファクト数 |
| image.creation\_time | Body | String | O | コンテナイメージ作成時間 |
| image.update\_time | Body | String | O | コンテナイメージ変更時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "image": {
        "id": 1,
        "name": "hy/busybox",
        "project_id": 2,
        "description": "",
        "pull_count": 8,
        "artifact_count": 1,
        "creation_time": "2023-03-27T01:55:13.176Z",
        "update_time": "2023-03-27T02:02:07.764Z"
    }
}
```

### コンテナイメージを削除する

コンテナイメージを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### アーティファクトリスト表示

アーティファクトリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/images/{imageName}/artifacts
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryNameOrId | URL | String | O | レジストリ名またはID |
| imageName | URL | String | O | コンテナイメージ名 |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |
| with\_scan\_overview | Query | String | X | 脆弱性情報を照会するかどうか |
| with\_accessory | Query | String | X | 認証情報を照会するかどうか |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| artifacts | Body | Array | O | アーティファクトリスト |
| artifacts.digest | Body | String | O | アーティファクトダイジェスト |
| artifacts.extra\_attrs | Body | Object | O | アーティファクト追加情報 |
| artifacts.scan\_overview | Body | Object | X | アーティファクトスキャン情報 |
| artifacts.accessories | Body | Object | X | アーティファクト認証情報 |
| artifacts.manifest\_media\_type | Body | String | O | アーティファクトタイプ |
| artifacts.media\_type | Body | String | O | アーティファクトタイプ |
| artifacts.size | Body | Integer | O | アーティファクトサイズ |
| artifacts.pull\_time | Body | String | O | アーティファクトpull時間 |
| artifacts.push\_time | Body | String | O | アーティファクトpush時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "artifacts": [
        {
            "digest": "sha256:ba7000206594c2d72c3ab550453004c0dc50961157e5ebd2fb8ea1890099d02d",
            "extra_attrs": {
                "architecture": "arm64",
                "author": "",
                "config": {
                    "Cmd": [
                        "sh"
                    ],
                    "Env": [
                        "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
                    ]
                },
                "created": "2023-03-17T01:46:42.521879748Z",
                "os": "linux"
            },
            "icon": "sha256:0048162a053eef4d4ce3fe7518615bef084403614f8bca43b40ae2e762e11e06",
            "id": 1,
            "labels": null,
            "manifest_media_type": "application/vnd.docker.distribution.manifest.v2+json",
            "media_type": "application/vnd.docker.container.image.v1+json",
            "project_id": 2,
            "pull_time": "2023-03-27T02:02:01.703Z",
            "push_time": "2023-03-27T01:55:14.082Z",
            "references": null,
            "repository_id": 1,
            "size": 2003144,
            "tags": [
                {
                    "artifact_id": 1,
                    "id": 1,
                    "immutable": false,
                    "name": "latest",
                    "pull_time": "2023-03-27T02:02:01.703Z",
                    "push_time": "2023-03-27T01:55:14.642Z",
                    "repository_id": 1,
                    "signed": false
                }
            ],
            "type": "IMAGE",
            "accessories": null
        }
    ]
}
```

### アーティファクト表示

アーティファクトを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |
| with\_scan\_overview | Query | String | X | 脆弱性情報を照会するかどうか |
| with\_accessory | Query | String | X | 認証情報を照会するかどうか |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| artifact | Body | Object | O | アーティファクト情報 |
| artifact.digest | Body | String | O | アーティファクトダイジェスト |
| artifact.extra\_attrs | Body | Object | O | アーティファクト追加情報 |
| artifact.scan\_overview | Body | Object | X | アーティファクトスキャン情報 |
| artifact.accessories | Body | Object | X | アーティファクト認証情報 |
| artifact.manifest\_media\_type | Body | String | O | アーティファクトタイプ |
| artifact.media\_type | Body | String | O | アーティファクトタイプ |
| artifact.size | Body | Integer | O | アーティファクトサイズ |
| artifact.pull\_time | Body | String | O | アーティファクトpull時間 |
| artifact.push\_time | Body | String | O | アーティファクトpush時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "artifact": {
        "digest": "sha256:ba7000206594c2d72c3ab550453004c0dc50961157e5ebd2fb8ea1890099d02d",
        "extra_attrs": {
            "architecture": "arm64",
            "author": "",
            "config": {
                "Cmd": [
                    "sh"
                ],
                "Env": [
                    "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
                ]
            },
            "created": "2023-03-17T01:46:42.521879748Z",
            "os": "linux"
        },
        "icon": "sha256:0048162a053eef4d4ce3fe7518615bef084403614f8bca43b40ae2e762e11e06",
        "id": 1,
        "labels": null,
        "manifest_media_type": "application/vnd.docker.distribution.manifest.v2+json",
        "media_type": "application/vnd.docker.container.image.v1+json",
        "project_id": 2,
        "pull_time": "2023-03-27T02:02:01.703Z",
        "push_time": "2023-03-27T01:55:14.082Z",
        "references": null,
        "repository_id": 1,
        "size": 2003144,
        "tags": [
            {
                "artifact_id": 1,
                "id": 1,
                "immutable": false,
                "name": "latest",
                "pull_time": "2023-03-27T02:02:01.703Z",
                "push_time": "2023-03-27T01:55:14.642Z",
                "repository_id": 1,
                "signed": false
            }
        ],
        "type": "IMAGE",
        "accessories": null
    }
}
```

### アーティファクトを削除する

アーティファクトを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### アーティファクトタグリスト表示

アーティファクトタグリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tags | Body | Array | O | アーティファクトタグリスト |
| tags.id | Body | Integer | O | アーティファクトタグID |
| tags.name | Body | String | O | アーティファクトタグ名 |
| tags.pull\_time | Body | String | O | アーティファクトタグpull時間 |
| tags.push\_time | Body | String | O | アーティファクトタグpush時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "tags": [
        {
            "artifact_id": 1,
            "id": 1,
            "immutable": false,
            "name": "latest",
            "pull_time": "2023-03-27T02:02:01.703Z",
            "push_time": "2023-03-27T01:55:14.642Z",
            "repository_id": 1,
            "signed": false
        }
    ]
}
```

### アーティファクトタグを作成する

アーティファクトタグを作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |
| name | Body | String | O | アーティファクトタグ名 |

例

```json
{
    "name": "test"
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### アーティファクトタグを削除する

アーティファクトタグを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags/{tagName}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |
| tagName | URL | String | O | アーティファクトタグ名 |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### アーティファクト脆弱性情報を表示

アーティファクト脆弱性情報を照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/additions/vulnerabilities
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| vulnerability | Body | Object | O | 脆弱性情報 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "vulnerability": {
        "application/vnd.security.vulnerability.report; version=1.1": {
            "generated_at": "2023-03-30T00:00:17.415030483Z",
            "scanner": {
                "name": "Trivy",
                "vendor": "Aqua Security",
                "version": "v0.35.0"
            },
            "severity": "Unknown",
            "vulnerabilities": [{
                    "id": "CVE-2011-3374",
                    "package": "apt",
                    "version": "2.2.4",
                    "fix_version": "",
                    "severity": "Low",
                    "description": "It was found that apt-key in apt, all versions, do not correctly validate gpg keys with the master keyring, leading to a potential man-in-the-middle attack.",
                    "links": [
                        "https://avd.aquasec.com/nvd/cve-2011-3374"
                    ],
                    "artifact_digests": [
                        "sha256:d77ece68485895479a344efa4934f80ad07a2814c3473d8580d3f31b35a615df"
                    ],
                    "preferred_cvss": {
                        "score_v3": null,
                        "score_v2": null,
                        "vector_v3": "",
                        "vector_v2": ""
                    },
                    "cwe_ids": [
                        "CWE-347"
                    ],
                    "vendor_attributes": {
                        "CVSS": {
                            "nvd": {
                                "V2Score": 4.3,
                                "V2Vector": "AV:N/AC:M/Au:N/C:N/I:P/A:N",
                                "V3Score": 3.7,
                                "V3Vector": "CVSS:3.1/AV:N/AC:H/PR:N/UI:N/S:U/C:N/I:L/A:N"
                            }
                        }
                    }
                }
            ]
        }
    }
}
```

### アーティファクトをスキャンする

アーティファクトをスキャンします。

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/scan
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| registryName | URL | String | O | レジストリ名 |
| imageName | URL | String | O | コンテナイメージ名 |
| reference | URL | String | O | アーティファクト名 |

#### レスポンス

このAPIは共通情報のみレスポンスします。

## コンテナイメージを複製

### 複製リスト表示

複製リストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/replications/policies
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| policies | Body | Array | O | 複製リスト |
| policies.filters | Body | Object List | O | 複製対象設定情報 |
| policies.filters.type | Body | String | O | 複製対象タイプ |
| policies.filters.value | Body | String | O | 複製対象フィルタ値 |
| policies.dest\_registry | Body | Object | O | 対象レジストリ情報 |
| policies.dest\_registsry.name | Body | String | O | 対象リージョン名 |
| policies.dest\_registsry.id | Body | Integer | X | 対象リージョンID、値が存在する場合はPush複製 |
| policies.creation\_time | Body | String | O | 作成時間 |
| policies.src\_registry | Body | Object | O | ソースレジストリ情報 |
| policies.src\_registry.name | Body | String | O | ソースリージョン名 |
| policies.src\_registry.id | Body | Integer | X | ソースリージョンID、値が存在する場合はPull複製 |
| policies.trigger | Body | Object | O | 複製実行方式 |
| policies.trigger.type | Body | String | O | manual(設定しない)/scheduled(ユーザー設定)/event\_based(イベントベース) |
| policies.trigger.trigger\_settings | Body | Object | X | 複製実行周期、複製実行方式がscheduledの場合は必須 |
| policies.trigger.trigger\_settings.cron | Body | String | X | 複製実行周期(Unix cron式使用) |
| policies.dest\_namespace | Body | String | X | 対象レジストリ |
| policies.id | Body | Integer | O | 複製ID |
| policies.enabled | Body | Boolean | O | 複製が有効かどうか |
| policies.name | Body | String | O | 複製名 |
| policies.dest\_project\_id | Body | String | O | 複製対象プロジェクト |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "policies": [
        {
            "update_time": "2023-03-27T23:59:49.381Z",
            "dest_namespace_replace_count": 1,
            "filters": [
                {
                    "type": "resource",
                    "value": "image"
                },
                {
                    "type": "name",
                    "value": "**"
                },
                {
                    "type": "tag",
                    "value": "image"
                }
            ],
            "dest_registry": {
                "status": "healthy",
                "credential": {
                    "access_key": "admin",
                    "access_secret": "*****",
                    "type": "basic"
                },
                "update_time": "2023-03-27T23:59:49.378Z",
                "name": "KR2",
                "url": "https://bddcecbd-alp-kr2-registry.container.cloud.toastoven.net",
                "insecure": true,
                "creation_time": "2023-03-14T02:41:04.893Z",
                "type": "harbor",
                "id": 1,
                "description": "replication"
            },
            "creation_time": "2023-03-27T23:59:49.112Z",
            "src_registry": {
                "status": "healthy",
                "credential": {
                    "access_secret": "*****",
                    "type": "secret"
                },
                "update_time": "",
                "name": "Local",
                "url": "http://harbor-63ae9964-core:80",
                "insecure": true,
                "creation_time": "0001-01-01T00:00:00Z",
                "type": "harbor"
            },
            "trigger": {
                "type": "event_based",
                "trigger_settings": {}
            },
            "id": 3,
            "enabled": true,
            "name": "test",
            "dest_project_id": "8x5SEWjM"
        }
    ]
}
```

### 複製表示

複製を照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| policyId | URL | String | O | 複製ID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| policy | Body | Object | O | 複製情報 |
| policy.filters | Body | Object List | O | 複製対象設定情報 |
| policy.filters.type | Body | String | O | 複製対象タイプ |
| policy.filters.value | Body | String | O | 複製対象フィルタ値 |
| policy.dest\_registry | Body | Object | O | 対象レジストリ情報 |
| policy.dest\_registsry.name | Body | String | O | 対象リージョン名 |
| policy.dest\_registsry.id | Body | Integer | X | 対象リージョンID、値が存在する場合はPush複製 |
| policy.creation\_time | Body | String | O | 作成時間 |
| policy.src\_registry | Body | Object | O | ソースレジストリ情報 |
| policy.src\_registry.name | Body | String | O | ソースリージョン名 |
| policy.src\_registry.id | Body | Integer | X | ソースリージョンID、値が存在する場合はPull複製 |
| policy.trigger | Body | Object | O | 複製実行方式 |
| policy.trigger.type | Body | String | O | manual(設定しない)/scheduled(ユーザー設定)/event\_based(イベントベース) |
| policy.trigger.trigger\_settings | Body | Object | X | 複製実行周期、複製実行方式がscheduledの場合は必須 |
| policy.trigger.trigger\_settings.cron | Body | String | X | 複製実行周期(Unix cron式使用) |
| policy.dest\_namespace | Body | String | X | 対象レジストリ |
| policy.id | Body | Integer | O | 複製ID |
| policy.enabled | Body | Boolean | O | 複製が有効かどうか |
| policy.name | Body | String | O | 複製名 |
| policy.dest\_project\_id | Body | String | O | 複製対象プロジェクト |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "policy": {
        "update_time": "2023-03-30T02:34:23.501Z",
        "dest_namespace_replace_count": 1,
        "filters": [
            {
                "type": "resource",
                "value": "image"
            },
            {
                "type": "name",
                "value": "**"
            },
            {
                "type": "tag",
                "value": "image"
            }
        ],
        "dest_registry": {
            "status": "healthy",
            "credential": {
                "access_key": "admin",
                "access_secret": "*****",
                "type": "basic"
            },
            "update_time": "2023-03-30T02:34:23.496Z",
            "name": "KR2",
            "url": "https://bddcecbd-alp-kr2-registry.container.cloud.toastoven.net",
            "insecure": true,
            "creation_time": "2023-03-14T02:41:04.893Z",
            "type": "harbor",
            "id": 1,
            "description": "replication"
        },
        "creation_time": "2023-03-30T02:34:23.154Z",
        "src_registry": {
            "status": "healthy",
            "credential": {
                "access_secret": "*****",
                "type": "secret"
            },
            "update_time": "",
            "name": "Local",
            "url": "http://harbor-63ae9964-core:80",
            "insecure": true,
            "creation_time": "0001-01-01T00:00:00Z",
            "type": "harbor"
        },
        "trigger": {
            "type": "event_based",
            "trigger_settings": {}
        },
        "id": 12,
        "enabled": true,
        "name": "test",
        "dest_project_id": "8x5SEWjM"
    }
}
```

### 複製を作成する

複製を作成します。

```
POST /ncr/v2.0/appkeys/{appKey}/replications/policies
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| dest\_registry | Body | Object | X | Push複製の対象レジストリ情報 |
| dest\_registry.name | Body | String | O | Push複製の対象リージョン名、KR2 |
| dest\_registry.id | Body | Integer | O | Push複製の対象リージョンID、1 |
| src\_registry | Body | Object | X | Pull複製の対象レジストリ情報 |
| src\_registry.name | Body | String | O | Pull複製の対象リージョン名、KR2 |
| src\_registry.id | Body | Integer | O | Pull複製の対象リージョンID, 1 |
| trigger | Body | Object | O | 複製実行方式 |
| trigger.type | Body | String | O | Push複製、manual(設定しない)/scheduled(ユーザー設定)/event\_based(イベントベース)<br>Pull複製、manual/scheduled |
| trigger.trigger\_settings | Body | Object | X | 複製実行周期、複製実行方式がscheduledの場合は必須設定 |
| trigger.trigger\_settings.cron | Body | String | X | 複製実行周期設定(Unix cron式使用) |
| dest\_namespace | Body | String | X | 対象レジストリ |
| filters | Body | Object List | X | 複製する対象設定情報、全体を対象にする場合は設定しない |
| filters.type | Body | String | X | 複製する対象設定、name(イメージ名)/tag(タグ名) |
| filters.value | Body | String | X | フィルタリングする値 |
| filters.decoration | Body | String | X | フィルタタイプがtagの場合は設定、matches(該当する)/excludes(除外した) |
| name | Body | String | O | 複製名 |
| dest\_project\_id | Body | String | X | 複製対象プロジェクト |

例

```json
{
    "name": "test",
    "dest_registry": {
        "name": "KR2",
        "id":1
    },
    "trigger": {
        "type":"event_based"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### 複製を削除する

複製を削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| policyId | URL | String | O | 複製ID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### 複製を変更する

複製を変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| policyId | URL | String | O | 複製ID |
| dest\_registry | Body | Object | X | Push複製の対象レジストリ情報 |
| dest\_registry.name | Body | String | X | Push複製の対象リージョン名、KR2 |
| dest\_registry.id | Body | Integer | X | Push複製の対象リージョンID, 1 |
| src\_registry | Body | Object | X | Pull複製の対象レジストリ情報 |
| src\_registry.name | Body | String | X | Pull複製の対象リージョン名、KR2 |
| src\_registry.id | Body | Integer | X | Pull複製の対象リージョンID、1 |
| trigger | Body | Object | O | 複製実行方式 |
| trigger.type | Body | String | O | Push複製、manual(設定しない)/scheduled(ユーザー設定)/event\_based(イベントベース)<br>Pull複製、manual/scheduled |
| trigger.trigger\_settings | Body | Object | 設定されている場合は必須 | 複製実行周期、複製実行方式がscheduledの場合は必須設定 |
| trigger.trigger\_settings.cron | Body | String | 設定されている場合は必須 | 複製実行周期設定(Unix cron式使用) |
| dest\_namespace | Body | String | 設定されている場合は必須 | 対象レジストリ |
| filters | Body | Object List | 設定されている場合は必須 | 複製する対象設定情報、全体を対象にする場合は設定しない |
| filters.type | Body | String | 設定されている場合は必須 | 複製する対象設定、name(イメージ名)/tag(タグ名) |
| filters.value | Body | String | 設定されている場合は必須 | フィルタリングする値 |
| filters.decoration | Body | String | 設定されている場合は必須 | フィルタタイプがtagの場合は設定、matches(該当する)/excludes(除外した) |
| name | Body | String | O | 複製名 |
| dest\_project\_id | Body | String | X | 複製対象プロジェクト |

例

```json
{
    "name": "test",
    "dest_registry": {
        "name": "KR2",
        "id":1
    },
    "trigger": {
        "type":"event_based"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### 複製ヒストリーリスト表示

複製ヒストリーリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| executions | Body | Array | O | 複製ヒストリーリスト |
| executions.status | Body | String | O | 複製状態 |
| executions.status\_text | Body | String | O | 複製状態 |
| executions.trigger | Body | String | O | 複製実行方式 |
| executions.start\_time | Body | String | O | 複製開始時間 |
| executions.end\_time | Body | String | O | 複製終了時間 |
| executions.id | Body | Integer | O | 複製ヒストリーID |
| executions.policy\_id | Body | Integer | O | 複製ID |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "executions": [
        {
            "status": "Succeed",
            "status_text": "no resources need to be replicated",
            "trigger": "scheduled",
            "start_time": "2023-03-29T09:39:59.09Z",
            "end_time": "2023-03-29T09:39:59.373Z",
            "id": 845,
            "policy_id": 9
        }
    ]
}
```

### 複製ヒストリー表示

複製ヒストリーを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions/{executionId}/tasks
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| executionId | URL | String | O | 複製ヒストリーID |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| tasks | Body | Array | O | 複製ヒストリー詳細情報 |
| tasks.status | Body | String | O | 複製状態 |
| tasks.start\_time | Body | String | O | 複製開始時間 |
| tasks.dst\_resource | Body | String | O | 対象リソース |
| tasks.src\_resource | Body | String | O | ソースリソース |
| tasks.id | Body | Integer | O | Task ID |
| tasks.execution\_id | Body | Integer | O | 複製ヒストリーID |
| tasks.end\_time | Body | String | O | 複製終了時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "tasks": [
        {
            "status": "Succeed",
            "job_id": "e71b392a391027ced941c4bd",
            "start_time": "2023-03-30T08:25:21Z",
            "dst_resource": "hy/nginx [1 item(s) in total]",
            "src_resource": "hy/nginx [1 item(s) in total]",
            "resource_type": "artifact",
            "operation": "copy",
            "id": 430,
            "execution_id": 894,
            "end_time": "2023-03-30T08:25:46Z"
        }
    ]
}
```

### 複製ヒストリーログ表示

複製ヒストリーログを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions/{executionId}/tasks/{taskId}/log
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| executionId | URL | String | O | 複製ヒストリーID |
| taskId | URL | String | O | 複製ヒストリーtask ID |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| log | Body | String | O | 複製ヒストリーログ |

### 複製を実行する

複製を実行します。

```
POST /ncr/v2.0/appkeys/{appKey}/replications/executions
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| policy\_id | Body | Integer | O | 複製ID |

例

```json
{
    "policy_id": 15
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

## イメージキャッシュ

### イメージキャッシュリスト表示

イメージキャッシュリストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/endpoints
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| page | Query | Integer | X | 照会するページ番号 |
| page\_size | Query | Integer | X | 照会するページサイズ(default: 10) |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| endpoints | Body | Array | O | イメージキャッシュリスト |
| endpoints.status | Body | String | O | イメージキャッシュ状態 |
| endpoints.credential | Body | Object | O | ソースレジストリ認証情報 |
| endpoints.credential.access\_key | Body | String | O | ソースレジストリaccess ID |
| endpoints.credential.access\_secret | Body | String | O | ソースレジストリaccess secret |
| endpoints.update\_time | Body | String | O | 変更時間 |
| endpoints.name | Body | String | O | イメージキャッシュ名 |
| endpoints.url | Body | String | O | ソースレジストリアドレス |
| endpoints.insecure | Body | Boolean | O | 証明書検証有無 |
| endpoints.creation\_time | Body | String | O | 作成時間 |
| endpoints.type | Body | String | O | ソースレジストリタイプ |
| endpoints.id | Body | Integer | O | イメージキャッシュID |
| endpoints.description | Body | Integer | O | イメージキャッシュの説明 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "endpoints": [
        {
            "status": "healthy",
            "credential": {
                "access_key": "hnk8WpQl8uYGGzDiXVAb",
                "access_secret": "*****",
                "type": "basic"
            },
            "update_time": "2023-03-28T01:59:42.025Z",
            "name": "cache",
            "url": "https://095679f7-kr1-registry.container.nhncloud.com",
            "insecure": false,
            "creation_time": "2023-03-28T01:59:42.025Z",
            "type": "ncr",
            "id": 3,
            "description": "proxycache/ncr"
        }
    ]
}
```

### イメージキャッシュを作成する

イメージキャッシュを作成します

```
POST /ncr/v2.0/appkeys/{appKey}/endpoints
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| name | Body | String | O | イメージキャッシュ名 |
| url | Body | String | O | ソースレジストリアドレス |
| type | Body | String | O | ソースレジストリタイプ、harbor/docker-hub/docker-registry/google-gcr/aws-ecr/azure-acr/quay/ncr |
| credential | Body | Object | O | ソースレジストリ認証情報 |
| credential.access\_key | Body | String | O | ソースレジストリaccess ID |
| credential.access\_secret | Body | String | O | ソースレジストリaccess secret |
| insecure | Body | Boolean | X | 証明書検証有無 |

例

```json
{
    "name": "test",
    "url": "https://095679f7-kr1-registry.container.nhncloud.com",
    "type": "ncr",
    "credential": {
        "access_key": "hnk8WpQl8uYGGzDiXVAb",
        "access_secret": "EL3vl7gPWeaEqwGs"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージキャッシュを削除する

イメージキャッシュを削除します。

```
DELETE /ncr/v2.0/appkeys/{appKey}/endpoints/{endpointId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| endpointId | URL | String | O | イメージキャッシュID |

#### レスポンス

このAPIは共通情報のみレスポンスします。

### イメージキャッシュを変更する

イメージキャッシュを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/endpoints/{endpointId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| endpointId | URL | String | O | イメージキャッシュID |
| name | Body | String | X | イメージキャッシュ名 |
| url | Body | String | X | ソースレジストリアドレス |
| access\_key | Body | String | X | ソースレジストリaccess ID |
| access\_secret | Body | String | X | ソースレジストリaccess secret |
| insecure | Body | Boolean | X | 証明書検証有無 |

例

```json
{
    "name": "test",
    "url": "https://095679f7-kr1-registry.container.nhncloud.com",
    "access_key": "hnk8WpQl8uYGGzDiXVAb",
    "access_secret": "EL3vl7gPWeaEqwGs"
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

## スキャン設定

### スキャン周期表示

スキャン周期を照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/scanAll/schedule
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| schedule | Body | Object | O | スキャン周期情報 |
| schedule.id | Body | Integer | O | スキャン周期ID |
| schedule.schedule | Body | Object | O | スキャン周期 |
| schedule.schedule.cron | Body | String | O | スキャン周期のcron形式 |
| schedule.schedule.type | Body | String | O | スキャン周期タイプ |
| schedule.status | Body | String | O | 状態 |
| schedule.creation\_time | Body | String | O | 作成時間 |
| schedule.update\_time | Body | String | O | 変更時間 |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "schedule": {
        "id": 5,
        "schedule": {
            "cron": "0 0 * * *",
            "type": "Daily"
        },
        "status": "Running",
        "creation_time": "2023-03-28T05:49:33.364Z",
        "update_time": "2023-03-28T05:49:33.364Z"
    }
}
```

### スキャン周期を設定する

スキャン周期を設定します。

```
PUT /ncr/v2.0/appkeys/{appKey}/scanAll/schedule
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| schedule | Body | Object | O | スキャン周期情報 |
| schedule.cron | Body | String | O | スキャン周期のcron形式 |
| schedule.type | Body | String | O | スキャン周期タイプ |

例

```json
{
    "schedule": {
        "cron": "0 0 * * *",
        "type": "Daily"
    }
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。

### 共通CVE許可リスト表示

共通CVE許可リストを照会します。

```
GET /ncr/v2.0/appkeys/{appKey}/CVEAllowlist
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |

#### レスポンス

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| cve\_allowlist | Body | Object | O | 許可リスト情報 |
| cve\_allowlist.id | Body | Integer | O | 許可リストID |
| cve\_allowlist.items | Body | Object List | O | 許可リスト |
| cve\_allowlist.items.cve\_id | Body | String | X | CVE ID |

例

```json
{
    "header": {
        "resultCode": 200,
        "resultMessage": "SUCCESS",
        "isSuccessful": true
    },
    "cve_allowlist": {
        "id": 14,
        "project_id": 0,
        "items": [
            {
                "cve_id": "CVE-2011-3374"
            }
        ]
    }
}
```

### 共通CVE許可リストを変更する

共通CVE許可リストを変更します。

```
PUT /ncr/v2.0/appkeys/{appKey}/CVEAllowlist
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | サービスAppkey |
| items | Body | Object List | O | 許可リスト |
| items.cve\_id | Body | String | O | CVE ID |

例

```json
{
    "items": [{
        "cve_id": "CVE-2011-3374"
    }]
}
```

#### レスポンス

このAPIは共通情報のみレスポンスします。
