## Container > NHN Container Registry(NCR) > API Guide

This document explains the APIs required for configuring container registries.
To use the APIs, you need a User Access Key and Secret Access Key. User Access Key and Secret Access Key are created on the Account > API Security Settings page of NHN Cloud Console.

### API Common Request Information

The service Appkey is required to use the API. The service Appkey is located in the <strong>URL and Appkey</strong> menu on the top of the console.
The API domain is as follows.

| Region | Domain |
| --- | --- |
| Korea (Pangyo) region | https://kr1-ncr.api.nhncloudservice.com |
| Korea (Pyeongchon) region | https://kr2-ncr.api.nhncloudservice.com |

The API headers are as follows.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| X-TC-AUTHENTICATION-ID | Header | String | O | User Access Key |
| X-TC-AUTHENTICATION-SECRET | Header | String | X (Required when created) | Secret Access Key |

### Common API Information

Returns <strong>200 OK</strong> for all API requests. For more information on the response results, see Response Body Header.

| Name | Type | Format | Description |
| --- | --- | --- | --- |
| header | Body | Object |  |
| header.isSuccessful | Body | Boolean | true: Normal<br>false: Error |
| header.resultCode | Body | Integer | 200: Normal<br>10000 or higher: Error |
| header.resultMessage | Body | String | "SUCCESS:" Normal<br>Others: Error cause message |

> [Caution] API responses may contain fields not described in this document. Those fields are for NHN Cloud internal usage and as such refrain from using them since they may be changed without prior notice.<br>
> [Caution] Option fields not entered when creating a resource may not appear in the response body when queried.

## Container Registry

### View Container Registries

List container registries.

```
GET /ncr/v2.0/appkeys/{appKey}/registries
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| registries | Body | Array | O | Container Registry List |
| registries.project_id | Body | Integer | O | Container registry ID |
| registries.name | Body | String | O | Container registry name |
| registries.creation_time | Body | String | O | Created time |
| registries.update_time | Body | String | O | Update time |
| registries.repo_count | Body | Integer | O | Number of images in container registry |
| registries.metadata | Body | Object map[string]string | O | Container registry settings |
| registries.metadata.auto_scan | Body | String | O | Whether to automatically scan image when pushing: true/false |
| registries.metadata.severity | Body | String | X | Vulnerability degree: critical/medium/high/low/none |
| registries.metadata.prevent_vul | Body | String | O | Whether or not to set pull prevention according to the vulnerability severity of the image: true/false |
| registries.metadata.reuse_sys_cve_allowlist | Body | String | O | common CVE allow list used or not: true/false |
| registries.metadata.enable_content_trust_cosign | Body | String | X | Prevent pulling unauthenticated images or not: true/false |
| registries.cve_allowlist | Body | Object | O | Vulnerability allow list |
| registries.cve_allowlist.id | Body | Integer | O | Vulnerability allow list ID |
| registries.cve_allowlist.items | Body | Object List | O | CVE list |
| registries.cve_allowlist.items.cve_id | Body | String | X | CVE ID |
| registries.registry_id | Body | Integer | O | Image cache type if non-zero, normal type if zero |
| registries.usage | Body | Integer | O | Container registry usage |
| registries.uri | Body | String | O | Container registry access URI |
| registries.private_uri | Body | String | O | Container registry access private URI |

Example

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
                "severity": "critical",
                "enable_content_trust_cosign": "true"
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

### View Container Registry

List container registry information.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Set the value of X-Is-Resource-Name to true if the registry name or ID or name is only numeric. |
| X-Is-Resource-Name | Header | String | X | Whether or not the value of registryNameOrId is a name, true/false., true/false |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| registry | Body | Object | O | Container registry information |
| registry.project_id | Body | Integer | O | Container registry ID |
| registry.name | Body | String | O | Container registry name |
| registry.creation_time | Body | String | O | Created time |
| registry.update_time | Body | String | O | Update time |
| registry.repo_count | Body | Integer | O | Number of images in container registry |
| registry.metadata | Body | Object map[string]string | O | Container registry settings |
| registry.metadata.auto_scan | Body | String | O | Whether to automatically scan image when pushing: true/false |
| registry.metadata.severity | Body | String | X | Vulnerability degree: critical/medium/high/low/none |
| registry.metadata.prevent_vul | Body | String | O | Whether or not to set pull prevention according to the vulnerability severity of the image: true/false |
| registry.metadata.reuse_sys_cve_allowlist | Body | String | O | common CVE allow list used or not: true/false |
| registries.metadata.enable_content_trust_cosign | Body | String | X | Prevent pulling unauthenticated images or not: true/false |
| registry.cve_allowlist | Body | Object | O | Vulnerability allow list |
| registry.cve_allowlist.id | Body | Integer | O | Vulnerability allow list ID |
| registry.cve_allowlist.items | Body | Object List | O | CVE list |
| registry.cve_allowlist.items.cve_id | Body | String | X | CVE ID |
| registry.registry_id | Body | Integer | O | Image cache type if non-zero, normal type if zero |
| registry.usage | Body | Integer | O | Container registry usage |
| registry.uri | Body | String | O | Container registry access URI |
| registry.private_uri | Body | String | O | Container registry access private URI |

Example

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
            "severity": "critical",
            "enable_content_trust_cosign": "true"
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

### Create a container registry

Create a container registry.

```
POST /ncr/v2.0/appkeys/{appKey}/registries
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| project_name | Body | String | O | Container registry name |
| metadata | Body | Object map[string]string | X | Container registry settings |
| metadata.auto_scan | Body | String | X | Whether to automatically scan image when pushing: true/false |
| metadata.severity | Body | String | X | Vulnerability degree: critical/medium/high/low/none |
| metadata.prevent_vul | Body | String | X | Whether or not to set pull prevention according to the vulnerability severity of the image: true/false |
| metadata.reuse_sys_cve_allowlist | Body | String | X | common CVE allow list used or not: true/false |
| registries.metadata.enable_content_trust_cosign | Body | String | X | Prevent pulling unauthenticated images or not: true/false |
| registry_id | Body | Integer | X | Image cache ID required when creating a registry of image cache types |

