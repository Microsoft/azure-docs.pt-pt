---
title: Access from Container Instances
description: Learn how to provide access to images in your private container registry from Azure Container Instances by using an Azure Active Directory service principal.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456514"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Authenticate with Azure Container Registry from Azure Container Instances

You can use an Azure Active Directory (Azure AD) service principal to provide access to your private container registries in Azure Container Registry.

In this article, you learn to create and configure an Azure AD service principal with *pull* permissions to your registry. Then, you start a container in Azure Container Instances (ACI) that pulls its image from your private registry, using the service principal for authentication.

## <a name="when-to-use-a-service-principal"></a>When to use a service principal

You should use a service principal for authentication from ACI in **headless scenarios**, such as in applications or services that create container instances in an automated or otherwise unattended manner.

For example, if you have an automated script that runs nightly and creates a [task-based container instance](../container-instances/container-instances-restart-policy.md) to process some data, it can use a service principal with pull-only permissions to authenticate to the registry. You can then rotate the service principal's credentials or revoke its access completely without affecting other services and applications.

Service principals should also be used when the registry [admin user](container-registry-authentication.md#admin-account) is disabled.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Authenticate using the service principal

To launch a container in Azure Container Instances using a service principal, specify its ID for `--registry-username`, and its password for `--registry-password`.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Scripts de exemplo

You can find the preceding sample scripts for Azure CLI on GitHub, as well versions for Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [O Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos seguintes

The following articles contain additional details on working with service principals and ACR:

* [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md)
* [Authenticate with Azure Container Registry from Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
