---
title: SKUs de registo de contentor do Azure
description: Compare os escalões de serviço diferentes disponíveis no Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: f36b206ff015511dea7369617febe9220282bbe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069050"
---
# <a name="azure-container-registry-skus"></a>SKUs de registo de contentor do Azure

O Azure Container Registry (ACR) está disponível em vários escalões de serviço, conhecidos como SKUs. Essas SKUs fornecem preços previsíveis e várias opções para consonância com os padrões de capacidade e a utilização do seu registo privado do Docker no Azure.

| SKU | Managed | Descrição |
| --- | :-------: | ----------- |
| **Básica** | Sim | Um ponto de entrada com otimização de custos para os programadores que estão a aprender sobre o Azure Container Registry. Registos básicos tem as mesmas capacidades programáticas como Standard e Premium (como o Azure Active Directory [integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad), [eliminação de imagem][container-registry-delete], e [webhooks][container-registry-webhook]). No entanto, o armazenamento incluído e o débito de imagem são mais adequadas para cenários de utilização mais baixos. |
| **Standard** | Sim | Registos padrão oferecem as mesmas capacidades do básico, com maior débito de armazenamento e de imagem incluído. Os registos Standard devem satisfazer as necessidades da maioria dos cenários de produção. |
| **Premium** | Sim | Registos Premium fornecem a quantidade máxima de armazenamento incluído e operações simultâneas, permitir cenários de grande volume. Além de débito mais elevado de imagem, o Premium adiciona funcionalidades, incluindo [georreplicação] [ container-registry-geo-replication] para gerir um registo único em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para a assinatura de etiqueta de imagem, e [firewalls e redes virtuais (pré-visualização)](container-registry-vnet.md) para restringir o acesso ao registo. |
|  Clássico (*não estão disponíveis depois de Abril de 2019*) | Não | Este SKU ativada na versão inicial do serviço do Azure Container Registry no Azure. Registos de clássicos são apoiados por uma conta de armazenamento que o Azure cria na sua subscrição, o que limita a capacidade para o ACR fornecer capacidades de nível mais alto, como o maior débito e a georreplicação. |

> [!IMPORTANT]
> O registo clássico SKU está sendo **preterido**e não estará disponível após **Abril de 2019**. Recomendamos que utilize o básico, Standard ou Premium para todos os registos de novo. Todos os registos de clássico existentes devem ser atualizados antes de Abril de 2019. Para informações de atualização, consulte [atualizar um registo clássico][container-registry-upgrade].

O básico, Standard e Premium SKUs (coletivamente chamado *geridos registos*) fornecem todas as mesmas capacidades programáticas. Eles também beneficiam [armazenamento de imagens] [ container-registry-storage] geridas completamente pelo Azure. Escolher um SKU de alto nível fornece mais desempenho e dimensionamento. Com várias camadas de serviços, pode começar a utilizar Basic então converter para Standard e Premium, à medida que aumenta de utilização do registo.

## <a name="sku-feature-matrix"></a>Matriz de recursos SKU

A tabela seguinte fornece detalhes sobre os recursos e os limites dos escalões de serviço básico, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterar SKUs

Pode alterar o SKU de um registo com a CLI do Azure ou no portal do Azure. Pode mover-se livremente entre os SKUs geridos, desde que o SKU de estar a mudar para tem a capacidade de armazenamento máximo. Quando mudar para um dos SKUs geridos da implementação clássica, não pode mover novamente para clássico – é uma conversão unidirecional.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre os SKUs na CLI do Azure, utilize o [atualização do az acr] [ az-acr-update] comando. Por exemplo, para mudar para o Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

No registo de contentor **descrição geral** no portal do Azure, selecione **Update**, em seguida, selecione um novo **SKU** na lista suspensa SKU.

![Atualizar o registo de contentor SKU no portal do Azure][update-registry-sku]

Se tiver um registo clássico, não é possível selecionar um SKU gerido no portal do Azure. Em vez disso, deve primeiro [atualizar] [ container-registry-upgrade] para um registo gerido.

## <a name="pricing"></a>Preços

Para obter informações sobre cada um dos SKUs de registo de contentor do Azure de preços, consulte [preços do Container Registry][container-registry-pricing].

Para obter detalhes sobre os preços para transferências de dados, consulte [detalhes dos preços da largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passos Seguintes

**Plano de registo de contentor do Azure**

Visite o [mapa de ACR] [ acr-roadmap] no GitHub para obter informações sobre funcionalidades futuras no serviço.

**UserVoice do registo de contentor do Azure**

Submeter e votar em sugestões de funcionalidades novas na [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
