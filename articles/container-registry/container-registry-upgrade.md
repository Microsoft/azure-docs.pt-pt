---
title: Atualizar um registro de contêiner do Azure clássico
description: Aproveite o conjunto de recursos expandido dos registros de contêiner gerenciados básico, Standard e Premium atualizando seu registro de contêiner clássico não gerenciado.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310381"
---
# <a name="upgrade-a-classic-container-registry"></a>Atualizar um registro de contêiner clássico

O ACR (registro de contêiner do Azure) está disponível em várias camadas de serviço, [conhecidas como SKUs](container-registry-skus.md). A versão inicial do ACR ofereceu um único SKU, clássico, que não tem vários recursos inerentes às SKUs Basic, Standard e Premium (coletivamente conhecidos como registros *gerenciados* ).

O SKU clássico está sendo preterido e não estará disponível depois de abril de 2019. Este artigo fornece detalhes sobre como migrar seu registro clássico não gerenciado para um dos SKUs gerenciados para que você possa aproveitar seu conjunto de recursos aprimorado.

## <a name="why-upgrade"></a>Por que atualizar?

O SKU do registro clássico está sendo preterido e não estará disponível depois de **abril de 2019**. Todos os registros clássicos existentes devem ser atualizados antes de 2019 de abril. Os recursos de gerenciamento do portal de registros clássicos serão divididos em fases. A criação de novos registros clássicos será desabilitada após 2019 de abril.

Devido à preterição planejada e aos recursos limitados de registros não gerenciados clássicos, todos os registros clássicos devem ser atualizados para registros gerenciados (Basic, Standard ou Premium). Essas SKUs de nível superior integram profundamente o registro aos recursos do Azure. Para obter mais informações sobre os preços e os recursos das diferentes camadas de serviço, consulte [SKUs do registro de contêiner](container-registry-skus.md).

O registro clássico depende da conta de armazenamento que o Azure provisiona automaticamente em sua assinatura do Azure quando você cria o registro. Por outro lado, os SKUs básico, Standard e Premium aproveitam os [recursos avançados de armazenamento](container-registry-storage.md) do Azure, manipulando de forma transparente o armazenamento de suas imagens para você. Uma conta de armazenamento separada não é criada em sua própria assinatura.

O armazenamento de registro gerenciado oferece os seguintes benefícios:

