---
title: Obter notificações de manutenção para VMs do Azure usando o PowerShell
description: Exibir notificações de manutenção para máquinas virtuais em execução no Azure e iniciar a manutenção de autoatendimento usando o PowerShell.
services: virtual-machines
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: f136fc3001e6ae224e264a59ceba66ed61ead865
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535837"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Manipulando a manutenção planejada usando o PowerShell

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Você pode usar o Azure PowerShell para ver quando as VMs estão agendadas para [manutenção](maintenance-notifications.md). As informações de manutenção planejada estão disponíveis no cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) quando você usa o parâmetro `-status`.
  
As informações de manutenção serão retornadas somente se houver manutenção planejada. Se nenhuma manutenção estiver agendada que afete a VM, o cmdlet não retornará nenhuma informação de manutenção. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

As propriedades a seguir são retornadas em MaintenanceRedeployStatus: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se você pode iniciar a manutenção na VM no momento |
| PreMaintenanceWindowStartTime         | O início da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM |
| PreMaintenanceWindowEndTime           | O final da janela de autoatendimento de manutenção quando você pode iniciar a manutenção em sua VM |
| MaintenanceWindowStartTime            | O início da manutenção agendada na qual o Azure inicia a manutenção em sua VM |
| MaintenanceWindowEndTime              | O fim da janela de manutenção agendada na qual o Azure inicia a manutenção em sua VM |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção na VM |



Você também pode obter o status de manutenção de todas as VMs em um grupo de recursos usando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e não ESPECIFICANDO uma VM.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

O exemplo do PowerShell a seguir usa sua ID de assinatura e retorna uma lista de VMs que estão agendadas para manutenção.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Iniciar a manutenção em sua VM usando o PowerShell

Usando as informações da função na seção anterior, o seguinte iniciará a manutenção em uma VM se **IsCustomerInitiatedMaintenanceAllowed** estiver definido como true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implementações clássicas

Se você ainda tiver VMs herdadas que foram implantadas usando o modelo de implantação clássico, poderá usar o PowerShell para consultar as VMs e iniciar a manutenção.

Para obter o status de manutenção de uma VM, digite:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar a manutenção na sua VM clássica, digite:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Passos Seguintes

Você também pode manipular a manutenção planejada usando o [CLI do Azure](maintenance-notifications-cli.md) ou o [portal](maintenance-notifications-portal.md).
