---
title: RBAC roles and permissions
description: Use Azure role-based access control (RBAC) and identity and access management (IAM) to provide fine-grained permissions to resources in an Azure container registry.
ms.topic: article
ms.date: 03/20/2019
ms.openlocfilehash: 8ef4f26dfd59c7b3b177ef58fa23e08f7e66d328
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456233"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roles and permissions

The Azure Container Registry service supports a set of Azure roles that provide different levels of permissions to an Azure container registry. Use Azure [role-based access control](../role-based-access-control/index.yml) (RBAC) to assign specific permissions to users or service principals that need to interact with a registry.

| Role/Permission       | [Access Resource Manager](#access-resource-manager) | [Create/delete registry](#create-and-delete-registry) | [Push image](#push-image) | [Pull image](#pull-image) | [Delete image data](#delete-image-data) | [Change policies](#change-policies) |   [Sign images](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Contribuinte | X | X | X |  X | X | X |  |  
| Leitor | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Differentiate users and services

Any time permissions are applied, a best practice is to provide the most limited set of permissions for a person, or service, to accomplish a task. The following permission sets represent a set of capabilities that may be used by humans and headless services.

### <a name="cicd-solutions"></a>CI/CD solutions

When automating `docker build` commands from CI/CD solutions, you need `docker push` capabilities. For these headless service scenarios, we suggest assigning the **AcrPush** role. This role, unlike the broader **Contributor** role, prevents the account from performing other registry operations or accessing Azure Resource Manager.

### <a name="container-host-nodes"></a>Container host nodes

Likewise, nodes running your containers need the **AcrPull** role, but shouldn't require **Reader** capabilities.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker extension

For tools like the Visual Studio Code [Docker extension](https://code.visualstudio.com/docs/azure/docker), additional resource provider access is required to list the available Azure container registries. In this case, provide your users access to the **Reader** or **Contributor** role. These roles allow `docker pull`, `docker push`, `az acr list`, `az acr build`, and other capabilities. 

## <a name="access-resource-manager"></a>Access Resource Manager

Azure Resource Manager access is required for the Azure portal and registry management with the [Azure CLI](/cli/azure/). For example, to get a list of registries by using the `az acr list` command, you need this permission set. 

## <a name="create-and-delete-registry"></a>Create and delete registry

The ability to create and delete Azure container registries.

## <a name="push-image"></a>Push image

The ability to `docker push` an image, or push another [supported artifact](container-registry-image-formats.md) such as a Helm chart, to a registry. Requires [authentication](container-registry-authentication.md) with the registry using the authorized identity. 

## <a name="pull-image"></a>Pull image

The ability to `docker pull` a non-quarantined image, or pull another [supported artifact](container-registry-image-formats.md) such as a Helm chart, from a registry. Requires [authentication](container-registry-authentication.md) with the registry using the authorized identity.

## <a name="delete-image-data"></a>Delete image data

The ability to [delete container images](container-registry-delete.md), or delete other [supported artifacts](container-registry-image-formats.md) such as Helm charts, from a registry.

## <a name="change-policies"></a>Change policies

The ability to configure policies on a registry. Policies include image purging, enabling quarantine, and image signing.

## <a name="sign-images"></a>Sign images

The ability to sign images, usually assigned to an automated process, which would use a service principal. This permission is typically combined with [push image](#push-image) to allow pushing a trusted image to a registry. For details, see [Content trust in Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Passos seguintes

* Learn more about assigning RBAC roles to an Azure identity by using the [Azure portal](../role-based-access-control/role-assignments-portal.md), the [Azure CLI](../role-based-access-control/role-assignments-cli.md), or other Azure tools.

* Learn about [authentication options](container-registry-authentication.md) for Azure Container Registry.
