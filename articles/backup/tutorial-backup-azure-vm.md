---
title: 'Tutorial: Multi-Azure VM backup com PowerShell'
description: Este tutorial detalha o backup de vários VMs Azure para um cofre dos Serviços de Recuperação usando o Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74171788"
---
# <a name="back-up-azure-vms-with-powershell"></a>Fazer cópias de segurança de VMs do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial descreve como implantar um cofre [azure backup](backup-overview.md) serviços para fazer backup vários VMs Azure usando powerShell.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um cofre de Serviços de Recuperação e detetete o contexto do cofre.
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Desencadeie um trabalho de backup a pedido para as máquinas virtuais protegidas Antes de poder fazer backup (ou proteger) uma máquina virtual, deve completar os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o seu ambiente para proteger os seus VMs.

> [!IMPORTANT]
> Este tutorial parte do princípio de que já criou um grupo de recursos e uma máquina virtual do Azure.

## <a name="sign-in-and-register"></a>Inscreva-se e registe-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. A primeira vez que utilizar o Azure Backup, deve registar o prestador do Serviço de Recuperação Azure na sua subscrição com o [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se já se registou, ignore este passo.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um [cofre dos Serviços](backup-azure-recovery-services-vault-overview.md) de Recuperação é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

* Neste tutorial, você cria o cofre no mesmo grupo de recursos e localização que o VM que você quer apoiar.
* A Azure Backup trata automaticamente o armazenamento de dados com cópia supérum. Por defeito, o cofre utiliza [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy-grs.md). A georedundgarante que os dados apoiados sejam replicados para uma região secundária de Azure, a centenas de quilómetros da região primária.

Crie o cofre da seguinte forma:

1. Use o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o cofre. Especifique o nome do grupo de recursos e a localização do VM que pretende fazer o apoio.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Desloque o contexto do cofre com [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * O contexto do cofre é o tipo de dados protegidos no cofre.
   * Uma vez definido o contexto, aplica-se a todos os cmdlets subsequentes

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

Os backups funcionam de acordo com o calendário especificado na política de backup. Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas.

* A política de proteção padrão desencadeia um trabalho de reserva uma vez por dia num momento especificado.
* A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias.

Para ativar e fazer backup o Azure VM neste tutorial, fazemos o seguinte:

1. Especifique um recipiente no cofre que detenha os seus dados de backup com [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Cada VM para cópia de segurança é um item. Para iniciar um trabalho de backup, obtém informações sobre o VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Faça uma cópia de segurança a pedido com[backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * O primeiro trabalho inicial de backup cria um ponto de recuperação total.
    * Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
    * Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Ativar e executar a cópia de segurança da seguinte forma:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas enquanto apoia a sua máquina virtual, reveja este artigo de resolução de [problemas.](backup-azure-vms-troubleshoot.md)

### <a name="deleting-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Se precisar de apagar um cofre, primeiro elimine os pontos de recuperação no cofre e, em seguida, desregilhe o cofre, da seguinte forma:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Passos seguintes

* [Reveja](backup-azure-vms-automation.md) uma passagem mais detalhada de backup e restauro de VMs Azure com PowerShell.
* [Gerir e monitorizar VMs Azure](backup-azure-manage-vms.md)
* [Restaurar as VMs do Azure](backup-azure-arm-restore-vms.md)
