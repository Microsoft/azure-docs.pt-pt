---
title: Eliminação suave para servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM
description: Saiba como a eliminação suave para o servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM torna as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: f1e3ecae5d643b8e32f8f4f07808d56cdc421163
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791379"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Eliminação suave para servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM

O Azure Backup fornece agora uma eliminação suave para o servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM. Isto para além do cenário de [eliminação suave](soft-delete-virtual-machines.md)da máquina azul já suportada.

[Soft delete](backup-azure-security-feature-cloud.md) é uma funcionalidade de segurança para ajudar a proteger os dados de backup mesmo após a eliminação. Com a eliminação suave, mesmo que um ator malicioso apague a cópia de segurança de uma base de dados (ou os dados de backup sejam acidentalmente eliminados), os dados de backup são conservados por 14 dias adicionais. Isto permite a recuperação desse item de backup sem perda de dados. Esta retenção adicional de 14 dias dos dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente.

>[!NOTE]
>Uma vez ativada a pré-visualização para uma subscrição, não é possível desativar a eliminação suave apenas para o servidor SQL ou DBs SAP HANA, mantendo-o ativado para máquinas virtuais no mesmo cofre. Pode criar cofres separados para controlo granular.

## <a name="steps-to-enroll-in-preview"></a>Passos para se inscrever na pré-visualização

1. Inscreva-se na sua Conta Azure.

   ```powershell
   Login-AzureRmAccount
   ```

2. Selecione a subscrição que pretende inscrever na pré-visualização:

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. Registe esta subscrição no programa de pré-visualização:

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. Aguarde 30 minutos para que a subscrição seja inscrita na pré-visualização.

5. Para verificar o estado, faça os seguintes cmdlets:

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. Uma vez que a subscrição mostre como registada, execute o seguinte comando:

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>Sempre que um novo cofre/cofre sou criado sob a subscrição ativada por eliminação suave, o seguinte comando precisa de ser reexecutado para permitir a funcionalidade para os cofres recém-criados.<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Eliminação suave para servidor SQL em Azure VM utilizando portal Azure

>[!NOTE]
>Estas instruções aplicam-se também ao SAP HANA em Azure VM.

1. Para eliminar os dados de cópia de segurança de uma base de dados num servidor SQL, a cópia de segurança deve ser interrompida. No portal Azure, vá ao cofre dos serviços de recuperação, vá ao item de reserva e escolha **stop backup**.

   ![Parar o backup](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. Na janela seguinte, será-lhe dada a opção de eliminar ou reter os dados de cópia de segurança. Se escolher Eliminar os dados de **backup,** a cópia de segurança da base de dados não será eliminada permanentemente. Em vez disso, os dados de backup serão conservados durante 14 dias no estado suave apagado. A eliminação é adiada até ao dia 15 com e-mails de alerta regulares no primeiro, 12º e 15º dia informando sobre o estado de backup da base de dados ao utilizador.

   ![Eliminar dados de cópia de segurança](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Durante esses 14 dias, no Cofre de Serviços de Recuperação, o item apagado suave aparecerá com um ícone vermelho de "soft-delete" ao lado.

   ![Itens apagados suaves](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Para restaurar o DB eliminado suavemente, deve primeiro não ser eliminado. Para não apagar, escolha o DB eliminado suavemente e, em seguida, selecione a opção **Não delete**.

   ![Base de dados de exclusão](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Aparecerá uma janela avisando que, se não for escolhida a eliminação, todos os pontos de restauro da base de dados serão não eliminados e disponíveis para a realização de uma operação de restauro. O item de backup será retido num estado de "stop protection with reretain data" com cópias de segurança interrompidas e dados de backup retidos para sempre sem uma política de backup eficaz.

   ![Aviso de desapago](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Neste ponto, também pode restaurar os dados selecionando **restaurar** para o item de backup suave eliminado escolhido.

   ![Restaurar VMs](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Após o processo de não exclusão, o estado voltará a "Parar a cópia de segurança com os dados de retenção" e, em seguida, poderá escolher a **cópia de segurança do Currículo**. A operação de **backup resume** o item de backup no estado ativo, associado a uma política de backup selecionada pelo utilizador que define os horários de backup e retenção.

   ![Retomar cópia de segurança](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Eliminação suave para servidor SQL em VM utilizando O PowerShell Azure

>[!NOTE]
>A versão Az.RecoveryServices necessária para utilizar soft-delete utilizando o Azure PowerShell é mínima de 2.2.0. Use `Install-Module -Name Az.RecoveryServices -Force` para obter a versão mais recente.

A sequência de passos para a utilização do Azure PowerShell é a mesma que no portal Azure, acima descrita.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Elimine o item de cópia de segurança utilizando o Azure PowerShell

Elimine o item de backup utilizando o cmdlet de proteção ps de [deficientes-AzRecoveryServicesBackup.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

O **DeleteState** do item de cópia de segurança passará de **NotDeleted** para **ToBeDeleted**. Os dados de cópia de segurança serão conservados durante 14 dias. Se pretender reverter a operação de eliminação, deve efetuar a eliminação do desmino.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de eliminação utilizando o Azure PowerShell

Em primeiro lugar, pegue o item de reserva relevante que está em estado de eliminação suave (isto é, prestes a ser eliminado).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de deseliminação utilizando o [undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.8.0) PS cmdlet.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

O **DeleteState** do item de cópia de segurança reverterá para **NotDeleted**. Mas a proteção ainda está parada. Retome a cópia de segurança para reativar a proteção.

## <a name="how-to-disable-soft-delete"></a>Como desativar o soft delete

Não é recomendável desativar esta funcionalidade. A única circunstância em que deve considerar desativar o soft delete é se planeia mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reproteger (como num ambiente de teste).) Para obter instruções sobre como desativar a eliminação suave, consulte [ativar e desativar](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)a eliminação suave .

## <a name="next-steps"></a>Passos seguintes

- Leia as [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) sobre apagar suavemente
- Leia sobre todas as [funcionalidades de segurança no Azure Backup](security-overview.md)
