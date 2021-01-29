---
title: Criar uma piscina em todas as zonas de disponibilidade
description: Aprenda a criar um pool de Lote com política zonal para ajudar a proteger contra falhas.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 98109e1b74106bc636eaa715575e4b30ab29f9e2
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056351"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Criar uma piscina de lote Azure em zonas de disponibilidade

As regiões azure que suportam [Zonas de Disponibilidade](https://azure.microsoft.com/global-infrastructure/availability-zones/) têm um mínimo de três zonas distintas, cada uma com a sua própria fonte de energia independente, rede e sistema de arrefecimento. Ao criar uma piscina Azure Batch utilizando a Configuração de Máquina Virtual, pode optar por providenciar a sua piscina de Lote através das Zonas de Disponibilidade. Criar o seu pool com esta política zonal ajuda a proteger os seus nós de computação Batch contra falhas ao nível do datacenter Azure.

Por exemplo, você poderia criar a sua piscina com política zonal em uma região Azure que suporta três Zonas de Disponibilidade. Se um datacenter Azure em uma Zona de Disponibilidade tiver uma falha de infraestrutura, o seu pool batch ainda terá nós saudáveis nas outras duas Zonas de Disponibilidade, pelo que o pool permanecerá disponível para agendamento de tarefas.

## <a name="regional-support-and-other-requirements"></a>Apoio regional e outros requisitos

O lote mantém a paridade com o Azure no suporte de Zonas de Disponibilidade. Para utilizar a opção zonal, a sua piscina deve ser criada numa [região de Azure suportada.](../availability-zones/az-region.md)

Para que o seu pool Batch seja alocado através de zonas de disponibilidade, a região de Azure em que a piscina é criada deve apoiar o VM SKU solicitado em mais de uma zona. Pode validá-lo ligando para a [API da Lista de Recursos Skus](/rest/api/compute/resourceskus/list) e verifique a **localizaçãoInfo** field of [resourceSku](/rest/api/compute/resourceskus/list#resourcesku). Certifique-se de que mais de uma zona é suportada para o VM SKU solicitado.

Para [o modo de subscrição do utilizador,](accounts.md#batch-accounts)certifique-se de que a subscrição na qual está a criar o seu pool não tem uma restrição de oferta de zona no VM SKU solicitado. Para confirmar isto, ligue para a [API da Lista de Recursos Skus](/rest/api/compute/resourceskus/list) e verifique as [RecursosSkuRestritions](/rest/api/compute/resourceskus/list#resourceskurestrictions). Se existir uma restrição de zona, pode submeter um [bilhete de apoio](../azure-portal/supportability/sku-series-unavailable.md) para remover a restrição de zona.

Note também que não é possível criar uma piscina com uma política zonal se tiver comunicação inter-node ativada e utilizar um [VM SKU que suporta a InfiniBand.](../virtual-machines/workloads/hpc/enable-infiniband.md)

## <a name="create-a-batch-pool-across-availability-zones"></a>Criar uma piscina de lote em zonas de disponibilidade

Os exemplos a seguir mostram como criar uma piscina de lote em zonas de disponibilidade.

> [!NOTE]
> Ao criar a sua piscina com uma política zonal, o serviço Batch tentará alocar a sua piscina em todas as Zonas de Disponibilidade da região selecionada; não é possível especificar uma determinada alocação através das zonas.

### <a name="batch-management-client-net-sdk"></a>Cliente de Gestão de Lotes .NET SDK

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Lote REST API

REST API URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo do pedido

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba como [criar uma piscina numa sub-rede de uma rede virtual Azure.](batch-virtual-network.md)
- Saiba como [criar uma piscina Azure Batch sem endereços IP públicos.](./batch-pool-no-public-ip-address.md)

