## Container > NHN Container Registry (NCR) > User Guide

## Prerequisites
### Install Docker
The NHN Container Registry (NCR) service is a service for storing and deploying Docker container images. To work with container images, you must first have Docker installed in your environment.

#### Windows
Download and install [Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows) from Docker Hub.

#### macOS
Download and install [Docker Desktop for Mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac) from Docker Hub.

#### Linux
Depending on your Linux distribution, the installation process is different. If you are using a distribution other than CentOS 7 or Ubuntu, check [Install Docker Engine](https://docs.docker.com/engine/install).

* CentOS 7
```
// Install packages required for Docker installation
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2

// Add the Docker repository
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

// Install Docker
$ sudo yum install -y docker-ce docker-ce-cli containerd.io

// Start the Docker service
$ sudo systemctl start docker
```

* Ubuntu
```
// Update the APT index
$ sudo apt-get update

// Install packages to use repository over HTTPS
$ sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common

// Add the GPG Key and verify
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

// Add the repository and update the APT index
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt-get update

// Install Docker
$ sudo apt-get install docker-ce docker-ce-cli containerd.io

// Start the Docker service
$ sudo systemctl start docker
```



### Check the User Access Key and Secret Key

You need User Access Key and Secret Key to log in to your user registry using the Docker command-line tool. User Access Key and Secret Key can be created in Account > **API Security Setting** page of the NHN Cloud Console.

* You can create up to 5 User Access Key IDs per NHN Cloud ID and 5 per IAM ID.

* If the User Secret Key is lost, it cannot be verified again and must be regenerated. For security, please keep the issued key in a safe place.

* It is recommended to change the User Access Key every 90 days.



## Use a Container Registry

> [Note]
> Users with member privileges cannot use the feature to store and delete container images.

### Create a User Registry

To use the registry service for the first time, you must first create a registry in the NCR Console. Go to the **Container > NHN Container Registry (NCR) > Management** service page and click the **Create Registry** button. After entering the name of the registry you want to create, click the Confirm button to create the registry.

### Check the User Registry Address
The address of the registry you created can be found in the registry list on the **Container > NHN Container Registry (NCR) > Management** service page.

### Log in to the User Registry
You need to use the Docker command-line tool to store container images or import them into your environment of choice. You must be logged in to access the user registry using the Docker command-line tool. After using the `docker login` command, enter the User Access Key of your NHN Cloud user account in `Username` and the Secret Key in `Password`.

```bash
$ docker login {user registry address}
Username: {NHN Cloud user account User Access Key}
Password: {NHN Cloud user account User Secret Key}
Login Succeeded
```

> [Note]
> The maximum validity period of a token obtained by the `docker login` command is 12 hours.



### Store a Container Image (Push)

To store the container image to the user registry, the name of the image to be uploaded must be set in the form of image name and tag including the user registry address. This can be specified using the **tag** command of the Docker command-line tool.

```bash
docker tag {image name}:{tag} {user registry address}/{image name}:{tag}
```

* Example

```bash
docker tag ubuntu:18.04 example-kr1-registry.container.nhncloud.com/ubuntu:18.04
```

> [Note]
> For container image names, only the combination of English lowercase letters, numbers, and some special characters (-, _, /, .) is allowed. Repository names are limited to a maximum of 255 characters including the registry address, and tag names are limited to a maximum of 128 characters. Long image names can be inconvenient to use. It is recommended to use a name of a suitable length.

You can now use the **push** command of the Docker command-line tool to store container images to your user registry.

```bash
docker push {user registry address}/{image name}:{tag name}
```

* Example
```bash
$ docker push example-kr1-registry.container.nhncloud.com/ubuntu:18.04
The push refers to repository [example-kr1-registry.container.nhncloud.com/ubuntu]
16542a8fc3be: Pushed
6597da2e2e52: Pushed
977183d4e999: Pushed
c8be1b8f4d60: Pushed
18.04: digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320 size: 1152
```

### View a Container Image
Stored container images can be viewed on the NCR Console.

* Image list
    You can view the list of container images by clicking the **View Image** button of the uploaded registry in the registry list on the **Container > NHN Container Registry (NCR) > Management** service page.

* Artifact list
    You can view the list of artifacts of an image by clicking the **View Artifact** button of the image in the image list. You can select a specific artifact from the artifact list to see details and a tag list.

* Tag list
    You can view the list of tags assigned to an artifact by clicking the artifact in the artifact list. You can create a new tag or search for a tag and delete it.

### Download a Container Image (Pull)
You can download an image using the **pull** command of the Docker command-line tool. Before that, you need to check the information of the image to download from the NCR Console.

```bash
docker pull {user registry address}/{image name}:{tag name}
```

* Example
```bash
$ docker pull example-en1-registry.container.nhncloud.com/ubuntu:18.04
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



## Manage a Container Registry

### Delete Container Images and Artifacts

If you no longer use an image stored in the registry, you can delete it from the NCR Console. To delete an image, select the image to delete on the image list view and click the **Delete Image** button. Likewise, to delete an artifact, select the artifact to delete in the artifact list view and click the **Delete Artifact** button.

### Create a Container Image Tag

You can create tags in the NCR Console without using the Docker command-line tool. On the artifact list view, select an artifact to add a tag to, and then select the **Tag** tab on the detailed information view at the bottom. You will then see a list of tags assigned to the currently selected artifact. If you click the **Create Tag** button, the **Create Tag** dialog box appears, and you can create a new tag by entering the tag name of your choice.

### Delete a Container Image Tag

Likewise, if you have tags that you no longer use, you can delete them in the NCR Console. Similar to creating a tag, go to the tag list view and select a tag to delete. If there are many tags and the tag does not appear in the tag list, you can use the tag search function to find the tags you want to delete. Select the tag to delete and click the **Delete Tag** button to delete the selected tag.

### Registry Webhook Settings

To receive notifications on image changes, register your webhook settings in the NCR Console. Select the registry to configure the webhook on, and select the **Webhook** tab on the details pane at the bottom. Click the **Create Webhook** button. When the **Create Webhook** dialog box appears, set the properties and click the **Confirm** button. Currently, notification settings using HTTP(S) calls and Slack messenger are supported.


### Container Image Cleanup

You can make settings to clean up (delete) the images and artifacts stored in a registry according to policies in the NCR Console. To use the image cleanup policy, select a repository to apply and click the **Image Cleanup** tab on the details pane at the bottom. 
Up to 15 image cleanup policies can be registered.

> [Caution] 
If the same policy is set for image cleanup and image protection, the image protection policy takes priority and the image cleanup feature may not work properly.

#### Image Cleanup Policy Setting

If you click the **Add Cleanup Policy** button in the **Policy Setting** tab, the **Add Cleanup Policy** dialog box appears, and you can set a new cleanup policy by entering the desired image, tag, and policy. 
The image cleanup policy is enabled and created. If you want to temporarily disable the policy, you can use the **Enable/Disable feature**.

* If a cleanup policy is added or deleted during the image cleanup operation, it will be reflected from the next image cleanup execution.

Images, tags, and cleanup policies are treated as AND conditions.

* Example of deleting all artifacts: `the entire image + unused tag names + the corresponding artifacts all excluded, and clean up`

> [Note] 
You cannot add duplicate cleanup policies with the same image, tag, and policy to the registry.
>
> `Except for artifacts with the pushed and pulled date of N, the cleanup` policy deletes artifacts that have been pushed and pulled after the date of N.

#### Delete Image Cleanup Policy

You can delete a cleanup policy by selecting it at the bottom of the **Policy Setting** tab and clicking the **Delete Cleanup Policy** button.

#### Test Run

You can test the image cleanup policy you have set by clicking the **Test Run** button on the **Policy Setting** tab. 
You can check the test run results in the **View History** tab.

#### Run Immediately

On the **Policy Setting** tab, click the **Run Immediately** button to manually run the image cleanup policy you have set. 
Execution results can be found in the **View History** tab.

#### Cleanup Cycle Setting

You can set a image cleanup policy to run cleanup automatically on a periodic basis. 
Repeat cycle uses cron expression (\* \* \* \* \*) and the meaning of each field is as follows.

| Field name | Acceptable range of values | Allowed special characters |
| --- | --- | --- |
| Minute | 0-59 | `*` `/` `,` `-` |
| Hour | 0-59 | `*` `/` `,` `-` |
| Date | 1-31 | `*` `/` `,` `?` |
| Month | 1-12<br>JAN-DEC | `*` `/` `,` `-` |
| Day | 0-6<br>SUN-SAT | `*` `/` `,` `?` |

> [Note]
> The time zone used with cron expressions is Coordinated Universal Time (UTC).

#### View History

You can view the image cleanup history in the **View History** tab. 
You can check the history details by clicking the queried information at the bottom.

### Container Image Protection

You can set the image protection feature to protect the images and artifacts stored in a registry from deletion and change in the NCR Console. To use the image protection feature, select a registry to apply and click the **Image Protection** tab on the details pane at the bottom. 
Up to 15 image protection policies can be registered.

#### Add Image Protection Policy

If you click the **Add Protection Policy**, **Add Protection Policy** dialog box appears, and you can set a new protection policy by entering the desired protection policy.
 The image protection policy is enabled and created. If you want to temporarily disable the policy, you can use the **Enable/Disable feature**.

> [Note] 
You cannot add duplicate protection policies with the same image and tag to the registry.

#### Delete Image Protection Policy

You can delete a image protection policy by selecting a protection policy to delete at the bottom of the **Image Protection** tab and clicking the **Delete Protection Policy** button.

<span id="private-uri"></span>
## Use Private URI
Private URI is an address for NCR that can be used within a VPC network of NHN Cloud. If you want to use the NCR service in an instance disconnected from the external network without having to connect to the internet gateway for enhanced security, you can use the Private URI feature.

> [Note]
You need to create NCR and Object Storage service gateway to use the Private URI in an instance unconnected with the internet gateway.

> [Note] 
Instance, Service Gateway, Object Storage and NCR must all use the same region.

### Create an NCR Service Gateway
Go to the **Network > Service Gateway** page and click **Create Service Gateway**. Enter **Name**, **VPC**, and **Subnet** of the service gateway to create, and select **NCR**in **Service** and click **Confirm** to create the NCR service gateway. 
![ncr_c001_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c001.png)

### Create an Object Storage Service Gateway
To import images from NCR using Private URI, you need to create a service gateway for Object Storage. The service gateway is required because NCR uses Object Storage to store image layers. When downloading images, NCR is accessed to import the image manifest before accessing Object Storage to download the actual image layer.

Go to the **Network > Service Gateway** page and click **Create Service Gateway**. Enter **Name**, **VPC**, and **Subnet** of the service gateway to create, select **Object Storage** in **Service** and click **Confirm** to create the Object Storage service gateway. 
![ncr_c002_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c005.png)

### Register Host
You must configure the domain and IP in the host file so that the NCR registry can be used through Private URI from an instance unconnected with the internet gateway. 
Enter the IP address of  the NCR service gateway, NCR Private Endpoint, the IP address of Object storage service gateway, and the Object Storage domain in the host file so that the IP of Private Endpoint can be found from the instance.

The IP addresses of NCR and the Object Storage service gateway can be found on the **Network > Service Gateway** page. 
![ncr_c003_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c003.png)

The NCR Private URI can be found on the **Basic Information** tab under the  **Container > NHN Container Registry (NCR) > Management** page by selecting the registry. The private endpoint is the private URI path excluding the registry name. 
![ncr_c004_20220927](https://static.toastoven.net/prod_ncr/20220927/ncr_ko_c004.png)

* Example
```
Private URI: private-example-kr1-registry.container.nhncloud.com/hello-world
Private Endpoint: private-example-kr1-registry.container.nhncloud.com
```

**Windows** 
Open `C:\Windows\System32\drivers\etc\hosts` and add the following content:
```
{NCR Services Gateway IP Address} {NCR Private Endpoint}
{Object Storage Services Gateway IP Address} {Object Storage Domain}
```

**Linux**
Open `/etc/hosts` and add the following content.
```
{NCR Services Gateway IP Address} {NCR Private Endpoint} 
Object Storage Services Gateway IP Address} {Object Storage Domain}
```


### Registry Work via Private URI
Connect to the instance and log in to the registry by running the `docker login` command. Depending on the instance configuration, you may need to prefix the following commands with `sudo` :
```shell
$ docker login {user private registry address}
Username: {NHN Cloud user account User Access Key}
Password: {NHN Cloud user account User Secret Key}
Login Succeeded
```

* Example
```shell
$ docker login private-example-kr1-registry.container.nhncloud.com
Username: hello-world
Password:
Login Succeeded
```

Run registry works such as `docker pull` to download sample images from the registry. Check the information of the image to be imported from the NCR Console.
```shell
$ docker pull {User Private URI}/{Image Name}:{Tag Name}
```

* Example
```
$ docker pull private-example-kr1-registry.container.nhncloud.com/hello-world/ubuntu:18.04
18.04: Pulling from ubuntu
5bed26d33875: Pull complete
f11b29a9c730: Pull complete
930bda195c84: Pull complete
78bf9a5ad49e: Pull complete
Digest: sha256:e5dd9dbb37df5b731a6688fa49f4003359f6f126958c9c928f937bec69836320
Status: Downloaded newer image for private-example-kr1-registry.container.nhncloud.com/ubuntu:18.04
private-example-kr1-registry.container.nhncloud.com/ubuntu:18.04