Example

```json
{
    "project_name": "registry"
}
```

#### Response

This API responds with common information.

### Delete a container registry

Deletes a container registry.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Set the value of X-Is-Resource-Name to true if the registry name or ID or name is only numeric. |
| X-Is-Resource-Name | Header | String | X | Whether or not the value of registryNameOrId is a name, true/false., true/false |

#### Response

This API responds with common information.

### Change a container registry

Change a container registry.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Set the value of X-Is-Resource-Name to true if the registry name or ID or name is only numeric. |
| X-Is-Resource-Name | Header | String | X | Whether or not the value of registryNameOrId is a name, true/false., true/false |
| metadata | Body | Object map[string]string | X | Container registry settings |
| metadata.auto_scan | Body | String | X | Whether to automatically scan image when pushing: true/false |
| metadata.severity | Body | String | X | Vulnerability degree: critical/medium/high/low/none |
| metadata.prevent_vul | Body | String | X | Whether or not to set pull prevention according to the vulnerability severity of the image: true/false |
| metadata.reuse_sys_cve_allowlist | Body | String | X | common CVE allow list used or not: true/false |
| registries.metadata.enable_content_trust_cosign | Body | String | X | Prevent pulling unauthenticated images or not: true/false |
| cve_allowlist | Body | Object | Required when set | Vulnerability allow list |
| cve_allowlist.items | Body | Object List | Required when set | CVE list |
| cve_allowlist.items.cve_id | Body | String | Required when set | CVE ID |

Example

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

#### Response

This API responds with common information.

### View Image Protection Policies

Retrieves image protection policies.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| immutable_tag_rules | Body | Array | O | Image protection policy list |
| immutable_tag_rules.id | Body | Integer | O | Image protection policy ID |
| immutable_tag_rules.disabled | Body | Boolean | O | Image protection policy activated or not |
| immutable_tag_rules.scope | Body | Object | O | Image protection policy information |
| immutable_tag_rules.scope.include | Body | Boolean | O | Image protection policy is set or not |
| immutable_tag_rules.scope.pattern | Body | String | O | Image to be protected |
| immutable_tag_rules.tag | Body | Object | O | Tag protection policy information |
| immutable_tag_rules.tag.include | Body | Boolean | O | Tag protection policy is set or not |
| immutable_tag_rules.tag.pattern | Body | String | O | Tag to be protected |

Example

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

### Create an Image protection policy

Creates an Image protection policy.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| scope | Body | Object | X | Image protection policy information |
| scope.include | Body | Boolean | X | Image protection policy is set or not |
| scope.pattern | Body | String | X | Image to be protected<br>For overall images :enter \**  |
| tag | Body | Object | X | Tag protection policy information |
| tag.include | Body | Boolean | X | Tag protection policy is set or not |
| tag.pattern | Body | String | X | Tag to be protected<br>For overall tags :enter \*\* |

Example

```json
{
    "scope": {
        "include": true,
        "pattern": "test"
    }
}
```

#### Response

This API responds with common information.

### Delete an image protection policy

Deletes an image protection policy.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| rule_id | URL | String | O | Image protection policy ID |

#### Response

This API responds with common information.

### Change an image protection policy

Change an image protection policy.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| rule_id | URL | String | O | Image protection policy ID |
| disabled | Body | Boolean | X | Image protection policy activated or not |
| scope | Body | Object | O | Image protection policy information |
| scope.include | Body | Boolean | O | Image protection policy is set or not |
| scope.pattern | Body | String | O | Image to be protected |
| tag | Body | Object | O | Tag protection policy information |
| tag.include | Body | Boolean | O | Tag protection policy is set or not |
| tag.pattern | Body | String | O | Tag to be protected |

