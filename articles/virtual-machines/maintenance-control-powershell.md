---
title: Controlo de manutenção para máquinas virtuais Azure usando PowerShell
description: Saiba como controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o controlo de manutenção e o PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: f33cb7d4d005f15d0a5fcc70d56ebd4698f86694
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988226"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Atualizações de controlo com Controlo de Manutenção e Azure PowerShell

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados ao Azure. Este tópico abrange as opções Azure PowerShell para o controlo de manutenção. Para obter mais sobre os benefícios da utilização do controlo de manutenção, das suas limitações e outras opções de gestão, consulte [atualizações da plataforma de gestão com o Controlo de Manutenção.](maintenance-control.md)
 
## <a name="enable-the-powershell-module"></a>Ativar o módulo PowerShell

Certifique-se de que `PowerShellGet` está atualizado.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Instale o `Az.Maintenance` módulo PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Se estiver a instalar localmente, certifique-se de que abre o seu pedido PowerShell como administrador.

Também pode ser solicitado que confirme que pretende instalar a partir de um *repositório não fidedvo*. Digite `Y` ou selecione **Sim a Todos** para instalar o módulo.


## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como recipiente para a sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado em *Eastus*. Se já tiver um grupo de recursos que pretende utilizar, pode saltar esta peça e substituir o nome do grupo de recursos pelo seu próprio nos restantes exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Utilize [a Configuração new-AzMaintenance](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* telescópio para o anfitrião. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

A `-MaintenanceScope host` utilização garante que a configuração de manutenção é utilizada para controlar atualizações ao hospedeiro.

Se tentar criar uma configuração com o mesmo nome, mas num local diferente, terá um erro. Os nomes de configuração devem ser exclusivos do seu grupo de recursos.

Pode consultar as configurações de manutenção disponíveis utilizando [a Configuração Get-AzMaintenance.](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Criar uma configuração de manutenção com janela programada

Também pode declarar uma janela agendada quando o Azure aplicará as atualizações dos seus recursos. Este exemplo cria uma configuração de manutenção chamada myConfig com uma janela programada de 5 horas na quarta segunda-feira de cada mês. Uma vez que crie uma janela programada, já não terá de aplicar as atualizações manualmente.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> A **duração da** manutenção deve ser *de 2 horas* ou mais. A **recorrência** da manutenção deve ser definida pelo menos uma vez em 35 dias.

A **recorrência da** manutenção pode ser expressa diariamente, semanal ou mensalmente. Alguns exemplos incluem:
 - diariamente- "recurEvery: Day" **ou** "recurEvery: 3Days" 
 - semanalmente- "recurEvery: 3Weeks" **ou** "recurEvery: Week Saturday,Sunday" 
 - mensal: "recurevery: Month day23,day24" **ou** "recurEvery: Month Last Sunday" **ou** "recurevery: Month Fourth Monday"  
      

## <a name="assign-the-configuration"></a>Atribuir a configuração

Utilize [a assinatura new-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) para atribuir a configuração ao seu VM isolado ou anfitrião dedicado Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração num VM utilizando o ID da configuração. Especifique `-ResourceType VirtualMachines` e forneça o nome do VM para , e o grupo de recursos do `-ResourceName` VM para `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para aplicar uma configuração a um anfitrião dedicado, também é necessário `-ResourceType hosts` incluir, `-ResourceParentName` com o nome do grupo anfitrião, e `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Verifique se há atualizações pendentes

Utilize [o Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) para ver se existem atualizações pendentes. Utilize `-subscription` para especificar a subscrição Azure do VM se for diferente daquela em que está iniciado.

Se não houver atualizações para mostrar, este comando não devolverá nada. Caso contrário, devolverá um objeto PSApplyUpdate:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações pendentes de um VM isolado. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar as atualizações pendentes de um anfitrião dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores pelos recursos pelos seus.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Aplicar atualizações

Utilize [o Novo AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolado

Crie um pedido para aplicar atualizações a um VM isolado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

No sucesso, este comando devolverá um `PSApplyUpdate` objeto. Pode utilizar o atributo Nome no `Get-AzApplyUpdate` comando para verificar o estado da atualização. Consulte [o estado da atualização do cheque](#check-update-status).

### <a name="dedicated-host"></a>Anfitrião dedicado

Aplique atualizações a um anfitrião dedicado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Verifique o estado da atualização
Utilize [o Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) para verificar o estado de uma atualização. Os comandos apresentados abaixo mostram o estado da última atualização utilizando `default` o `-ApplyUpdateName` parâmetro. Pode substituir o nome da atualização (devolvida pelo comando [New-AzApplyUpdate)](/powershell/module/az.maintenance/new-azapplyupdate) para obter o estado de uma atualização específica.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime será o momento em que a atualização foi concluída, iniciada por si ou pela plataforma no caso de não ser utilizada a janela de auto-manutenção. Se nunca tiver havido uma atualização aplicada através do controlo de manutenção, mostrará o valor predefinido.

### <a name="isolated-vm"></a>VM isolado

Verifique se há atualizações para uma máquina virtual específica.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Verifique se há novidades para um anfitrião dedicado.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Remover uma configuração de manutenção

Utilize [remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) para eliminar uma configuração de manutenção.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Próximos passos
Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
