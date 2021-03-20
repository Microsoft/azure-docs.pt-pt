---
title: Excluir suave para servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM
description: Saiba como a eliminação suave para o servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM torna as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91254300"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Excluir suave para servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM

O Azure Backup agora fornece uma eliminação suave para o servidor SQL em Azure VM e SAP HANA em cargas de trabalho Azure VM. Isto para além do já suportado cenário de eliminação suave da [máquina Azure Virtual.](soft-delete-virtual-machines.md)

[A eliminação suave](backup-azure-security-feature-cloud.md) é uma funcionalidade de segurança para ajudar a proteger os dados de backup mesmo após a eliminação. Com a eliminação suave, mesmo que um ator malicioso elimine a cópia de segurança de uma base de dados (ou os dados de cópia de segurança sejam acidentalmente eliminados), os dados de backup são retidos por mais 14 dias. Isto permite a recuperação desse item de backup sem perda de dados. Esta retenção adicional de 14 dias dos dados de backup no estado de "soft delete" não incorre em qualquer custo para o cliente.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Excluir suave para servidor SQL em Azure VM usando o portal Azure

>[!NOTE]
>Estas instruções aplicam-se também ao SAP HANA em Azure VM.

1. Para eliminar os dados de cópia de segurança de uma base de dados num servidor SQL, a cópia de segurança tem de ser interrompida. No portal Azure, vá ao cofre dos Serviços de Recuperação, vá ao item de reserva e escolha **'Parar de fazer backup'.**

   ![Parar o backup](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. Na janela seguinte, será-lhe dada a opção de eliminar ou reter os dados de backup. Se escolher **Eliminar dados de backup,** a cópia de segurança da base de dados não será permanentemente eliminada. Em vez disso, os dados de backup serão retidos durante 14 dias no estado de apagação suave. A eliminação é adiada até ao dia 15 com e-mails de alerta regular no primeiro, dia 12 e 15 dias informando sobre o estado de backup da base de dados ao utilizador.

   ![Eliminar dados de cópia de segurança](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos Serviços de Recuperação, o item apagado suave aparecerá com um ícone vermelho de "soft-delete" ao lado.

   ![Itens apagados suaves](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Para restaurar o DB apagado suave, deve primeiro ser desescolado. Para desembolsar, escolha o DB apagado suave e, em seguida, selecione a opção **Undelete**.

   ![Base de dados undelete](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Uma janela aparecerá avisando que, se não for escolhida, todos os pontos de restauro para a base de dados serão indelegados e disponíveis para a realização de uma operação de restauro. O item de backup será retido num estado de "stop protection with reserv datas", com cópias de segurança paradas e dados de backup retidos para sempre sem nenhuma política de backup eficaz.

   ![Aviso undelete](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Neste ponto, também pode restaurar os dados selecionando **Restaurar** para o item de backup depagou suavemente escolhido.

   ![Restaurar VMs](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Após o processo de desembrulhá-lo, o estado voltará a "Parar a cópia de segurança com os dados de retenção" e, em seguida, pode escolher **a cópia de segurança do Currículo**. A operação **de backup do Currículo** traz de volta o item de backup no estado ativo, associado a uma política de backup selecionada pelo utilizador que define os horários de backup e retenção.

   ![Retomar backup](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Excluir suave para servidor SQL em VM usando Azure PowerShell

>[!NOTE]
>A versão Az.RecoveryServices necessária para utilizar o soft-delete utilizando o Azure PowerShell é mínima de 2.2.0. Use `Install-Module -Name Az.RecoveryServices -Force` para obter a versão mais recente.

A sequência de passos para a utilização do Azure PowerShell é a mesma que no portal Azure, descrito acima.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Elimine o item de backup usando a Azure PowerShell

Elimine o item de cópia de segurança utilizando o cmdlet de proteção Para [deficientes-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

O **DeleteState** do item de cópia de segurança mudará de **NotDeleted** para **ToBeDeleted**. Os dados de backup serão retidos por 14 dias. Se desejar reverter a operação de eliminação, deve efetuar-se a eliminação.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de eliminação utilizando a Azure PowerShell

Em primeiro lugar, pegue o item de backup relevante que está em estado de eliminação suave (isto é, prestes a ser eliminado).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Em seguida, efetue a operação de eliminação utilizando o [cmdlet Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

O **DeleteState** do item de cópia de segurança reverterá para **NotDeleted**. Mas a proteção ainda está parada. Retomar a cópia de segurança para voltar a ativar a proteção.

## <a name="how-to-disable-soft-delete"></a>Como desativar a eliminação suave

Desativar esta funcionalidade não é recomendado. A única circunstância em que deve considerar a desativação de apagamento suave é se estiver a planear mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reprotecer (como num ambiente de teste).) Para obter instruções sobre como desativar a eliminação suave, consulte [ativar e desativar a eliminação suave](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Passos seguintes

- Leia as [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) sobre apagar suaves
- Leia sobre todos os [recursos de segurança no Azure Backup](security-overview.md)
