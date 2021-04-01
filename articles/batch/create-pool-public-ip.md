---
title: Criar uma piscina com endereços IP públicos especificados
description: Saiba como criar uma piscina de Lote que utilize os seus próprios endereços IP públicos.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91849333"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Criar uma piscina Azure Batch com endereços IP públicos especificados

Quando criar uma piscina Azure Batch, pode [providenciar a piscina numa sub-rede de uma rede virtual Azure](batch-virtual-network.md) (VNet) que especifique. As máquinas virtuais na piscina do Lote são acedidas através de endereços IP públicos que são criados por Batch. Estes endereços IP públicos podem ser alterados ao longo do tempo de vida do pool, o que significa que as definições da sua rede podem ficar desatualizadas se os endereços IP não forem atualizados.

Pode criar uma lista de endereços IP públicos estáticos para usar com as máquinas virtuais na sua piscina. Isto permite-lhe controlar a lista de endereços IP públicos e garantir que não mudarão inesperadamente. Isto pode ser especialmente útil se estiver a trabalhar com qualquer serviço externo, como uma base de dados, que restringe o acesso a determinados endereços IP.

Para obter informações sobre a criação de piscinas sem endereços IP públicos, leia [Criar um pool Azure Batch sem endereços IP públicos.](./batch-pool-no-public-ip-address.md)

## <a name="prerequisites"></a>Pré-requisitos

- **Autenticação.** Para utilizar um endereço IP público, a API do cliente Batch deve utilizar a autenticação do [Azure Ative Directory (AD).](batch-aad-auth.md)

- **Um Azure VNet.** Tem de utilizar uma [rede virtual](batch-virtual-network.md) a partir da mesma subscrição Azure na qual está a criar a sua piscina e os seus endereços IP. Apenas VNets baseados em Recursos Azure podem ser utilizados. Certifique-se de que o VNet satisfaz todos os [requisitos gerais](batch-virtual-network.md#vnet-requirements).

- **Pelo menos um endereço IP público Azure**. Para criar um ou mais endereços IP públicos, pode utilizar o [portal Azure](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), a [Interface Azure Command-Line (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)ou [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Certifique-se de seguir os requisitos listados abaixo.

> [!NOTE]
> O lote atribui automaticamente recursos de rede adicionais no grupo de recursos que contém os endereços IP públicos. Por cada 100 nós dedicados, o Batch atribui geralmente um grupo de segurança de rede (NSG) e um equilibrador de carga. Estes recursos são limitados pelas quotas de recursos da subscrição. Ao utilizar piscinas maiores, poderá ter de [solicitar um aumento de quota](batch-quota-limit.md#increase-a-quota) para um ou mais destes recursos.

## <a name="public-ip-address-requirements"></a>Requisitos de endereço IP públicos

Tenha em mente os seguintes requisitos ao criar os seus endereços IP públicos:

- Os endereços IP públicos devem estar na mesma subscrição e região que a conta Batch que utiliza para criar a sua piscina.
- A **atribuição do endereço IP** deve ser definida como **Estática**.
- **O SKU** tem de ser definido como **Norma**.
- Deve ser especificado um nome DNS.
- Os endereços IP públicos devem ser utilizados apenas para os pools de configuração de máquinas virtuais. Nenhum outro recurso deve utilizar estes endereços IP, ou o pool pode sofrer falhas de alocação.
- Nenhuma política de segurança ou bloqueios de recursos deve restringir o acesso de um utilizador ao endereço IP público.
- O número de endereços IP públicos especificados para a piscina deve ser suficientemente grande para acomodar o número de VMs destinados à piscina. Esta deve ser, pelo menos, a soma das propriedades **targetDedicatedNodes**   e **targetLowPriorityNodes**   da piscina. Se não houver endereços IP suficientes, o pool atribui parcialmente os nós de computação e ocorrerá um erro de redimensionar. Atualmente, o Batch usa um endereço IP público para cada 100 VMs.
- Tenha sempre um tampão adicional de endereços IP públicos. Recomendamos a adição de pelo menos um endereço IP público adicional, ou aproximadamente 10% do total de endereços IP públicos que adicionar a uma piscina, o que for maior. Este tampão adicional ajudará o Batch com a sua otimização interna ao escalonar, bem como permitir uma escala mais rápida após uma escala infrutífera para cima ou para baixo.
- Uma vez criada a piscina, não é possível adicionar ou alterar a lista de endereços IP públicos utilizados pela Piscina. Se precisar de modificar a lista, deve apagar a piscina e recriá-la.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Criar uma piscina de lote com endereços IP públicos

O exemplo abaixo mostra como usar a [API de Serviço de Lote Azure](/rest/api/batchservice/pool/add) para criar uma piscina que utiliza endereços IP públicos.

### <a name="batch-service-rest-api"></a>API REST do Serviço do Batch

URI da API REST

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Corpo do Pedido

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Saiba como [criar uma piscina numa sub-rede de uma rede virtual Azure.](batch-virtual-network.md)
- Saiba como [criar uma piscina Azure Batch sem endereços IP públicos.](./batch-pool-no-public-ip-address.md)