* As imagens de contêiner são [criptografadas em repouso](container-registry-storage.md#encryption-at-rest).
* As imagens são armazenadas usando o [armazenamento com redundância geográfica](container-registry-storage.md#geo-redundant-storage), garantindo o backup de suas imagens com replicação de várias regiões (somente SKU Premium).
* Capacidade de [mover livremente entre SKUs](container-registry-skus.md#changing-skus), permitindo maior taxa de transferência quando você escolhe uma SKU de nível superior. Com cada SKU, o ACR pode atender aos seus requisitos de taxa de transferência conforme suas necessidades aumentam.
* O modelo de segurança unificado para o registro e seu armazenamento fornece gerenciamento de direitos simplificado. Você gerencia permissões somente para o registro de contêiner, sem precisar também gerenciar permissões para uma conta de armazenamento separada.

Para obter detalhes adicionais sobre o armazenamento de imagem no ACR, confira [armazenamento de imagem de contêiner no registro de contêiner do Azure](container-registry-storage.md).

## <a name="migration-considerations"></a>Considerações sobre a migração

Quando você atualiza um registro clássico para um registro gerenciado, o Azure deve copiar todas as imagens de contêiner existentes da conta de armazenamento criada pelo ACR em sua assinatura para uma conta de armazenamento gerenciada pelo Azure. Dependendo do tamanho do registro, esse processo pode levar alguns minutos a várias horas. Para fins de estimativa, espere um tempo de migração de aproximadamente 0,5 GiB por minuto.

Durante o processo de conversão `docker push` , as operações são desabilitadas durante os últimos 10% da migração. `docker pull`continua a funcionar normalmente.

Não exclua nem modifique o conteúdo da conta de armazenamento que faz backup do registro clássico durante o processo de conversão. Isso pode resultar na corrupção de suas imagens de contêiner.

Depois que a migração for concluída, a conta de armazenamento em sua assinatura que originalmente fez o backup do registro clássico não será mais usada pelo registro de contêiner do Azure. Depois de verificar se a migração foi bem-sucedida, considere excluir a conta de armazenamento para ajudar a minimizar o custo.

>[!IMPORTANT]
> A atualização do clássico para um dos SKUs gerenciados é um **processo**unidirecional. Depois de converter um registro clássico em básico, Standard ou Premium, não será possível reverter para o clássico. No entanto, você pode mover livremente entre SKUs gerenciadas com capacidade suficiente para o registro.

## <a name="how-to-upgrade"></a>Como atualizar

Você pode atualizar um registro clássico não gerenciado para um dos SKUs gerenciados de várias maneiras. Nas seções a seguir, descrevemos o processo de uso do [CLI do Azure][azure-cli] and the [Azure portal][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Atualizar no CLI do Azure

Para atualizar um registro clássico no CLI do Azure, execute o comando [AZ ACR Update][az-acr-update] e especifique a nova SKU para o registro. No exemplo a seguir, um registro clássico chamado *myclassicregistry* é atualizado para o SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Quando a migração for concluída, você deverá ver uma saída semelhante à seguinte. Observe que o `sku` é "Premium" e o `storageAccount` is `null`, indicando que o Azure agora gerencia o armazenamento de imagem para esse registro.

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Se você especificar um SKU de registro gerenciado cuja capacidade máxima é menor que o tamanho do registro clássico, você receberá uma mensagem de erro semelhante à seguinte.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se você receber um erro semelhante, execute o comando [AZ ACR Update][az-acr-update] novamente e ESPECIFIQUE o SKU sugerido, que é o próximo SKU de nível mais alto que pode acomodar suas imagens.

## <a name="upgrade-in-azure-portal"></a>Atualizar no portal do Azure

Quando você atualiza um registro clássico usando o portal do Azure, o Azure seleciona automaticamente o SKU Standard ou Premium, dependendo de qual SKU pode acomodar suas imagens. Por exemplo, se o registro contiver menos de 100 GiB em imagens, o Azure selecionará e converterá automaticamente o registro clássico para Standard (máximo de 100 GiB).

Para atualizar o registro clássico usando o portal do Azure, navegue até a **visão geral** do registro de contêiner e selecione **atualizar para o registro gerenciado**.

![Botão de atualização do registro clássico na interface do usuário do portal do Azure][update-classic-01-upgrade]

Selecione **OK** para confirmar que você deseja atualizar para um registro gerenciado.

Durante a migração, o portal indica que o **estado de provisionamento** do registro está sendo *atualizado*. Conforme mencionado anteriormente, `docker push` as operações são desabilitadas durante os últimos 10% da migração. Não exclua nem atualize a conta de armazenamento usada pelo registro clássico enquanto a migração estiver em andamento, fazendo isso pode resultar em corrupção de imagem.

![Progresso da atualização do registro clássico na interface do usuário do portal do Azure][update-classic-03-updating]

Quando a migração for concluída, o **estado de provisionamento** indicará *êxito*e você poderá retomar as operações normais com o registro.

![Estado de conclusão da atualização do registro clássico na interface do usuário do portal do Azure][update-classic-04-updated]

## <a name="next-steps"></a>Passos Seguintes

Depois de atualizar um registro clássico para um registro gerenciado, o Azure não usa mais a conta de armazenamento que originalmente apoiava o registro clássico. Para reduzir o custo, considere excluir a conta de armazenamento ou o contêiner de BLOBs dentro da conta que contém as imagens de contêiner antigas.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com