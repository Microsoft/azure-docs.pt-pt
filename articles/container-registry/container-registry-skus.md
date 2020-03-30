---
title: Níveis de serviço e SKUs
description: Conheça as funcionalidades e limites nos níveis de serviço Basic, Standard e Premium (SKUs) do Registo de Contentores Azure.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456282"
---
# <a name="azure-container-registry-skus"></a>Registo de Contentores Azure SKUs

O Registo de Contentores Azure (ACR) está disponível em vários níveis de serviço, conhecidos como SKUs. Estes SKUs fornecem preços previsíveis e várias opções para alinhar com os padrões de capacidade e utilização do seu registo privado docker em Azure.

| SKU | Descrição |
| --- | ----------- |
| **Básico** | Um ponto de entrada com otimização de custos para os programadores que estão a aprender sobre o Azure Container Registry. Os registos básicos têm as mesmas capacidades programáticas que a Standard e o Premium (como a integração da [autenticação](container-registry-authentication.md#individual-login-with-azure-ad)do Diretório Ativo Azure, [a eliminação de imagem][container-registry-delete]e os [webhooks).][container-registry-webhook] No entanto, o armazenamento incluído e a entrada de imagem são mais adequados para cenários de utilização mais baixos. |
| **Standard** | Os registos standard oferecem as mesmas capacidades que o Basic, com o aumento do armazenamento e do rendimento da imagem. Os registos Standard devem satisfazer as necessidades da maioria dos cenários de produção. |
| **Premium** | Os registos premium fornecem a maior quantidade de armazenamento incluído e operações simultâneas, permitindo cenários de grande volume. Além de uma maior entrada de imagem, o Premium adiciona funcionalidades como [a geo-replicação][container-registry-geo-replication] para gerir um único registo em várias regiões, confiança de [conteúdo](container-registry-content-trust.md) para a assinatura de etiquetas de imagem, [firewalls e redes virtuais (pré-visualização)](container-registry-vnet.md) para restringir o acesso ao registo. |

As SKUs Básicas, Standard e Premium fornecem todas as mesmas capacidades programáticas. Todos beneficiam também do armazenamento de [imagem][container-registry-storage] gerido inteiramente pelo Azure. Escolher um SKU de nível superior proporciona mais desempenho e escala. Com vários níveis de serviço, pode começar com o Basic, depois converter-se para Standard e Premium à medida que o seu uso de registo aumenta.

## <a name="sku-features-and-limits"></a>Recursos e limites SKU

A tabela seguinte detalha as características e limites dos níveis de serviço Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Mudança de SKUs

Pode alterar o SKU de um registo com o Azure CLI ou no portal Azure. Pode mover-se livremente entre SKUs enquanto o SKU para o qual está a mudar tiver a capacidade máxima de armazenamento necessária. 

### <a name="azure-cli"></a>CLI do Azure

Para se mover entre As ES kus no ClI Azure, use o comando de [atualização az acr.][az-acr-update] Por exemplo, para mudar para Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

No registo de contentores **Visão geral** no portal Azure, selecione **Update**e, em seguida, selecione um novo **SKU** a partir da queda do SKU.

![Atualizar registo de contentores SKU no portal Azure][update-registry-sku]

## <a name="pricing"></a>Preços

Para obter informações sobre cada uma das SKUs do Registo de Contentores De Teia, consulte os preços do registo de [contentores][container-registry-pricing].

Para mais detalhes sobre os preços das transferências de dados, consulte os detalhes de [preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passos seguintes

**Roteiro do registo de contentores de Azure**

Visite o [ACR Roadmap][acr-roadmap] no GitHub para encontrar informações sobre as próximas funcionalidades do serviço.

**Registo de Contentores Azure UserVoice**

Submeta e vote em novas sugestões de funcionalidades no [ACR UserVoice][container-registry-uservoice].

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