$ docker images
REPOSITORY                                              TAG     IMAGE ID        CREATED         SIZE
example-kr1-registry.container.nhncloud.com/ubuntu   18.04   4e5021d210f6    12 days ago     64.2MB
```

## Replicate a Container Image

The replication feature provided by NCR replicates images between regions. The specific characteristics of the replication feature are as follows.

* Replicate images from a region (A) to a target region (B).
* When an image is uploaded to a region (A), the image is automatically replicated to a target region (B).
* Users can start replication directly if they want.
* If the same image already exists in a target region (B), it will not be replicated.
* Even when the original image is deleted from a region (A), the replicated image is not deleted from a target region (B).

To use the image replication feature, click the **Replication** tab in the NCR Console.

### Replication Configuration Settings

Click **Create Replication** and enter the required information to configure replication in the **Create Replication** dialog box.

> [Note]
The status can be displayed as **Disabled** immediately after creating replication. When replication is set ready, the status turns to **Enabled**. 
If the status doesn’t change after a few minutes, click **Refresh**.

### Auto Replication

Once replication configuration is complete, when an image is uploaded to a region, it is automatically replicated to the target region.

> [Caution] 
Only newly uploaded images are automatically replicated. 
If you want to replicate the uploaded image before configuring replication, use the **Manual Replication** feature.

### Manual Replication

After clicking **Run Replication** , click **Confirm** in the **Run Replication** dialog box to start replication.

> [Caution] 
If replication is executed before the Garbage Collection feature is run, the capacity of the image replicated in the target region (B) may be smaller than that of the original image. 
After a certain period of time, the capacity of the original image becomes smaller.

### Replication History

You can check the replication progress and history in the replication history. To check the replication history, click the configured replication and click the **Replication History** tab on the**View Details** page at the bottom. 
You can find the history details by clicking the searched information at the bottom.

## Use Image Cache

Provides a feature to download and cache images from a source registry (a different remote registry).
When an image pull request is made to an image cache type registry, images are provided based on the classification as follows.

| Classification | Image provided |
| --- | --- |
| Cached image different from the registry image | Source registry image downloaded and provided |
| Cached image same with the source registry image | Cached image provided |
| Source registry not connected | Cached image provided |
| Image deleted from the source registry | Image not provided |

The image cache type registry downloads and provides the source registry image when the requested image is not found as follows. 
![D-NCR_imagecache_01](https://static.toastoven.net/prod_ncr/20221129/D-NCR_imagecache_01.png)

### Create Image Cache

To use the image cache, you must register a source registry. Click the **image cache** tab from the NCR console and click **Create Image Cache**. Enter the source registry information on the**Create Image Cache** dialog box.
The supported source registry type, URL, Access ID, and Access Secret are as follows.

| Source Registry Type | URL | Access ID | Access Secret |
| --- | --- | --- | --- |
| NHN Container Registry(NCR) | `https://xxxxxxxx-$REGION-registry.container.nhncloud.com` | User Access Key ID | Secret Access Key |
| Amazon Elastic Container Registry | `https://$AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazoneaws.com` | IAM Access Key ID | IAM Secret Access Key |
| Azure Container Registry | `https://$REGISTRY_NAME.azurecr.io` | Access Key User Name | Access Key Password |
| Google Cloud Container Registry | `https://$REGION` | \_json_key | Private key of service account (JSON type) |
| Docker Hub | `https://hub.docker.com` | Username | Password |
| Docker Registry | `docker-registry address` | Username | Password |
| Harbor | `Harbor address` | Username | Password |
| Quay | `https://quay.io` | json_file | {<br>"account_name": "$사용자 계정",<br>"docker_cli_password": "$Quay에서 생성한 암호화된 Password"<br>} |

