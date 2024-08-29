## Container > NHN Container Registry(NCR) > API 가이드

컨테이너 레지스트리를 구성하기 위한 API를 기술합니다.
API를 사용하려면 User Access Key와 Secret Access Key가 필요합니다. User Access Key와 Secret Access Key는 NHN Cloud 콘솔의 계정 > **API 보안 설정** 페이지에서 생성합니다.

### API 요청 공통 정보

API를 사용하려면 서비스 AppKey가 필요합니다. 서비스 Appkey는 콘솔 상단 <strong>URL & Appkey</strong> 메뉴에서 확인이 가능합니다.
API 도메인은 다음과 같습니다.

| 리전 | 도메인 |
| --- | --- |
| 한국(판교) 리전 | https://kr1-ncr.api.gncloud.go.kr |

API 헤더는 다음과 같습니다

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| X-TC-AUTHENTICATION-ID | Header | String | O | User Access Key |
| X-TC-AUTHENTICATION-SECRET | Header | String | X (생성하였을 경우 필수) | Secret Access Key |

### API 응답 공통 정보

모든 API 요청에 <strong>200 OK</strong>로 응답합니다. 자세한 응답 결과는 응답 본문 헤더를 참고합니다.

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| header | Body | Object |  |
| header.isSuccessful | Body | Boolean | true: 정상<br>false: 오류 |
| header.resultCode | Body | Integer | 200: 정상<br>10000 이상: 오류 |
| header.resultMessage | Body | String | "SUCCESS": 정상<br>그 외: 오류 원인 메시지 |

> [주의] API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.<br>
> [주의] 리소스를 생성할 때 입력하지 않은 옵션 필드는 조회할 때 응답 본문에 나타나지 않을 수 있습니다.

## 컨테이너 레지스트리

### 컨테이너 레지스트리 목록 보기

컨테이너 레지스트리 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| registries | Body | Array | O | 컨테이너 레지스트리 목록 |
| registries.project\_id | Body | Integer | O | 컨테이너 레지스트리 ID |
| registries.name | Body | String | O | 컨테이너 레지스트리 이름 |
| registries.creation\_time | Body | String | O | 생성 시간 |
| registries.update\_time | Body | String | O | 변경 시간 |
| registries.repo\_count | Body | Integer | O | 컨테이너 레지스트리에 있는 이미지 개수 |
| registries.metadata | Body | Object map[string]string | O | 컨테이너 레지스트리 설정 |
| registries.metadata.auto\_scan | Body | String | O | 이미지를 push할 때 자동으로 스캔 여부: true/false |
| registries.metadata.severity | Body | String | X | 취약점 심각도: critical/medium/high/low/none |
| registries.metadata.prevent\_vul | Body | String | O | 이미지의 취약점 심각도에 따라 pull 방지 설정 여부: true/false |
| registries.metadata.reuse\_sys\_cve\_allowlist | Body | String | O | 공통 CVE 허용 목록 사용 여부: true/false |
| registries.cve\_allowlist | Body | Object | O | 취약점 허용 목록 |
| registries.cve\_allowlist.id | Body | Integer | O | 취약점 허용 목록 ID |
| registries.cve\_allowlist.items | Body | Object List | O | CVE 목록 |
| registries.cve\_allowlist.items.cve\_id | Body | String | X | CVE ID |
| registries.registry\_id | Body | Integer | O | 0이 아니면 이미지 캐시 유형, 0이면 일반 유형 |
| registries.usage | Body | Integer | O | 컨테이너 레지스트리 사용량 |
| registries.uri | Body | String | O | 컨테이너 레지스트리 접근 URI |
| registries.private\_uri | Body | String | O | 컨테이너 레지스트리 접근 Private URI |

예시

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

### 컨테이너 레지스트리 보기

