---
title: Autenticar com o registro de contêiner do Azure de instâncias de contêiner do Azure
description: Saiba como fornecer acesso a imagens em seu registro de contêiner privado de instâncias de contêiner do Azure usando uma entidade de serviço Azure Active Directory.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: d2099de9ad909b23b79a92a831d7730b1cf126e3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311628"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar com o registro de contêiner do Azure de instâncias de contêiner do Azure

Você pode usar uma entidade de serviço do Azure Active Directory (AD do Azure) para fornecer acesso aos seus registros de contêiner privado no registro de contêiner do Azure.

Neste artigo, você aprende a criar e configurar uma entidade de serviço do Azure AD com permissões de *pull* para o registro. Em seguida, você inicia um contêiner em ACI (instâncias de contêiner do Azure) que efetua pull de sua imagem do registro particular, usando a entidade de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para autenticação de ACI em **cenários**sem periféricos, como em aplicativos ou serviços que criam instâncias de contêiner de maneira automatizada ou autônoma.

Por exemplo, se você tiver um script automatizado que é executado à noite e cria uma [instância de contêiner baseada em tarefa](../container-instances/container-instances-restart-policy.md) para processar alguns dados, ele pode usar uma entidade de serviço com permissões somente de pull para autenticar no registro. Em seguida, você pode girar as credenciais da entidade de serviço ou revogar seu acesso completamente sem afetar outros serviços e aplicativos.

As entidades de serviço também devem ser usadas quando o [usuário administrador](container-registry-authentication.md#admin-account) do registro estiver desabilitado.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar usando a entidade de serviço

Para iniciar um contêiner em instâncias de contêiner do Azure usando uma entidade de serviço, especifique `--registry-username`sua ID para e sua `--registry-password`senha para.

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

Você pode encontrar os scripts de exemplo anteriores para CLI do Azure no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos Seguintes

Os artigos a seguir contêm detalhes adicionais sobre como trabalhar com entidades de serviço e ACR:

* [Autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md)
* [Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
