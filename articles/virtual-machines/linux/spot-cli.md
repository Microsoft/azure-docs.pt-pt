---
title: Utilize o CLI para implantar máquinas virtuais Azure Spot
description: Saiba como usar o CLI para implementar máquinas virtuais Azure Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: abb29c0826e38af0bbbc1b59e41234acdaaca0f9
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100554773"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Implementar máquinas virtuais Azure Spot utilizando o Azure CLI

A utilização de [Máquinas Virtuais Azure Spot](../spot-vms.md) permite-lhe tirar partido da nossa capacidade não utilizada com uma economia de custos significativa. Em qualquer momento em que a Azure precise da capacidade de volta, a infraestrutura Azure irá despejar máquinas virtuais Azure Spot. Portanto, as máquinas virtuais Azure Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento de lotes, ambientes dev/teste, grandes cargas de trabalho de computação, e muito mais.

Os preços das máquinas virtuais Azure Spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços em VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Tem a opção de definir um preço máximo que está disposto a pagar, por hora, pelo VM. O preço máximo de uma Máquina Virtual Azure Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765` seria um preço máximo de $0.98765 USD por hora. Se definir o preço `-1` máximo, o VM não será despejado com base no preço. O preço do VM será o preço atual para a Máquina Virtual Azure Spot ou o preço de um VM padrão, que sempre é menor, desde que haja capacidade e quota disponível. Para obter mais informações sobre a fixação do preço máximo, consulte [máquinas virtuais Azure Spot - Preços](../spot-vms.md#pricing).

O processo de criação de uma Máquina Virtual Azure Spot utilizando o Azure CLI é o mesmo que detalhado no [artigo de arranque rápido](./quick-create-cli.md). Basta adicionar o parâmetro "--prioridade Spot", definir o `--eviction-policy` Deallocate (este é o padrão) ou `Delete` , e fornecer um preço máximo ou `-1` . 


## <a name="install-azure-cli"></a>Instalar a CLI do Azure

Para criar máquinas virtuais Azure Spot, tem de executar a versão Azure CLI 2.0.74 ou posterior. Execute **az --version** para descobrir a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

Inicie sessão no Azure com [início de sessão](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Criar uma máquina virtual Azure Spot

Este exemplo mostra como implementar uma Máquina Virtual Linux Azure Spot que não será despejada com base no preço. A política de despejo está definida para negociar o VM, para que possa ser reiniciada mais tarde. Se pretender eliminar o VM e o disco subjacente quando o VM for despejado, desajuste `--eviction-policy` para `Delete` .

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

Você pode [simular um despejo](/rest/api/compute/virtualmachines/simulateeviction) de uma Máquina Virtual Azure Spot, para testar quão bem a sua aplicação irá remexe para um despejo súbito. 

Substitua as seguintes informações: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Próximos passos**

Também pode criar uma máquina virtual Azure Spot utilizando [a Azure PowerShell,](../windows/spot-powershell.md) [portal](../spot-portal.md)ou um [modelo](spot-template.md).

Consultar informações sobre preços correntes utilizando os preços de [retalho Azure API](/rest/api/cost-management/retail-prices/azure-retail-prices) para obter informações sobre a Máquina Virtual Azure Spot. O `meterName` e vai conter `skuName` `Spot` ambos.

Se encontrar um erro, consulte [códigos de erro](../error-codes-spot.md).