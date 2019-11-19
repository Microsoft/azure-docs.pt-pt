---
title: Início rápido – backup da VM do modelo do Resource Manager
description: Saiba como fazer backup de suas máquinas virtuais com Azure Resource Manager modelo
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc
ms.openlocfilehash: 7218b496a3cd94362d27b1883fa5055b819768c3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171943"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Fazer backup de uma máquina virtual no Azure com o modelo do Resource Manager

O [backup do Azure](backup-overview.md) faz backup de computadores e aplicativos locais e VMs do Azure. Este artigo mostra como fazer backup de uma VM do Azure com o modelo do Resource Manager e Azure PowerShell. Este guia de início rápido concentra-se no processo de implantação de um modelo do Resource Manager para criar um cofre de serviços de recuperação. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte a [documentação do Resource Manager](/azure/azure-resource-manager/) e a [referência de modelo](/azure/templates/microsoft.recoveryservices/allversions).

Como alternativa, você pode fazer backup de uma VM usando [Azure PowerShell](./quick-backup-vm-powershell.md), a [CLI do Azure](quick-backup-vm-cli.md)ou na [portal do Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Criar uma VM e um cofre dos serviços de recuperação

Um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup é executado, ele cria um ponto de recuperação dentro do cofre dos serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

O modelo usado neste guia de início rápido é de [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Este modelo permite que você implante um cofre de serviços de recuperação e VM do Windows simples configurados com o DefaultPolicy para proteção.

Para implantar o modelo, selecione **Experimente-** o para abrir o Azure cloud Shell e cole o seguinte script do PowerShell na janela do Shell. Para colar o código, clique com o botão direito do mouse na janela do Shell e selecione **colar**.

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

Azure PowerShell é usado para implantar o modelo do Resource Manager neste guia de início rápido. O [portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md), [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)e a [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md) também podem ser usados para implantar modelos.

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

O modelo cria uma VM e permite de volta na VM. Depois de implantar o modelo, você precisará iniciar um trabalho de backup. Para obter mais informações, consulte [Iniciar um trabalho de backup](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

Para monitorar o trabalho de backup, consulte [monitorar o trabalho de backup](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Limpar a implantação

Se você não precisar mais fazer backup da VM, poderá limpá-la.

- Se você quiser tentar restaurar a VM, ignore a limpeza.
- Se você usou uma VM existente, poderá ignorar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) final para deixar o grupo de recursos e a VM em vigor.

Desabilite a proteção, remova os pontos de restauração e o cofre. Em seguida, exclua o grupo de recursos e os recursos de VM associados, da seguinte maneira:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial.

- [Saiba como](tutorial-backup-vm-at-scale.md) fazer backup de VMs no portal do Azure.
- [Saiba como](tutorial-restore-disk.md) restaurar rapidamente uma VM
