---
title: Início rápido-fazer backup de uma VM com o PowerShell
description: Neste guia de início rápido, saiba como fazer backup de suas máquinas virtuais do Azure com o módulo Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171950"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o PowerShell

O módulo [AZ Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) é usado para criar e gerenciar recursos do Azure na linha de comando ou em scripts.

O [backup do Azure](backup-overview.md) faz backup de computadores e aplicativos locais e VMs do Azure. Este artigo mostra como fazer backup de uma VM do Azure com o módulo AZ. Como alternativa, você pode fazer backup de uma VM usando o [CLI do Azure](quick-backup-vm-cli.md)ou na [portal do Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma VM, pode [criá-la com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este início rápido requer o módulo Azure PowerShell AZ versão 1.0.0 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Entrar e registrar-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. Na primeira vez que você usar o backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), da seguinte maneira:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup é executado, ele cria um ponto de recuperação dentro do cofre dos serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Ao criar o cofre:

- Para o grupo de recursos e o local, especifique o grupo de recursos e o local da VM que você deseja fazer backup.
- Se você usou este [script de exemplo](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar a VM, o grupo de recursos é **MyResource**Group, a VM é ***myVM**e os recursos estão na região **WestEurope** .
- O backup do Azure manipula automaticamente o armazenamento para os dados de backup. Por padrão, o cofre usa o [armazenamento com redundância geográfica (GRS)](../storage/common/storage-redundancy-grs.md). A redundância geográfica garante que os dados de backup sejam replicados para uma região secundária do Azure, a centenas de quilômetros de distância da região primária.

Agora, crie um cofre:

1. Use o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Defina o contexto do cofre com [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext), da seguinte maneira:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Altere a configuração de redundância de armazenamento (LRS/GRS) do cofre com [set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty), da seguinte maneira:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > A redundância de armazenamento só poderá ser modificada se não houver itens de backup protegidos ao cofre.

## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure

Você habilita o backup para uma VM do Azure e especifica uma política de backup.

- A política define quando os backups são executados e por quanto tempo os pontos de recuperação criados pelos backups devem ser mantidos.
- A política de proteção padrão executa um backup uma vez por dia para a VM e retém os pontos de recuperação criados por 30 dias. Você pode usar essa política padrão para proteger rapidamente sua VM.

Habilite o backup da seguinte maneira:

1. Primeiro, defina a política padrão com [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Habilite o backup de VM com [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique a política, o grupo de recursos e o nome da VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

Os backups são executados de acordo com o agendamento especificado na política de backup. Você também pode executar um backup sob demanda:

- O primeiro trabalho de backup inicial cria um ponto de recuperação completo.
- Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
- Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Para executar um backup sob demanda, use o [backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Você especifica um contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Cada VM da qual vai fazer uma cópia de segurança é tratada como um item. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Execute um trabalho de backup sob demanda da seguinte maneira:

1. Especifique o contêiner, obtenha as informações da VM e execute o backup.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Talvez seja necessário aguardar até 20 minutos, já que o primeiro trabalho de backup cria um ponto de recuperação completo. Monitore o trabalho conforme descrito no próximo procedimento.

## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

1. Execute [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para monitorar o status do trabalho.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    A saída é semelhante ao exemplo a seguir, que mostra o trabalho como **InProgress**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando o status do trabalho for **concluído**, a VM será protegida e terá um ponto de recuperação completo armazenado.

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
