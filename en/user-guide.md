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

To use the registry service for the first time, you must first create a registry in the NCR Console. Go to the **Container > NHN Container Registry (NCR) > Management** service page and click the **Create Registry** button. After entering the name of the registry you want to create, click the OK button to create the registry.

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

To receive notifications on image changes, register your webhook settings in the NCR Console. Select the registry to configure the webhook on, and select the **Webhook** tab on the details pane at the bottom. Click the **Create Webhook** button. When the **Create Webhook** dialog box appears, set the properties and click the **OK** button. Currently, notification settings using HTTP(S) calls and Slack messenger are supported.