컨테이너 레지스트리 정보를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryNameOrId | URL | String | O | 레지스트리 이름 또는 ID, 이름이 숫자로만 되어 있으면 X-Is-Resource-Name 값 true 설정 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId 값 이름 여부, true/false |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| registry | Body | Object | O | 컨테이너 레지스트리 정보 |
| registry.project\_id | Body | Integer | O | 컨테이너 레지스트리 ID |
| registry.name | Body | String | O | 컨테이너 레지스트리 이름 |
| registry.creation\_time | Body | String | O | 생성 시간 |
| registry.update\_time | Body | String | O | 변경 시간 |
| registry.repo\_count | Body | Integer | O | 컨테이너 레지스트리에 있는 이미지 개수 |
| registry.metadata | Body | Object map[string]string | O | 컨테이너 레지스트리 설정 |
| registry.metadata.auto\_scan | Body | String | O | 이미지를 push할 때 자동으로 스캔 여부: true/false |
| registry.metadata.severity | Body | String | X | 취약점 심각도: critical/medium/high/low/none |
| registry.metadata.prevent\_vul | Body | String | O | 이미지의 취약점 심각도에 따라 pull 방지 설정 여부: true/false |
| registry.metadata.reuse\_sys\_cve\_allowlist | Body | String | O | 공통 CVE 허용 목록 사용 여부: true/false |
| registry.cve\_allowlist | Body | Object | O | 취약점 허용 목록 |
| registry.cve\_allowlist.id | Body | Integer | O | 취약점 허용 목록 ID |
| registry.cve\_allowlist.items | Body | Object List | O | CVE 목록 |
| registry.cve\_allowlist.items.cve\_id | Body | String | X | CVE ID |
| registry.registry\_id | Body | Integer | O | 0이 아니면 이미지 캐시 유형, 0이면 일반 유형 |
| registry.usage | Body | Integer | O | 컨테이너 레지스트리 사용량 |
| registry.uri | Body | String | O | 컨테이너 레지스트리 접근 URI |
| registry.private\_uri | Body | String | O | 컨테이너 레지스트리 접근 Private URI |

예시

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

### 컨테이너 레지스트리 생성하기

컨테이너 레지스트리를 생성합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| project\_name | Body | String | O | 컨테이너 레지스트리 이름 |
| metadata | Body | Object map[string]string | X | 컨테이너 레지스트리 설정 |
| metadata.auto\_scan | Body | String | X | 이미지를 push할 때 자동으로 스캔 여부: true/false |
| metadata.severity | Body | String | X | 취약점 심각도: critical/medium/high/low/none |
| metadata.prevent\_vul | Body | String | X | 이미지의 취약점 심각도에 따라 pull 방지 설정 여부: true/false |
| metadata.reuse\_sys\_cve\_allowlist | Body | String | X | 공통 CVE 허용 목록 사용 여부: true/false |
| registry\_id | Body | Integer | X | 이미지 캐시 유형의 레지스트리 생성 시 필요한 이미지 캐시 ID |

예시

```json
{
    "project_name": "registry"
}
```

#### 응답

이 API는 공통 정보만 응답합니다.

### 컨테이너 레지스트리 삭제하기

컨테이너 레지스트리를 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryNameOrId | URL | String | O | 레지스트리 이름 또는 ID, 이름이 숫자로만 되어 있으면 X-Is-Resource-Name 값 true 설정 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId 값 이름 여부, true/false |

#### 응답

이 API는 공통 정보만 응답합니다.

### 컨테이너 레지스트리 변경하기

컨테이너 레지스트리를 변경합니다.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryNameOrId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryNameOrId | URL | String | O | 레지스트리 이름 또는 ID, 이름이 숫자로만 되어 있으면 X-Is-Resource-Name 값 true 설정 |
| X-Is-Resource-Name | Header | String | X | registryNameOrId 값 이름 여부, true/false |
| metadata | Body | Object map[string]string | X | 컨테이너 레지스트리 설정 |
| metadata.auto\_scan | Body | String | X | 이미지를 push할 때 자동으로 스캔 여부: true/false |
| metadata.severity | Body | String | X | 취약점 심각도: critical/medium/high/low/none |
| metadata.prevent\_vul | Body | String | X | 이미지의 취약점 심각도에 따라 pull 방지 설정 여부: true/false |
| metadata.reuse\_sys\_cve\_allowlist | Body | String | X | 공통 CVE 허용 목록 사용 여부: true/false |
| cve\_allowlist | Body | Object | 설정되어 있을 시 필수 | 취약점 허용 목록 |
| cve\_allowlist.items | Body | Object List | 설정되어 있을 시 필수 | CVE 목록 |
| cve\_allowlist.items.cve\_id | Body | String | 설정되어 있을 시 필수 | CVE ID |

