---
title: SKU não está disponível erros
description: Descreve como resolver problemas com o erro não disponível no SKU ao implementar recursos com o Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503903"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros de SKUs não disponíveis

Este artigo descreve como resolver o erro **SkuNotAvailable.** Se não conseguir encontrar um SKU adequado nessa região/zona ou numa região/zona alternativa que satisfaça as suas necessidades comerciais, envie um [pedido de SKU](/troubleshoot/azure/general/region-access-request-process) ao Suporte Azure.

## <a name="symptom"></a>Sintoma

Ao utilizar um recurso (normalmente uma máquina virtual), recebe o seguinte código de erro e mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recebe este erro quando o recurso SKU que selecionou (tal como o tamanho VM) não está disponível para o local que selecionou.

Se estiver a implementar uma instância de definição de VM ou spot spot Azure Spot, não há qualquer capacidade para o Azure Spot neste local. Para obter mais informações, consulte [as mensagens de erro do Spot](../../virtual-machines/error-codes-spot.md).

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

Para filtrar por localização e SKU, utilize:

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

O comando devolve resultados como:

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Solução 2 - Azure CLI

Para determinar quais SKUs estão disponíveis numa região, utilize o comando [az vm list-skus.](/cli/azure/vm#az_vm_list_skus) Utilize o `--location` parâmetro para filtrar a saída por localização. Use o `--size` parâmetro para procurar por um nome de tamanho parcial. Use o `--all` parâmetro para mostrar todas as informações, incluindo tamanhos que não estão disponíveis para a subscrição atual.

Tem de ter a versão 2.15.0 do Azure CLI ou mais tarde. Para verificar a sua versão, use `az --version` . Se necessário, [atualize a sua instalação](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

O comando devolve resultados como:

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
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