---
title: Erros não disponíveis sKU
description: Descreve como resolver problemas o Erro Não Disponível do SKU ao implementar recursos com o Gestor de Recursos Azure.
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: be341a5ed5321fe71b0e3b34ba5f6cc823958c8b
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462297"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolver erros para SKU não disponíveis

Este artigo descreve como resolver o erro **SkuNotDisponível.** Se não conseguir encontrar um SKU adequado nessa região/zona ou numa região/zona alternativa que satisfaça as suas necessidades de negócio, envie um [pedido de SKU](https://aka.ms/skurestriction) ao Suporte Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao implantar um recurso (normalmente uma máquina virtual), recebe o seguinte código de erro e mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recebe este erro quando o recurso SKU que selecionou (como o tamanho VM) não está disponível para o local que selecionou.

Se estiver a implementar uma instância de conjunto de vm ou de escala spot Azure, não há qualquer capacidade para o Azure Spot neste local. Para mais informações, consulte [as mensagens](../../virtual-machines/error-codes-spot.md)de erro do Spot .

## <a name="solution-1---powershell"></a>Solução 1 - PowerShell

Para determinar quais as SKUs disponíveis numa região/zona, utilize o comando [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku) Filtre os resultados por localização. Deve ter a versão mais recente da PowerShell para este comando.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Os resultados incluem uma lista de SKUs para a localização e quaisquer restrições para esse SKU. Note que um SKU pode ser listado como `NotAvailableForSubscription`.

```powershell
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

A despesa "fc" no final devolve mais detalhes.

## <a name="solution-2---azure-cli"></a>Solução 2 - CLI do Azure

Para determinar quais as UsS KK estão disponíveis numa região, utilize o comando `az vm list-skus`. Utilize o parâmetro `--location` para filtrar a saída para o local que está a utilizar. Utilize o parâmetro `--size` para procurar por um nome de tamanho parcial.

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

Para determinar quais as UsC disponíveis numa região, utilize o [portal](https://portal.azure.com). Inscreva-se no portal e adicione um recurso através da interface. Ao definir os valores, vê as SKUs disponíveis para esse recurso. Não precisas de completar o destacamento.

Por exemplo, inicie o processo de criação de uma máquina virtual. Para ver outro tamanho disponível, selecione **Tamanho De mudança**.

![Criar VM](./media/error-sku-not-available/create-vm.png)

Pode filtrar e percorrer os tamanhos disponíveis.

![SKUs disponível](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Solução 4 - REST

Para determinar quais as SKUs disponíveis numa região, utilize a operação [Resource Skus - List.](/rest/api/compute/resourceskus/list)

Devolve As UsS e regiões disponíveis no seguinte formato:

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

