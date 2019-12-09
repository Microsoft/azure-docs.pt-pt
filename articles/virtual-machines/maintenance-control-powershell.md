---
title: Controle de manutenção para máquinas virtuais do Azure usando o PowerShell
description: Saiba como controlar quando a manutenção é aplicada às suas VMs do Azure usando o controle de manutenção e o PowerShell.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932713"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Visualização: controlar atualizações com controle de manutenção e Azure PowerShell

Gerenciar atualizações de plataforma, que não exigem uma reinicialização, usando o controle de manutenção. O Azure frequentemente atualiza sua infraestrutura para melhorar a confiabilidade, desempenho, segurança ou lançamento de novos recursos. A maioria das atualizações é transparente para os usuários. Algumas cargas de trabalho confidenciais, como jogos, streaming de mídia e transações financeiras, não podem tolerar até poucos segundos de uma VM congelando ou desconectando para manutenção. O controle de manutenção oferece a opção de aguardar atualizações de plataforma e aplicá-las em uma janela sem interrupção de 35 dias. 

O controle de manutenção permite que você decida quando aplicar atualizações às suas VMs isoladas.

Com o controle de manutenção, você pode:
- Atualizações em lote em um pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatize as atualizações de plataforma para sua janela de manutenção usando Azure Functions.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

> [!IMPORTANT]
> O controle de manutenção está atualmente em visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitações

- As VMs devem estar em um [host dedicado](./linux/dedicated-hosts.md)ou ser criadas usando um [tamanho de VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será aplicada automaticamente e as restrições de disponibilidade não serão respeitadas.
- O usuário deve ter acesso ao **proprietário do recurso** .


## <a name="enable-the-powershell-module"></a>Habilitar o módulo do PowerShell

Verifique se `PowerShellGet` está atualizado.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Os cmdlets do PowerShell AZ. Maintenance estão em versão prévia, portanto, você precisa instalar o módulo com o parâmetro `AllowPrerelease` no Cloud Shell ou na instalação local do PowerShell.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Se você estiver instalando localmente, abra o prompt do PowerShell como administrador.

Você também pode ser solicitado a confirmar que deseja instalar a partir de um *repositório não confiável*. Digite `Y` ou selecione **Sim para todos** para instalar o módulo.



## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

Crie um grupo de recursos como um contêiner para sua configuração. Neste exemplo, um grupo de recursos chamado *myMaintenanceRG* é criado em *lesteus*. Se você já tiver um grupo de recursos que deseja usar, poderá ignorar essa parte e substituir o nome do grupo de recursos por sua conta no restante dos exemplos.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Use [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) para criar uma configuração de manutenção. Este exemplo cria uma configuração de manutenção chamada *myconfig* com escopo para o host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

O uso de `-MaintenanceScope host` garante que a configuração de manutenção seja usada para controlar atualizações no host.

Se você tentar criar uma configuração com o mesmo nome, mas em um local diferente, receberá um erro. Os nomes de configuração devem ser exclusivos para sua assinatura.

Você pode consultar as configurações de manutenção disponíveis usando [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Atribuir a configuração

Use [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) para atribuir a configuração à VM isolada ou ao host dedicado do Azure.

### <a name="isolated-vm"></a>VM isolada

Aplique a configuração a uma VM usando a ID da configuração. Especifique `-ResourceType VirtualMachines` e forneça o nome da VM para `-ResourceName`e o grupo de recursos da VM para `-ResourceGroupName`. 

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

### <a name="dedicate-host"></a>Host dedicado

Para aplicar uma configuração a um host dedicado, você também precisa incluir `-ResourceType hosts`, `-ResourceParentName` com o nome do grupo de hosts e `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Verificar se há atualizações pendentes

Use [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) para ver se há atualizações pendentes. Use `-subscription` para especificar a assinatura do Azure da VM se ela for diferente da que você fez logon. 

### <a name="isolated-vm"></a>VM isolada

Verifique se há atualizações pendentes para uma VM isolada. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Para verificar se há atualizações pendentes para um host dedicado. Neste exemplo, a saída é formatada como uma tabela para facilitar a leitura. Substitua os valores dos recursos pelos seus próprios.

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

Use [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) para aplicar atualizações pendentes.

### <a name="isolated-vm"></a>VM isolada

Crie uma solicitação para aplicar atualizações a uma VM isolada.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

### <a name="dedicated-host"></a>Anfitrião dedicado

Aplicar atualizações a um host dedicado.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="remove-a-maintenance-configuration"></a>Remover uma configuração de manutenção

Use [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) para excluir uma configuração de manutenção.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).
