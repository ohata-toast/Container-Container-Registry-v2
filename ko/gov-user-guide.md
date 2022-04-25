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

* User Access Key ID는 NHN Cloud 계정당 5개, IAM 계졍당 5개까지 생성 할 수 있습니다.

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
docker tag ubuntu:18.04 example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
```

> [참고]
> 컨테이너 이미지 이름으로는 영어 소문자, 숫자, 일부 특수문자(-, _, /, .) 조합만 허용됩니다. 리포지토리 이름은 레지스트리 주소를 포함해 최대 255자, 태그 이름은 최대 128자로 제한됩니다. 이미지 이름이 길면 사용하기 불편할 수 있습니다. 적당한 길이의 이름을 사용하시기 바랍니다.

이제 Docker 명령줄 도구의 **push** 명령을 사용해 컨테이너 이미지를 사용자 레지스트리에 저장할 수 있습니다.

```bash
docker push {사용자 레지스트리 주소}/{이미지 이름}:{태그 이름}
```

* 예시
```bash
$ docker push example-kr1-registry.container.cloud.toast.com/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.cloud.toast.com/ubuntu]
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


### Garbage Collection

레지스트리, 이미지 또는 아티팩트를 삭제할 경우 메타데이터만 삭제되기 때문에 저장 공간은 바로 반환되지 않습니다. 그러므로 리소스를 삭제한 뒤 저장 공간을 확보하려면 Garbage Collection 기능을 사용해야 합니다. NHN Cloud Console에서 Garbage Collection을 사용하려면 **Container > NHN Container Registry(NCR) > Garbage Collection** 페이지로 이동합니다.

Garbage Collection을 본격적으로 실행하기 전 **테스트 실행**을 통해 삭제 대상 목록과 저장 공간 확보 예상값을 확인할 수 있습니다. **Garbage Collection** 페이지에서 **지금 실행** 또는 **테스트 실행** 버튼을 클릭하면 실행할 수 있습니다. 실행 결과에 따라 **실행 상태** 항목에 **성공** 또는 **실패**로 나타납니다. 로그 보기 버튼을 클릭하면 Garbage Collection 결과를 확인할 수 있습니다.

> [참고]
>
> 업로드 중인 이미지 또는 아티팩트에 손상을 가하지 않도록, Garbage Collection을 실행하더라도 최근 2시간 이내에 업로드된 이미지 또는 아티팩트는 정리하지 않습니다.