> [Note]
The allowed source registry ports are 80 and 443.

### Modify Image Cache

You can change the source registry. Click **Image Cache**  >  **Modify Image Cache** and enter the source registry information on the **Modify Image Cache** dialog box.

### Delete Image Cache

You can delete the image cache you no longer use. Select an image cache to delete from **Image Cache** on the NCR console and click  the **Delete Image Cache** button.

> [Note]
An image cache cannot be deleted if a registry targeting that image cache exists.

### Create an Image Cache Type Registry

To create an image cache type registry, click **Create Registry** on the **Management** tab. Select **Image Cache** for purpose of use in the **Create Registry** dialog box and select an image to cache.

> [Note]
You cannot proceed with image push in an image cache type registry.

> [Note]
In an image cache type registry, an image cleanup policy of deleting artifacts except ` artifacts that have been pulled within 7 days` will be added.
> * You can have a different cached image management policy for each registry by adding image cleanup/protection policies.

> [Note]
When using the container image replication feature, an image cache type registry is changed to a normal type registry and replicated.

### Import an Image from the image cache type registry

You can use the pull command of the Docker command-line tool to import images from an image cache type registry.

```
docker pull {Address of an user image cache type registry}/{Registry name of a source registry}/{Image name}:{Tag name}
```

* Example

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