Example

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

#### Response

This API responds with common information.

### View image cleanup policies

List image cleanup policies

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| retention_rules | Body | Array | O | Image cleanup policy list |
| retention_rules.id | Body | Integer | O | Image cleanup policy ID |
| retention_rules.disabled | Body | Boolean | O | Image cleanup policy activated or not |
| retention_rules.parameters | Body | Object map | O | Cleanup policy settings |
| retention_rules.parameters.always | Body | Integer | X | Always retain only n artifacts |
| retention_rules.parameters.latest_pulled_n_images | Body | Integer | X | Only retain the last n artifacts pulled |
| retention_rules.parameters.latest_pushed_n_images | Body | Integer | X | Only retain the last n artifacts pushed |
| retention_rules.parameters.n_days_since_last_pull | Body | Integer | X | Only retain artifacts pushed n days ago |
| retention_rules.parameters.n_days_since_last_push | Body | Integer | X | Only retain artifacts pulled n days ago |
| retention_rules.tag | Body | Object | O | Tag cleanup policy information |
| retention_rules.tag.include | Body | Boolean | O | Tag cleanup policy is set or not |
| retention_rules.tag.pattern | Body | String | O | Tags to be cleaned up |
| retention_rules.tag.untagged | Body | Boolean | O | Whether or not to include untagged artifacts |
| retention_rules.scope | Body | Object | O | Image cleanup policy information |
| retention_rules.scope.include | Body | Boolean | O | Image cleanup policy is set or not |
| retention_rules.scope.pattern | Body | String | O | Images to be cleaned up |

Example

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

### Create an Image cleanup policy

Create an Image cleanup policy.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| tag | Body | Object | X | Tag cleanup policy information |
| tag.include | Body | Boolean | X | Tag cleanup policy is set or not |
| tag.pattern | Body | String | X | Tags to be cleaned up<br>For overall tags :enter \*\* |
| tag.untagged | Body | Boolean | X | Whether or not to include untagged artifacts |
| scope | Body | Object | X | Image cleanup policy information |
| scope.include | Body | Boolean | X | Image cleanup policy is set or not |
| scope.pattern | Body | String | X | Images to be cleaned up<br>For overall images :enter \**  |
| disabled | Body | Boolean | X | Image cleanup policy activated or not<br>Set to enabled (false) if not provided |
| parameters | Body | Object map | O | Cleanup policy settings |
| parameters.always | Body | Integer | X | Always retain only n artifacts |
| parameters.latest_pulled_n_images | Body | Integer | X | Only retain the last n artifacts pulled |
| parameters.latest_pushed_n_images | Body | Integer | X | Only retain the last n artifacts pushed |
| parameters.n_days_since_last_pull | Body | Integer | X | Only retain artifacts pushed n days ago |
| parameters.n_days_since_last_push | Body | Integer | X | Only retain artifacts pulled n days ago |

Example

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

#### Response

This API responds with common information.

### Delete an image cleanup policy

Deletes an image protection policy.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| id | Query | Integer | O | Image cleanup policy ID |

#### Response

This API responds with common information.

### Change an image cleanup policy

Change an image protection policy.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/{rule_id}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| rule_id | URL | String | O | Image cleanup policy ID |
| disabled | Body | Boolean | X | Image cleanup policy activated or not |
| tag | Body | Object | O | Tag cleanup policy information |
| tag.include | Body | Boolean | O | Tag cleanup policy is set or not |
| tag.pattern | Body | String | O | Tags to be cleaned up |
| tag.untagged | Body | Boolean | O | Whether or not to include untagged artifacts |
| scope | Body | Object | O | Image cleanup policy information |
| scope.include | Body | Boolean | O | Image cleanup policy is set or not |
| scope.pattern | Body | String | O | Images to be cleaned up |
| parameters | Body | Object map | O | Cleanup policy settings |
| parameters.always | Body | Integer | Required when set | Always retain only n artifacts |
| parameters.latest_pulled_n_images | Body | Integer | Required when set | Only retain the last n artifacts pulled |
| parameters.latest_pushed_n_images | Body | Integer | Required when set | Only retain the last n artifacts pushed |
| parameters.n_days_since_last_pull | Body | Integer | Required when set | Only retain artifacts pushed n days ago |
| parameters.n_days_since_last_push | Body | Integer | Required when set | Only retain artifacts pulled n days ago |

Example

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

#### Response

This API responds with common information.

### View image cleanup histories

