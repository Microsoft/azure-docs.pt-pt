---
title: 'Tutorial: vários backups de VM do Azure com o PowerShell'
description: Este tutorial detalha o backup de várias VMs do Azure em um cofre de serviços de recuperação usando Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171788"
---
# <a name="back-up-azure-vms-with-powershell"></a>Fazer cópias de segurança de VMs do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial descreve como implantar um cofre dos serviços de recuperação de [backup do Azure](backup-overview.md) para fazer backup de várias VMs do Azure usando o PowerShell.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um cofre dos serviços de recuperação e defina o contexto do cofre.
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Disparar um trabalho de backup sob demanda para as máquinas virtuais protegidas antes de poder fazer backup (ou proteger) uma máquina virtual, você deve concluir os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar seu ambiente para proteger suas VMs.

> [!IMPORTANT]
> Este tutorial parte do princípio de que já criou um grupo de recursos e uma máquina virtual do Azure.

## <a name="sign-in-and-register"></a>Entrar e registrar-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. Na primeira vez que você usar o backup do Azure, deverá registrar o provedor de serviços de recuperação do Azure em sua assinatura com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se você já tiver se registrado, ignore esta etapa.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contêiner lógico que armazena dados de backup para recursos protegidos, como VMs do Azure. Quando um trabalho de backup é executado, ele cria um ponto de recuperação dentro do cofre dos serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

* Neste tutorial, você cria o cofre no mesmo grupo de recursos e no mesmo local que a VM da qual você deseja fazer backup.
* O backup do Azure manipula automaticamente o armazenamento para os dados de backup. Por padrão, o cofre usa o [armazenamento com redundância geográfica (GRS)](../storage/common/storage-redundancy-grs.md). A redundância geográfica garante que os dados de backup sejam replicados para uma região secundária do Azure, a centenas de quilômetros de distância da região primária.

Crie o cofre da seguinte maneira:

1. Use o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o cofre. Especifique o nome do grupo de recursos e o local da VM que você deseja fazer backup.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Defina o contexto do cofre com [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * O contexto do cofre é o tipo de dados protegidos no cofre.
   * Depois que o contexto é definido, ele se aplica a todos os cmdlets subsequentes

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

Os backups são executados de acordo com o agendamento especificado na política de backup. Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas.

* A política de proteção padrão dispara um trabalho de backup uma vez por dia em um horário especificado.
* A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias.

Para habilitar e fazer backup da VM do Azure neste tutorial, fazemos o seguinte:

1. Especifique um contêiner no cofre que contém os dados de backup com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Cada VM para backup é um item. Para iniciar um trabalho de backup, você obtém informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Execute um backup sob demanda com[backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * O primeiro trabalho de backup inicial cria um ponto de recuperação completo.
    * Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
    * Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Habilite e execute o backup da seguinte maneira:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Resolução de problemas

Se você encontrar problemas ao fazer backup de sua máquina virtual, examine este [artigo de solução de problemas](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Se você precisar excluir um cofre, primeiro exclua os pontos de recuperação no cofre e, em seguida, cancele o registro do cofre, da seguinte maneira:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Passos seguintes

* [Examine](backup-azure-vms-automation.md) uma explicação mais detalhada do backup e da restauração de VMs do Azure com o PowerShell.
* [Gerenciar e monitorar VMs do Azure](backup-azure-manage-vms.md)
* [Restaurar VMs do Azure](backup-azure-arm-restore-vms.md)