If you are importing an image from an official image or single-level repository, you must use  `library as the registry name of the source registry`.

```
docker pull {Address of an user image cache type registry}/library/hello-world:latest
```

* Image example

```
$ docker pull example-kr1-registry.container.nhncloud.com/docker/library/hello-world:latest

$ docker images
REPOSITORY                                                                   TAG             IMAGE ID       CREATED         SIZE
example-kr1-registry.container.nhncloud.com/docker/library/hello-world       latest          feb5d9fea6a5   14 months ago   13.3kB
```

> [Note]
It takes up to 200 seconds to process image caching. Image caching is not processed if downloading an image from the source registry takes more than 200 seconds.
> * When you are importing the same image again, the image continues to be downloaded.

> [Note]
If the capacity is increased even though image caching has not been processed, the increased capacity will be restored after 2 hours.

### Delete Image Cache Type Registry

You can delete the image cache type registry you no longer use. Select a registry to delete from **Management** on the NCR console and click the **Delete Registry** button.

## Image Vulnerability Scanning

NCR provides the vulnerability scanning feature for images. If you enable the NCR service, the vulnerability scanning is available by default.
You can manually start scanning of selected artifacts. You can also set the cycle to automatically scan all artifacts in NCR at specific intervals.

### Scan Artifacts

