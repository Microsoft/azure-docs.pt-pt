---
title: SKUs do registro de contêiner do Azure
description: Compare as diferentes camadas de serviço disponíveis no registro de contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: bf620178a0c10661126b3e52c7b908ccc9a90d89
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68311894"
---
# <a name="azure-container-registry-skus"></a>SKUs do registro de contêiner do Azure

O ACR (registro de contêiner do Azure) está disponível em várias camadas de serviço, conhecidas como SKUs. Essas SKUs fornecem preços previsíveis e várias opções para alinhar a capacidade e os padrões de uso de seu registro privado do Docker no Azure.

| SKU | Managed | Descrição |
| --- | :-------: | ----------- |
| **Básica** | Sim | Um ponto de entrada com otimização de custos para os programadores que estão a aprender sobre o Azure Container Registry. Os registros básicos têm os mesmos recursos de programação que o Standard e o Premium (como Azure Active Directory [integração de autenticação](container-registry-authentication.md#individual-login-with-azure-ad), [exclusão de imagem][container-registry-delete]e WebHooks). [][container-registry-webhook] No entanto, o armazenamento incluído e a taxa de transferência de imagem são mais apropriados para cenários de uso mais baixos. |
| **Standard** | Sim | Os registros padrão oferecem os mesmos recursos que o básico, com maior taxa de transferência de armazenamento e imagem incluída. Os registos Standard devem satisfazer as necessidades da maioria dos cenários de produção. |
| **Premium** | Sim | Os registros Premium fornecem a maior quantidade de operações de armazenamento e simultâneas incluídas, permitindo cenários de alto volume. Além da taxa de transferência de imagem mais alta, o Premium adiciona recursos, incluindo [replicação geográfica][container-registry-geo-replication] para gerenciar um único registro em várias regiões, [confiança de conteúdo](container-registry-content-trust.md) para assinatura de marca de imagem e [firewalls e redes virtuais (visualização)](container-registry-vnet.md) para restringir o acesso ao registro. |
|  Clássico (*não disponível após abril de 2019*) | Não | Esta SKU habilitou a versão inicial do serviço de registro de contêiner do Azure no Azure. Os registros clássicos são apoiados por uma conta de armazenamento que o Azure cria em sua assinatura, o que limita a capacidade de o ACR fornecer recursos de nível mais alto, como maior taxa de transferência e replicação geográfica. |

> [!IMPORTANT]
> O SKU do registro clássico estásendo preterido e não estará disponível depois de **abril de 2019**. É recomendável usar Basic, Standard ou Premium para todos os novos registros. Todos os registros clássicos existentes devem ser atualizados antes de 2019 de abril. Para obter informações de atualização, consulte [atualizar um registro clássico][container-registry-upgrade].

Os SKUs básico, Standard e Premium (coletivamente chamados de *registros gerenciados*) fornecem os mesmos recursos de programação. Todos eles também se beneficiam do [armazenamento de imagem][container-registry-storage] gerenciado inteiramente pelo Azure. A escolha de uma SKU de nível superior fornece mais desempenho e escala. Com várias camadas de serviço, você pode começar a usar o básico e, em seguida, converter para Standard e Premium conforme o uso do registro aumenta.

## <a name="sku-feature-matrix"></a>Matriz de recursos de SKU

A tabela a seguir fornece detalhes dos recursos e limites das camadas de serviço Basic, Standard e Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Alterando SKUs

Você pode alterar a SKU de um registro com a CLI do Azure ou na portal do Azure. Você pode mover livremente entre SKUs gerenciadas, contanto que o SKU que você está alternando tenha a capacidade máxima de armazenamento necessária. Quando você alterna para um dos SKUs gerenciados do clássico, não pode voltar para o clássico--é uma conversão unidirecional.

### <a name="azure-cli"></a>CLI do Azure

Para mover entre SKUs no CLI do Azure, use o comando [AZ ACR Update][az-acr-update] . Por exemplo, para mudar para Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Portal do Azure

Na **visão geral** do registro de contêiner no portal do Azure, selecione **Atualizar**e, em seguida, selecione uma nova **SKU** na lista suspensa SKU.

![Atualizar SKU do registro de contêiner no portal do Azure][update-registry-sku]

Se você tiver um registro clássico, não poderá selecionar uma SKU gerenciada dentro do portal do Azure. Em vez disso, você deve primeiro [Atualizar][container-registry-upgrade] para um registro gerenciado.

## <a name="pricing"></a>Preços

Para obter informações sobre preços em cada uma das SKUs do registro de contêiner do Azure, consulte [preços do registro de contêiner][container-registry-pricing].

Para obter detalhes sobre os preços das transferências de dados, consulte [detalhes de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Passos seguintes

**Roteiro do registro de contêiner do Azure**

Visite o [roteiro do ACR][acr-roadmap] no GitHub para encontrar informações sobre os próximos recursos do serviço.

**UserVoice do registro de contêiner do Azure**

Envie e vote em sugestões de novos recursos no UserVoice do [ACR][container-registry-uservoice].

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
