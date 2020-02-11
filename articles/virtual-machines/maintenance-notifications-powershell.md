---
title: Obtenha notificações de manutenção para VMs Azure usando powerShell
description: Consulte as notificações de manutenção para máquinas virtuais em funcionamento em Azure e inicie a manutenção do self-service utilizando o PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: c23a06c502bd43b52cb35220b711084c7c8c1c7a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121835"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Manuseamento de manutenção planeada usando powerShell

**Este artigo aplica-se a máquinas virtuais que executam tanto o Linux como o Windows.**

Pode utilizar o Azure Powershell para ver quando os VMs estão programados para [manutenção](maintenance-notifications.md). As informações de manutenção planeadas estão disponíveis a partir do cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) quando utilizar o parâmetro `-status`.
  
As informações de manutenção só são devolvidas se houver manutenção planeada. Se não estiver prevista qualquer manutenção que tenha impacto no VM, o cmdlet não devolve nenhuma informação de manutenção. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

As seguintes propriedades são devolvidas sob o Estatuto de Reafectação de Manutenção: 

| Valor | Descrição   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se pode iniciar a manutenção no VM neste momento |
| PreMaintenanceWindowStartTime         | O início da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM |
| PreMaintenanceWindowEndTime           | O fim da janela de self-service de manutenção quando pode iniciar a manutenção no seu VM |
| MaintenanceWindowStartTime            | O início da manutenção programada em que o Azure inicia a manutenção no seu VM |
| MaintenanceWindowEndTime              | O fim da janela programada de manutenção na qual o Azure inicia a manutenção no seu VM |
| LastOperationResultCode               | O resultado da última tentativa de iniciar a manutenção no VM |



Também pode obter o estado de manutenção de todos os VMs num grupo de recursos, utilizando o [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e não especificando um VM.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

O exemplo seguinte da PowerShell pega no id de subscrição e devolve uma lista de VMs que estão programados para manutenção.

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

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Inicie a manutenção no seu VM usando powerShell

Utilizando informações da função na secção anterior, a seguinte inicia a manutenção num VM se **o IsCustomerIniciardMaintenanceAllowed** for definido como verdadeiro.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Implementações clássicas

Se ainda tiver VMs legados que foram implantados usando o modelo de implementação clássico, pode usar o PowerShell para consultar VMs e iniciar a manutenção.

Para obter o estado de manutenção de um VM, escreva:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Para iniciar a manutenção do seu VM clássico, escreva:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Passos seguintes

Também pode manusear a manutenção planeada utilizando o [Azure CLI](maintenance-notifications-cli.md) ou [portal](maintenance-notifications-portal.md).
