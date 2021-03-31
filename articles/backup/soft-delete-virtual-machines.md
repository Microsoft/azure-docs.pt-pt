---
title: Eliminação recuperável para máquinas virtuais
description: Saiba como a eliminação suave para máquinas virtuais torna as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89022246"
---
# <a name="soft-delete-for-virtual-machines"></a>Eliminação recuperável para máquinas virtuais

A eliminação suave para VMs protege as cópias de segurança dos seus VMs de eliminação não intencional. Mesmo depois de as cópias de segurança serem eliminadas, são preservadas em estado de eliminação suave por mais 14 dias.

> [!NOTE]
> A eliminação suave apenas protege os dados de backup eliminados. Se um VM for eliminado sem uma cópia de segurança, a função de eliminação suave não preservará os dados. Todos os recursos devem ser protegidos com Azure Backup para garantir a plena resiliência.
>

## <a name="supported-regions"></a>Regiões suportadas

Atualmente é apoiado nos EUA, Leste da Ásia, Canadá Central, Canadá Oriental, França Central, França Sul, Coreia Central, Coreia do Sul, Reino Unido, Reino Unido, Oeste da Austrália, Austrália Sudeste, Norte da Europa, Eua Ocidental, Eua Ocidental, Eua Ocidentais, Centro-Americano, Ásia Do Sudeste, Norte-Americano, Centro-Sul dos EUA, Japão Oriental, Japão Ocidental, Índia Sul, Índia Central, Índia Central, 2 , Suíça Norte, Suíça Oeste, Noruega Oeste, Noruega Leste, e todas as regiões nacionais.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Excluir suave para VMs usando o portal Azure

1. Para eliminar os dados de backup de um VM, a cópia de segurança tem de ser interrompida. No portal Azure, vá ao cofre dos Serviços de Recuperação, clique com o botão direito no item de backup e escolha **Stop backup**.

   ![Screenshot de Itens de Backup do portal Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela seguinte, será-lhe dada a opção de eliminar ou reter os dados de backup. Se escolher **Eliminar dados de backup** e, em seguida, parar a cópia de **segurança,** a cópia de segurança VM não será permanentemente eliminada. Em vez disso, os dados de backup serão retidos durante 14 dias no estado de apagação suave. Se **forem escolhidos dados de cópias de segurança,** é enviado um alerta de e-mail para o ID de e-mail configurado informando o utilizador de que restam 14 dias de retenção prolongada para dados de backup. Além disso, é enviado um alerta de e-mail no 12º dia informando que faltam mais dois dias para ressuscitar os dados eliminados. A eliminação é adiada até ao dia 15, quando ocorrerá uma eliminação permanente e é enviado um alerta de e-mail final informando sobre a eliminação permanente dos dados.

   ![Screenshot do portal Azure, ecrã stop backup](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos Serviços de Recuperação, o VM apagado suave aparecerá com um ícone vermelho de "soft-delete" ao lado.

   ![Screenshot do portal Azure, VM em estado de exclusão suave](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se houver itens de reserva apagados no cofre, o cofre não pode ser apagado nessa altura. Tente apagar o cofre depois de os itens de reserva serem permanentemente apagados, e não há itens em estado de eliminação suave deixados no cofre.

4. Para restaurar o VM apagado suave, tem primeiro de ser desesmado. Para desembolsar, escolha o VM de apagação suave e, em seguida, selecione a opção **Undelete**.

   ![Screenshot do portal Azure, Undelete VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Uma janela aparecerá avisando que, se não for escolhida, todos os pontos de restauro para o VM serão indelegados e disponíveis para a realização de uma operação de restauro. O VM será retido num estado de "stop protection with reserv datas", com cópias de segurança paradas e dados de backup retidos para sempre sem nenhuma política de backup eficaz.

   ![Screenshot do portal Azure, Confirme vM undelete](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, também pode restaurar o VM selecionando **O VM de Restauro** a partir do ponto de restauro escolhido.  

   ![Screenshot do portal Azure, Restaurar a opção VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo só irá executar e limpar pontos de recuperação expirados após o utilizador realizar a operação **de backup do Currículo.**

5. Após o processo de desembrulhá-lo, o estado voltará a "Parar a cópia de segurança com os dados de retenção" e, em seguida, pode escolher **a cópia de segurança do Currículo**. A operação **de backup do Currículo** traz de volta o item de backup no estado ativo, associado a uma política de backup selecionada pelo utilizador que define os horários de backup e retenção.

   ![Screenshot do portal Azure, Retomar a opção de backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Excluir suave para VMs usando Azure PowerShell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para utilizar o soft-delete utilizando o Azure PowerShell é mínima de 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Como descrito acima para o portal Azure, a sequência de passos é a mesma enquanto se utiliza Azure PowerShell também.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Elimine o item de backup usando a Azure PowerShell

Elimine o item de cópia de segurança utilizando o cmdlet de proteção Para [deficientes-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O 'DeleteState' do item de backup passará de 'NotDeleted' para 'ToBeDeleted'. Os dados de backup serão retidos por 14 dias. Se desejar reverter a operação de eliminação, deve efetuar-se a eliminação.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de eliminação utilizando a Azure PowerShell

Em primeiro lugar, pegue o item de backup relevante que está em estado de eliminação suave (isto é, prestes a ser eliminado).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, efetue a operação de eliminação utilizando o [cmdlet Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

O 'DeleteState' do item de cópia de segurança reverterá para 'NotDeleted'. Mas a proteção ainda está parada. [Retomar a cópia](./backup-azure-vms-automation.md#change-policy-for-backup-items) de segurança para voltar a ativar a proteção.

## <a name="soft-delete-for-vms-using-rest-api"></a>Excluir suave para VMs usando REST API

- Elimine as cópias de segurança utilizando a API REST, como mencionado [aqui.](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)
- Se desejar desfazer estas operações de eliminação, consulte as etapas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)mencionadas .

## <a name="how-to-disable-soft-delete"></a>Como desativar a eliminação suave

Desativar esta funcionalidade não é recomendado. A única circunstância em que deve considerar a desativação de apagamento suave é se estiver a planear mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reprotecer (como num ambiente de teste).) Para obter instruções sobre como desativar a eliminação suave, consulte [ativar e desativar a eliminação suave](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Passos seguintes

- Leia as [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) sobre apagar suaves
- Leia sobre todos os [recursos de segurança no Azure Backup](security-overview.md)
