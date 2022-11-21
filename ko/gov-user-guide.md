## Container > NHN Container Registry(NCR) > 사용 가이드

## 사전 준비
### Docker 설치
NHN Container Registry(이하 NCR) 서비스는 Docker 컨테이너 이미지를 저장하고 배포하기 위한 서비스입니다. 컨테이너 이미지를 다루려면 우선 사용자의 환경에 Docker가 설치되어 있어야 합니다.

#### Windows
Docker Hub에서 [Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)를 다운로드해 설치합니다.

#### macOS
Docker Hub에서 [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)을 다운로드해 설치합니다.

#### Linux
Linux 배포판에 따라 설치 과정이 다릅니다. CentOS 7, Ubuntu가 아닌 다른 배포판을 사용한다면 [Install Docker Engine](https://docs.docker.com/engine/install)을 확인하세요.

* CentOS 7
```
// Docker 설치에 필요한 패키지 설치
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2

// Docker 저장소 추가
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

// Docker 설치
$ sudo yum install -y docker-ce docker-ce-cli containerd.io

// Docker 서비스 시작
$ sudo systemctl start docker
```

* Ubuntu
```
// apt 인덱스 업데이트
$ sudo apt-get update

// repository over HTTPS를 사용하기 위한 패키지 설치
$ sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common

// GPG Key를 추가하고 확인
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

// 저장소 추가하고 apt 인덱스 업데이트
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt-get update

// Docker 설치
$ sudo apt-get install docker-ce docker-ce-cli containerd.io

// Docker 서비스 시작
$ sudo systemctl start docker
```



### User Access Key/Secret Key 확인

Docker 명령줄 도구를 이용해 사용자 레지스트리에 로그인하려면 User Access Key와 Secret Key가 필요합니다. User Access Key와 Secret Key는 NHN Cloud Console의 계정 > **API 보안 설정** 페이지에서 생성할 수 있습니다.

* User Access Key ID는 NHN Cloud ID 당 5개, IAM ID 당 5개까지 생성 할 수 있습니다.

* User Secret Key를 잃어버린 경우 다시 확인할 수 없으므로 재생성해야 합니다. 보안을 위해 발급된 키는 안전한 장소에 따로 보관해 주세요.

* User Access Key는 90일마다 변경하기를 권장합니다.



## 컨테이너 레지스트리 사용

> [참고]
> 멤버 권한의 사용자는 컨테이너 이미지 저장, 삭제 기능은 사용할 수 없습니다.

### 사용자 레지스트리 생성

레지스트리 서비스를 처음 사용하려면 먼저 NCR Console에서 레지스트리를 생성해야 합니다. **Container > NHN Container Registry(NCR) > Management** 서비스 페이지로 이동하여 **레지스트리 생성** 버튼을 클릭합니다. 생성하고자 하는 레지스트리의 이름을 입력한 뒤, 확인 버튼을 클릭하면 레지스트리를 생성할 수 있습니다.

### 사용자 레지스트리 주소 확인
생성한 레지스트리의 주소는 **Container > NHN Container Registry(NCR) > Management** 서비스 페이지의 레지스트리 목록에서 확인할 수 있습니다.

### 사용자 레지스트리 로그인
컨테이너 이미지를 저장하거나, 원하는 환경으로 가져오려면 Docker 명령줄 도구를 이용해야 합니다. Docker 명령줄 도구를 이용해 사용자 레지스트리에 접근하려면 로그인을 해야 합니다. `docker login` 명령을 사용한 후 `Username`에는 NHN Cloud 사용자 계정의 User Access Key를, `Password`로는 Secret Key를 각각 입력합니다.

```bash
$ docker login {사용자 레지스트리 주소}
Username: {NHN Cloud 사용자 계정 User Access Key}
Password: {NHN Cloud 사용자 계정 User Secret Key}
Login Succeeded
```

> [참고]
> `docker login` 명령으로 얻는 토큰의 최대 유효 시간은 12시간입니다.



### 컨테이너 이미지 저장(Push)

컨테이너 이미지를 사용자 레지스트리에 저장하려면 업로드할 이미지의 이름을 사용자 레지스트리 주소를 포함한 이미지(Image) 이름과 태그(Tag) 형식으로 설정해야 합니다. Docker 명령줄 도구의 **tag** 명령을 이용해 지정할 수 있습니다.

```bash
docker tag {이미지 이름}:{태그} {사용자 레지스트리 주소}/{이미지 이름}:{태그}
```

* 예시

```bash
docker tag ubuntu:18.04 example-kr1-registry.container.nhncloud.com/ubuntu:18.04
```

> [참고]
> 컨테이너 이미지 이름으로는 영어 소문자, 숫자, 일부 특수문자(-, _, /, .) 조합만 허용됩니다. 리포지토리 이름은 레지스트리 주소를 포함해 최대 255자, 태그 이름은 최대 128자로 제한됩니다. 이미지 이름이 길면 사용하기 불편할 수 있습니다. 적당한 길이의 이름을 사용하시기 바랍니다.

이제 Docker 명령줄 도구의 **push** 명령을 사용해 컨테이너 이미지를 사용자 레지스트리에 저장할 수 있습니다.

```bash
docker push {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시
```bash
$ docker push example-kr1-registry.container.nhncloud.com/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.nhncloud.com/ubuntu]
16542a8fc3be: Pushed
6597da2e2e52: Pushed
977183d4e999: Pushed
c8be1b8f4d60: Pushed
18.04: digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320 size: 1152
```

### 컨테이너 이미지 조회
저장된 컨테이너 이미지는 NCR Console에서 조회할 수 있습니다.

* 이미지 목록
    **Container > NHN Container Registry(NCR) > Management** 서비스 페이지의 레지스트리 목록에서 업로드한 레지스트리의 **이미지 보기** 버튼을 클릭하면 컨테이너 이미지 목록을 볼 수 있습니다.

* 아티팩트 목록
    이미지 목록에서 원하는 이미지의 **아티팩트 보기** 버튼을 클릭하면 해당 이미지의 아티팩트 목록을 볼 수 있습니다. 아티팩트 목록에서 특정 아티팩트를 선택하여 상세 정보 및 태그 목록을 확인할 수 있습니다.

* 태그 목록
    아티팩트 목록에서 원하는 아티팩트를 클릭하면 선택한 아티팩트에 지정된 태그 목록을 조회할 수 있습니다. 새로 태그를 생성하거나 태그를 검색해서 삭제할 수 있습니다.

### 컨테이너 이미지 가져오기(Pull)
Docker 명령줄 도구의 **pull** 명령을 사용해 이미지를 가져올 수 있습니다. 이를 위해 NHN Cloud Console에서 가져올 이미지의 정보를 확인해야 합니다.

```bash
docker pull {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시
```bash
$ docker pull example-kr1-registry.container.nhncloud.com/ubuntu:18.04
18.04: Pulling from ubuntu
5bed26d33875: Pull complete
f11b29a9c730: Pull complete
930bda195c84: Pull complete
78bf9a5ad49e: Pull complete
Digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320
Status: Downloaded newer image for example-kr1-registry.container.nhncloud.com/ubuntu:18.04
example-kr1-registry.container.nhncloud.com/ubuntu:18.04

$ docker images
REPOSITORY                                              TAG     IMAGE ID        CREATED         SIZE
example-kr1-registry.container.nhncloud.com/ubuntu   18.04   4e5021d210f6    12 days ago     64.2MB
```



## 컨테이너 레지스트리 관리

### 컨테이너 이미지 및 아티팩트 삭제

레지스트리에 저장된 이미지를 더 이상 사용하지 않는다면 NCR Console에서 삭제할 수 있습니다. 이미지를 삭제하려면 이미지 목록 보기 화면에서 삭제할 이미지를 선택한 후 **이미지 삭제** 버튼을 클릭합니다. 마찬가지로 아티팩트를 삭제하려면 아티팩트 목록 보기 화면에서 삭제할 아티팩트를 선택한 후 **아티팩트 삭제** 버튼을 클릭합니다.

### 컨테이너 이미지 태그 생성

Docker 명령줄 도구 없이 NCR Console에서 태그를 생성할 수 있습니다. 아티팩트 목록 보기 화면에서 태그를 추가할 아티팩트를 선택한 뒤, 하단의 상세 정보 보기 화면에서 **태그** 탭을 선택합니다. 그러면 현재 선택한 아티팩트에 지정된 태그 목록을 볼 수 있습니다. 여기서 **태그 생성** 버튼을 클릭하면 **태그 생성** 대화 상자가 나타나고, 원하는 태그 이름을 입력하여 새로운 태그를 생성할 수 있습니다.

### 컨테이너 이미지 태그 삭제

더 이상 사용하지 않는 태그가 있다면 마찬가지로 NCR Console에서 태그를 삭제할 수 있습니다. 태그 생성할 때와 마찬가지로 태그 목록 보기 화면으로 이동한 후 삭제할 태그를 선택합니다. 태그가 많아 태그 목록에 바로 나타나지 않는 경우 태그 검색 기능을 활용하여 삭제하고자 하는 태그를 찾을 수 있습니다. 삭제할 태그를 선택하고 **태그 삭제** 버튼을 클릭하면 선택한 태그를 삭제할 수 있습니다.

### 레지스트리 웹훅 설정
이미지 변경 사항 알림을 받으려면 NCR Console에서 웹훅 설정을 등록합니다. 웹훅을 설정할 레지스트리를 선택하고 하단의 상세 정보 보기 화면에서 **웹훅** 탭을 선택합니다. **웹훅 생성** 버튼을 클릭합니다. **웹훅 생성** 대화 상자가 나타나면 속성을 지정한 후 **확인** 버튼을 클릭합니다. 현재는 HTTP(S) 호출과 Slack 메신저를 이용한 알림 설정을 지원합니다.

> [참고]
>
> 공공 클라우드에서는 웹훅 설정 시 목적지 포트로 80, 88, 443, 8443만을 지원합니다.

### 컨테이너 이미지 정리

레지스트리에 저장된 이미지와 아티팩트를 정책에 따라 정리(삭제) 하도록 NCR Console에서 설정할 수 있습니다. 이미지 정리 정책을 사용하려면 설정할 레지스트리를 선택하고 하단의 상세 정보 보기 화면에서 **이미지 정리** 탭을 클릭합니다.
이미지 정리 정책은 최대 15개까지 등록할 수 있습니다.

> [주의]
> 이미지 정리와 보호에 동일한 정책이 설정된 경우 이미지 보호 정책이 우선 처리되어 이미지 정리 기능이 정상적으로 동작하지 않을 수 있습니다.

#### 이미지 정리 정책 설정

**정책 설정** 탭에서 **정리 정책 추가** 버튼을 클릭하면 **정리 정책 추가** 대화 상자가 나타나고, 원하는 이미지, 태그, 정책을 입력하여 새로운 정리 정책을 설정할 수 있습니다.
이미지 정리 정책은 활성화되어 생성됩니다. 일시적으로 정책을 적용하지 않으려면 **활성화/비활성화 기능**을 사용할 수 있습니다.

* 이미지 정리 동작 중 정리 정책 추가/삭제가 되는 경우 다음 이미지 정리 실행부터 반영됩니다.

이미지, 태그, 정리 정책은 AND 조건으로 처리됩니다.

* 전체 아티팩트 삭제 예제: `전체 이미지 + 사용하지 않는 태그 이름 + 해당 아티팩트 모두 제외하고, 정리`

> [참고]
> 레지스트리에 이미지, 태그, 정책이 모두 동일한 정리 정책을 중복으로 추가할 수 없습니다.
>
> `Push/Pull 한 날짜가 N 일인 아티팩트 제외하고, 정리` 정책은 Push/Pull을 수행한 지 N 일을 초과한 아티팩트를 제거합니다.

#### 이미지 정리 정책 삭제

**정책 설정** 탭 하단에서 삭제할 정리 정책을 선택하고 **정리 정책 삭제** 버튼을 클릭하여 삭제할 수 있습니다.

#### 테스트 실행

**정책 설정** 탭에서 **테스트 실행** 버튼을 클릭하여 설정한 이미지 정리 정책을 테스트할 수 있습니다.
테스트 실행 결과는 **히스토리 조회** 탭에서 확인할 수 있습니다.

#### 지금 실행

**정책 설정** 탭에서 **지금 실행** 버튼을 클릭하여 설정한 이미지 정리 정책을 수동으로 실행합니다.
실행 결과는 **히스토리 조회** 탭에서 확인할 수 있습니다.

#### 정리 주기 설정

이미지 정리 정책을 주기적으로 자동 실행되도록 설정할 수 있습니다.
반복 주기는 Cron 표현식(\* \* \* \* \*)을 이용하며 각 필드의 의미는 아래와 같습니다.

| 필드명 | 값의 허용 범위 | 허용된 특수문자 |
| --- | --- | --- |
| 분 | 0-59 | `*` `/` `,` `-` |
| 시간 | 0-59 | `*` `/` `,` `-` |
| 일 | 1-31 | `*` `/` `,` `?` |
| 월 | 1-12<br>JAN-DEC | `*` `/` `,` `-` |
| 요일 | 0-6<br>SUN-SAT | `*` `/` `,` `?` |

#### 히스토리 조회

**히스토리 조회** 탭에서 이미지 정리 히스토리를 조회할 수 있습니다.
하단의 조회된 정보를 클릭하여 히스토리 상세 정보를 확인할 수 있습니다.

### 컨테이너 이미지 보호

레지스트리에 저장된 이미지와 아티팩트가 삭제/변경되지 않도록 NCR Console에서 이미지 보호 기능을 설정할 수 있습니다. 이미지 보호 기능을 사용하려면 설정할 레지스트리를 선택하고 하단의 상세 정보 보기 화면에서 **이미지 보호** 탭을 클릭합니다.
이미지 보호 정책은 최대 15개까지 등록할 수 있습니다.

#### 이미지 보호 정책 추가

**보호 정책 추가** 버튼을 클릭하면 **보호 정책 추가** 대화 상자가 나타나고, 원하는 보호 정책을 입력하여 새로운 보호 정책을 설정할 수 있습니다.
이미지 보호 정책은 활성화되어 생성됩니다. 일시적으로 정책을 적용하지 않으려면 **활성화/비활성화 기능**을 사용할 수 있습니다.

> [참고]
> 레지스트리에 이미지와 태그가 동일한 보호 정책을 중복으로 추가할 수 없습니다.

#### 이미지 보호 정책 삭제

**이미지 보호** 탭 하단에서 삭제할 보호 정책을 선택하고 **보호 정책 삭제** 버튼을 클릭하여 삭제할 수 있습니다.

## 이미지 캐시 사용

소스 레지스트리(다른 원격 레지스트리)에서 이미지를 다운로드하여 캐싱하는 기능을 제공합니다.
이미지 캐시 유형의 레지스트리로 이미지 Pull 요청이 되면 아래와 같이 구분하여 이미지 제공을 결정합니다.

| 구분 | 이미지 제공 |
| --- | --- |
| 캐싱된 이미지와 소스 레지스트리의 이미지가 다름 | 소스 레지스트리의 이미지를 다운로드하여 이미지 제공 |
| 캐싱된 이미지와 소스 레지스트리의 이미지가 동일 | 캐싱된 이미지 제공 |
| 소스 레지스트리가 연결되지 않음 | 캐싱된 이미지 제공 |
| 소스 레지스트리에서 이미지가 삭제 | 이미지 제공하지 않음 |

이미지 캐시 유형의 레지스트리는 아래와 같이 요청한 이미지가 없는 경우 소스 레지스트리의 이미지를 다운로드하여 제공합니다.
![D-NCR_imagecache_01](https://static.toastoven.net/prod_ncr/20221129/D-NCR_imagecache_01.png)

### 이미지 캐시 생성

이미지 캐시를 사용하려면 소스 레지스트리를 등록해야 합니다. NCR Console에서 **이미지 캐시** 탭을 클릭한 뒤 **이미지 캐시 생성**을 클릭합니다. **이미지 캐시 생성** 대화 상자에서 소스 레지스트리의 정보를 입력합니다.
지원되는 소스 레지스트리 유형과 URL, Access ID, Access Secret은 아래와 같습니다.

| 소스 레지스트리 유형 | URL | Access ID | Access Secret |
| --- | --- | --- | --- |
| NHN Container Registry(NCR) | `https://xxxxxxxx-$REGION-registry.container.nhncloud.com` | User Access Key ID | Secret Access Key |
| Amazon Elastic Container Registry | `https://$AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazoneaws.com` | IAM 액세스키 ID | IAM 비밀 액세스 키 |
| Azure Container Registry | `https://$REGISTRY_NAME.azurecr.io` | 액세스 키 사용자 이름 | 액세스 키 암호 |
| Google Cloud Container Registry | `https://$REGION` | \_json\_key | 서비스 계정의 비공개 키(JSON 유형) |
| Docker Hub | `https://hub.docker.com` | Username | Password |
| Docker Registry | `docker-registry 주소` | Username | Password |
| Harbor | `Harbor 주소` | Username | Password |
| Quay | `https://quay.io` | json\_file | {<br>"account\_name": "$사용자 계정",<br>"docker\_cli\_password": "$Quay에서 생성한 암호화된 Password"<br>} |

> [참고] 허용되는 소스 레지스트리의 포트는 80, 443입니다.

### 이미지 캐시 수정

소스 레지스트리를 변경할 수 있습니다. NCR Console에서 **이미지 캐시**  >  **이미지 캐시 수정**을 클릭한 뒤 **이미지 캐시 수정** 대화 상자에서 소스 레지스트리의 정보를 입력합니다.

### 이미지 캐시 삭제

더 이상 사용하지 않는 이미지 캐시를 삭제할 수 있습니다. NCR Console의 **이미지 캐시**에서 삭제하려는 이미지 캐시를 선택한 뒤 **이미지 캐시 삭제** 버튼을 클릭합니다.

> [참고]
> 해당 이미지 캐시를 대상으로 지정한 레지스트리가 존재하면 이미지 캐시를 삭제할 수 없습니다.

### 이미지 캐시 유형의 레지스트리 생성

이미지 캐시 유형의 레지스트리를 생성하려면 NCR Console에서 **관리** 탭에서 **레지스트리 생성**을 클릭합니다. **레지스트리 생성** 대화 상자에서 사용 용도를 **이미지 캐시**를 선택한 뒤 이미지 캐시 대상을 선택합니다.

> [참고]
> 이미지 캐시 유형의 레지스트리에는 Image Push를 할 수 없습니다.
> 이미지 캐시 유형의 레지스트리에는 자동으로 `Pull 한 날짜가 7일 이내인 아티팩트는 제외하고 정리` 이미지 정리 정책이 추가됩니다.
> * 이미지 정리/보호 정책을 추가하여 레지스트리마다 캐싱된 이미지 관리 정책을 다르게 할 수 있습니다.
> 컨테이너 이미지 복제 기능을 사용하게 되면 이미지 캐시 유형의 레지스트리는 일반 유형의 레지스트리로 변경되어 복제됩니다.

### 이미지 캐시 유형의 레지스트리에서 이미지 가져오기(Pull)

Docker 명령줄 도구의 pull 명령을 사용해 이미지 캐시 유형의 레지스트리에서 이미지를 가져올 수 있습니다.

```
docker pull {사용자 이미지 캐시 유형의 레지스트리 주소}/{소스 레지스트리의 레지스트리 이름}/{이미지 이름}:{태그 이름}
```

* 예시

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

공식 이미지 또는 단일 레벨 저장소에서 이미지를 가져오는 경우 `소스 레지스트리의 레지스트리 이름을 library`로 사용해야 합니다.

```
docker pull {사용자 이미지 캐시 유형의 레지스트리 주소}/library/hello-world:latest
```

* 공식 이미지 예시

```
$ docker pull example-kr1-registry.container.nhncloud.com/docker/library/hello-world:latest

$ docker images
REPOSITORY                                                                   TAG             IMAGE ID       CREATED         SIZE
example-kr1-registry.container.nhncloud.com/docker/library/hello-world       latest          feb5d9fea6a5   14 months ago   13.3kB
```

> [주의] 이미지 캐싱 처리 최대 시간은 200초입니다. 소스 레지스트리에서 이미지를 다운로드하는 데 200초 이상의 시간이 소요될 경우 이미지 캐싱이 처리되지 않습니다.
>
> 동일한 이미지 가져오기를 다시 실행할 경우 이미지를 이어받기가 진행됩니다.
>
> 이미지 캐싱이 처리되지 않았음에도 용량이 증가한 경우 증가한 용량은 2시간 이후 원상태로 복구됩니다.

### 이미지 캐시 유형의 레지스트리 삭제

더 이상 사용하지 않는 이미지 캐시 유형의 레지스트리를 삭제할 수 있습니다. NCR Console의 **관리** 에서 삭제하려는 레지스트리를 선택한 뒤 **레지스트리 삭제** 버튼을 클릭합니다.