You can view the list of container images by clicking the **View Image** button of the uploaded registry in the registry list on the **Container** > **NHN Container Registry (NCR)** page.
You can view the list of artifacts for an image by clicking the **View Artifact** button for the image from the image list.
Start vulnerability scanning by clicking **Scan** after selecting an artifact from the artifact list.

* The following status is shown in the **Vulnerability** column for the artifact.
    * Not Scanned (-): Artifact is not scanned.
    * Scanning (In progress): Scanning is in progress.
    * Stopped (Stopped): Scanning is canceled upon **Stop Scanning** request.
    * If the artifact scanning completes successfully, it indicates the overall severity level, along with the total number of vulnerabilities found and the number of vulnerabilities that can be fixed.

> [Note]
> You can start scanning at any time as long as the status is not **Scanning**.

### Retrieve Vulnerability Information

You can view the vulnerability details from the **Image Scan** tab after selecting an artifact from the artifact list.

### Scan Settings

You can set a cycle to automatically scan vulnerabilities.
Select a cycle for scanning from **Auto Scan** by clicking **Scan Settings** on the **Container** > **NHN Container Registry(NCR)** page.

| Cycle | Description |
| --- | --- |
| None | Auto scan is not set. |
| Hourly | Run a scan at the start of every hour . |
| Daily | Run a scan at midnight every day. Since the UCT time is used, scan actually runs every day at 9:00 AM Korean time. |
| Weekly | Run a scan every Saturday at midnight. Since the UCT time is used, scan actually runs on Sunday at 9:00 AM Korean time. |
| Custom | Run a scan according to the cron job configured by the user. |