List image cleanup histories.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| executions | Body | Array | O | Image cleanup history list |
| executions.id | Body | Integer | O | Image cleanup history ID |
| executions.dry_run | Body | Boolean | O | Whether or not to execute test |
| executions.trigger | Body | String | O | Image cleanup execution method |
| executions.start_time | Body | String | O | Start time of image cleanup |
| executions.end_time | Body | String | O | End time of image cleanup |
| executions.status | Body | String | O | Status of image cleanup |

Example

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

### View Image Cleanup History Details

Retrieves image cleanup history details.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| executionId | URL | String | O | Image cleanup history ID |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tasks | Body | Array | O | Task list |
| tasks.id | Body | Integer | O | Task ID |
| tasks.repository | Body | String | O | Names of the cleaned-up images |
| tasks.retained | Body | Integer | O | Image protection is set or not |
| tasks.total | Body | Integer | O | Number of images cleaned up |
| tasks.start_time | Body | String | O | Start time of cleanup |
| tasks.end_time | Body | String | O | End time of cleanup |
| tasks.status | Body | String | O | Task status |

Example

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

### View image cleanup history logs

Retrieve image cleanup history logs.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks/{taskId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| executionId | URL | String | O | Image cleanup history ID |
| taskId | URL | String | O | Image cleanup task ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tasks | Body | String | O | Image cleanup log |

### Execute an image cleanup policy

Execute an image cleanup policy.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| dry_run | Body | Boolean | X | Whether or not to execute test |

#### Response

This API responds with common information.

### View image cleanup cycle

Retrieve image cleanup cycle.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| trigger | Body | Object | O | Image cleanup cycle information |
| trigger.kind | Body | String | O | Image cleanup type |
| trigger.settings | Body | Object | O | Image cleanup cycle settings |
| trigger.settings.cron | Body | String | O | Image cleanup cycle |

Example

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

### Create an Image cleanup cycle

Create an Image cleanup cycle.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryId | URL | String | O | Registry ID |
| settings | Body | Object | O | Image cleanup cycle settings |
| settings.cron | Body | String | O | Image cleanup cycle |

Example

```json
{
    "settings": {
        "cron": "0 0 * * 0"
    }
}
```

#### Response

This API responds with common information.

### View webhooks

List webhooks.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Registry name or ID, if the name only consists of numbers, set the value for X-Is-Resource-Name to true |
| X-Is-Resource-Name | Header | String | X | Whether the value for registryNameOrId is name, true/false |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| policies | Body | Array | O | Webhook list |
| policies.creation_time | Body | String | O | Webhook creation time |
| policies.enabled | Body | Boolean | O | Webhook activated or not |
| policies.event_types | Body | String List | O | Webhook event type, PUSH_ARTIFACT/PULL_ARTIFACT/DELETE_ARTIFACT |
| policies.id | Body | Integer | O | Webhook ID |
| policies.name | Body | String | O | Webhook name |
| policies.project_id | Body | Integer | O | Registry ID |
| policies.targets | Body | Object List | O | Webhook Information |
| policies.targets.address | Body | String | O | Webhook endpoint |
| policies.targets.type | Body | String | O | Webhook notification type, http/slack |
| policies.targets.auth_header | Body | String | X | Authentication header |
| policies.targets.skip_cert_verify | Body | Boolean | X | Certificate is verified or not |
| policies.update_time | Body | String | O | Webhook change time |

Example

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

### View a Webhook

Retrieve a Webhook.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Registry name or ID, if the name only consists of numbers, set the value for X-Is-Resource-Name to true |
| X-Is-Resource-Name | Header | String | X | Whether the value for registryNameOrId is name, true/false |
| policyId | URL | String | O | Webhook ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| policy | Body | Object | O | Webhook Information |
| policy.creation_time | Body | String | O | Webhook creation time |
| policy.enabled | Body | Boolean | O | Webhook activated or not |
| policy.event_types | Body | String List | O | Webhook event type, PUSH_ARTIFACT/PULL_ARTIFACT/DELETE_ARTIFACT |
| policy.id | Body | Integer | O | Webhook ID |
| policy.name | Body | String | O | Webhook name |
| policy.project_id | Body | Integer | O | Registry ID |
| policy.targets | Body | Object List | O | Webhook Information |
| policy.targets.address | Body | String | O | Webhook endpoint |
| policy.targets.type | Body | String | O | Webhook notification type, http/slack |
| policy.targets.auth_header | Body | String | X | Authentication header |
| policy.targets.skip_cert_verify | Body | Boolean | X | Certificate is verified or not |
| policy.update_time | Body | String | O | Webhook change time |

Example

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

### Create a webhook

Create a webhook.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Registry name or ID, if the name only consists of numbers, set the value for X-Is-Resource-Name to true |
| X-Is-Resource-Name | Header | String | X | Whether the value for registryNameOrId is name, true/false |
| enabled | Body | Boolean | X | Webhook activated or not<br>Set to false if not entered |
| event_types | Body | String List | O | Webhook event type, PUSH_ARTIFACT/PULL_ARTIFACT/DELETE_ARTIFACT |
| name | Body | String | X | Webhook name |
| targets | Body | Object List | O | Webhook Information |
| targets.address | Body | String | O | Webhook endpoint |
| targets.type | Body | String | O | Webhook notification type, http/slack |
| targets.auth_header | Body | String | X | Authentication header |
| targets.skip_cert_verify | Body | Boolean | X | Certificate is verified or not |

Example

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

#### Response

This API responds with common information.

### Delete a webhook

Delete a webhook.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Registry name or ID, if the name only consists of numbers, set the value for X-Is-Resource-Name to true |
| X-Is-Resource-Name | Header | String | X | Whether the value for registryNameOrId is name, true/false |
| policyId | URL | String | O | Webhook ID |

#### Response

This API responds with common information.

### Change a webhook

Change a webhook.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}/webhook/policies/{policyId}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryNameOrId | URL | String | O | Registry name or ID, if the name only consists of numbers, set the value for X-Is-Resource-Name to true |
| X-Is-Resource-Name | Header | String | X | Whether the value for registryNameOrId is name, true/false |
| policyId | URL | String | O | Webhook ID |
| enabled | Body | Boolean | X | Webhook activated or not |
| event_types | Body | String List | O | Webhook event type, PUSH_ARTIFACT/PULL_ARTIFACT/DELETE_ARTIFACT |
| name | Body | String | X | Webhook name |
| targets | Body | Object List | O | Webhook Information |
| targets.address | Body | String | O | Webhook endpoint |
| targets.type | Body | String | O | Webhook notification type, http/slack |
| targets.auth_header | Body | String | X | Authentication header |
| targets.skip_cert_verify | Body | Boolean | X | Certificate is verified or not |

