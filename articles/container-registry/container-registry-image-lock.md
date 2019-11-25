---
title: Bloquear imagens
description: Set attributes for a container image or repository so it can't be deleted or overwritten in an Azure container registry.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 9e55a6688be9f51f1c1b237ae86bd57692a86592
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456325"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Lock a container image in an Azure container registry

In an Azure container registry, you can lock an image version or a repository so that it can't be deleted or updated. To lock an image or a repository, update its attributes using the Azure CLI command [az acr repository update][az-acr-repository-update]. 

This article requires that you run the Azure CLI in Azure Cloud Shell or locally (version 2.0.55 or later recommended). Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

> [!IMPORTANT]
> This article doesn't apply to locking an entire registry, for example, using **Settings > Locks** in the Azure portal, or `az lock` commands in the Azure CLI. Locking a registry resource doesn't prevent you from creating, updating, or deleting data in repositories. Locking a registry only affects management operations such as adding or deleting replications, or deleting the registry itself. More information in [Lock resources to prevent unexpected changes](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="scenarios"></a>Cenários

By default, a tagged image in Azure Container Registry is *mutable*, so with appropriate permissions you can repeatedly update and push an image with the same tag to a registry. Container images can also be [deleted](container-registry-delete.md) as needed. This behavior is useful when you develop images and need to maintain a size for your registry.

However, when you deploy a container image to production, you might need an *immutable* container image. An immutable image is one that you can't accidentally delete or overwrite. Use the [az acr repository update][az-acr-repository-update] command to set repository attributes so you can:

* Lock an image version, or an entire repository

* Protect an image version or repository from deletion, but allow updates

* Prevent read (pull) operations on an image version, or an entire repository

See the following sections for examples.

## <a name="lock-an-image-or-repository"></a>Lock an image or repository 

### <a name="show-the-current-repository-attributes"></a>Show the current repository attributes
To see the current attributes of a repository, run the following [az acr repository show][az-acr-repository-show] command:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Show the current image attributes
To see the current attributes of a tag, run the following [az acr repository show][az-acr-repository-show] command:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Lock an image by tag

To lock the *myrepo/myimage:tag* image in *myregistry*, run the following [az acr repository update][az-acr-repository-update] command:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Lock an image by manifest digest

To lock a *myrepo/myimage* image identified by manifest digest (SHA-256 hash, represented as `sha256:...`), run the following command. (To find the manifest digest associated with one or more image tags, run the [az acr repository show-manifests][az-acr-repository-show-manifests] command.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Lock a repository

To lock the *myrepo/myimage* repository and all images in it, run the following command:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Protect an image or repository from deletion

### <a name="protect-an-image-from-deletion"></a>Protect an image from deletion

To allow the *myrepo/myimage:tag* image to be updated but not deleted, run the following command:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Protect a repository from deletion

The following command sets the *myrepo/myimage* repository so it can't be deleted. Individual images can still be updated or deleted.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Prevent read operations on an image or repository

To prevent read (pull) operations on the *myrepo/myimage:tag* image, run the following command:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

To prevent read operations on all images in the *myrepo/myimage* repository, run the following command:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Unlock an image or repository

To restore the default behavior of the *myrepo/myimage:tag* image so that it can be deleted and updated, run the following command:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

To restore the default behavior of the *myrepo/myimage* repository and all images so that they can be deleted and updated, run the following command:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Passos seguintes

In this article, you learned about using the [az acr repository update][az-acr-repository-update] command to prevent deletion or updating of image versions in a repository. To set additional attributes, see the [az acr repository update][az-acr-repository-update] command reference.

To see the attributes set for an image version or repository, use the [az acr repository show][az-acr-repository-show] command.

For details about delete operations, see [Delete container images in Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