> [Note]
> If you set a cycle from **Scan Settings** on the **Container** > **NHN Container Registry(NCR)** page, it is applied to all registries in NCR.

### CVE Allow List

If you start scanning, the CVE (Common Vulnerabilities and Exposures) contained in the image are identified. The image may not be allowed to run depending on the severity of the CVE.
In this case, the user can ignore a specific CVE by creating the CVE allow list.    
You can set a common CVE allow list across NCR or individual CVE allow list per registry.

**Set a Common CVE Allow List across NCR**
</br>Click **Scan Settings** on the **Container** > **NHN Container Registry(NCR)** page. Enter a CVE ID list to be ignored on **Allow Common Registry CVE** > **Allow List**. You can add multiple CVE IDs using commas or line breaks. 

**Set an Individual CVE Allow List per Registry**
</br>Click **Image Scan Settings** from the **Image Scan** tab after selecting a registry from the registry list on the **Container** > **NHN Container Registry(NCR)** page. Enter a CVE ID list to be ignored on **Allow CVE** > **Allow List**. You can add multiple CVE IDs using commas or line breaks.

> [Note]
> If you set an allow list for individual registry, the common allow list for registries is no longer used.

### Registry Settings

You can configure settings for vulnerabilities per registry.

**Auto Scan When Push**
</br>When the user pushes an image, scan is automatically run.
</br>Set **Auto Scan When Push** by clicking **Create Registry** on the **Container** > **NHN Container Registry(NCR)** page.

**Prevent Image Deployment**
</br> You may not allow the execution of the image depending on the severity of the vulnerability. You cannot pull images with detected vulnerabilities above the set severity. The vulnerabilities added to the CVE allow list are ignored.
</br> Click **Image Scan Settings** from the **Image Scan** tab after selecting a registry from the registry list on the **Container** > **NHN Container Registry (NCR)** page. Select a severity level not to be allowed in **Prevent Pull**.

> [Note]
> When you pull an image for the first time after adding a setting to prevent image deployment in the image cache registry, the setting is not applied because the vulnerability information of the image does not yet exist in the image cache registry.


## Image Trust

You can verify the integrity of an image by signing the image in NCR and verifying the signature.

### Prerequisites