Example

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

#### Response

This API responds with common information.

## Container image

### View container images

List container images.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| images | Body | Array | O | Container image list |
| images.id | Body | Integer | O | Container image ID |
| images.name | Body | String | O | Container image name |
| images.project_id | Body | Integer | O | Registry ID |
| images.pull_count | Body | Integer | O | Number of container image pulls |
| images.artifact_count | Body | Integer | O | Number of container image artifacts |
| images.creation_time | Body | String | O | Container image creation time |
| images.update_time | Body | String | O | Container image change time |

Example

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

### View a container Image

Retrieve a container Image.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| image | Body | Object | O | Container image list |
| image.id | Body | Integer | O | Container image ID |
| image.name | Body | String | O | Container image name |
| image.project_id | Body | Integer | O | Registry ID |
| image.pull_count | Body | Integer | O | Number of container image pulls |
| image.artifact_count | Body | Integer | O | Number of container image artifacts |
| image.creation_time | Body | String | O | Container image creation time |
| image.update_time | Body | String | O | Container image change time |

Example

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

### Delete a container image

Delete a container image.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |

#### Response

This API responds with common information.

### View artifacts

List artifacts.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |
| with_scan_overview | Query | String | X | Whether or not to retrieve vulnerability information |
| with_accessory | Query | String | X | Whether or not to retrieve authentication information |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| artifacts | Body | Array | O | Artifact list |
| artifacts.digest | Body | String | O | Artifact digest |
| artifacts.extra_attrs | Body | Object | O | Additional artifact information |
| artifacts.scan_overview | Body | Object | X | Artifact scan information |
| artifacts.accessories | Body | Object | X | Artifact authentication information |
| artifacts.manifest_media_type | Body | String | O | Artifact type |
| artifacts.media_type | Body | String | O | Artifact type |
| artifacts.size | Body | Integer | O | Artifact size |
| artifacts.pull_time | Body | String | O | Artifact pull time |
| artifacts.push_time | Body | String | O | Artifact push time |

Example

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

### View artifacts

Retrieve artifacts.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |
| with_scan_overview | Query | String | X | Whether or not to retrieve vulnerability information |
| with_accessory | Query | String | X | Whether or not to retrieve authentication information |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| artifact | Body | Object | O | Artifact information |
| artifact.digest | Body | String | O | Artifact digest |
| artifact.extra_attrs | Body | Object | O | Additional artifact information |
| artifact.scan_overview | Body | Object | X | Artifact scan information |
| artifact.accessories | Body | Object | X | Artifact authentication information |
| artifact.manifest_media_type | Body | String | O | Artifact type |
| artifact.media_type | Body | String | O | Artifact type |
| artifact.size | Body | Integer | O | Artifact size |
| artifact.pull_time | Body | String | O | Artifact pull time |
| artifact.push_time | Body | String | O | Artifact push time |

Example

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

### Delete an artifact

Deletes an artifact.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |

#### Response

This API responds with common information.

### View artifact tags

