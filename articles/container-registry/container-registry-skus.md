---
title: Níveis e funcionalidades de serviço de registo
description: Conheça as funcionalidades e limites (quotas) nos níveis básico, standard e premium (SKUs) do Registo de Contentores Azure.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b55fc16dcd6dcb544ed4597ce4bdc6ba17b52646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015673"
---
# <a name="azure-container-registry-service-tiers"></a>Níveis de serviço de registo de contentores Azure

O Registo de Contentores Azure está disponível em vários níveis de serviço (também conhecidos como SKUs). Estes níveis fornecem preços previsíveis e várias opções para se alinhar com a capacidade e padrões de utilização do seu registo privado de Docker em Azure.

| Escalão de serviço | Description |
| --- | ----------- |
| **Básica** | Um ponto de entrada com otimização de custos para os programadores que estão a aprender sobre o Azure Container Registry. Os registos básicos têm as mesmas capacidades programáticas que o Standard e o Premium (como [a integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad)do Azure Ative Directory, [a eliminação de imagens][container-registry-delete]e [os webhooks).][container-registry-webhook] No entanto, o armazenamento incluído e a produção de imagem são mais adequados para cenários de menor utilização. |
| **Standard** | Os registos padrão oferecem as mesmas capacidades que o Basic, com o aumento do armazenamento incluído e produção de imagem. Os registos Standard devem satisfazer as necessidades da maioria dos cenários de produção. |
| **Premium** | Os registos premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de grande volume. Além de uma maior produção de imagem, o Premium adiciona funcionalidades como [a geo-replicação][container-registry-geo-replication] para a gestão de um único registo em várias regiões, [a confiança de conteúdos](container-registry-content-trust.md) para a assinatura de etiquetas de imagem, a [ligação privada com os pontos finais privados](container-registry-private-link.md) para restringir o acesso ao registo. |

Os níveis Básico, Standard e Premium fornecem todas as mesmas capacidades programáticas. Todos eles também beneficiam do armazenamento de [imagem][container-registry-storage] gerido inteiramente pela Azure. Escolher um nível mais alto proporciona mais desempenho e escala. Com vários níveis de serviço, pode começar com o Basic, depois converter para Standard e Premium à medida que o seu registo aumenta.

## <a name="service-tier-features-and-limits"></a>Características e limites de nível de serviço

O quadro seguinte detalha as características e os limites de registo dos níveis de serviço Básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Mudança de níveis

Pode alterar o nível de serviço de um registo com o CLI Azure ou no portal Azure. Pode mover-se livremente entre os níveis, desde que o nível para o qual esteja a mudar tenha a capacidade máxima de armazenamento necessária. 

Não há tempo de inatividade ou impacto nas operações de registo quando se desloca entre os níveis de serviço.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre os níveis de serviço no CLI Azure, utilize o comando [de atualização az acr.][az-acr-update] Por exemplo, para mudar para Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **visão geral** do registo do contentor no portal Azure, selecione **Update**, em seguida, selecione um novo **SKU** a partir do drop-down SKU.

![Atualizar registo de contentores SKU no portal Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para obter informações sobre preços em cada um dos níveis de serviço de registo de contentores Azure, consulte [os preços do registo de contentores][container-registry-pricing].

Para obter mais informações sobre os preços das transferências de dados, consulte [detalhes de preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passos seguintes

**Roteiro do Registo de Contentores de Azure**

Visite o [Roteiro ACR][acr-roadmap] no GitHub para encontrar informações sobre as próximas funcionalidades do serviço.

**Utilizador do Registo de Contentores Azure**

Submeta e vote novas sugestões de funcionalidades no [ACR UserVoice][container-registry-uservoice].

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