예시

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

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 보호 정책 목록 보기

이미지 보호 정책 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| immutable\_tag\_rules | Body | Array | O | 이미지 보호 정책 목록 |
| immutable\_tag\_rules.id | Body | Integer | O | 이미지 보호 정책 ID |
| immutable\_tag\_rules.disabled | Body | Boolean | O | 이미지 보호 정책 활성화 여부 |
| immutable\_tag\_rules.scope | Body | Object | O | 이미지에 대한 보호 정책 정보 |
| immutable\_tag\_rules.scope.include | Body | Boolean | O | 이미지에 대한 보호 정책 설정 여부 |
| immutable\_tag\_rules.scope.pattern | Body | String | O | 보호 대상 이미지 |
| immutable\_tag\_rules.tag | Body | Object | O | 태그에 대한 보호 정책 정보 |
| immutable\_tag\_rules.tag.include | Body | Boolean | O | 태그에 대한 보호 정책 설정 여부 |
| immutable\_tag\_rules.tag.pattern | Body | String | O | 보호 대상 태그 |

예시

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

### 이미지 보호 정책 생성하기

이미지 보호 정책을 생성합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| scope | Body | Object | X | 이미지에 대한 보호 정책 정보 |
| scope.include | Body | Boolean | X | 이미지에 대한 보호 정책 설정 여부 |
| scope.pattern | Body | String | X | 보호 대상 이미지<br>전체 이미지 대상: \*\* 입력 |
| tag | Body | Object | X | 태그에 대한 보호 정책 정보 |
| tag.include | Body | Boolean | X | 태그에 대한 보호 정책 설정 여부 |
| tag.pattern | Body | String | X | 보호 대상 태그<br>전체 태그 대상: \*\* 입력 |

예시

```json
{
    "scope": {
        "include": true,
        "pattern": "test"
    }
}
```

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 보호 정책 삭제하기

이미지 보호 정책을 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| rule\_id | URL | String | O | 이미지 보호 정책 ID |

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 보호 정책 변경하기

이미지 보호 정책을 변경합니다.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/immutabletagrules/{rule_id}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| rule\_id | URL | String | O | 이미지 보호 정책 ID |
| disabled | Body | Boolean | X | 이미지 보호 정책 활성화 여부 |
| scope | Body | Object | O | 이미지에 대한 보호 정책 정보 |
| scope.include | Body | Boolean | O | 이미지에 대한 보호 정책 설정 여부 |
| scope.pattern | Body | String | O | 보호 대상 이미지 |
| tag | Body | Object | O | 태그에 대한 보호 정책 정보 |
| tag.include | Body | Boolean | O | 태그에 대한 보호 정책 설정 여부 |
| tag.pattern | Body | String | O | 보호 대상 태그 |

예시

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

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 정리 정책 목록 보기

이미지 정리 정책 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| retention\_rules | Body | Array | O | 이미지 정리 정책 목록 |
| retention\_rules.id | Body | Integer | O | 이미지 정리 정책 ID |
| retention\_rules.disabled | Body | Boolean | O | 이미지 정리 정책 활성화 여부 |
| retention\_rules.parameters | Body | Object map | O | 정리 정책 설정 |
| retention\_rules.parameters.always | Body | Integer | X | 항상 n개의 아티팩트만 보관 |
| retention\_rules.parameters.latest\_pulled\_n\_images | Body | Integer | X | 가장 최근에 Pull한 n개의 아티팩트만 보관 |
| retention\_rules.parameters.latest\_pushed\_n\_images | Body | Integer | X | 가장 최근에 Push한 n개의 아티팩트만 보관 |
| retention\_rules.parameters.n\_days\_since\_last\_pull | Body | Integer | X | Push한 날짜가 n일인 아티팩트만 보관 |
| retention\_rules.parameters.n\_days\_since\_last\_push | Body | Integer | X | Pull한 날짜가 n일인 아티팩트만 보관 |
| retention\_rules.tag | Body | Object | O | 태그에 대한 정리 정책 정보 |
| retention\_rules.tag.include | Body | Boolean | O | 태그에 대한 정리 정책 설정 여부 |
| retention\_rules.tag.pattern | Body | String | O | 정리 대상 태그 |
| retention\_rules.tag.untagged | Body | Boolean | O | 태깅되지 않은 아티팩트 포함 여부 |
| retention\_rules.scope | Body | Object | O | 이미지에 대한 정리 정책 정보 |
| retention\_rules.scope.include | Body | Boolean | O | 이미지에 대한 정리 정책 설정 여부 |
| retention\_rules.scope.pattern | Body | String | O | 정리 대상 이미지 |

