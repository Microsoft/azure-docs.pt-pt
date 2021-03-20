---
title: Controlo de manutenção para upgrades de imagem DE EM em conjuntos de escala de máquina virtual Azure usando PowerShell
description: Aprenda a controlar quando as atualizações automáticas de imagem do SO forem lançadas para os conjuntos de escala de máquina virtual Azure utilizando o controlo de manutenção e o PowerShell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91974843"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Pré-visualização: Controlo de manutenção para upgrades de imagem DE EM em conjuntos de escala de máquina virtual Azure usando PowerShell

O controlo de manutenção permite-lhe decidir quando aplicar atualizações automáticas de imagem de so de hóspedes nos conjuntos de escala de máquina virtual. Este tópico abrange as opções Azure PowerShell para o controlo de manutenção. Para obter mais informações sobre a utilização do controlo de manutenção, consulte [o controlo de manutenção para conjuntos de balanças de máquinas virtuais Azure](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> O controlo de manutenção para atualizações de imagem DO em conjuntos de escala de máquina virtual Azure está atualmente em Visualização Pública.
> Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


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

## <a name="connect-to-an-azure-account"></a>Ligar a uma conta do Azure

Ligue-se à sua conta Azure desejada utilizando [o Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) e [o Set-AzAccount](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como recipiente para a sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado em *Eastus2*. Se já tem um grupo de recursos que pretende utilizar, pode saltar esta parte. Basta substituir o nome do grupo de recursos pelo seu próprio nos restantes exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Utilize [a Configuração new-AzMaintenance](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myConfig* telescópio para a imagem OS. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> A **duração da** manutenção deve ser *de 5 horas* ou mais. A **recorrência da** manutenção deve ser definida como *Dia*.

A `-MaintenanceScope OSImage` utilização garante que a configuração de manutenção é utilizada para controlar atualizações ao so do hóspede.

Se tentar criar uma configuração com o mesmo nome, mas num local diferente, terá um erro. Os nomes de configuração devem ser exclusivos do seu grupo de recursos.

Pode consultar as configurações de manutenção disponíveis utilizando [a Configuração Get-AzMaintenance.](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Associe a sua balança de máquina virtual definida à configuração de manutenção

Um conjunto de escala de máquina virtual pode ser associado a qualquer configuração de Manutenção, independentemente da região e subscrição da configuração manutenção. Ao optar pela configuração manutenção, novas atualizações de imagem DE SO para o conjunto de escala serão automaticamente agendadas na próxima janela de manutenção disponível.

Utilize [o New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) para associar a sua balança de máquina virtual definir a configuração de manutenção.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Ativar atualização automática de SO

Pode ativar atualizações automáticas de SO para cada conjunto de balanças de máquinas virtuais que utilizarão o controlo de manutenção. Consulte o documento [Azure virtual machine scale set automatic OS image upgrades](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) para permitir upgrades automáticos de SO no seu conjunto de escala de máquina virtual. 


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre manutenção e atualizações para máquinas virtuais em funcionamento em Azure.

> [!div class="nextstepaction"]
> [Manutenção e atualizações](maintenance-and-updates.md)