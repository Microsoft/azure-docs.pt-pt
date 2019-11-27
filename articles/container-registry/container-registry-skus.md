---
title: Níveis de serviço e SKUs
description: Saiba mais sobre os recursos e limites nas camadas de serviço básico, Standard e Premium (SKUs) do registro de contêiner do Azure.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456282"
---
# <a name="azure-container-registry-skus"></a>SKUs do registro de contêiner do Azure

O ACR (registro de contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções para alinhar a capacidade e os padrões de uso de seu registro privado do Docker no Azure.

| SKU | Descrição |
| --- | ----------- |
| **Básica** | Um ponto de entrada com otimização de custos para os programadores que estão a aprender sobre o Azure Container Registry. Os registros básicos têm os mesmos recursos de programação que o Standard e o Premium (como Azure Active Directory [integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad), [exclusão de imagem][container-registry-delete]e [WebHooks][container-registry-webhook]). No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Os registros padrão oferecem os mesmos recursos que o básico, com maior taxa de transferência de armazenamento e imagem incluída. Os registos Standard devem satisfazer as necessidades da maioria dos cenários de produção. |
| **Premium** | Os registros Premium fornecem a maior quantidade de operações de armazenamento e simultâneas incluídas, permitindo cenários de alto volume. Além da taxa de transferência de imagem mais alta, o Premium adiciona recursos como [replicação geográfica][container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para assinatura de marca de imagem, [firewalls e redes virtuais (versão prévia)](container-registry-vnet.md) para restringir o acesso ao registro. |

Todos os SKUs básico, Standard e Premium fornecem os mesmos recursos de programação. Todos eles também se beneficiam do [armazenamento de imagem][container-registry-storage] gerenciado inteiramente pelo Azure. A escolha de uma SKU de nível superior fornece mais desempenho e escala. Com várias camadas de serviço, você pode começar a usar o básico e, em seguida, converter para Standard e Premium conforme o uso do registro aumenta.

## <a name="sku-features-and-limits"></a>Recursos e limites de SKU

A tabela a seguir fornece detalhes dos recursos e limites das camadas de serviço Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU de um registro com a CLI do Azure ou na portal do Azure. Você pode mover-se livremente entre SKUs, desde que o SKU que você está alternando tenha a capacidade máxima de armazenamento necessária. 

### <a name="azure-cli"></a>CLI do Azure

Para mover entre SKUs no CLI do Azure, use o comando [AZ ACR Update][az-acr-update] . Por exemplo, para mudar para Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar**e, em seguida, selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para obter informações sobre preços em cada uma das SKUs do registro de contêiner do Azure, consulte [preços do registro de contêiner][container-registry-pricing].

Para obter detalhes sobre os preços das transferências de dados, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passos seguintes

**Roteiro do registro de contêiner do Azure**

Visite o [roteiro do ACR][acr-roadmap] no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do registro de contêiner do Azure**

Envie e vote em sugestões de novos recursos no [UserVoice do ACR][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
