---
title: Quickstart - Backup vM do modelo de gestor de recursos
description: Saiba como fazer o back up das suas máquinas virtuais com o modelo do Gestor de Recursos Azure
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826536"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Quickstart: Apoie uma máquina virtual em Azure com um modelo ARM

[O Azure Backup](backup-overview.md) apoia máquinas e aplicações no local e VMs Azure. Este artigo mostra-lhe como fazer o back up de um Azure VM com um modelo Azure Resource Manager (modelo ARM) e Azure PowerShell. Este quickstart foca-se no processo de implantação de um modelo ARM para criar um cofre dos Serviços de Recuperação. Para obter mais informações sobre o desenvolvimento de modelos ARM, consulte a documentação do [Gestor de Recursos Azure](../azure-resource-manager/index.yml) e a referência do [modelo](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo. Em alternativa, pode apoiar um VM utilizando [a Azure PowerShell,](./quick-backup-vm-powershell.md)o [Azure CLI,](quick-backup-vm-cli.md)ou no [portal Azure](quick-backup-vm-portal.md).

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure quickstart Templates](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Este modelo permite-lhe implementar um cofre simples do Windows VM e dos Serviços de Recuperação configurado com a DefaultPolicy for Protection.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

Os recursos definidos no modelo são:

- [**Microsoft.Storage/storageAcontas**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/cofres**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>Implementar o modelo

Para implementar o modelo, selecione **Experimente-o** para abrir a Camada de Nuvem Azure e, em seguida, cole o seguinte script PowerShell na janela do reservatório. Para colar o código, clique com o botão direito na janela da concha e, em seguida, **selecione Pasta**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

A Azure PowerShell é utilizado para implantar o modelo ARM neste arranque rápido. O [portal Azure](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)e [Rest API](../azure-resource-manager/templates/deploy-rest.md) também podem ser usados para implantar modelos.

## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

O modelo cria um VM e permite voltar a ser o VM. Depois de implementar o modelo, tem de iniciar um trabalho de reserva. Para obter mais informações, consulte [Iniciar um trabalho de backup](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

Para monitorizar o trabalho de backup, consulte [monitorar o trabalho de reserva](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-resources"></a>Limpar os recursos

Se já não precisar de fazer o reforço do VM, pode limpá-lo.

- Se quiser experimentar restaurar o VM, ignore a limpeza.
- Se utilizar um VM existente, pode saltar o cmdlet final [do Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para deixar o grupo de recursos e o VM no lugar.

Desative a proteção, remova os pontos de restauro e o cofre. Em seguida, elimine o grupo de recursos e os recursos VM associados, da seguinte forma:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial.

- [Saiba como](tutorial-backup-vm-at-scale.md) apoiar os VMs no portal Azure.
- [Saiba como](tutorial-restore-disk.md) restaurar rapidamente um VM
- [Saiba como](../azure-resource-manager/templates/template-tutorial-create-first-template.md) criar modelos ARM.
