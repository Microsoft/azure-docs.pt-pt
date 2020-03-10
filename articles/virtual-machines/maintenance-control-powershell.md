---
title: Controlo de manutenção para máquinas virtuais Azure utilizando powerShell
description: Aprenda a controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o Controle de Manutenção e powerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 7e4586a5fba91fbc7432aa352b9608be728e8654
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382708"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Pré-visualização: As atualizações de controlo de controlo de controlo de manutenção com o Controlo de Manutenção e a PowerShell Azure

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando o controlo de manutenção. O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de esperar nas atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados.

Com controlo de manutenção, pode:
- Atualizações do lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Amate automaticamente as atualizações da plataforma para a sua janela de manutenção utilizando funções Azure.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O Controlo de Manutenção encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitações

- Os VMs devem estar num [hospedeiro dedicado,](./linux/dedicated-hosts.md)ou ser criados com um [tamanho VM isolado](./linux/isolation.md).
- Após 35 dias, será aplicada uma atualização automaticamente.
- O utilizador deve ter acesso ao Colaborador do **Recurso.**


## <a name="enable-the-powershell-module"></a>Ativar o módulo PowerShell

Certifique-se de que `PowerShellGet` está atualizado.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Os cmdlets Az.Maintenance PowerShell estão em pré-visualização, pelo que é necessário instalar o módulo com o `AllowPrerelease` parâmetro na Cloud Shell ou na instalação local da PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Se estiver a instalar-se localmente, certifique-se de que abre o seu pedido PowerShell como administrador.

Também lhe é pedido que confirme que pretende instalar a partir de um *repositório não confiável*. Escreva `Y` ou **selecione Sim a Todos** para instalar o módulo.



## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como recipiente para a sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado no *leste.* Se já tem um grupo de recursos que pretende utilizar, pode saltar esta parte e substituir o nome do grupo de recursos por si próprio nos restantes exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Utilize a [Configuração New-AzMaintenancePara](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* ao anfitrião. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

A utilização `-MaintenanceScope host` garante que a configuração de manutenção é utilizada para controlar as atualizações do hospedeiro.

Se tentar criar uma configuração com o mesmo nome, mas num local diferente, terá um erro. Os nomes de configuração devem ser exclusivos da sua subscrição.

Pode consultar as configurações de manutenção disponíveis utilizando a [Configuração Get-AzMaintenance](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Utilize a [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) para atribuir a configuração ao seu VM isolado ou ao anfitrião dedicado Azure.

### <a name="isolated-vm"></a>VM isolado

Aplique a configuração num VM utilizando o ID da configuração. Especifique `-ResourceType VirtualMachines` e forneça o nome do VM para `-ResourceName`, e o grupo de recursos do VM para `-ResourceGroupName`. 

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

Para aplicar uma configuração a um anfitrião dedicado, também precisa de incluir `-ResourceType hosts`, `-ResourceParentName` com o nome do grupo anfitrião, e `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Verifique as atualizações pendentes

Utilize [o Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) para ver se existem atualizações pendentes. Utilize `-subscription` para especificar a subscrição Azure do VM se for diferente daquela em que está ligado.

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

Verifique se há atualizações pendentes para um VM isolado. Neste exemplo, a saída é formatada como uma tabela de legibilidade.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar as atualizações pendentes para um anfitrião dedicado. Neste exemplo, a saída é formatada como uma tabela de legibilidade. Substitua os valores pelos recursos pelos seus.

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

Utilize o [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolado

Crie um pedido para aplicar atualizações a um VM isolado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

No sucesso, este comando devolverá um objeto `PSApplyUpdate`. Pode utilizar o atributo nome no comando `Get-AzApplyUpdate` para verificar o estado da atualização. Consulte o [estado da atualização](#check-update-status).

### <a name="dedicated-host"></a>Anfitrião dedicado

Aplique atualizações para um anfitrião dedicado.

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
Utilize [o Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) para verificar o estado de uma atualização. Os comandos abaixo mostrados mostram o estado da última atualização utilizando `default` para o parâmetro `-ApplyUpdateName`. Pode substituir o nome da atualização (devolvido pelo comando [New-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) para obter o estado de uma atualização específica.

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
O LastUpdateTime será o momento em que a atualização foi completada, iniciada por si ou pela plataforma no caso de não ter sido utilizada a janela de auto-manutenção. Se nunca houve uma atualização aplicada através do controlo de manutenção, mostrará o valor predefinido.

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

Consulte as atualizações para um anfitrião dedicado.

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

Utilize a [Configuração de Eliminação-AzManutenção](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) para eliminar uma configuração de manutenção.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
