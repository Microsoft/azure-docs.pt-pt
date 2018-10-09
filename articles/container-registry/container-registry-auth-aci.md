---
title: Autenticar com o registo de contentores do Azure de instâncias de contentor do Azure
description: Saiba como fornecer acesso a imagens no seu registo de contentor privado do Azure Container Instances ao utilizar um principal de serviço do Azure Active Directory.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 32b1788d73e1c323d93b40b778bc64a1ba45c4ad
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855838"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar com o registo de contentores do Azure de instâncias de contentor do Azure

Pode utilizar um principal de serviço do Azure Active Directory (Azure AD) para fornecer acesso aos seus registos de contentor privado no Azure Container Registry.

Neste artigo, vai aprender a criar e configurar um principal de serviço do Azure AD com *pull* permissões para o seu registo. Em seguida, iniciar um contentor no Azure Container Instances (ACI) que obtém a imagem do seu registo privado, com o principal de serviço para a autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando utilizar um principal de serviço

Deve utilizar um principal de serviço para a autenticação do ACI em **cenários sem periféricos**, como no caso de aplicações ou serviços que criar instâncias de contentor de forma autónoma automatizada ou de outra forma.

Por exemplo, se tiver um script automatizado que é executada todas as noites e cria um [instância de contentor baseado em tarefas](../container-instances/container-instances-restart-policy.md) para processar alguns dados, ele pode utilizar um principal de serviço com permissões de (leitor) pull-only para autenticar para o registo. Em seguida, pode alternar credenciais do principal de serviço ou revogar o acesso totalmente sem afetar outros serviços e aplicações.

Principais de serviço também devem ser utilizado quando o Registro [utilizador de admin](container-registry-authentication.md#admin-account) está desativada.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar com o principal de serviço

Para iniciar um contentor no Azure Container Instances com um principal de serviço, especificar sua ID para `--registry-username`e a respetiva palavra-passe para `--registry-password`.

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

Pode encontrar os scripts de exemplo anterior para a CLI do Azure no GitHub, como versões bem para o Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos Seguintes

Detalhes adicionais sobre como trabalhar com os principais de serviço e o ACR de conter os seguintes artigos:

* [Autenticação de registo de contentor do Azure com os principais de serviço](container-registry-auth-service-principal.md)
* [Autenticar com o registo de contentores do Azure de serviço Kubernetes do Azure (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
