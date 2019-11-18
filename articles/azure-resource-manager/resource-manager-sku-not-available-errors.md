---
title: Erros de SKU não disponível
description: Descreve como solucionar o erro SKU não disponível ao implantar recursos com o Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.openlocfilehash: 56afca6b6a59ca08f3fd59c4d9b3ebf12bda415a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150486"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros de SKU não disponível

Este artigo descreve como resolver o erro **SkuNotAvailable** . Se não for possível encontrar um SKU adequado nessa região ou em uma região alternativa que atenda às suas necessidades de negócios, envie uma [solicitação de SKU](https://aka.ms/skurestriction) para o suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao implantar um recurso (normalmente uma máquina virtual), você recebe o seguinte código de erro e mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Você recebe esse erro quando o SKU de recurso que você selecionou (como o tamanho da VM) não está disponível para o local selecionado.

## <a name="solution-1---powershell"></a>Solução 1 - PowerShell

Para determinar quais SKUs estão disponíveis em uma região, use o comando [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) . Filtre os resultados por local. Você deve ter a versão mais recente do PowerShell para este comando.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Os resultados incluem uma lista de SKUs para o local e quaisquer restrições para essa SKU. Observe que uma SKU pode ser listada como `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Solução 2 - CLI do Azure

Para determinar quais SKUs estão disponíveis em uma região, use o comando `az vm list-skus`. Use o parâmetro `--location` para filtrar a saída para o local que você está usando. Use o parâmetro `--size` para pesquisar por um nome de tamanho parcial.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

O comando retorna resultados como:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Solução de 3 - portal do Azure

Para determinar quais SKUs estão disponíveis em uma região, use o [portal](https://portal.azure.com). Entre no portal e adicione um recurso por meio da interface. Conforme você define os valores, você vê os SKUs disponíveis para esse recurso. Você não precisa concluir a implantação.

Por exemplo, inicie o processo de criação de uma máquina virtual. Para ver outro tamanho disponível, selecione **alterar tamanho**.

![Criar VM](./media/resource-manager-sku-not-available-errors/create-vm.png)

Você pode filtrar e percorrer os tamanhos disponíveis.

![SKUs disponíveis](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Solução 4-REST

Para determinar quais SKUs estão disponíveis em uma região, use a operação [SKUs de recurso-lista](/rest/api/compute/resourceskus/list) .

Ele retorna os SKUs e as regiões disponíveis no seguinte formato:

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

