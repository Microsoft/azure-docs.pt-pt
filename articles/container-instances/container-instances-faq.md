---
title: Perguntas mais frequentes
description: Answers for frequently asked questions related to the Azure Container Instances service
author: dkkapur
ms.topic: article
ms.date: 4/25/2019
ms.openlocfilehash: b5888efe210ab0f3794895d350c5647b6f685880
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484139"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Frequently asked questions about Azure Container Instances

This article addresses frequently asked questions about Azure Container Instances.

## <a name="deployment"></a>Implementação

### <a name="how-large-can-my-container-image-be"></a>How large can my container image be?

The maximum size for a deployable container image on Azure Container Instances is 15 GB. You might be able to deploy larger images depending on the exact availability at the moment you deploy, but this is not guaranteed.

The size of your container image impacts how long it takes to deploy, so generally you want to keep your container images as small as possible.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>How can I speed up the deployment of my container?

Because one of the main determinants of deployment times is the image size, look for ways to reduce the size. Remove layers you don't need, or reduce the size of layers in the image (by picking a lighter base OS image). For example, if you're running Linux containers, consider using Alpine as your base image rather than a full Ubuntu Server. Similarly, for Windows containers, use a Nano Server base image if possible. 

You should also check the list of pre-cached images in Azure Container Images, available via the [List Cached Images](/rest/api/container-instances/listcachedimages) API. You might be able to switch out an image layer for one of the pre-cached images. 

See more [detailed guidance](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) on reducing container startup time.

### <a name="what-windows-base-os-images-are-supported"></a>What Windows base OS images are supported?

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 base images

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`, `sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`,  `10.0.14393.x`

> [!NOTE]
> Windows images based on Semi-Annual Channel release 1709 or 1803 are not supported.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 and client base images (preview)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`, `10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`, `1809`, `10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`, `10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>What .NET or .NET Core image layer should I use in my container? 

Use the smallest image that satisfies your requirements. For Linux, you could use a *runtime-alpine* .NET Core image, which has been supported since the release of .NET Core 2.1. For Windows, if you are using the full .NET Framework, then you need to use a Windows Server Core image (runtime-only image, such as  *4.7.2-windowsservercore-ltsc2016*). Runtime-only images are smaller but do not support workloads that require the .NET SDK.

## <a name="availability-and-quotas"></a>Availability and quotas

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>How many cores and memory should I allocate for my containers or the container group?

This really depends on your workload. Start small and test performance to see how your containers do. [Monitor CPU and memory resource usage](container-instances-monitor.md), and then add cores or memory based on the kind of processes that you deploy in the container. 

Make sure also to check the [resource availability](container-instances-region-availability.md#availability---general) for the region you are deploying in for the upper bounds on CPU cores and memory available per container group. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>What underlying infrastructure does ACI run on?

Azure Container Instances aims to be a serverless containers-on-demand service, so we want you to be focused on developing your containers, and not worry about the infrastructure! For those that are curious or wanting to do comparisons on performance, ACI runs on sets of Azure VMs of various SKUs, primarily from the F and the D series. We expect this to change in the future as we continue to develop and optimize the service. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>I want to deploy thousand of cores on ACI - can I get my quota increased?
 
Yes (sometimes). See the [quotas and limits](container-instances-quotas.md) article for current quotas and which limits can be increased by request.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Can I deploy with more than 4 cores and 16 GB of RAM?

Not yet. Currently, these are the maximums for a container group. Contact Azure Support with specific requirements or requests. 

### <a name="when-will-aci-be-in-a-specific-region"></a>When will ACI be in a specific region?

Current region availability is published [here](container-instances-region-availability.md#availability---general). If you have a requirement for a specific region, contact Azure Support.

## <a name="features-and-scenarios"></a>Features and scenarios

### <a name="how-do-i-scale-a-container-group"></a>How do I scale a container group?

Currently, scaling is not available for containers or container groups. If you need to run more instances, use our API to automate and create more requests for container group creation to the service. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>What features are available to instances running in a custom VNet?

You can deploy container groups in an Azure virtual network of your choice, and delegate private IPs to the container groups to route traffic within the VNet across your Azure resources. Deployment of a container group into a virtual network is currently in preview, and some aspects of this feature may change prior to general availability (GA). See [Preview limitations](container-instances-vnet.md#preview-limitations) for updated information.

## <a name="pricing"></a>Preços

### <a name="when-does-the-meter-start-running"></a>When does the meter start running?

Container group duration is calculated from the time that we start to pull your first container's image (for a new deployment) or your container group is restarted (if already deployed), until the container group is stopped. See details at [Container Instances pricing](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Do I stop being charged when my containers are stopped?

Meters stop running once your entire container group is stopped. As long as a container in your container group is running, we hold the resources in case you want to start the containers up again. 

## <a name="next-steps"></a>Passos seguintes

* [Learn more](container-instances-overview.md) about Azure Container Instances.
* [Troubleshoot common issues](container-instances-troubleshooting.md) in Azure Container Instances.