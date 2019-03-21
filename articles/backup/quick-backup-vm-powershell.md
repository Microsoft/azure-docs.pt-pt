---
title: Início Rápido do Azure – Fazer cópia de segurança de uma VM com o PowerShell
description: Saiba como fazer cópias de segurança das suas máquinas virtuais com o Azure PowerShell
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: aa637571ca11ea294b1f95df49855d7ee81b3001
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258875"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O [do Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) módulo é utilizado para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. 

[O Azure Backup](backup-overview.md) efetua uma cópia de segurança de máquinas no local e aplicações e as VMs do Azure. Este artigo mostra-lhe como fazer cópias de segurança de uma VM do Azure com o módulo de AZ. Em alternativa, pode fazer backup de uma VM com o [CLI do Azure](quick-backup-vm-cli.md), ou no [portal do Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma VM, pode [criá-la com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este início rápido requer o Azure PowerShell AZ versão 1.0.0 do módulo ou posterior. Executar ` Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).


## <a name="log-in-and-register"></a>Iniciar sessão e registar

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```
2. Na primeira vez que utilizar o Azure Backup, tem de registar o fornecedor de serviços de recuperação do Azure na sua subscrição com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), da seguinte forma:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

R [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor lógico que armazena dados de cópia de segurança de recursos protegidos, como as VMs do Azure. Quando é executada uma tarefa de cópia de segurança, ele cria um ponto de recuperação no interior do Cofre de serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Quando cria o Cofre:

- Para o grupo de recursos e a localização, especifique o grupo de recursos e a localização da VM que pretende criar cópias de segurança.
- Se utilizou isso [script de exemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar a VM, o grupo de recursos é **myResourceGroup**, a VM está ***myVM**, e os recursos estão no **WestEurope**  região.
- Cópia de segurança do Azure processa automaticamente o armazenamento de cópias de segurança. Por predefinição utiliza o Cofre [armazenamento Georredundante (GRS)](../storage/common/storage-redundancy-grs.md). Redundância geográfica garante que uma cópia de segurança dados são replicados para uma região secundária do Azure, centenas de quilómetros de distância da região primária.

Agora, crie um cofre.


1. Utilize o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o Cofre:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Definir o contexto do cofre com [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), da seguinte forma:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```


## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure

Ativar cópia de segurança para uma VM do Azure e especifique uma política de cópia de segurança.

- A política define quando executar cópias de segurança e o tempo que os pontos de recuperação criados pelas cópias de segurança devem ser mantidos.
- A política de proteção predefinida executa uma cópia de segurança uma vez por dia para a VM e mantém os pontos de recuperação criados durante 30 dias. Pode utilizar esta política predefinida para proteger rapidamente a sua VM. 

Ative cópia de segurança da seguinte forma:

1. Em primeiro lugar, defina a política predefinida com [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Ativar cópia de segurança VM com [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique a política, o grupo de recursos e o nome da VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

Executam cópias de segurança em conformidade com a agenda especificada na política de cópia de segurança. Também pode executar um backup ad hoc:

- A primeira tarefa de cópia de segurança inicial cria um ponto de recuperação completo.
- Depois da cópia de segurança inicial, cada tarefa de cópia de segurança cria pontos de recuperação incrementais.
- Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Para executar um backup ad hoc, utilize o[AzRecoveryServicesBackupItem de cópia de segurança](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem). 
- Especificar um contentor no cofre que contém os seus dados de cópia de segurança com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Cada VM da qual vai fazer uma cópia de segurança é tratada como um item. Para iniciar uma tarefa de cópia de segurança, é obter informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Execute uma tarefa de cópia de segurança ad hoc da seguinte forma:

1. Especifique o contentor e obter informações da VM para executar a cópia de segurança.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Poderá ter de aguardar até 20 minutos, uma vez que a primeira tarefa de cópia de segurança cria um ponto de recuperação completo. Monitorize a tarefa, conforme descrito no procedimento seguinte.


## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

1. Execute [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para monitorizar o estado da tarefa.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    O resultado é semelhante ao exemplo seguinte, que mostra a tarefa como **InProgress**:

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando o estado da tarefa é **concluído**, a VM está protegida e armazenou um ponto de recuperação completo.


## <a name="clean-up-the-deployment"></a>Limpar a implementação

Se já não precisar de fazer uma cópia de segurança da VM, pode limpá-los.
- Se quiser experimentar o restauro de VM, ignore o limpa cópia de segurança.
- Se tiver utilizado uma VM existente, pode ignorar o último [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet para manter o grupo de recursos e a VM no local.

Desative a proteção, remova os pontos de restauro e o cofre. Em seguida, eliminar o grupo de recursos e os recursos VM associados, da seguinte forma:

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