예시

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

### 이미지 정리 정책 생성하기

이미지 정리 정책을 생성합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| tag | Body | Object | X | 태그에 대한 정리 정책 정보 |
| tag.include | Body | Boolean | X | 태그에 대한 정리 정책 설정 여부 |
| tag.pattern | Body | String | X | 정리 대상 태그<br>전체 태그 대상: \*\* 입력 |
| tag.untagged | Body | Boolean | X | 태깅되지 않은 아티팩트 포함 여부 |
| scope | Body | Object | X | 이미지에 대한 정리 정책 정보 |
| scope.include | Body | Boolean | X | 이미지에 대한 정리 정책 설정 여부 |
| scope.pattern | Body | String | X | 정리 대상 이미지<br>전체 이미지 대상: \*\* 입력 |
| disabled | Body | Boolean | X | 이미지 정리 정책 활성화 여부<br>미입력 시 활성화(false) 설정 |
| parameters | Body | Object map | O | 정리 정책 설정 |
| parameters.always | Body | Integer | X | 항상 n개의 아티팩트만 보관 |
| parameters.latest\_pulled\_n\_images | Body | Integer | X | 가장 최근에 Pull한 n개의 아티팩트만 보관 |
| parameters.latest\_pushed\_n\_images | Body | Integer | X | 가장 최근에 Push한 n개의 아티팩트만 보관 |
| parameters.n\_days\_since\_last\_pull | Body | Integer | X | Push한 날짜가 n일인 아티팩트만 보관 |
| parameters.n\_days\_since\_last\_push | Body | Integer | X | Pull한 날짜가 n일인 아티팩트만 보관 |

예시

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

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 정리 정책 삭제하기

이미지 보호 정책을 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| id | Query | Integer | O | 이미지 정리 정책 ID |

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 정리 정책 변경하기

이미지 보호 정책을 변경합니다.

