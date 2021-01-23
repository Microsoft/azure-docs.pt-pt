---
title: Quickstart - Apoie um VM com PowerShell
description: Neste Quickstart, aprenda a apoiar as suas máquinas virtuais Azure com o módulo Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 81005a3032355f566aef2a6794cf2ec5038b0dd8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737121"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o PowerShell

O módulo [Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) é utilizado para criar e gerir recursos Azure a partir da linha de comando ou em scripts.

[O Azure Backup](backup-overview.md) apoia máquinas e aplicações no local e VMs Azure. Este artigo mostra-lhe como apoiar um Azure VM com o módulo AZ. Em alternativa, pode fazer uma utilização de um VM utilizando o [Azure CLI,](quick-backup-vm-cli.md)ou no [portal Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma VM, pode [criá-la com o Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json).

Este arranque rápido requer a versão 1.0 ou mais tarde do módulo Azure PowerShell AZ. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Inscreva-se e registe-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. A primeira vez que utilizar o Azure Backup, deve registar o prestador do Serviço de Recuperação Azure na sua assinatura com [o Register-AzResourceProvider,](/powershell/module/az.Resources/Register-azResourceProvider)da seguinte forma:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Quando se cria o cofre:

- Para o grupo de recursos e localização, especifique o grupo de recursos e a localização do VM que pretende fazer o back up.
- Se usou este [script de amostra](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json) para criar o VM, o grupo de recursos é o **myResourceGroup,** o VM é **_myVM_*, e os recursos estão na região **de WestEurope.**
- O Azure Backup lida automaticamente com o armazenamento para dados com cópia de segurança. Por predefinição, o cofre utiliza [o Armazenamento Geo-Redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). A geo-redundância garante que os dados com cópias de segurança são replicados numa região secundária de Azure, a centenas de quilómetros da região primária.

Agora crie um cofre:

1. Utilize o  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Desaperte o contexto do cofre com [Set-AzRecoveryServicesVaultContext,](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)da seguinte forma:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Alterar a configuração de redundância de armazenamento (LRS/GRS) do cofre com [Set-AzRecoveryServicesBackupProperty,](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)da seguinte forma:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Armazenamento A redundância só pode ser modificada se não houver itens de reserva protegidos no cofre.

## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure

Ativa a cópia de segurança para um Azure VM e especifica uma política de backup.

- A política define quando os backups são executados, e quanto tempo os pontos de recuperação criados pelos backups devem ser mantidos.
- A política de proteção predefinitiva executa uma cópia de segurança uma vez por dia para o VM, e mantém os pontos de recuperação criados durante 30 dias. Pode utilizar esta política por defeito para proteger rapidamente o seu VM.

Ativar a cópia de segurança da seguinte forma:

1. Em primeiro lugar, defina a política por defeito com [a Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Ativar a cópia de segurança VM com [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique a política, o grupo de recursos e o nome VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

As cópias de segurança são executadas de acordo com o horário especificado na política de backup. Também pode executar uma cópia de segurança a pedido:

- O primeiro trabalho inicial de backup cria um ponto de recuperação completo.
- Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
- Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Para executar uma cópia de segurança a pedido, utilize o [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Especifica um recipiente no cofre que contém os seus dados de backup com [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Cada VM da qual vai fazer uma cópia de segurança é tratada como um item. Para iniciar um trabalho de backup, obtém informações sobre o VM com [a Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Faça um trabalho de backup a pedido da seguinte forma:

1. Especifique o recipiente, obtenha informações em VM e execute a cópia de segurança.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Pode ter de esperar até 20 minutos, já que o primeiro trabalho de reserva cria um ponto de recuperação completo. Monitorize o trabalho como descrito no procedimento seguinte.

## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

1. Executar [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para monitorizar o estado do trabalho.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    A produção é semelhante ao exemplo a seguir, que mostra o trabalho como **InProgress:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando o estado de trabalho estiver **concluído,** o VM está protegido e tem um ponto de recuperação completo armazenado.

## <a name="clean-up-the-deployment"></a>Limpe a implementação

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

## <a name="next-steps"></a>Próximos passos

Neste início rápido, criou um cofre dos Serviços de Recuperação, ativou a proteção numa VM e criou o ponto de recuperação inicial.

- [Saiba como](tutorial-backup-vm-at-scale.md) apoiar os VMs no portal Azure.
- [Saiba como](tutorial-restore-disk.md) restaurar rapidamente um VM