---
title: Atualizar um registo de contentor do Azure clássico
description: Tire partido da expandidos conjunto de funcionalidades do Basic, Standard e Premium geridos registos de contentores ao atualizar o seu registo de contentor clássico não gerido.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a5099feee34eb5497b68987485412e29ad5d5365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480359"
---
# <a name="upgrade-a-classic-container-registry"></a>Atualizar um registo de contentor clássico

O Azure Container Registry (ACR) está disponível em vários escalões de serviço, [conhecido como SKUs](container-registry-skus.md). A versão inicial do ACR oferecido um único SKU, clássico, que não possui vários recursos inerentes para o básico, Standard e Premium SKUs (coletivamente conhecido como *geridos* registos).

O SKU clássico está a ser preterido e não estará disponível depois de Abril de 2019. Este artigo fornece detalhes sobre como migrar seu registo clássico não gerenciado para um dos SKUs geridos, para que possam tirar partido do seu conjunto de recursos aprimorados.

## <a name="why-upgrade"></a>Por que atualizar?

O registo clássico SKU está sendo **preterido**e não estará disponível após **Abril de 2019**. Todos os registos de clássico existentes devem ser atualizados antes de Abril de 2019. Recursos de portal de gestão de registos de clássico serão descontinuados. Criação de novos registos de clássico será desativada passados Abril de 2019.

Devido a preterição planeada e seus recursos limitados de registos de clássico não gerido, todos os registos de clássico devem ser atualizados para registos geridos (básico, Standard ou Premium). Essas SKUs de nível mais alto integrar o registo mais profundamente às capacidades do Azure. Para obter mais informações sobre preços e capacidades de diferentes escalões de serviço, consulte [SKUs do registo de contentor](container-registry-skus.md).

O registo clássico depende da conta de armazenamento Azure Aprovisiona automaticamente na sua subscrição do Azure ao criar o registo. Por outro lado, o básico, Standard e Premium SKUs tirar partido do Azure [avançadas funcionalidades de armazenamento](container-registry-storage.md) ao tratar de forma transparente o armazenamento das suas imagens para. Não é criada uma conta de armazenamento em separado na sua própria subscrição.

Armazenamento de registo gerido fornece as seguintes vantagens:

* São imagens de contentor [encriptados em descanso](container-registry-storage.md#encryption-at-rest).
* As imagens são armazenadas [armazenamento georredundante](container-registry-storage.md#geo-redundant-storage), garantindo cópia de segurança das suas imagens com replicação de multirregião (apenas para SKU Premium).
* Capacidade de livremente [mover entre SKUs](container-registry-skus.md#changing-skus), permitindo um débito mais elevado, ao escolher um SKU de nível superior. Com cada SKU, ACR pode satisfazer as suas necessidades de débito à medida que suas necessidades aumentam.
* Modelo de segurança unificada para o registo e o respetivo armazenamento fornece gestão de direitos simplificada. Gerir permissões apenas para o registo de contentor, sem ter de gerir também permissões para uma conta de armazenamento separada.

Para obter detalhes adicionais sobre armazenamento de imagens no ACR, consulte [armazenamento de imagens de contentor no Azure Container Registry](container-registry-storage.md).

## <a name="migration-considerations"></a>Considerações sobre a migração

Quando atualiza um registo de clássico para um registo gerido, Azure tem de copiar todas as imagens de contentor existente da conta de armazenamento criados ACR na sua subscrição para uma conta de armazenamento gerida pelo Azure. Dependendo do tamanho do seu registo, este processo pode demorar alguns minutos a várias horas. Para fins de estimativa, esperar que um tempo de migração de aproximadamente 0,5 GiB por minuto.

Durante o processo de conversão, `docker push` operações estão desativadas durante os últimos 10% da migração. `docker pull` continua a funcionar normalmente.

Não eliminar ou modificar os conteúdos da conta de armazenamento de segurança do registo clássico durante o processo de conversão. Fazer isso pode resultar em danos imagens de contentor.

Quando a migração estiver concluída, a conta de armazenamento na sua subscrição que originalmente seu registo clássico de segurança já não é utilizada pelo Azure Container Registry. Depois de verificar se a migração foi concluída com êxito, considere eliminar a conta de armazenamento para o ajudar a minimizar os custos.

>[!IMPORTANT]
> A atualização da implementação clássica para um dos SKUs geridos é um **processo unidirecional**. Assim que tenha convertido um registo para básico, Standard ou Premium do clássico, não é possível reverter para clássico. Pode, no entanto, livremente mover entre os SKUs geridos com capacidade suficiente para o seu registo.

## <a name="how-to-upgrade"></a>Como atualizar

Pode atualizar um registo clássico não gerenciado para um dos SKUs geridos de várias formas. As secções seguintes, descrevemos o processo para utilizar o [CLI do Azure] [ azure-cli] e o [portal do Azure][azure-portal].

## <a name="upgrade-in-azure-cli"></a>Atualização na CLI do Azure

Para atualizar um registo clássico na CLI do Azure, execute o [atualização do az acr] [ az-acr-update] de comando e especifique o novo SKU para o registo. No exemplo a seguir, o nome de um registo clássico *myclassicregistry* é atualizado para o SKU Premium:

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

Quando a migração estiver concluída, deve ver um resultado semelhante ao seguinte. Tenha em atenção que o `sku` é "Premium" e o `storageAccount` é `null`, que indica que o Azure gere agora o armazenamento de imagens para este registo.

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

Se especificar um registo gerido SKU cuja capacidade máxima é inferior ao tamanho do seu registo clássico, receberá uma mensagem de erro semelhante ao seguinte.

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

Se receber um erro semelhante, execute o [atualização do az acr] [ az-acr-update] novamente o comando e especificar o SKU sugerido, que é o SKU de nível mais elevado de next que pode acomodar suas imagens.

## <a name="upgrade-in-azure-portal"></a>Atualizar no portal do Azure

Ao atualizar um registo clássico com o portal do Azure, o Azure seleciona automaticamente o Standard ou Premium SKU, dependendo de qual SKU consegue acomodar suas imagens. Por exemplo, se o seu registo contém menos de 100 GiB em imagens, Azure seleciona automaticamente e converte o registo de clássico para o Standard (máximo de 100 GiB).

Para atualizar o seu registo clássico com o portal do Azure, navegue para o registo de contentor **descrição geral** e selecione **atualização para o registo gerido**.

![Clique no botão no portal do Azure da interface do Usuário de atualização de registo clássico][update-classic-01-upgrade]

Selecione **OK** para confirmar que pretende atualizar para um registo gerido.

Durante a migração, o portal indica que o Registro **estado de aprovisionamento** é *atualização*. Conforme mencionado anteriormente, `docker push` operações estão desativadas durante os últimos 10% da migração. Não delete ou update, a conta de armazenamento utilizada pelo registo clássico, enquanto a migração está em curso – se o fizer, pode resultar em danos de imagem.

![Progresso no portal do Azure da interface do Usuário da atualização de registo clássico][update-classic-03-updating]

Quando a migração estiver concluída, o **estado de aprovisionamento** indica *Succeeded*, e pode retomar as operações normais com o registo.

![Estado de conclusão no portal do Azure da interface do Usuário de atualização de registo clássico][update-classic-04-updated]

## <a name="next-steps"></a>Passos Seguintes

Assim que tiver atualizado um registo de clássico para um registo gerido, o Azure já não utiliza a conta de armazenamento que originalmente de segurança do registo clássico. Para reduzir os custos, considere eliminar a conta de armazenamento ou o contentor de BLOBs na conta que contém as imagens de contentor antigo.

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com