```
PUT /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/{rule_id}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| rule\_id | URL | String | O | 이미지 정리 정책 ID |
| disabled | Body | Boolean | X | 이미지 정리 정책 활성화 여부 |
| tag | Body | Object | O | 태그에 대한 정리 정책 정보 |
| tag.include | Body | Boolean | O | 태그에 대한 정리 정책 설정 여부 |
| tag.pattern | Body | String | O | 정리 대상 태그 |
| tag.untagged | Body | Boolean | O | 태깅되지 않은 아티팩트 포함 여부 |
| scope | Body | Object | O | 이미지에 대한 정리 정책 정보 |
| scope.include | Body | Boolean | O | 이미지에 대한 정리 정책 설정 여부 |
| scope.pattern | Body | String | O | 정리 대상 이미지 |
| parameters | Body | Object map | O | 정리 정책 설정 |
| parameters.always | Body | Integer | 설정되어 있을 시 필수 | 항상 n개의 아티팩트만 보관 |
| parameters.latest\_pulled\_n\_images | Body | Integer | 설정되어 있을 시 필수 | 가장 최근에 Pull한 n개의 아티팩트만 보관 |
| parameters.latest\_pushed\_n\_images | Body | Integer | 설정되어 있을 시 필수 | 가장 최근에 Push한 n개의 아티팩트만 보관 |
| parameters.n\_days\_since\_last\_pull | Body | Integer | 설정되어 있을 시 필수 | Push한 날짜가 n일인 아티팩트만 보관 |
| parameters.n\_days\_since\_last\_push | Body | Integer | 설정되어 있을 시 필수 | Pull한 날짜가 n일인 아티팩트만 보관 |

예시

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

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 정리 히스토리 목록 보기

이미지 정리 히스토리 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| executions | Body | Array | O | 이미지 정리 히스토리 목록 |
| executions.id | Body | Integer | O | 이미지 정리 히스토리 ID |
| executions.dry\_run | Body | Boolean | O | 테스트 실행 여부 |
| executions.trigger | Body | String | O | 이미지 정리 실행 방식 |
| executions.start\_time | Body | String | O | 이미지 정리 시작 시간 |
| executions.end\_time | Body | String | O | 이미지 정리 종료 시간 |
| executions.status | Body | String | O | 이미지 정리 상태 |

예시

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

### 이미지 정리 히스토리 상세 보기

이미지 정리 히스토리를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| executionId | URL | String | O | 이미지 정리 히스토리 ID |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tasks | Body | Array | O | 태스크 목록 |
| tasks.id | Body | Integer | O | 태스크 ID |
| tasks.repository | Body | String | O | 정리된 이미지 이름 |
| tasks.retained | Body | Integer | O | 이미지 보호 설정 여부 |
| tasks.total | Body | Integer | O | 정리된 개수 |
| tasks.start\_time | Body | String | O | 정리 시작 시간 |
| tasks.end\_time | Body | String | O | 정리 종료 시간 |
| tasks.status | Body | String | O | 태스크 상태 |

예시

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

### 이미지 정리 히스토리 로그 보기

이미지 정리 히스토리 로그를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions/{executionId}/tasks/{taskId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| executionId | URL | String | O | 이미지 정리 히스토리 ID |
| taskId | URL | String | O | 이미지 정리 태스크 ID |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tasks | Body | String | O | 이미지 정리 로그 |

### 이미지 정리 정책 실행

이미지 정리 정책을 실행합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/executions
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| dry\_run | Body | Boolean | X | 테스트 실행 여부 |

#### 응답

이 API는 공통 정보만 응답합니다.

### 이미지 정리 주기 보기

이미지 정리 주기를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| trigger | Body | Object | O | 이미지 정리 주기 정보 |
| trigger.kind | Body | String | O | 이미지 정리 유형 |
| trigger.settings | Body | Object | O | 이미지 정리 주기 설정 |
| trigger.settings.cron | Body | String | O | 이미지 정리 주기 |

예시

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

### 이미지 정리 주기 생성하기

이미지 정리 주기를 생성합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryId}/retentions/schedule
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryId | URL | String | O | 레지스트리 ID |
| settings | Body | Object | O | 이미지 정리 주기 설정 |
| settings.cron | Body | String | O | 이미지 정리 주기 |

예시

```json
{
    "settings": {
        "cron": "0 0 * * 0"
    }
}
```

#### 응답

이 API는 공통 정보만 응답합니다.

## 컨테이너 이미지

### 컨테이너 이미지 목록 보기

컨테이너 이미지 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| images | Body | Array | O | 컨테이너 이미지 목록 |
| images.id | Body | Integer | O | 컨테이너 이미지 ID |
| images.name | Body | String | O | 컨테이너 이미지 이름 |
| images.project\_id | Body | Integer | O | 레지스트리 ID |
| images.pull\_count | Body | Integer | O | 컨테이너 이미지 pull 횟수 |
| images.artifact\_count | Body | Integer | O | 컨테이너 이미지 아티팩트 개수 |
| images.creation\_time | Body | String | O | 컨테이너 이미지 생성 시간 |
| images.update\_time | Body | String | O | 컨테이너 이미지 변경 시간 |

예시

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

### 컨테이너 이미지 보기

컨테이너 이미지를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| image | Body | Object | O | 컨테이너 이미지 목록 |
| image.id | Body | Integer | O | 컨테이너 이미지 ID |
| image.name | Body | String | O | 컨테이너 이미지 이름 |
| image.project\_id | Body | Integer | O | 레지스트리 ID |
| image.pull\_count | Body | Integer | O | 컨테이너 이미지 pull 횟수 |
| image.artifact\_count | Body | Integer | O | 컨테이너 이미지 아티팩트 개수 |
| image.creation\_time | Body | String | O | 컨테이너 이미지 생성 시간 |
| image.update\_time | Body | String | O | 컨테이너 이미지 변경 시간 |

예시

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

### 컨테이너 이미지 삭제하기

컨테이너 이미지를 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |

#### 응답

이 API는 공통 정보만 응답합니다.

### 아티팩트 목록 보기

아티팩트 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |
| with\_scan\_overview | Query | String | X | 취약점 정보 조회 여부 |
| with\_accessory | Query | String | X | 인증 정보 조회 여부 |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| artifacts | Body | Array | O | 아티팩트 목록 |
| artifacts.digest | Body | String | O | 아티팩트 다이제스트 |
| artifacts.extra\_attrs | Body | Object | O | 아티팩트 추가 정보 |
| artifacts.scan\_overview | Body | Object | X | 아티팩트 스캔 정보 |
| artifacts.accessories | Body | Object | X | 아티팩트 인증 정보 |
| artifacts.manifest\_media\_type | Body | String | O | 아티팩트 타입 |
| artifacts.media\_type | Body | String | O | 아티팩트 타입 |
| artifacts.size | Body | Integer | O | 아티팩트 크기 |
| artifacts.pull\_time | Body | String | O | 아티팩트 pull 시간 |
| artifacts.push\_time | Body | String | O | 아티팩트 push 시간 |

예시

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

### 아티팩트 보기

아티팩트를 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| reference | URL | String | O | 아티팩트 이름 |
| with\_scan\_overview | Query | String | X | 취약점 정보 조회 여부 |
| with\_accessory | Query | String | X | 인증 정보 조회 여부 |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| artifact | Body | Object | O | 아티팩트 정보 |
| artifact.digest | Body | String | O | 아티팩트 다이제스트 |
| artifact.extra\_attrs | Body | Object | O | 아티팩트 추가 정보 |
| artifact.scan\_overview | Body | Object | X | 아티팩트 스캔 정보 |
| artifact.accessories | Body | Object | X | 아티팩트 인증 정보 |
| artifact.manifest\_media\_type | Body | String | O | 아티팩트 타입 |
| artifact.media\_type | Body | String | O | 아티팩트 타입 |
| artifact.size | Body | Integer | O | 아티팩트 크기 |
| artifact.pull\_time | Body | String | O | 아티팩트 pull 시간 |
| artifact.push\_time | Body | String | O | 아티팩트 push 시간 |

예시

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

### 아티팩트 삭제하기

아티팩트를 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| reference | URL | String | O | 아티팩트 이름 |

#### 응답

이 API는 공통 정보만 응답합니다.

### 아티팩트 태그 목록 보기

아티팩트 태그 목록을 조회합니다.

```
GET /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| reference | URL | String | O | 아티팩트 이름 |
| page | Query | Integer | X | 조회할 페이지 번호 |
| page\_size | Query | Integer | X | 조회할 페이지 크기(default: 10) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| tags | Body | Array | O | 아티팩트 태그 목록 |
| tags.id | Body | Integer | O | 아티팩트 태그 ID |
| tags.name | Body | String | O | 아티팩트 태그 이름 |
| tags.pull\_time | Body | String | O | 아티팩트 태그 pull 시간 |
| tags.push\_time | Body | String | O | 아티팩트 태그 push 시간 |

예시

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

### 아티팩트 태그 생성하기

아티팩트 태그를 생성합니다.

```
POST /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| reference | URL | String | O | 아티팩트 이름 |
| name | Body | String | O | 아티팩트 태그 이름 |

예시

```json
{
    "name": "test"
}
```

#### 응답

이 API는 공통 정보만 응답합니다.

### 아티팩트 태그 삭제하기

아티팩트 태그를 삭제합니다.

```
DELETE /ncr/v2.0/appkeys/{appKey}/registries/{registryName}/images/{imageName}/artifacts/{reference}/tags/{tagName}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| registryName | URL | String | O | 레지스트리 이름 |
| imageName | URL | String | O | 컨테이너 이미지 이름 |
| reference | URL | String | O | 아티팩트 이름 |
| tagName | URL | String | O | 아티팩트 태그 이름 |

#### 응답

이 API는 공통 정보만 응답합니다.
