---
title: Início rápido do Azure - fazer cópia de segurança uma VM com o modelo do Resource Manager
description: Saiba como criar cópias de segurança suas máquinas virtuais com o modelo Azure Resource Manager
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b144d7509562b8ca0bca6299caee4a7ce292f4a6
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481370"
---
# <a name="back-up-a-virtual-machine-in-azure-with-resource-manager-template"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o modelo do Resource Manager

[O Azure Backup](backup-overview.md) efetua uma cópia de segurança de máquinas no local e aplicações e as VMs do Azure. Este artigo mostra-lhe como fazer uma cópia de segurança de uma VM do Azure com o modelo do Resource Manager e o Azure PowerShell. Este início rápido centra-se sobre o processo de implantação de um modelo do Resource Manager para criar um cofre dos serviços de recuperação. Para obter mais informações sobre como desenvolver modelos do Resource Manager, consulte [documentação do Resource Manager](/azure/azure-resource-manager/) e o [referência de modelo](/azure/templates/microsoft.recoveryservices/allversions).

Em alternativa, pode fazer backup de uma VM com [do Azure PowerShell](./quick-backup-vm-powershell.md), o [CLI do Azure](quick-backup-vm-cli.md), ou no [portal do Azure](quick-backup-vm-portal.md).

## <a name="create-a-vm-and-recovery-services-vault"></a>Criar uma VM e o Cofre dos serviços de recuperação

R [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor lógico que armazena dados de cópia de segurança de recursos protegidos, como as VMs do Azure. Quando é executada uma tarefa de cópia de segurança, ele cria um ponto de recuperação no interior do Cofre de serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

O modelo utilizado neste início rápido é partir [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Este modelo permite-lhe implementar simples VM do Windows e o Cofre dos serviços de recuperação configurado com o DefaultPolicy para proteção.

Para implementar o modelo, selecione **experimente** para abrir o Azure Cloud shell e, em seguida, cole o seguinte script do PowerShell para a janela do shell. Colar o código, com o botão direito da janela do shell e, em seguida, selecione **colar**.

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

O Azure PowerShell é utilizado para implementar o modelo do Resource Manager neste guia de introdução. O [portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md), [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md), e [Rest API](../azure-resource-manager/resource-group-template-deploy-rest.md) também pode ser utilizado para implementar modelos.

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

O modelo cria uma VM e permite voltar na VM. Depois de implementar o modelo, terá de iniciar uma tarefa de cópia de segurança. Para obter mais informações, consulte [iniciar uma tarefa de cópia de segurança](./quick-backup-vm-powershell.md#start-a-backup-job).

## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

Para monitorizar a tarefa de cópia de segurança, consulte [monitorizar a tarefa de cópia de segurança](./quick-backup-vm-powershell.md#monitor-the-backup-job).

## <a name="clean-up-the-deployment"></a>Limpar a implementação

Se já não precisar de fazer uma cópia de segurança da VM, pode limpá-los.

- Se quiser experimentar o restauro de VM, ignore o limpa cópia de segurança.
- Se tiver utilizado uma VM existente, pode ignorar o último [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet para manter o grupo de recursos e a VM no local.

Desative a proteção, remova os pontos de restauro e o cofre. Em seguida, elimine o grupo de recursos e os recursos VM associados, da seguinte forma:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial.

- [Saiba como](tutorial-backup-vm-at-scale.md) para efetuar cópias de segurança de VMs no portal do Azure.
- [Saiba como](tutorial-restore-disk.md) restaurar rapidamente uma VM