List artifact tags.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry Name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tags | Body | Array | O | Artifact tag list |
| tags.id | Body | Integer | O | Artifact tag ID |
| tags.name | Body | String | O | Artifact tag name |
| tags.pull_time | Body | String | O | Artifact tag pull time |
| tags.push_time | Body | String | O | Artifact tag push time |

Example

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

### Create an artifact tag

Create an artifact tag.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |
| name | Body | String | O | Artifact tag name |

Example

```json
{
    "name": "test"
}
```

#### Response

This API responds with common information.

### Delete an artifact tag

Delete an artifact tag.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags/{tagName}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |
| tagName | URL | String | O | Artifact tag name |

#### Response

This API responds with common information.

### View artifact vulnerability information

Retrieve artifact vulnerability information.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/additions/vulnerabilities
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| vulnerability | Body | Object | O | Vulnerability information |

Example

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

### Scan an artifact

Scan an artifact.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/scan
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| registryName | URL | String | O | Registry name |
| imageName | URL | String | O | Container image name |
| reference | URL | String | O | Artifact name |

#### Response

This API responds with common information.

## Replicate a Container Image

### View replications

List replications

```
GET /ncr/v2.0/appkeys/{appKey}/replications/policies
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| policies | Body | Array | O | Replication list |
| policies.filters | Body | Object List | O | Setting information to be replicated |
| policies.filters.type | Body | String | O | Type to be replicated |
| policies.filters.value | Body | String | O | Filter value to be replicated  |
| policies.dest_registry | Body | Object | O | Target registry information |
| policies.dest_registsry.name | Body | String | O | Target region name |
| policies.dest_registsry.id | Body | Integer | X | If the destination region ID and value exist, perform push replication. |
| policies.creation_time | Body | String | O | Created time |
| policies.src_registry | Body | Object | O | Source registry information |
| policies.src_registry.name | Body | String | O | Source region name |
| policies.src_registry.id | Body | Integer | X | If the source region ID and value exist, perform push replication. |
| policies.trigger | Body | Object | O | How replication works |
| policies.trigger.type | Body | String | O | manual (no setting) / scheduled (user setting) / event_based (event_based) |
| policies.trigger.trigger_settings | Body | Object | X | It is required to specify the replication cycle when the replication mode is set to scheduled. |
| policies.trigger.trigger_settings.cron | Body | String | X | Replication cycle (using Unix cron expression) |
| policies.dest_namespace | Body | String | X | Target registry |
| policies.id | Body | Integer | O | Replication ID |
| policies.enabled | Body | Boolean | O | Replication activated or not |
| policies.name | Body | String | O | Replication name |
| policies.dest\_project\_id | Body | String | O | Project to replicate |

Example

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
                    "value": "**"
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

### View a replication

Retrieve a replication.

```
GET /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| policyId | URL | String | O | Replication ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| policy | Body | Object | O | Replication Information |
| policy.filters | Body | Object List | O | Setting information to be replicated |
| policy.filters.type | Body | String | O | Type to be replicated |
| policy.filters.value | Body | String | O | Filter value to be replicated  |
| policy.dest_registry | Body | Object | O | Target registry information |
| policy.dest_registsry.name | Body | String | O | Target region name |
| policy.dest_registsry.id | Body | Integer | X | If the destination region ID and value exist, perform push replication. |
| policy.creation_time | Body | String | O | Created time |
| policy.src_registry | Body | Object | O | Source registry information |
| policy.src_registry.name | Body | String | O | Source region name |
| policy.src_registry.id | Body | Integer | X | If the source region ID and value exist, perform push replication. |
| policy.trigger | Body | Object | O | How replication works |
| policy.trigger.type | Body | String | O | manual (no setting) / scheduled (user setting) / event_based (event_based) |
| policy.trigger.trigger_settings | Body | Object | X | It is required to specify the replication cycle when the replication mode is set to scheduled. |
| policy.trigger.trigger_settings.cron | Body | String | X | Replication cycle (using Unix cron expression) |
| policy.dest_namespace | Body | String | X | Target registry |
| policy.id | Body | Integer | O | Replication ID |
| policy.enabled | Body | Boolean | O | Replication activated or not |
| policy.name | Body | String | O | Replication name |
| policy.dest\_project\_id | Body | String | O | Project to target |
| policies.override | Body | Boolean | X | Override duplicate image or not |

