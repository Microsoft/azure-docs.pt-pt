---
title: SKU não está disponível erros
description: Descreve como resolver problemas com o erro não disponível no SKU ao implementar recursos com o Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 37ad70093cce4cea6689fecbc3b6ccb91db5017b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333227"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros de SKUs não disponíveis

Este artigo descreve como resolver o erro **SkuNotAvailable.** Se não conseguir encontrar um SKU adequado nessa região/zona ou numa região/zona alternativa que satisfaça as suas necessidades comerciais, envie um [pedido de SKU](https://aka.ms/skurestriction) ao Suporte Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao utilizar um recurso (normalmente uma máquina virtual), recebe o seguinte código de erro e mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recebe este erro quando o recurso SKU que selecionou (tal como o tamanho VM) não está disponível para o local que selecionou.

Se estiver a implementar uma instância de definição de VM ou spot spot Azure Spot, não existe qualquer capacidade para o Azure Spot neste local. Para obter mais informações, consulte [as mensagens de erro do Spot](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Solução 1 - PowerShell

Para determinar quais SKUs estão disponíveis numa região/zona, utilize o comando [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtrar os resultados por localização. Deve ter a mais recente versão do PowerShell para este comando.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Os resultados incluem uma lista de SKUs para a localização e quaisquer restrições para esse SKU. Note que um SKU pode estar listado como `NotAvailableForSubscription` .

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Algumas amostras adicionais:

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

Appending "fc" no final retorna mais detalhes.

## <a name="solution-2---azure-cli"></a>Solução 2 - Azure CLI

Para determinar quais SKUs estão disponíveis numa região, utilize o `az vm list-skus` comando. Utilize o `--location` parâmetro para filtrar a saída para o local que está a utilizar. Use o `--size` parâmetro para procurar por um nome de tamanho parcial.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

O comando devolve resultados como:

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```

## <a name="solution-3---azure-portal"></a>Solução 3 - Portal Azure

Para determinar quais os SKUs disponíveis numa região, utilize o [portal](https://portal.azure.com). Inscreva-se no portal e adicione um recurso através da interface. Ao definir os valores, vê os SKUs disponíveis para esse recurso. Não precisas de completar o destacamento.

Por exemplo, inicie o processo de criação de uma máquina virtual. Para ver outro tamanho disponível, selecione **Alterar o tamanho**.

![Criar VM](./media/error-sku-not-available/create-vm.png)

Pode filtrar e deslocar-se através dos tamanhos disponíveis.

![SKUs Disponíveis](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Solução 4 - REST

Para determinar quais SKUs estão disponíveis numa região, utilize a operação [Resource Skus - List.](/rest/api/compute/resourceskus/list)

Devolve SKUs e regiões disponíveis no seguinte formato:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

