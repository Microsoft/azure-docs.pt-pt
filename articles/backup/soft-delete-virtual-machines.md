---
title: Eliminação suave para máquinas virtuais
description: Saiba como a eliminação suave para máquinas virtuais torna as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: ba00b235ea70bcc2dabbd5a91a3f7003f9bbed49
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82765776"
---
# <a name="soft-delete-for-virtual-machines"></a>Eliminação suave para máquinas virtuais

A eliminação suave para VMs protege as cópias de segurança dos seus VMs de eliminação não intencional. Mesmo depois de as cópias de segurança serem eliminadas, são preservadas em estado de eliminação suave por 14 dias adicionais.

> [!NOTE]
> A eliminação suave apenas protege os dados de backup eliminados. Se um VM for eliminado sem uma cópia de segurança, a função soft-delete não preservará os dados. Todos os recursos devem ser protegidos com o Azure Backup para garantir a plena resiliência.
>

## <a name="supported-regions"></a>Regiões suportadas

A soft delete é atualmente apoiada no Centro-Oeste dos EUA, Ásia Oriental, Canadá Central, Canadá Leste, França Central, França Sul, Coreia Central, Coreia do Sul, Reino Unido Sul, Reino Unido Oeste, Austrália Leste, Austrália Sudeste, Norte da Europa, Oeste dos EUA, Oeste DOS EUA, Sudeste Asiático, Norte-Central DOS EUA, Centro-Sul dos EUA, Japão Leste, Japão Oeste, Índia Sul, Índia Central, Índia Ocidental, Oeste, LESTE DOS EUA 2 - Suíça Norte, Suíça Oeste, Noruega Oeste, Noruega Leste, e todas as regiões nacionais.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Eliminação suave para VMs utilizando portal Azure

1. Para eliminar os dados de cópia de segurança de um VM, a cópia de segurança deve ser interrompida. No portal Azure, vá ao cofre dos serviços de recuperação, clique à direita no item de reserva e escolha parar de **reserva**.

   ![Screenshot dos itens de backup do portal Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela seguinte, será-lhe dada a opção de eliminar ou reter os dados de cópia de segurança. Se escolher **eliminar os dados** de cópia de segurança e, em seguida, parar a cópia de **segurança,** a cópia de segurança VM não será eliminada permanentemente. Em vez disso, os dados de backup serão conservados durante 14 dias no estado suave apagado. Se eliminar os dados de **backup,** um alerta de e-mail de exclusão é enviado para o ID de e-mail configurado informando o utilizador de que restam 14 dias de retenção prolongada para dados de backup. Além disso, um alerta de e-mail é enviado no dia 12 informando que faltam mais dois dias para ressuscitar os dados apagados. A eliminação é adiada até ao 15º dia, quando ocorrer a eliminação permanente e é enviado um alerta final de e-mail informando sobre a eliminação permanente dos dados.

   ![Screenshot do portal Azure, ecrã stop backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no Cofre de Serviços de Recuperação, o VM suave apagado aparecerá com um ícone vermelho de "soft-delete" ao lado.

   ![Screenshot do portal Azure, VM em estado de eliminação suave](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se houver itens de reserva apagados no cofre, o cofre não pode ser apagado nessa altura. Por favor, tente a eliminação do cofre depois que os itens de reserva sejam permanentemente apagados, e não há nenhum item em estado suave apagado no cofre.

4. Para restaurar o VM eliminado suavemente, deve primeiro não ser eliminado. Para não apagar, escolha o VM eliminado suavemente e, em seguida, selecione a opção **Não apagar**.

   ![Screenshot do portal Azure, Undelete VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Aparecerá uma janela avisando que, se não for escolhida a eliminação, todos os pontos de restauro do VM serão não eliminados e disponíveis para a realização de uma operação de restauro. O VM será retido num estado de "stop protection with reretain data" com cópias de segurança interrompidas e dados de backup retidos para sempre sem uma política de backup eficaz.

   ![Screenshot do portal Azure, Confirme não excluir VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, também pode restaurar o VM selecionando Restore **VM** a partir do ponto de restauro escolhido.  

   ![Screenshot do portal Azure, restaurar a opção VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo só funcionará e limpará os pontos de recuperação expirados após o utilizador executar a operação de **backup do Currículo.**

5. Após o processo de não exclusão, o estado voltará a "Parar a cópia de segurança com os dados de retenção" e, em seguida, poderá escolher a **cópia de segurança do Currículo**. A operação de **backup resume** o item de backup no estado ativo, associado a uma política de backup selecionada pelo utilizador que define os horários de backup e retenção.

   ![Screenshot do portal Azure, Opção de cópia de segurança do Currículo](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Eliminação suave para VMs usando O PowerShell Azure

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para utilizar soft-delete utilizando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Tal como descrito acima para o portal Azure, a sequência de passos é a mesma ao utilizar também o Azure PowerShell.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Elimine o item de cópia de segurança utilizando o Azure PowerShell

Elimine o item de backup utilizando o cmdlet de proteção ps de [deficientes-AzRecoveryServicesBackup.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O 'DeleteState' do item de cópia de segurança passará de 'NotDeleted' para 'ToBeDeleted'. Os dados de cópia de segurança serão conservados durante 14 dias. Se pretender reverter a operação de eliminação, deve efetuar a eliminação do desmino.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de eliminação utilizando o Azure PowerShell

Em primeiro lugar, pegue o item de reserva relevante que está em estado de eliminação suave (isto é, prestes a ser eliminado).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de deseliminação utilizando o [undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS cmdlet.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

O 'DeleteState' do item de cópia de segurança reverterá para 'NotDeleted'. Mas a proteção ainda está parada. [Retome a cópia de segurança](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) para reativar a proteção.

## <a name="soft-delete-for-vms-using-rest-api"></a>Eliminação suave para VMs utilizando API REST

- Elimine as cópias de segurança utilizando a API REST como mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se o utilizador desejar desfazer estas operações de eliminação, consulte as etapas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)mencionadas .

## <a name="how-to-disable-soft-delete"></a>Como desativar o soft delete

Não é recomendável desativar esta funcionalidade. A única circunstância em que deve considerar desativar o soft delete é se planeia mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reproteger (como num ambiente de teste).) Para obter instruções sobre como desativar a eliminação suave, consulte [ativar e desativar](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)a eliminação suave .

## <a name="next-steps"></a>Passos seguintes

- Leia as [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) sobre apagar suavemente
- Leia sobre todas as [funcionalidades de segurança no Azure Backup](security-overview.md)