Example

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
                "value": "**"
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
        "dest_project_id": "8x5SEWjM",
        "override": true
    }
}
```

### Create a replication

Create a replication.

```
POST /ncr/v2.0/appkeys/{appKey}/replications/policies
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| dest_registry | Body | Object | X | Registry information for push replication |
| dest_registry.name | Body | String | O | Region name for push replication, KR2 |
| dest_registry.id | Body | Integer | O | Region ID for push replication, 1 |
| src_registry | Body | Object | X | Registry information for pull replication |
| src_registry.name | Body | String | O | Region name for pull replication, KR2 |
| src_registry.id | Body | Integer | O | Region ID for push replication, 1 |
| trigger | Body | Object | O | How replication works |
| trigger.type | Body | String | O | Push replication, manual (no setting)/scheduled (user setting)/event_based (event_based).<br>Pull replication, manual/scheduled |
| trigger.trigger_settings | Body | Object | X | It is required to specify the replication cycle when the replication mode is set to schedule. |
| trigger.trigger_settings.cron | Body | String | X | Set the replication cycle(using Unix cron expression) |
| dest_namespace | Body | String | X | Target registry |
| filters | Body | Object List | X | It is not necessary to set the Information to be replicated for the target if the target is all. |
| filters.type | Body | String | X | Set the target to be replicated: name (image name) and tag (tag name) |
| filters.value | Body | String | X | Value to be filtered |
| filters.decoration | Body | String | X | Setting when the filter type is tag, matches (matching) /excludes (excluding) |
| name | Body | String | O | Replication name |
| dest\_project\_id | Body | String | X | Project to target |
| policy.override | Body | Boolean | X | Override duplicate image or not |

Example

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

#### Response

This API responds with common information.

### Delete an replication

Delete an replication.

