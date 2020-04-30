---
title: Quickstart - Back up a VM with PowerShell
description: Neste Quickstart, aprenda a fazer o back up das suas máquinas virtuais Azure com o módulo Azure PowerShell.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74171950"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Fazer uma cópia de segurança de uma máquina virtual no Azure com o PowerShell

O módulo [Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) é utilizado para criar e gerir recursos Azure a partir da linha de comando ou em scripts.

[O Azure Backup](backup-overview.md) apoia as máquinas e aplicações no local e os VMs Azure. Este artigo mostra-lhe como fazer o back up de um VM Azure com o módulo AZ. Em alternativa, pode fazer o backup a um VM utilizando o [Azure CLI,](quick-backup-vm-cli.md)ou no [portal Azure](quick-backup-vm-portal.md).

Este início rápido ativa a cópia de segurança numa VM do Azure existente. Se tiver de criar uma VM, pode [criá-la com o Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Este quickstart requer a versão 1.0.0 ou posterior do módulo Azure PowerShell AZ. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Inscreva-se e registe-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. A primeira vez que utilizar o Azure Backup, deve registar o prestador do Serviço de Recuperação Azure na sua subscrição com o [Register-AzResourceProvider,](/powershell/module/az.Resources/Register-azResourceProvider)da seguinte forma:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um [cofre dos Serviços](backup-azure-recovery-services-vault-overview.md) de Recuperação é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

Quando crias o cofre:

- Para o grupo de recursos e localização, especifique o grupo de recursos e a localização do VM que pretende fazer o apoio.
- Se usou este [script de amostra](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) para criar o VM, o grupo de recursos é o **myResourceGroup**, o VM é ***myVM**, e os recursos estão na região da **Europa Ocidental.**
- A Azure Backup trata automaticamente o armazenamento de dados com cópia supérum. Por defeito, o cofre utiliza [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy-grs.md). A georedundgarante que os dados apoiados sejam replicados para uma região secundária de Azure, a centenas de quilómetros da região primária.

Agora crie um cofre:

1. Utilize o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) para criar o cofre:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Desloque o contexto do cofre com [set-AzRecoveryServicesVaultContext,](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)da seguinte forma:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Altere a configuração de redundância de armazenamento (LRS/GRS) do cofre com [Set-AzRecoveryServicesBackupProperty,](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)da seguinte forma:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Armazenamento A redundância só pode ser modificada se não houver itens de reserva protegidos para o cofre.

## <a name="enable-backup-for-an-azure-vm"></a>Ativar a cópia de segurança em VMs do Azure

Habilita-se a fazer backup para um VM Azure e especifica uma política de backup.

- A política define quando os backups funcionam e quanto tempo os pontos de recuperação criados pelos backups devem ser mantidos.
- A política de proteção por defeito executa um backup uma vez por dia para o VM, e mantém os pontos de recuperação criados por 30 dias. Pode utilizar esta política de incumprimento para proteger rapidamente o seu VM.

Ativar o backup da seguinte forma:

1. Em primeiro lugar, detete a política de incumprimento com [a Get-AzRecoveryServicesBackupProtectionPolicy:](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Ativar a cópia de segurança VM com a Proteção de [BackupBackup Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Especifique a política, o grupo de recursos e o nome VM.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Iniciar uma tarefa de cópia de segurança

Os backups funcionam de acordo com o horário especificado na política de backup. Também pode executar um backup a pedido:

- O primeiro trabalho inicial de backup cria um ponto de recuperação total.
- Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
- Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Para executar uma cópia de segurança a pedido, utilize o [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Especifica um recipiente no cofre que detém os seus dados de backup com [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Cada VM da qual vai fazer uma cópia de segurança é tratada como um item. Para iniciar um trabalho de backup, obtém informações sobre o VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Faça um trabalho de backup a pedido da seguinte forma:

1. Especifique o recipiente, obtenha informações vm e faça a cópia de segurança.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Talvez precise supor até 20 minutos, já que o primeiro trabalho de reserva cria um ponto de recuperação total. Monitorize o trabalho conforme descrito no próximo procedimento.

## <a name="monitor-the-backup-job"></a>Monitorizar a tarefa de cópia de segurança

1. Executar [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) para monitorizar o estado de trabalho.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    A saída é semelhante ao seguinte exemplo, que mostra o trabalho como **InProgress:**

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. Quando o estado de trabalho estiver **concluído,** o VM está protegido e tem um ponto de recuperação completo armazenado.

## <a name="clean-up-the-deployment"></a>Limpar a implantação

Se já não precisar de apoiar o VM, pode limpá-lo.

- Se quiser tentar restaurar o VM, salte a limpeza.
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
