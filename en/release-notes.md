## Container > NHN Container Registry (NCR) > Release Notes

### August 27, 2024

#### Added Features

* Released Public APIs for NCR.
  * For more information about Public API, see [API Guide](/Container/NCR/en/public-api/).
* You receive notifications about events that occur in the registry through Resource Watcher.
  * For more information, see [Resource Watcher](/Governance%20&%20Audit/Resource%20Watcher/en/overview).
* You can view a list of image signatures

#### Feature Updates

* Added the feature to replicate between projects.


### May 28, 2024

#### Added Features
* Added the featue to block deployment of unsigned images.

#### Feature Updates
* Modified image cleanup/protection policies to identify images that contain `/`.
* Expanded artifact lookup items.
* Modified image cleanup policies to be unlimited in number.

### February 27, 2024

#### Added Features
* You can checkout events occurred in the NCR console from CloudTrail.
* Added an overwrite option to the image replication feature.

#### Feature Updates
* Made modifications so that image keyword search is available.

### November 28, 2023

#### Added Features
* Added the feature to set whether to use Public URIs.
* Added the `Image Uploader` permission, which allows you to upload images only.

### August 29, 2023

#### Feature Updates

* Added the feature to manage OCI artifacts.
* Added the Quota feature.

### May 30, 2023

#### Feature Updates

* Added the feature to replicate pull between regions.

### March 28, 2023

#### Added Features

* Added the image trust feature.

### January 31, 2023

#### Added Features

* Added the image vulnerability scanning feature.

### November 29, 2022

#### Added Features

* Added the image cache feature.

### September 27, 2022

#### Added Features

* Added the Private URI feature
  * Added the Private URI feature that allows users to use the NCR service from an instance unconnected with the internet gateway.
  * For more details, see [Private URI User Guide](./user-guide/#private-uri).

### July 26, 2022

#### Added Features

* Added image cleanup and image protection features.

### May 24, 2022

#### Feature Updates

* Made modifications so that nhncloud.com is used as the registry domain.

### April 26, 2022

#### Feature Updates

* Made modifications so that the Garbage Collection feature is not exposed to users.

### March 29, 2022

#### Added Features

* Added the inter-region replication feature.

#### Feature Updates

* Changed the name of the Container Registry service to NHN Container Registry (NCR).

### January 25, 2022

#### Bug Fixes
* Fixed an issue where service activation failed intermittently.
* Fixed an issue where the token validity period was set shorter than intended.
* Subdivided error messages generated due to invalid endpoint when creating webhooks.


### November 23, 2021
#### New Service Release
* Provides a more stable and comfortable environment with improvements from the previous version of Container Registry service.
* Provides various features by supplementing the drawbacks of the previous version of Container Registry service. Supported features will be updated continuously.
