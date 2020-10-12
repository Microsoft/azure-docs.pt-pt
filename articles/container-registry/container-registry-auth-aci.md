---
title: Acesso a partir de instâncias de contentores
description: Saiba como fornecer acesso a imagens no seu registo privado de contentores a partir de Instâncias de Contentores Azure utilizando um diretor de serviço do Azure Ative.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74456514"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar com registo de contentores Azure a partir de instâncias de contentores Azure

Pode utilizar um diretor de serviço Azure Ative (Azure AD) para fornecer acesso aos seus registos privados de contentores no Registo de Contentores Azure.

Neste artigo, aprende a criar e configurar um diretor de serviço AZure AD com permissões *de retirada* para o seu registo. Em seguida, inicie um contentor em Azure Container Instances (ACI) que retira a sua imagem do seu registo privado, utilizando o principal de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando usar um principal de serviço

Deverá utilizar um principal de serviço para autenticação a partir de ACI em **cenários sem cabeça,** como em aplicações ou serviços que criem casos de contentores de forma automatizada ou sem vigilância.

Por exemplo, se tiver um script automatizado que funciona todas as noites e cria uma [instância de contentor baseada em tarefas](../container-instances/container-instances-restart-policy.md) para processar alguns dados, pode usar um principal de serviço com permissões apenas para autenticar o registo. Em seguida, pode rodar as credenciais do diretor de serviço ou revogar completamente o seu acesso sem afetar outros serviços e aplicações.

Os princípios de serviço também devem ser utilizados quando o [utilizador de administração](container-registry-authentication.md#admin-account) de registo é desativado.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar usando o principal de serviço

Para lançar um contentor em Instâncias de Contentores Azure utilizando um principal de serviço, especifique o seu ID para `--registry-username` , e a sua palavra-passe para `--registry-password` .

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

Pode encontrar os scripts de amostra anteriores para Azure CLI no GitHub, bem como versões para Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos contêm detalhes adicionais sobre o trabalho com os diretores de serviço e a ACR:

* [Autenticação do Registo de Contentores Azure com principais serviços](container-registry-auth-service-principal.md)
* [Autenticar com registo de contentores Azure do Serviço Azure Kubernetes (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
