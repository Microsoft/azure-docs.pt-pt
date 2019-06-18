---
title: SKU do Azure não está disponíveis erros | Documentos da Microsoft
description: Descreve como resolver problemas relacionados com o SKU de erro não está disponível durante a implementação.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: 1dd0532452c3558e53f0236998953d2055ed328c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390771"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros de SKU não disponível

Este artigo descreve como resolver o **SkuNotAvailable** erro. Se não for possível encontrar um SKU adequado nessa região ou uma região alternativa que cumpra o seu negócio precisa, submeta um [pedido SKU](https://aka.ms/skurestriction) ao suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao implementar um recurso (normalmente, uma máquina virtual), receberá o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recebe este erro quando o SKU que selecionou (por exemplo, o tamanho da VM) do recurso não está disponível para a localização que selecionou.

## <a name="solution-1---powershell"></a>Solução 1 - PowerShell

Para determinar qual SKUs estão disponíveis numa região, utilize o [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku) comando. Filtre os resultados com base na localização. Tem de ter a versão mais recente do PowerShell para este comando.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Os resultados incluem uma lista de SKUs para a localização e quaisquer restrições para esse SKU. Tenha em atenção que um SKU poderão ser listado como `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Solução 2 - CLI do Azure

Para determinar qual SKUs estão disponíveis numa região, utilize o `az vm list-skus` comando. Utilize o `--location` parâmetro para filtrar a saída para a localização que estiver a utilizar. Utilize o `--size` parâmetro para procurar por um nome de tamanho parcial.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

O comando devolve resultados como:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Solução de 3 - portal do Azure

Para determinar qual SKUs estão disponíveis numa região, utilize o [portal](https://portal.azure.com). Inicie sessão no portal e adicionar um recurso por meio da interface. À medida que define os valores, verá os SKUs disponíveis para esse recurso. Não precisa de concluir a implementação.

Por exemplo, inicie o processo de criação de uma máquina virtual. Para ver outro tamanho disponível, selecione **alterar o tamanho**.

![Criar VM](./media/resource-manager-sku-not-available-errors/create-vm.png)

Pode filtrar e desloque-se por meio dos tamanhos disponíveis.

![SKUs disponíveis](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Solução 4 - REST

Para determinar qual SKUs estão disponíveis numa região, utilize o [Skus de recursos - lista](/rest/api/compute/resourceskus/list) operação.

Devolve a SKUs disponíveis e as regiões no seguinte formato:

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

