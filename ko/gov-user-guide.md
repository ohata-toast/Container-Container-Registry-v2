## Container > NHN Container Registry(NCR) > 사용 가이드

## 사전 준비
### Docker 설치
NHN Container Registry(이하 NCR) 서비스는 Docker 컨테이너 이미지를 저장하고 배포하기 위한 서비스입니다. 컨테이너 이미지를 다루려면 우선 사용자의 환경에 Docker가 설치되어 있어야 합니다.

#### Windows
Docker Hub에서 [Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows)를 다운로드해 설치합니다.

#### macOS
Docker Hub에서 [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac)을 다운로드해 설치합니다.

#### Linux
Linux 배포판에 따라 설치 과정이 다릅니다. CentOS 7, Ubuntu가 아닌 다른 배포판을 사용한다면 [Install Docker Engine](https://docs.docker.com/engine/install)을 확인하십시오.

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
docker tag ubuntu:18.04 example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
```

> [참고]
> 컨테이너 이미지 이름으로는 영어 소문자, 숫자, 일부 특수문자(-, _, /, .) 조합만 허용됩니다. 리포지토리 이름은 레지스트리 주소를 포함해 최대 255자, 태그 이름은 최대 128자로 제한됩니다. 이미지 이름이 길면 사용하기 불편할 수 있습니다. 적당한 길이의 이름을 사용하십시오.

이제 Docker 명령줄 도구의 **push** 명령을 사용해 컨테이너 이미지를 사용자 레지스트리에 저장할 수 있습니다.

```bash
docker push {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시
```bash
$ docker push example-kr1-registry.container.nhncloud.com/registry/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.nhncloud.com/registry/ubuntu]
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
Docker 명령줄 도구의 **pull** 명령을 사용해 이미지를 가져올 수 있습니다. 이를 위해 NCR Console에서 가져올 이미지의 정보를 확인해야 합니다.

```bash
docker pull {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시
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

<span id="public-uri"></span>
## Public URI 사용
Public URI는 외부에서 NCR에 접근할 수 있도록 제공하는 주소입니다. 레지스트리를 생성할 때 Public URI 사용 여부를 설정하여 레지스트리에 대한 접근을 제어할 수 있습니다. Public URI를 사용함으로써, 외부 시스템이나 서비스에서 레지스트리의 이미지를 활용할 수 있게 됩니다.

Public URI를 사용하지 않도록 설정하면 레지스트리는 외부에서 접근할 수 없도록 제한됩니다. 이 설정은 레지스트리에 대한 접근을 NHN Cloud의 VPC 네트워크로 제한하여 외부에서의 무단 다운로드나 업로드 시도를 방지하고 보안을 강화합니다.

> [참고]
> Public URI를 사용하지 않도록 설정하면, 외부에서 레지스트리에 저장된 이미지에 접근할 수 없게 됩니다. 설정을 변경할 때에는 주변 시스템 및 서비스에 영향을 미칠 수 있으므로, 변경 전에 충분한 검증과 테스트를 수행해야 합니다.

<span id="private-uri"></span>
## Private URI 사용
Private URI는 NHN Cloud의 VPC 네트워크 내에서 사용할 수 있는 NCR 주소입니다. 보안을 강화하기 위해 인터넷 게이트웨이에 연결하지 않고 외부 네트워크를 단절한 인스턴스에서 NCR 서비스를 사용하고자 할 때 Private URI 기능을 활용할 수 있습니다.

> [참고]
> Private URI는 한국(평촌) 리전에서만 사용 가능합니다.

> [참고]
> 인터넷 게이트웨이에 연결하지 않은 인스턴스에서 Private URI를 이용하려면 NCR과 Object Storage 서비스 게이트웨이를 생성해야 합니다.

> [참고]
> 인스턴스, 서비스 게이트웨이, Object Storage와 NCR은 모두 동일한 리전을 사용해야 합니다.

### NCR 서비스 게이트웨이 생성
**Network > Service Gateway** 페이지로 이동하여 **서비스 게이트웨이 생성**을 클릭합니다. 생성하고자 하는 서비스 게이트웨이의 **이름**, **VPC**, **서브넷**을 입력하고 **서비스**를 **NCR**로 선택한 뒤 **확인**을 클릭하면 NCR 서비스 게이트웨이가 생성됩니다.
![ncr_c001_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c001.png)

### Object Storage 서비스 게이트웨이 생성
NCR에서 Private URI를 이용하여 이미지를 가져오려면 Object Storage에 대한 서비스 게이트웨이를 생성해야 합니다. NCR은 Object Storage를 사용하여 이미지 계층을 저장하기 때문에 서비스 게이트웨이가 필요합니다. 이미지를 다운로드할 때도 NCR에 접근하여 이미지 매니페스트를 가져온 뒤 Object Storage에 접근하여 실제 이미지 계층을 다운로드합니다.

**Network > Service Gateway** 페이지로 이동하여 **서비스 게이트웨이 생성**을 클릭합니다. 생성하고자 하는 서비스 게이트웨이의 **이름**, **VPC**, **서브넷**을 입력하고 **서비스**를 **Object Storage**로 선택한 뒤 **확인**을 클릭하면 Object Storage 서비스 게이트웨이가 생성됩니다.
![ncr_c002_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c005.png)

### 호스트 등록
인터넷 게이트웨이에 연결하지 않은 인스턴스에서 Private URI를 통해 NCR 레지스트리를 사용할 수 있도록 호스트 파일에 도메인과 IP를 설정해야 합니다.
인스턴스에서 Private Endpoint의 IP를 찾을 수 있도록 호스트 파일에 NCR 서비스 게이트웨이 IP 주소와 NCR Private Endpoint, Object Storage 서비스 게이트웨이 IP 주소와 Object Storage 도메인을 입력합니다.

NCR과 Object Storage 서비스 게이트웨이의 IP 주소는 **Network > Service Gateway** 페이지에서 확인할 수 있습니다.
![ncr_c003_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c003.png)

NCR Private URI는 **Container > NHN Container Registry(NCR) > 관리** 페이지에서 레지스트리를 선택 후 하단의 **기본 정보** 탭에서 확인할 수 있습니다. Private Endpoint는 Private URI에서 레지스트리 이름을 제외한 경로입니다.
![ncr_c004_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c004.png)

* 예시
```
Private URI: private-example-kr1-registry.container.nhncloud.com/hello-world
Private Endpoint: private-example-kr1-registry.container.nhncloud.com
```

**Windows**
`C:\Windows\System32\drivers\etc\hosts` 파일을 열고 다음 내용을 추가합니다.
```
{NCR 서비스 게이트웨이 IP 주소} {NCR Private Endpoint}
{Object Storage 서비스 게이트웨이 IP 주소} {Object Storage 도메인}
```

**Linux**
`/etc/hosts` 파일을 열고 다음 내용을 추가합니다.
```
{NCR 서비스 게이트웨이 IP 주소} {NCR Private Endpoint}
{Object Storage 서비스 게이트웨이 IP 주소} {Object Storage 도메인}
```


### Private URI를 통한 레지스트리 작업
인스턴스에 접속하고 `docker login` 명령을 실행하여 레지스트리에 로그인합니다. 인스턴스 구성에 따라 다음 명령에 `sudo`를 접두사로 붙여야 할 수도 있습니다.
```shell
$ docker login {사용자 Private 레지스트리 주소}
Username: {NHN Cloud 사용자 계정 User Access Key}
Password: {NHN Cloud 사용자 계정 User Secret Key}
Login Succeeded
```

* 예시
```shell
$ docker login private-example-kr1-registry.container.nhncloud.com
Username: hello-world
Password:
Login Succeeded
```

`docker pull`과 같은 레지스트리 작업을 수행하여 레지스트리에서 샘플 이미지를 다운로드합니다. NCR Console에서 가져올 이미지의 정보를 확인합니다.
```shell
$ docker pull {사용자 Private URI}/{이미지 이름}:{태그 이름}
```

* 예시
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


## 컨테이너 이미지 복제

NCR에서 제공하는 복제 기능은 리전 간 이미지를 복제합니다. 복제 기능의 구체적인 특징은 다음과 같습니다.

* 리전 간 양방향 이미지 복제를 합니다.
* 사용자가 복제 실행 방식을 선택할 수 있습니다.
* 대상 리전에 이미 동일한 이미지가 존재하는 경우 복제하지 않습니다.
* 현재 리전(A)에서 복제 이미지의 원본이 삭제되어도 대상 리전(B)에 복제된 이미지는 삭제하지 않습니다.

이미지 복제 기능을 사용하려면 NCR Console에서 **복제** 탭을 클릭합니다.

### 복제 구성 설정

**복제 생성**을 클릭한 뒤 **복제 생성** 대화 상자에서 복제 구성에 필요한 정보를 입력합니다.

> [참고]
> 복제 생성 직후에는 활성 상태가 **비활성화**로 표시될 수 있습니다. 복제 준비가 완료되면 **활성화**로 표시됩니다.
> 수 분 후에도 상태가 변경되지 않는 경우 **새로 고침**을 클릭합니다.

### 복제 대상 필터

* **소스 이미지 이름 필터**: 이미지 이름 또는 일부를 입력하여 지정된 대상을 복제합니다.
* **소스 태그 필터**: 태그 이름 또는 일부를 입력하여 지정된 대상을 복제합니다. 이 필터에 대해 일치/제외를 지정할 수 있습니다.

필터는 다음과 같은 패턴을 지원합니다.

| Pattern | Description | String(Match or not) |
| --- | --- | --- |
| * : path/* | 구분 기호 `/` 를 제외한 모든 문자와 일치합니다. | path/hello-world(Y) <br> path/my/hello-world(N) |
| \*\* : path/\*\* | 모든 문자와 일치합니다. | path/hello-world(Y) <br> path/my/hello-world(Y) |
| ? : 1.? | 구분 기호 `/` 를 제외한 임의의 한 문자와 일치합니다. | 1.0(Y) <br> 1.01(N) |
| {} : {path,ncr}/** | 쉼표로 구분된 항목 중 하나와 일치하는 문자와 일치합니다. | path/hello-world(Y) <br> ncr/hello-world(Y) <br> nhn/hello-world(N) |

### 자동 복제

* **이벤트 기반**: 현재 리전에 이미지가 업로드될 때 자동으로 대상 리전으로 복제가 실행됩니다.
* **사용자 설정**: 사용자가 설정한 주기에 따라 복제가 실행됩니다.

> [주의]
> **이벤트 기반** 방식은 Push 유형의 복제에서만 사용할 수 있습니다.
> 새로 업로드되는 이미지만 자동 복제가 실행됩니다.
> 복제 구성 전에 업로드된 이미지를 복제하려면 **수동 복제** 기능을 이용합니다.

### 수동 복제

**복제 실행**을 클릭한 뒤 **복제 실행** 대화 상자에서 **확인**을 클릭하면 복제가 실행됩니다.

> [주의]
> Garbage Collection 기능이 실행되기 전 복제를 실행할 경우 대상 리전(B)에 복제된 이미지의 용량이 원본보다 작을 수 있습니다.
> 일정 시간 이후 원본 이미지의 용량이 작아집니다.

### 복제 히스토리

복제 히스토리에서 복제 진행 상황 및 이력을 확인할 수 있습니다. 복제 히스토리를 확인하려면 구성한 복제를 클릭하고 하단의 **상세 정보 보기** 화면에서 **복제 히스토리** 탭을 클릭합니다.
하단의 조회된 정보를 클릭하여 히스토리 상세 정보를 확인할 수 있습니다.

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

> [참고]
> 허용되는 소스 레지스트리의 포트는 80, 443입니다.

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

> [참고]
> 이미지 캐시 유형의 레지스트리에는 자동으로 `Pull 한 날짜가 7일 이내인 아티팩트는 제외하고 정리` 이미지 정리 정책이 추가됩니다.
> * 이미지 정리/보호 정책을 추가하여 레지스트리마다 캐싱된 이미지 관리 정책을 다르게 할 수 있습니다.

> [참고]
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

> [주의]
> 이미지 캐싱 처리 최대 시간은 200초입니다. 소스 레지스트리에서 이미지를 다운로드하는 데 200초 이상의 시간이 소요될 경우 이미지 캐싱이 처리되지 않습니다.
> * 동일한 이미지 가져오기를 다시 실행할 경우 이미지를 이어받기가 진행됩니다.

> [참고]
> 이미지 캐싱이 처리되지 않았음에도 용량이 증가한 경우 증가한 용량은 2시간 이후 원상태로 복구됩니다.

### 이미지 캐시 유형의 레지스트리 삭제

더 이상 사용하지 않는 이미지 캐시 유형의 레지스트리를 삭제할 수 있습니다. NCR Console의 **관리** 에서 삭제하려는 레지스트리를 선택한 뒤 **레지스트리 삭제** 버튼을 클릭합니다.

## 이미지 취약점 스캐닝

NCR 서비스를 활성화하면 기본으로 이미지 취약점 스캐닝 기능을 사용할 수 있습니다.
이미지 취약점 스캐닝을 사용하면 선택한 아티팩트에 대해 수동으로 스캔을 시작할 수 있습니다. 또한 특정 간격으로 NCR 내 모든 아티팩트를 자동으로 스캔하도록 주기를 설정할 수도 있습니다.

### 아티팩트 스캔

**Container** > **NHN Container Registry(NCR)** 페이지의 레지스트리 목록에서 업로드한 레지스트리의 **이미지 보기** 버튼을 클릭하면 컨테이너 이미지 목록을 볼 수 있습니다.
이미지 목록에서 원하는 이미지의 **아티팩트 보기** 버튼을 클릭하면 해당 이미지의 아티팩트 목록을 볼 수 있습니다.
아티팩트 목록에서 아티팩트를 선택한 후 **스캔**을 클릭하여 취약점 스캔을 실행합니다.

* 아티팩트의 **취약점** 열에는 다음 상태가 표시됩니다.
    * Not Scanned(-): 아티팩트가 스캔 된 적이 없습니다.
    * Scanning(진행중): 스캔 작업이 진행 중입니다.
    * Stopped(중지됨): **스캔 중지** 요청으로 스캔 작업이 취소되었습니다.
    * 아티팩트 스캔이 성공적으로 완료되면 발견된 총취약점 수 및 수정할 수 있는 취약점 수와 함께 전체 심각도 수준을 나타냅니다.

> [참고]
> 상태가 **Scanning**이 아니면 언제든지 스캔을 시작할 수 있습니다.

### 취약점 정보 조회

아티팩트 목록에서 아티팩트를 선택한 후 **이미지 스캔** 탭에서 자세한 취약점 정보를 확인할 수 있습니다.

### 스캔 설정

자동으로 취약점을 스캔하는 주기를 설정할 수 있습니다.
**Container** > **NHN Container Registry(NCR)** > **스캔 설정**을 클릭한 뒤 **자동 스캔**에서 설정할 주기를 선택합니다.

| 주기 | 설명 |
| --- | --- |
| None | 자동 스캔이 설정되지 않았습니다. |
| Hourly | 매 시간 시작 시 스캔을 실행합니다. |
| Daily | 매일 자정에 스캔을 실행합니다. UTC 시간을 사용하므로 실제로 한국 시간 기준 매일 오전 9시에 실행합니다. |
| Weekly | 매주 토요일 자정에 스캔을 실행합니다. UTC 시간을 사용하므로 실제로 한국 시간 기준 일요일 오전 9시에 실행합니다. |
| Custom | 사용자가 설정한 cron job에 따라 스캔을 실행합니다. |

> [참고]
> **Container** > **NHN Container Registry(NCR)** 페이지에서 **스캔 설정**을 하면 NCR 내 모든 레지스트리에 적용됩니다.

### CVE 허용 목록

스캔을 실행하면 이미지에 포함된 CVE(common vulnerabilities and exposures, 공통 취약성 및 노출)가 식별됩니다.
CVE의 심각도에 따라 이미지의 실행을 허용하지 않을 수 있습니다.
이 경우 사용자는 CVE 허용 목록을 만들어 특정 CVE를 무시할 수 있습니다. NCR 전체에 공통 CVE 허용 목록을 설정하거나 레지스트리별로 개별 CVE 허용 목록을 설정할 수 있습니다.

**NCR 전체의 공통 CVE 허용 목록 설정**
**Container** > **NHN Container Registry(NCR)** 페이지에서 **스캔 설정**을 클릭합니다. **공통 레지스트리 CVE 허용** > **허용 목록**에 무시할 CVE ID 목록을 입력합니다. 쉼표나 줄 바꿈을 사용하여 여러 CVE ID를 추가할 수 있습니다.

**레지스트리별 개별 CVE 허용 목록 설정**
**Container** > **NHN Container Registry(NCR)** 페이지의 레지스트리 목록에서 레지스트리를 선택한 후 **이미지 스캔** 탭의 **이미지 스캔 설정**을 클릭합니다. **CVE 허용** > **허용 목록**에 무시할 CVE ID 목록을 입력합니다. 쉼표나 줄 바꿈을 사용하여 여러 CVE ID를 추가할 수 있습니다.

> [참고]
> 개별 레지스트리에 허용 목록을 설정하면 공통 레지스트리 허용 목록은 더 이상 사용되지 않습니다.

### 레지스트리 설정

레지스트리별로 취약점 관련 설정을 할 수 있습니다.

**Push 시 자동 스캔**
사용자가 이미지를 push 하면 자동으로 스캔을 실행합니다.
**Container** > **NHN Container Registry(NCR)** 페이지에서 **레지스트리 생성**을 클릭하여 **Push 시 자동 스캔**을 설정합니다.

**이미지 배포 방지**
취약점의 심각도에 따라 이미지의 실행을 허용하지 않을 수 있습니다. 설정한 심각도를 포함하여 그 이상의 심각도가 검출된 이미지는 pull 할 수 없습니다. CVE 허용 목록에 추가된 취약점은 무시됩니다.
**Container** > **NHN Container Registry(NCR)** 페이지의 레지스트리 목록에서 레지스트리를 선택한 후 **이미지 스캔**탭의 **이미지 스캔 설정**을 클릭합니다. **Pull 방지**에 허용하지 않을 심각도를 선택합니다.

> [참고]
> 이미지 캐시 레지스트리에 배포 방지 설정을 하고 처음 이미지를 pull 하는 경우에는 이미지 캐시 레지스트리에 아직 이미지의 취약점 정보가 존재하지 않으므로 배포 방지 설정이 적용되지 않습니다.

## 이미지 신뢰 기능

NCR에 있는 이미지에 서명하고 서명을 검증하여 이미지의 무결성을 확인할 수 있습니다.

### 사전 준비

NCR은 sigstore/cosign 솔루션을 이용하여 이미지 서명 기능을 제공합니다. 이미지 신뢰 기능을 사용하려면 sigstore/cosign 클라이언트가 설치되어 있어야 합니다.
[sigstore/cosign](https://docs.sigstore.dev/cosign/installation/)를 참고하여 설치합니다.

**Windows**
[Cosign for Windows](https://github.com/sigstore/cosign/releases/download/v2.0.0/cosign-windows-amd64.exe)를 다운로드해 설치합니다.

### 키 페어 생성

아티팩트를 서명하고 검증하기 위한 키 페어를 로컬에 생성합니다.
명령어를 실행한 경로에 Private/Public 키 파일이 생성됩니다.

```bash
$ cosign generate-key-pair
Enter password for private key: 
Enter password for private key again: 
Private key written to cosign.key
Public key written to cosign.pub
```

### 아티팩트 서명

Private 키를 이용하여 서명하고 signature를 NCR에 저장합니다.

```bash
$ cosign sign --key {private key file} {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시

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

> [참고]
> 이미지 캐시 유형의 레지스트리에 있는 이미지는 서명이 불가합니다.

**아티팩트 서명 여부 확인**
아티팩트 목록의 **인증** 열에서 아티팩트들의 서명 여부를 확인할 수 있습니다.

### 아티팩트의 서명 검증

Public 키를 이용하여 위변조를 검증합니다.

```bash
$ cosign sign --key {public key file} {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시

```bash
$ cosign verify --key cosign.pub 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest

Verification for 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The signatures were verified against the specified public key

[{"critical":{"identity":{"docker-reference":"517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox"},"image":{"docker-manifest-digest":"sha256:5e42fbc46b177f10319e8937dd39702e7891ce6d8a42d60c1b4f433f94200bd2"},"type":"cosign container image signature"},"optional":{"Bundle":{"SignedEntryTimestamp":"MEYCIQDw+fMYgkFqzoAT2LOJaLogLyGKMzhcz31RZEcdc1+84wIhAJ46+JqazStGtkqaJWRcmRkk97/nJ4L0wrNXhj1JCifO","Payload":{"body":"eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiI1YzMxNmNjYjRmMTBjMDhkNmY4ODVjMDVkNzhlZjMyODliZDMxYjhhMTliMDAwZTAwOTkwMzcxM2Y0ZGRmYTViIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FWUNJUUR5WEtYSEpzamZNZDJIMjRxWkliTDFSOE1XTFV3K0pKdHg3Z1J5T1JEYTd3SWhBSXAvalNWQkhMdm5NczY4ZWVSMFBVUkZtQjI0b0VFRVN5NmZKVHhXT0JXVyIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGWTBWcEsyMU9UR05wYzBaSWQyVXlOamRhUzJsc01ERkNRMk0xTWdwTlowTkdVWGhDYkRsa2NGTlJOakowVmtrMFNsQmxLMGxRVjJ0VFFXc3JNRzlKWmtZMU9GRjJVMUpxTm1OcU1XSm1SWEpKUVhOVGNWVm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=","integratedTime":1679898462,"logIndex":16394784,"logID":"c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"}}}}]
```

> [참고]
> 다른 키로 여러 번 서명할 경우 모든 키로 검증이 가능합니다.

## 서비스 이용 권한

서비스 이용 권한을 이용하여 사용자별로 NCR 사용을 통제할 수 있습니다.

### 권한별 사용 기능

NCR 서비스 이용 권한은 다음과 같습니다.

| 권한 | 설명 |
| --- | --- |
| Project Admin<br>Project Member<br>Service Admin | NHN Container Registry(NCR) 서비스 Create(생성), Read(읽기), Update(갱신), Delete(삭제) |
| Service Viewer | NHN Container Registry(NCR) 서비스 Read(읽기) |
| Image Uploader | NHN Container Registry(NCR) 서비스 Read(읽기), 이미지 Create(생성)) |

NCR 서비스 이용 권한에 의해 사용할 수 있는 기능은 다음과 같습니다.

| Action | Project Admin<br>Project Member<br>Service Admin | Service Viewer | Image Uploader |
| --- | --- | --- | --- |
| 레지스트리 조회 | ✓ | ✓ | ✓ |
| 레지스트리 생성/삭제 | ✓ |  |  |
| 레지스트리 복제 목록 조회 | ✓ | ✓ | ✓ |
| 레지스트리 복제 생성/수정/삭제/실행 | ✓ |  |  |
| 레지스트리 복제 히스토리 조회 | ✓ | ✓ | ✓ |
| 이미지 보기 | ✓ | ✓ | ✓ |
| 이미지 Pull | ✓ | ✓ |  |
| 이미지 Push | ✓ |  | ✓ |
| 이미지 삭제 | ✓ |  |  |
| 아티팩트 보기 | ✓ | ✓ | ✓ |
| 아티팩트 삭제 | ✓ |  |  |
| 아티팩트 태그 조회 | ✓ | ✓ | ✓ |
| 아티팩트 태그 생성/삭제 | ✓ |  |  |
| 웹훅 조회 | ✓ | ✓ | ✓ |
| 웹훅 생성/수정/삭제 | ✓ |  |  |
| 이미지 정리 정책 조회 | ✓ | ✓ | ✓ |
| 이미지 정리 히스토리 조회 | ✓ | ✓ | ✓ |
| 이미지 정리 정책 생성/삭제/실행 | ✓ |  |  |
| 이미지 정리 규칙 활성화/비활성화 | ✓ |  |  |
| 이미지 보호 정책 조회 | ✓ | ✓ | ✓ |
| 이미지 보호 정책 추가/삭제 | ✓ |  |  |
