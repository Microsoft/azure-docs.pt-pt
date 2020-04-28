---
title: Acesso a partir de Instâncias de Contentores
description: Saiba como fornecer acesso a imagens no seu registo de contentores privados a partir de Casos de Contentores Azure utilizando um diretor de serviço de Diretório Ativo Azure.
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456514"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Autenticar com registo de contentores Azure de instâncias de contentores azure

Pode utilizar um diretor de serviço azure Ative Directory (Azure AD) para fornecer acesso aos seus registos de contentores privados no Registo de Contentores Azure.

Neste artigo, aprende a criar e configurar um diretor de serviço azure AD com permissões de *pull* para o seu registo. Em seguida, inicia um contentor em Casos de Contentores Azure (ACI) que retira a sua imagem do seu registo privado, utilizando o principal de serviço para autenticação.

## <a name="when-to-use-a-service-principal"></a>Quando utilizar um diretor de serviço

Deve utilizar um diretor de serviço para autenticação a partir de ACI em **cenários sem cabeça**, como em aplicações ou serviços que criam instâncias de contentores de forma automatizada ou de outra forma sem vigilância.

Por exemplo, se tiver um script automatizado que funciona todas as noites e cria uma [instância de contentores baseada em tarefas](../container-instances/container-instances-restart-policy.md) para processar alguns dados, pode usar um diretor de serviço com permissões apenas para autenticar o registo. Em seguida, pode rodar as credenciais do diretor de serviço ou revogar completamente o seu acesso sem afetar outros serviços e aplicações.

Os diretores de serviço também devem ser utilizados quando o [utilizador administrativo](container-registry-authentication.md#admin-account) do registo estiver desativado.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Autenticar usando o diretor de serviço

Para lançar um contentor em Casos de Contentores Azure utilizando um diretor de serviço, especifique o seu ID para `--registry-username`. e a sua palavra-passe para `--registry-password`.

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

Pode encontrar os scripts de amostra anteriores para o Azure CLI no GitHub, bem como versões para o Azure PowerShell:

* [CLI do Azure][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos contêm detalhes adicionais sobre o trabalho com os diretores de serviço e a ACR:

* [Autenticação do Registo de Contentores Azure com diretores de serviço](container-registry-auth-service-principal.md)
* [Autenticação com Registo de Contentores Azure do Serviço Azure Kubernetes (AKS)](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