```
DELETE /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| policyId | URL | String | O | Replication ID |

#### Response

This API responds with common information.

### Change a replication

Change a replication.

```
PUT /ncr/v2.0/appkeys/{appKey}/replications/policies/{policyId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| policyId | URL | String | O | Replication ID |
| dest_registry | Body | Object | X | Registry information for push replication |
| dest_registry.name | Body | String | X | Region name for push replication, KR2 |
| dest_registry.id | Body | Integer | X | Region ID for push replication, 1 |
| src_registry | Body | Object | X | Registry information for pull replication |
| src_registry.name | Body | String | X | Region name for pull replication, KR2 |
| src_registry.id | Body | Integer | X | Region ID for push replication, 1 |
| trigger | Body | Object | O | How replication works |
| trigger.type | Body | String | O | Push replication, manual (no setting)/scheduled (user setting)/event_based (event_based).<br>Pull replication, manual/scheduled |
| trigger.trigger_settings | Body | Object | Required when set | It is required to specify the replication cycle when the replication mode is set to schedule. |
| trigger.trigger_settings.cron | Body | String | Required when set | Set the replication cycle(using Unix cron expression) |
| dest_namespace | Body | String | Required when set | Target registry |
| filters | Body | Object List | Required when set | It is not necessary to set the Information to be replicated for the target if the target is all. |
| filters.type | Body | String | Required when set | Set the target to be replicated: name (image name) and tag (tag name) |
| filters.value | Body | String | Required when set | Value to be filtered |
| filters.decoration | Body | String | Required when set | Setting when the filter type is tag, matches (matching) /excludes (excluding) |
| name | Body | String | O | Replication name |
| dest\_project\_id | Body | String | X | Project to target |
| policy.override | Body | Boolean | X | Override duplicate image or not |

Example

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

#### Response

This API responds with common information.

### View replication histories

List replication histories.

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| executions | Body | Array | O | Replication history list |
| executions.status | Body | String | O | Replication Status |
| executions.status_text | Body | String | O | Replication Status |
| executions.trigger | Body | String | O | How replication works |
| executions.start_time | Body | String | O | Replication start time |
| executions.end_time | Body | String | O | Replication end time |
| executions.id | Body | Integer | O | Replication history ID |
| executions.policy_id | Body | Integer | O | Replication ID |

Example

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

### View a replication history

Retrieves a replication history.

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions/{executionId}/tasks
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| executionId | URL | String | O | Replication history ID |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| tasks | Body | Array | O | Details of replication history |
| tasks.status | Body | String | O | Replication Status |
| tasks.start_time | Body | String | O | Replication start time |
| tasks.dst_resource | Body | String | O | Target Resource |
| tasks.src_resource | Body | String | O | Source Resource |
| tasks.id | Body | Integer | O | Task ID |
| tasks.execution_id | Body | Integer | O | Replication history ID |
| tasks.end_time | Body | String | O | Replication end time |

Example

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

### View replication history logs

Retrieve replication history logs.

```
GET /ncr/v2.0/appkeys/{appKey}/replications/executions/{executionId}/tasks/{taskId}/log
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| executionId | URL | String | O | Replication history ID |
| taskId | URL | String | O | Replication history task ID |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| log | Body | String | O | Replication history log |

### Execute a replication

Executes a replication.

```
POST /ncr/v2.0/appkeys/{appKey}/replications/executions
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| policy_id | Body | Integer | O | Replication ID |

Example

```json
{
    "policy_id": 15
}
```

#### Response

This API responds with common information.

## Image Cache

### View Image Caches

List Image Caches.

```
GET /ncr/v2.0/appkeys/{appKey}/endpoints
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| page | Query | Integer | X | Page number to search |
| page_size | Query | Integer | X | Page size to query (default: 10) |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| endpoints | Body | Array | O | Image Cache list |
| endpoints.status | Body | String | O | Image Cache status |
| endpoints.credential | Body | Object | O | Source registry authentication information |
| endpoints.credential.access_key | Body | String | O | Source registry access ID |
| endpoints.credential.access_secret | Body | String | O | Source registry access secret |
| endpoints.update_time | Body | String | O | Update time |
| endpoints.name | Body | String | O | Image Cache Name |
| endpoints.url | Body | String | O | Source Registry URL |
| endpoints.insecure | Body | Boolean | O | Certificate is verified or not |
| endpoints.creation_time | Body | String | O | Created time |
| endpoints.type | Body | String | O | Source Registry Type |
| endpoints.id | Body | Integer | O | Image Cache ID |
| endpoints.description | Body | Integer | O | Image Cache description |

Example

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

### Create an Image Cache

Creates an Image Cache.

```
POST /ncr/v2.0/appkeys/{appKey}/endpoints
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| name | Body | String | O | Image Cache Name |
| url | Body | String | O | Source Registry URL |
| type | Body | String | O | Source registry type, harbor/docker-hub/docker-registry/google-gcr/aws-ecr/azure-acr/quay/ncr |
| credential | Body | Object | O | Source registry authentication information |
| credential.access_key | Body | String | O | Source registry access ID |
| credential.access_secret | Body | String | O | Source registry access secret |
| insecure | Body | Boolean | X | Certificate is verified or not |

Example

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

#### Response

This API responds with common information.

### Delete an Image Cache

Delete an Image Cache.

```
DELETE /ncr/v2.0/appkeys/{appKey}/endpoints/{endpointId}
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| endpointId | URL | String | O | Image Cache ID |

#### Response

This API responds with common information.

### Change an Image Cache 

Change an Image Cache.

```
PUT /ncr/v2.0/appkeys/{appKey}/endpoints/{endpointId}
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| endpointId | URL | String | O | Image Cache ID |
| name | Body | String | X | Image Cache Name |
| url | Body | String | X | Source Registry URL |
| access_key | Body | String | X | Source registry access ID |
| access_secret | Body | String | X | Source registry access secret |
| insecure | Body | Boolean | X | Certificate is verified or not |

Example

```json
{
    "name": "test",
    "url": "https://095679f7-kr1-registry.container.nhncloud.com",
    "access_key": "hnk8WpQl8uYGGzDiXVAb",
    "access_secret": "EL3vl7gPWeaEqwGs"
}
```

#### Response

This API responds with common information.

## Scan Settings

### View the scan cycle

Retrieve the scan cycle.

```
GET /ncr/v2.0/appkeys/{appKey}/scanAll/schedule
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| schedule | Body | Object | O | Scan cycle information |
| schedule.id | Body | Integer | O | Scan cycle ID |
| schedule.schedule | Body | Object | O | Scan cycle |
| schedule.schedule.cron | Body | String | O | Cron format of scan cycle |
| schedule.schedule.type | Body | String | O | Scan cycle type |
| schedule.status | Body | String | O | Status |
| schedule.creation_time | Body | String | O | Created time |
| schedule.update_time | Body | String | O | Update time |

Example

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

### Set the scan cycle

Sets the scan cycle.

```
PUT /ncr/v2.0/appkeys/{appKey}/scanAll/schedule
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| schedule | Body | Object | O | Scan cycle information |
| schedule.cron | Body | String | O | Cron format of scan cycle |
| schedule.type | Body | String | O | Scan cycle type |

Example

```json
{
    "schedule": {
        "cron": "0 0 * * *",
        "type": "Daily"
    }
}
```

#### Response

This API responds with common information.

### View the common CVE allow list

Retrieve the common CVE allow list.

```
GET /ncr/v2.0/appkeys/{appKey}/CVEAllowlist
```

#### Request

This API does not require a request body.

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |

#### Response

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| cve_allowlist | Body | Object | O | Allow List information |
| cve_allowlist.id | Body | Integer | O | Allow List ID |
| cve_allowlist.items | Body | Object List | O | Allow List |
| cve_allowlist.items.cve_id | Body | String | X | CVE ID |

Example

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

### Change the common CVE allow list

Change the common CVE allow list.

```
PUT /ncr/v2.0/appkeys/{appKey}/CVEAllowlist
```

#### Request

| Name | Type | Format | Required | Description |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | Service Appkey |
| items | Body | Object List | O | Allow List |
| items.cve_id | Body | String | O | CVE ID |

Example

```json
{
    "items": [{
        "cve_id": "CVE-2011-3374"
    }]
}
```

#### Response

This API responds with common information.
