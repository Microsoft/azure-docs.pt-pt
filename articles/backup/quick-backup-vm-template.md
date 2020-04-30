---
title: Quickstart - Modelo de Gestor de Recursos VM Backup
description: Aprenda a fazer o back up das suas máquinas virtuais com o modelo de Gestor de Recursos Azure
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d6fb73801f0f460daf2ed70f8dc88187e41ea887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458850"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Volte a fazer uma máquina virtual em Azure com modelo de Gestor de Recursos

[O Azure Backup](backup-overview.md) apoia as máquinas e aplicações no local e os VMs Azure. Este artigo mostra-lhe como fazer o back up de um VM Azure com modelo de Gestor de Recursos e Azure PowerShell. Este quickstart centra-se no processo de implementação de um modelo de Gestor de Recursos para criar um cofre de Serviços de Recuperação. Para obter mais informações sobre o desenvolvimento de modelos de Gestor de Recursos, consulte a [documentação do Gestor de Recursos](/azure/azure-resource-manager/) e a referência do [modelo](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Em alternativa, pode fazer o backup a um VM utilizando [o Azure PowerShell,](./quick-backup-vm-powershell.md)o [Azure CLI](quick-backup-vm-cli.md)ou no [portal Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Criar um cofre vm e serviços de recuperação

Um [cofre dos Serviços](backup-azure-recovery-services-vault-overview.md) de Recuperação é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos de quickstart Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Este modelo permite-lhe implementar um cofre simples de Serviços vM e de Recuperação do Windows configurado com a Política padrão de Proteção.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json" range="1-247" highlight="221-245":::

Os recursos definidos no modelo são:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresss**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/cofres**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContentors/protectedItems**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults/backupfabrics/protectioncontainers/protecteditems)

### <a name="deploy-the-template"></a>Implementar o modelo

Para implementar o modelo, selecione **Experimente-o** para abrir a Casca de Nuvem Azure e, em seguida, colar o seguinte script PowerShell na janela da concha. Para colar o código, clique à direita na janela da concha e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

O Azure PowerShell é utilizado para implementar o modelo de Gestor de Recursos neste arranque rápido. O [portal Azure,](../azure-resource-manager/templates/deploy-portal.md) [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)e [Rest API](../azure-resource-manager/templates/deploy-rest.md) também podem ser usados para implementar modelos.

## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

O modelo cria um VM e permite voltar ao VM. Depois de implementar o modelo, precisa de iniciar um trabalho de reserva. Para mais informações, consulte Iniciar um trabalho de [reserva.](./quick-backup-vm-powershell.md#start-a-backup-job)

### <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

Para monitorizar o trabalho de reserva, consulte [monitorize o trabalho de reserva.](./quick-backup-vm-powershell.md#monitor-the-backup-job)

## <a name="clean-up-the-deployment"></a>Limpar a implantação

Se já não precisar de apoiar o VM, pode limpá-lo.

- Se quiser tentar restaurar o VM, ignore a limpeza.
- Se usou um VM existente, pode saltar o cmdlet final [remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para deixar o grupo de recursos e VM no lugar.

Desativar a proteção, remover os pontos de restauro e o cofre. Em seguida, elimine o grupo de recursos e os recursos VM associados, da seguinte forma:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial.

- [Aprenda](tutorial-backup-vm-at-scale.md) a apoiar os VMs no portal Azure.
- [Aprenda](tutorial-restore-disk.md) a restaurar rapidamente um VM
- [Aprenda](../azure-resource-manager/templates/template-tutorial-create-first-template.md) a criar modelos de Gestor de Recursos.
