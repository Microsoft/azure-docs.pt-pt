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
ms.openlocfilehash: b11f88cbb29016032cbf536a2c970573eda82152
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262857"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar com o registro de contêiner do Azure de instâncias de contêiner do Azure

Você pode usar uma entidade de serviço do Azure Active Directory (AD do Azure) para fornecer acesso aos seus registros de contêiner privado no registro de contêiner do Azure.

Neste artigo, você aprende a criar e configurar uma entidade de serviço do Azure AD com permissões de *pull* para o registro. Em seguida, você inicia um contêiner em ACI (instâncias de contêiner do Azure) que efetua pull de sua imagem do registro particular, usando a entidade de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando usar uma entidade de serviço

Você deve usar uma entidade de serviço para autenticação de ACI em **cenários sem periféricos**, como em aplicativos ou serviços que criam instâncias de contêiner de maneira automatizada ou autônoma.

Por exemplo, se você tiver um script automatizado que é executado à noite e cria uma [instância de contêiner baseada em tarefa](../container-instances/container-instances-restart-policy.md) para processar alguns dados, ele pode usar uma entidade de serviço com permissões somente de pull para autenticar no registro. Em seguida, você pode girar as credenciais da entidade de serviço ou revogar seu acesso completamente sem afetar outros serviços e aplicativos.

As entidades de serviço também devem ser usadas quando o [usuário administrador](container-registry-authentication.md#admin-account) do registro estiver desabilitado.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar usando a entidade de serviço

Para iniciar um contêiner em instâncias de contêiner do Azure usando uma entidade de serviço, especifique sua ID para `--registry-username` e sua senha para `--registry-password`.

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
* [O Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos seguintes

Os artigos a seguir contêm detalhes adicionais sobre como trabalhar com entidades de serviço e ACR:

* [Autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md)
* [Autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
