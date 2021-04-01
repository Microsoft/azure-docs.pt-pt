---
title: 'Tutorial: Backup VM Murúltiplo Azure com PowerShell'
description: Este tutorial detalha o backup de vários VMs Azure para um cofre de Serviços de Recuperação usando a Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980960"
---
# <a name="back-up-azure-vms-with-powershell"></a>Fazer cópias de segurança de VMs do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial descreve como implantar um cofre [Azure Backup](backup-overview.md) Recovery Services para apoiar vários VMs Azure usando PowerShell.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie um cofre dos Serviços de Recuperação e desemote o contexto do cofre.
> * Definir uma política de cópias de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Desempate um trabalho de backup a pedido para as máquinas virtuais protegidas Antes de poder fazer backup (ou proteger) uma máquina virtual, tem de completar os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o seu ambiente para proteger os seus VMs.

> [!IMPORTANT]
> Este tutorial pressupõe que já criou um grupo de recursos e uma máquina virtual Azure.

## <a name="sign-in-and-register"></a>Inscreva-se e registe-se

1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```

2. A primeira vez que utilizar o Azure Backup, tem de registar o prestador do Serviço de Recuperação Azure na sua assinatura com [o Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se já se registou, ignore este passo.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um [cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) é um recipiente lógico que armazena dados de backup para recursos protegidos, como os VMs Azure. Quando um trabalho de reserva funciona, cria um ponto de recuperação dentro do cofre dos Serviços de Recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.

* Neste tutorial, cria-se o cofre no mesmo grupo de recursos e local que o VM que pretende fazer.
* O Azure Backup lida automaticamente com o armazenamento para dados com cópia de segurança. Por predefinição, o cofre utiliza [o Armazenamento Geo-Redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). A geo-redundância garante que os dados com cópias de segurança são replicados numa região secundária de Azure, a centenas de quilómetros da região primária.

Crie o cofre da seguinte forma:

1. Utilize o  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o cofre. Especifique o nome do grupo de recursos e a localização do VM que pretende fazer o back up.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por esta razão, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação de Backup numa variável.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Desaperte o contexto do cofre com [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * O contexto do cofre é o tipo de dados protegidos no cofre.
   * Uma vez definido o contexto, aplica-se a todos os cmdlets subsequentes

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

As cópias de segurança são executadas de acordo com o horário especificado na política de backup. Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas.

* A política de proteção predefinida desencadeia um trabalho de backup uma vez por dia, numa hora especificada.
* A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias.

Para ativar e fazer o backup do Azure VM neste tutorial, fazemos o seguinte:

1. Especifique um recipiente no cofre que detenha os seus dados de backup com [o Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Cada VM para cópia de segurança é um item. Para iniciar um trabalho de backup, obtém informações sobre o VM com [a Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Faça uma cópia de segurança a pedido com[o Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * O primeiro trabalho inicial de backup cria um ponto de recuperação completo.
    * Após o backup inicial, cada trabalho de backup cria pontos de recuperação incrementais.
    * Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Ativar e executar a cópia de segurança da seguinte forma:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Resolução de problemas

Se tiver problemas enquanto faz backup da sua máquina virtual, reveja este [artigo de resolução de problemas](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Se precisar de apagar um cofre, primeiro apague os pontos de recuperação no cofre e, em seguida, desagrega o cofre, da seguinte forma:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Passos seguintes

* [Reveja](backup-azure-vms-automation.md) uma passagem mais detalhada de backup e restauro de VMs Azure com PowerShell.
* [Gerir e monitorizar os VMs do Azure](backup-azure-manage-vms.md)
* [Restaurar as VMs do Azure](backup-azure-arm-restore-vms.md)
