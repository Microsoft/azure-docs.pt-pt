---
title: Utilize o CLI para implantar VMs Azure Spot
description: Saiba como usar o CLI para implantar VMs Azure Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 71e0f2e87fc71deef0bdc4dd48425cdc9dd99dc8
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200775"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Implementar VMs spot utilizando o CLI Azure

A utilização de [VMs Azure Spot](../spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma poupança significativa de custos. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar os VM spot. Por isso, os VM spot são ótimos para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços dos VM spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de um Spot VM pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço para o VM será o preço atual para o Spot ou o preço para um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível. Para obter mais informações sobre a fixação do preço máximo, consulte [Spot VMs - Preços](../spot-vms.md#pricing).

O processo de criação de um VM com spot utilizando o CLI Azure é o mesmo que detalhado no [artigo de arranque rápido](./quick-create-cli.md). Basta adicionar o parâmetro "--prioridade Spot", definir o `--eviction-policy` Deallocate (este é o padrão) ou `Delete` , e fornecer um preço máximo ou `-1` . 


## <a name="install-azure-cli"></a>Instalar a CLI do Azure

Para criar VMs spot, tem de executar a versão Azure CLI 2.0.74 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Inicie sessão no Azure com [início de sessão](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Criar um Spot VM

Este exemplo mostra como implementar um Linux Spot VM que não será despejado com base no preço. A política de despejo está definida para negociar o VM, para que possa ser reiniciada mais tarde. Se pretender eliminar o VM e o disco subjacente quando o VM for despejado, desajuste `--eviction-policy` para `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Após a criação do VM, pode consultar para ver o preço máximo de faturação para todos os VMs do grupo de recursos.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simular um despejo

Você pode [simular um despejo](/rest/api/compute/virtualmachines/simulateeviction) de um Spot VM, para testar quão bem a sua aplicação irá remexer para um despejo súbito. 

Substitua as seguintes informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Próximos passos**

Também pode criar um Spot VM utilizando [a Azure PowerShell,](../windows/spot-powershell.md) [portal](../spot-portal.md)ou um [modelo](spot-template.md).

Consultar as informações atuais sobre preços usando a [AZure preços de retalho API](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre preços no Spot. O `meterName` e vai conter `skuName` `Spot` ambos.

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).