---
title: Eliminação suave para Backup Azure
description: Aprenda a utilizar funcionalidades de segurança no Azure Backup para tornar as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 2b0d7a00bce8dfa427958f6db6d7174b9d5f7a79
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116406"
---
# <a name="soft-delete-for-azure-backup"></a>Eliminação suave para Backup Azure

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para se proteger de tais ataques, o Azure Backup fornece agora funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação.

Uma dessas características é a eliminação suave. Com a eliminação suave, mesmo que um ator malicioso apague uma cópia de segurança (ou os dados de backup sejam acidentalmente eliminados), os dados de backup são retidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias de dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente.

A proteção de eliminação suave está disponível para estes serviços:

- [Eliminação suave para máquinas virtuais Azure](soft-delete-virtual-machines.md)
- [Eliminação suave para servidor SQL em VM Azure e eliminação suave para SAP HANA em cargas de trabalho VM Azure](soft-delete-sql-saphana-in-azure-vm.md)

Este gráfico de fluxo mostra os diferentes passos e estados de um item de reserva quando o Soft Delete está ativado:

![Ciclo de vida de artigo de backup eliminado suave](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Permitir e desativar a eliminação suave

A eliminação suave é ativada por padrão em cofres recém-criados para proteger os dados de backup de exclusões acidentais ou maliciosas.  Não é recomendável desativar esta funcionalidade. A única circunstância em que deve considerar desativar o soft delete é se planeia mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reproteger (como num ambiente de teste).) Só o dono do cofre pode desativar esta funcionalidade. Se desativar esta funcionalidade, todas as futuras eliminações de itens protegidos resultarão em remoção imediata, sem a capacidade de restaurar. Os dados de backup que existem em estado suave apagado antes de desativar esta funcionalidade permanecerão em estado suave apagado durante o período de 14 dias. Se pretender eliminá-las permanentemente imediatamente, então terá de os eliminar e apagar novamente para ser eliminado permanentemente.

 É importante lembrar que uma vez que o soft delete é desativado, a funcionalidade é desativada para todos os tipos de cargas de trabalho, incluindo o servidor SQL e cargas de trabalho SAP HANA. Por exemplo, uma vez ativada a [pré-visualização SQL Server/ SAP HANA](https://docs.microsoft.com/azure/backup/soft-delete-sql-saphana-in-azure-vm#steps-to-enroll-in-preview) para uma subscrição, não é possível desativar a eliminação suave apenas para servidor SQL ou DBS SAP HANA, mantendo-o ativado para máquinas virtuais no mesmo cofre. Pode criar cofres separados para controlo granular.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desativar soft delete usando portal Azure

Para desativar a eliminação suave, siga estes passos:

1. No portal Azure, vá ao seu cofre e depois vá para **definições**  ->  **propriedades**.
2. No painel de propriedades, selecione Atualização de **Definições**de Segurança  ->  **Update**.  
3. No painel de definições de segurança, em **Soft Delete,** selecione **Desativar**.

![Desativar a eliminação suave](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desativar soft delete usando Azure PowerShell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para utilizar soft-delete utilizando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Para desativar, utilize o [Set-AzRecoveryServicesBackupBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS cmdlet.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Desativar soft delete usando REST API

Para desativar a funcionalidade de eliminação suave utilizando a API REST, consulte os passos [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)mencionados .

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Eliminação permanente de itens de backup eliminados suaves

Os dados de backup em estado eliminado suave antes de desativar esta funcionalidade permanecerão em estado suave apagado. Se pretender eliminá-los permanentemente, então elimine-os e elimine-os novamente para ser permanentemente eliminado.

### <a name="using-azure-portal"></a>Com o Portal do Azure

Siga estes passos.

1. Siga os passos para [desativar a eliminação suave](#enabling-and-disabling-soft-delete).

2. No portal Azure, vá ao seu cofre, vá a **Backup Items,** e escolha o item suave apagado.

   ![Escolha o item apagado suave](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **Não apagar**.

   ![Escolha Não excluir](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Uma janela aparecerá. **Selecione Undelete**.

   ![Selecione Undelete](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **eliminar os dados de backup** para eliminar permanentemente os dados de backup.

   ![Escolha eliminar dados de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digite o nome do item de reserva para confirmar que pretende eliminar os pontos de recuperação.

   ![Digite o nome do artigo de reserva](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Para eliminar os dados de cópia de segurança do artigo, **selecione Eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de backup foram eliminados.

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Se os itens foram eliminados antes de a eliminação suave ter sido desativada, então estarão em estado de eliminação suave. Para eliminá-los imediatamente, a operação de eliminação tem de ser invertida e executada novamente.

Identifique os itens que estão em estado de soft-deleted.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, inverta a operação de eliminação que foi realizada quando a eliminação suave foi ativada.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Uma vez que o soft-delete está agora desativado, a operação de eliminação resultará na remoção imediata dos dados de backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Com a API REST

Se os itens foram eliminados antes de a eliminação suave ter sido desativada, então estarão em estado de eliminação suave. Para eliminá-los imediatamente, a operação de eliminação tem de ser invertida e executada novamente.

1. Em primeiro lugar, desfazer as operações de eliminação com os passos [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)mencionados .
2. Em seguida, desative a funcionalidade de eliminação suave utilizando a API REST utilizando os passos [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)mencionados .
3. Em seguida, elimine as cópias de segurança utilizando a API REST como mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Preciso de ativar a funcionalidade de eliminação suave em todos os cofres?

Não, está incorporado e habilitado por padrão para todos os cofres dos serviços de recuperação.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Posso configurar o número de dias para os quais os meus dados serão mantidos em estado de eliminação suave após a operação de eliminação estar concluída?

Não, está fixado a 14 dias de retenção adicional após a operação de eliminação.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Preciso pagar o custo por esta retenção adicional de 14 dias?

Não, esta retenção adicional de 14 dias vem livre de custos como parte da funcionalidade soft-delete.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso executar uma operação de restauro quando os meus dados estão em estado de eliminação suave?

Não, é necessário desapagar o recurso suave apagado para restaurar. A operação de exclusão trará o recurso de volta para a **proteção Stop com o estado** de retenção onde pode restaurar a qualquer momento. O colecionador de lixo continua em pausa neste estado.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>As minhas fotos seguirão o mesmo ciclo de vida que os meus pontos de recuperação no cofre?

Sim.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso voltar a ativar as cópias de segurança programadas para um recurso apagado?

A eliminação seguida de uma operação de retoma protegerá novamente o recurso. A operação de retoma associa uma política de backup para desencadear as cópias de segurança programadas com o período de retenção selecionado. Além disso, o coletor de lixo funciona assim que a operação de retoma estiver concluída. Se desejar efetuar um restauro a partir de um ponto de recuperação que já ultrapassou a data de validade, é aconselhável fazê-lo antes de desencadear a operação de retoma.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso apagar o meu cofre se houver itens apagados macios no cofre?

O cofre dos Serviços de Recuperação não pode ser apagado se houver itens de reserva em estado de apagar suavemente no cofre. Os artigos apagados são eliminados permanentemente 14 dias após a eliminação da operação. Se não puder esperar 14 dias, [desative suavemente,](#enabling-and-disabling-soft-delete)desapague os itens apagados macios e elimine-os novamente para ser eliminado permanentemente. Depois de garantir que não existem itens protegidos e não há itens apagados macios, o cofre pode ser apagado.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso apagar os dados mais cedo do que os 14 dias de eliminação suave após a eliminação?

Não. Não se pode forçar a apagar os itens apagados. São automaticamente apagados após 14 dias. Esta funcionalidade de segurança está ativada para salvaguardar os dados de back-up de exclusões acidentais ou maliciosas.  Deve esperar 14 dias antes de realizar qualquer outra ação sobre o artigo.  Itens apagados não serão carregados.  Se precisar de reproteger os itens marcados para apagar suavemente dentro de 14 dias num novo cofre, contacte o suporte da Microsoft.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de eliminação suave podem ser realizadas em PowerShell ou CLI?

As operações de eliminação suave podem ser efetuadas através do PowerShell. Atualmente, o CLI não é suportado.

## <a name="next-steps"></a>Próximos passos

- [Visão geral das funcionalidades de segurança no Azure Backup](security-overview.md)
