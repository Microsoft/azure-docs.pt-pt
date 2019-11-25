---
title: Container image storage
description: Details on how your Docker container images are stored in Azure Container Registry, including security, redundancy, and capacity.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456202"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Container image storage in Azure Container Registry

Every [Basic, Standard, and Premium](container-registry-skus.md) Azure container registry benefits from advanced Azure storage features like encryption-at-rest for image data security and geo-redundancy for image data protection. The following sections describe both the features and limits of image storage in Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Encryption-at-rest

All container images in your registry are encrypted at rest. Azure automatically encrypts an image before storing it, and decrypts it on-the-fly when you or your applications and services pull the image.

## <a name="geo-redundant-storage"></a>Armazenamento georredundante

Azure uses a geo-redundant storage scheme to guard against loss of your container images. Azure Container Registry automatically replicates your container images to multiple geographically distant data centers, preventing their loss in the event of a regional storage failure.

## <a name="geo-replication"></a>Georreplicação

For scenarios requiring even more high-availability assurance, consider using the [geo-replication](container-registry-geo-replication.md) feature of Premium registries. Geo-replication helps guard against losing access to your registry in the event of a *total* regional failure, not just a storage failure. Geo-replication provides other benefits, too, like network-close image storage for faster pushes and pulls in distributed development or deployment scenarios.

## <a name="image-limits"></a>Image limits

The following table describes the container image and storage limits in place for Azure container registries.

| Recurso | Limite |
| -------- | :---- |
| Repositórios | Sem limite |
| Imagens | Sem limite |
| Layers | Sem limite |
| Etiquetas | Sem limite|
| Armazenamento | 5 TB |

Very high numbers of repositories and tags can impact the performance of your registry. Periodically delete unused repositories, tags, and images as part of your registry maintenance routine. Deleted registry resources like repositories, images, and tags *cannot* be recovered after deletion. For more information about deleting registry resources, see [Delete container images in Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Custo de armazenamento

For full details about pricing, see [Azure Container Registry pricing][pricing].

## <a name="next-steps"></a>Passos seguintes

For more information about the different Azure Container Registry SKUs (Basic, Standard, Premium), see [Azure Container Registry SKUs](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
