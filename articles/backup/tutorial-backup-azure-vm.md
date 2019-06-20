---
title: Fazer cópias de segurança de várias VMs do Azure com o PowerShell
description: Este tutorial apresenta detalhes sobre cópia de segurança de várias VMs do Azure para um cofre de serviços de recuperação com o Azure PowerShell.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 7cbe2cca37ce237409042e40b4a60311aed2446c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273986"
---
# <a name="back-up-azure-vms-with-powershell"></a>Fazer uma cópia de segurança de VMs do Azure com o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Este tutorial descreve como implementar um [Azure Backup](backup-overview.md) cofre dos serviços de recuperação para efetuar cópias de segurança de várias VMs do Azure com o PowerShell.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um cofre dos serviços de recuperação e defina o contexto do cofre.
> * Definir uma política de cópia de segurança
> * Aplicar a política de cópia de segurança para proteger várias máquinas virtuais
> * Acionar uma tarefa de cópia de segurança a pedido para as máquinas virtuais protegidas antes de pode criar cópias de segurança (ou proteger) uma máquina virtual, tem de concluir o [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger as suas VMs. 

> [!IMPORTANT]
> Este tutorial parte do princípio de que já criou um grupo de recursos e uma máquina virtual do Azure.


## <a name="log-in-and-register"></a>Iniciar sessão e registar


1. Inicie sessão na sua subscrição do Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

    ```powershell
    Connect-AzAccount
    ```
2. Na primeira vez que utilizar o Azure Backup, tem de registar o fornecedor de serviços de recuperação do Azure na sua subscrição com [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Se já tiver registrado, ignore este passo.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

R [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) é um contentor lógico que armazena dados de cópia de segurança de recursos protegidos, como as VMs do Azure. Quando é executada uma tarefa de cópia de segurança, ele cria um ponto de recuperação no interior do Cofre de serviços de recuperação. Em seguida, pode utilizar um destes pontos de recuperação para restaurar dados para um determinado ponto no tempo.


- Neste tutorial, vai criar o Cofre no mesmo grupo de recursos e localização da VM que pretende criar cópias de segurança.
- Cópia de segurança do Azure processa automaticamente o armazenamento de cópias de segurança. Por predefinição utiliza o Cofre [armazenamento Georredundante (GRS)](../storage/common/storage-redundancy-grs.md). Redundância geográfica garante que uma cópia de segurança dados são replicados para uma região secundária do Azure, centenas de quilómetros de distância da região primária.

Crie o Cofre da seguinte forma:

1. Utilize o [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)para criar o cofre. Especifique o nome do grupo de recursos e localização da VM que pretende criar cópias de segurança.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```
2. Muitos cmdlets do Azure Backup requerem o objeto do cofre dos Serviços de Recuperação como entrada. Por este motivo, é conveniente armazenar o objeto do cofre dos Serviços de Recuperação do Backup numa variável.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```
    
3. Definir o contexto do cofre com [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   - O contexto do cofre é o tipo de dados protegidos no cofre.
   - Depois que o contexto é definido, ela se aplica a todos os cmdlets subsequentes

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Fazer uma cópia de segurança de VMs do Azure

Executam cópias de segurança em conformidade com a agenda especificada na política de cópia de segurança. Quando cria um cofre dos Serviços de Recuperação, aquele inclui políticas de proteção e retenção predefinidas.

- A política de proteção predefinida aciona uma tarefa de cópia de segurança, uma vez por dia a um tempo especificado.
- A política de retenção predefinida retém o ponto de recuperação diária durante 30 dias. 

Para ativar e de cópia de segurança a VM do Azure neste tutorial, vamos fazer o seguinte:

1. Especificar um contentor no cofre que contém os seus dados de cópia de segurança com [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Cada VM para cópia de segurança é um item. Para iniciar uma tarefa de cópia de segurança, é obter informações sobre a VM com [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Executar cópias de segurança ad hoc com[AzRecoveryServicesBackupItem de cópia de segurança](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem). 
    - A primeira tarefa de cópia de segurança inicial cria um ponto de recuperação completo.
    - Depois da cópia de segurança inicial, cada tarefa de cópia de segurança cria pontos de recuperação incrementais.
    - Os pontos de recuperação incrementais são eficientes em termos de armazenamento e tempo, uma vez que só transferem as alterações feitas desde a última cópia de segurança.

Ativar e executar a cópia de segurança da seguinte forma:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Resolução de problemas 

Caso se depare com problemas durante o backup a sua máquina virtual, veja esta [artigo de resolução de problemas](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Eliminar um cofre dos Serviços de Recuperação

Se tiver de eliminar um cofre, primeiro de eliminar os pontos de recuperação no cofre e, em seguida, anular o registo do cofre, da seguinte forma:


```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Passos Seguintes

- [Revisão](backup-azure-vms-automation.md) instruções mais detalhadas da cópia de segurança e restaurar VMs do Azure com o PowerShell. 
- [Gerir e monitorizar VMs do Azure](backup-azure-manage-vms.md)
- [Restaurar VMs do Azure](backup-azure-arm-restore-vms.md)
