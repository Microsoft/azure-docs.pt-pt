---
title: Receba notificações de manutenção para VMs Azure usando PowerShell
description: Ver notificações de manutenção para máquinas virtuais em funcionamento em Azure e iniciar a manutenção de self-service utilizando o PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 9942ad359bf1e1e7a02f30979ba4a9325d90d484
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336059"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Manuseamento da manutenção planeada utilizando o PowerShell

**Este artigo aplica-se a máquinas virtuais que executam o Linux e o Windows.**

Pode utilizar a Azure PowerShell para ver quando estão programados VMs para [manutenção](maintenance-notifications.md). As informações de manutenção planeadas estão disponíveis a partir do cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) quando utilizar o `-status` parâmetro.
  
As informações de manutenção só são devolvidas se houver manutenção planeada. Se não for programada qualquer manutenção que tenha impacto no VM, o cmdlet não devolve nenhuma informação de manutenção. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

Saída

```
MaintenanceRedeployStatus               : 
  IsCustomerInitiatedMaintenanceAllowed : True
  PreMaintenanceWindowStartTime         : 5/14/2018 12:30:00 PM
  PreMaintenanceWindowEndTime           : 5/19/2018 12:30:00 PM
  MaintenanceWindowStartTime            : 5/21/2018 4:30:00 PM
  MaintenanceWindowEndTime              : 6/4/2018 4:30
  LastOperationResultCode               : None 
```

As seguintes propriedades são devolvidas em ManutençãoRedeployStatus: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento |
| PreMaintenanceWindowStartTime         | O início da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM |
| PreMaintenanceWindowEndTime           | O fim da janela de autosserviço de manutenção quando pode iniciar a manutenção no seu VM |
| ManutençãoWindowStartTime            | O início da manutenção programada em que a Azure inicia a manutenção no seu VM |
| ManutençãoWindowEndTime              | O fim da janela programada de manutenção em que a Azure inicia a manutenção no seu VM |
| ÚltimaOperaçãoResultCode               | O resultado da última tentativa de iniciar a manutenção no VM |



Também pode obter o estado de manutenção de todos os VMs de um grupo de recursos utilizando [o Get-AzVM](/powershell/module/az.compute/get-azvm) e não especificando um VM.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

O exemplo seguinte da PowerShell pega no seu ID de subscrição e devolve uma lista de VMs que estão programados para manutenção.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Inicie a manutenção no seu VM utilizando o PowerShell

Utilizando informações da função na secção anterior, inicia-se a manutenção num VM se **o IsCustomerInitiatedMaintenanceAllowed** for definido como verdadeiro.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implementações clássicas

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se ainda tiver VMs legados que foram implantados usando o modelo de implementação clássico, pode utilizar o PowerShell para consultar VMs e iniciar a manutenção.

Para obter o estado de manutenção de um VM, escreva:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar a manutenção no seu VM clássico, escreva:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Passos seguintes

Também pode manusear a manutenção planeada utilizando o [Azure CLI](maintenance-notifications-cli.md) ou [o portal](maintenance-notifications-portal.md).