NCR provides the image signature feature by using the sigstore/cosign solution. To use the image trust feature, you must install the sigstore/cosign client.
Refer to [sigstore/cosign](https://docs.sigstore.dev/cosign/installation/) to install the client.

**Windows**
Downloand and install [Cosign for Windows](https://github.com/sigstore/cosign/releases/download/v2.0.0/cosign-windows-amd64.exe).

### Create Key Pair

Create a key pair in your local in order to sign and verify artifacts.
Private and public key files are created in the path where the command is executed.

```bash
$ cosign generate-key-pair
Enter password for private key: 
Enter password for private key again: 
Private key written to cosign.key
Public key written to cosign.pub
```

### Sign Artifact

Sign using the private key and store the signature in NCR.

```bash
$ cosign sign --key {private key file} {user registry address}/{image name}:{tag name}
```

* Example

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

> [Note]
> You cannot sign images in registries of image cache type.

**Check Artifact Signature**
You can find whether artifacts are signed in the **Authentication** column of the artifact list.

### Verify Artifact Signature

Verify whether the signature is forged or tampered using the public key.

```bash
$ cosign sign --key {public key file} {user registry address}/{image name}:{tag name}
```

* Example

```bash
$ cosign verify --key cosign.pub 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest

Verification for 517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox:latest --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The signatures were verified against the specified public key

[{"critical":{"identity":{"docker-reference":"517a8ef5-kr1-registry.container.nhncloud.com/hy/busybox"},"image":{"docker-manifest-digest":"sha256:5e42fbc46b177f10319e8937dd39702e7891ce6d8a42d60c1b4f433f94200bd2"},"type":"cosign container image signature"},"optional":{"Bundle":{"SignedEntryTimestamp":"MEYCIQDw+fMYgkFqzoAT2LOJaLogLyGKMzhcz31RZEcdc1+84wIhAJ46+JqazStGtkqaJWRcmRkk97/nJ4L0wrNXhj1JCifO","Payload":{"body":"eyJhcGlWZXJzaW9uIjoiMC4wLjEiLCJraW5kIjoiaGFzaGVkcmVrb3JkIiwic3BlYyI6eyJkYXRhIjp7Imhhc2giOnsiYWxnb3JpdGhtIjoic2hhMjU2IiwidmFsdWUiOiI1YzMxNmNjYjRmMTBjMDhkNmY4ODVjMDVkNzhlZjMyODliZDMxYjhhMTliMDAwZTAwOTkwMzcxM2Y0ZGRmYTViIn19LCJzaWduYXR1cmUiOnsiY29udGVudCI6Ik1FWUNJUUR5WEtYSEpzamZNZDJIMjRxWkliTDFSOE1XTFV3K0pKdHg3Z1J5T1JEYTd3SWhBSXAvalNWQkhMdm5NczY4ZWVSMFBVUkZtQjI0b0VFRVN5NmZKVHhXT0JXVyIsInB1YmxpY0tleSI6eyJjb250ZW50IjoiTFMwdExTMUNSVWRKVGlCUVZVSk1TVU1nUzBWWkxTMHRMUzBLVFVacmQwVjNXVWhMYjFwSmVtb3dRMEZSV1VsTGIxcEplbW93UkVGUlkwUlJaMEZGWTBWcEsyMU9UR05wYzBaSWQyVXlOamRhUzJsc01ERkNRMk0xTWdwTlowTkdVWGhDYkRsa2NGTlJOakowVmtrMFNsQmxLMGxRVjJ0VFFXc3JNRzlKWmtZMU9GRjJVMUpxTm1OcU1XSm1SWEpKUVhOVGNWVm5QVDBLTFMwdExTMUZUa1FnVUZWQ1RFbERJRXRGV1MwdExTMHRDZz09In19fX0=","integratedTime":1679898462,"logIndex":16394784,"logID":"c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"}}}}]
```

> [Note]
> You can verify with any key if you sign multiple times with different keys.

## Service Permission

You can control the use of NCR for each user by using the service permissions.

### Features for Permission

The service permissions of the NCR service are as follows.

| Permission | Description |
| --- | --- |
| Project Admin<br>Project Member<br>Service Admin | Create, Read, Update, Delete the NHN Container Registry (NCR) service |
| Service Viewer | Read the NHN Container Registry (NCR) service |

The features available by the NCR service permissions are as follows.

| Action | Project Admin<br>Project Member<br>Service Admin | Service Viewer |
| --- | --- | --- |
| View registries | ✓ | ✓ |
| Create/Delete registries | ✓ |  |
| View a registry replication list | ✓ | ✓ |
| Create/Modify/Delete/Run registry replications | ✓ |  |
| View a registry replication history | ✓ | ✓ |
| View images | ✓ | ✓ |
| Pull images | ✓ | ✓ |
| Push images | ✓ |  |
| Delete images | ✓ |  |
| View artifacts | ✓ | ✓ |
| Delete artifacts | ✓ |  |
| View artifact tags | ✓ | ✓ |
| Create/Delete artifact tags | ✓ |  |
| View webhook | ✓ | ✓ |
| Create/Modify/Delete webhooks | ✓ |  |
| View image cleanup policy | ✓ | ✓ |
| View image cleanup history | ✓ | ✓ |
| Create/Delete/Run image cleanup policy | ✓ |  |
| Enable/Disable image cleanup rule | ✓ |  |
| View image protection policy | ✓ | ✓ |
| Add/Delete image protection policy | ✓ |  |