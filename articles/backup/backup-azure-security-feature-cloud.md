---
title: Excluir suave para cópia de segurança do Azure
description: Aprenda a usar funcionalidades de segurança no Azure Backup para tornar as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95254054"
---
# <a name="soft-delete-for-azure-backup"></a>Excluir suave para cópia de segurança do Azure

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para evitar tais ataques, o Azure Backup fornece agora funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação.

Uma dessas características é a eliminação suave. Com a eliminação suave, mesmo que um ator malicioso elimine uma cópia de segurança (ou os dados de cópia de segurança sejam acidentalmente eliminados), os dados de cópia de segurança são retidos por mais 14 dias, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "soft delete" não incorrem em qualquer custo para si.

A proteção para eliminação suave está disponível para estes serviços:

- [Excluir suave para máquinas virtuais Azure](soft-delete-virtual-machines.md)
- [Eliminar suavemente para servidor SQL em Azure VM e excluir suavemente para as cargas de trabalho SAP HANA em Azure VM](soft-delete-sql-saphana-in-azure-vm.md)

Este gráfico de fluxo mostra os diferentes passos e estados de um item de backup quando o Soft Delete está ativado:

![Ciclo de vida do item de backup apagado suave](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Permitir e desativar a eliminação suave

A eliminação suave é ativada por padrão em cofres recém-criados para proteger os dados de backup de eliminações acidentais ou maliciosas.  Desativar esta funcionalidade não é recomendado. A única circunstância em que deve considerar a desativação de apagamento suave é se estiver a planear mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de apagar e reprotecer (como num ambiente de teste).) Só o proprietário do cofre pode desativar esta função. Se desativar esta funcionalidade, todas as futuras eliminações de itens protegidos resultarão numa remoção imediata, sem a capacidade de restaurar. Os dados de backup que existem em estado de apagação suave antes de desativar esta funcionalidade, permanecerão em estado de apagação suave durante o período de 14 dias. Se desejar eliminá-las imediatamente, terá de desafiá-las e eliminá-las novamente para ser permanentemente eliminada.

É importante lembrar que uma vez que a eliminação suave é desativada, a funcionalidade é desativada para todos os tipos de cargas de trabalho. Por exemplo, não é possível desativar a exclusão suave apenas para servidor SQL ou DBs SAP HANA, mantendo-o ativado para máquinas virtuais no mesmo cofre. Pode criar abóbadas separadas para controlo granular.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desativar a eliminação suave usando o portal Azure

Para desativar a eliminação suave, siga estes passos:

1. No portal Azure, vá ao seu cofre e, em seguida, vá para **As**  ->  **Definições Propriedades**.
2. No painel de propriedades, selecione **Atualização de Definições de Segurança**  ->  .  
3. No painel de definições de segurança, em **Soft Delete**, selecione **Desativar**.

![Desativar a eliminação suave](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desativar a eliminação suave usando a Azure PowerShell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para utilizar o soft-delete utilizando o Azure PowerShell é mínima de 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Para desativar, utilize o [cmdlet Set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) PowerShell.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Desativar a eliminação suave usando REST API

Para desativar a funcionalidade de eliminação suave utilizando a API REST, consulte os passos [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)mencionados .

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Eliminação permanente de itens de backup apagados suaves

Os dados de backup em estado apagado suave antes de desativar esta funcionalidade, permanecerão em estado de apagação suave. Se desejar eliminá-las imediatamente, desembrulhe-as e elimine-as novamente para ser permanentemente eliminada.

### <a name="using-azure-portal"></a>Com o Portal do Azure

Siga estes passos:

1. Siga os passos para [desativar a eliminação suave](#enabling-and-disabling-soft-delete).

2. No portal Azure, vá ao seu cofre, vá a **Itens de Reserva** e escolha o item apagado suave.

   ![Escolha um item apagado suave](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **Undelete**.

   ![Escolha Undelete](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Aparecerá uma janela. Selecione **Undelete**.

   ![Selecione Undelete](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **Eliminar os dados de backup** para eliminar permanentemente os dados de backup.

   ![Escolha Eliminar dados de backup](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Digite o nome do item de cópia de segurança para confirmar que pretende eliminar os pontos de recuperação.

   ![Digite o nome do item de reserva](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Para eliminar os dados de cópia de segurança do item, selecione **Eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de cópia de segurança foram eliminados.

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Se os itens foram eliminados antes de serem desativadas, estarão num estado de eliminação suave. Para eliminá-los imediatamente, a operação de eliminação tem de ser invertida e, em seguida, executada novamente.

Identifique os itens que estão em estado de apagação suave.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, inverta a operação de eliminação que foi efetuada quando foi ativada a eliminação suave.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Uma vez que a eliminação suave está agora desativada, a operação de eliminação resultará na remoção imediata dos dados de backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Com a API REST

Se os itens foram eliminados antes de serem desativadas, estarão num estado de eliminação suave. Para eliminá-los imediatamente, a operação de eliminação tem de ser invertida e, em seguida, executada novamente.

1. Em primeiro lugar, desfaça as operações de eliminação com os passos [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)mencionados.
2. Em seguida, desative a funcionalidade de eliminação suave utilizando a API REST utilizando os passos [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)mencionados .
3. Em seguida, elimine as cópias de segurança utilizando a API REST, como mencionado [aqui.](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Preciso de ativar a função de eliminação suave em cada cofre?

Não, é incorporado e habilitado por defeito para todos os cofres dos Serviços de Recuperação.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Posso configurar o número de dias para os quais os meus dados serão retidos em estado de eliminação suave após a conclusão da operação de eliminação?

Não, é fixado a 14 dias de retenção adicional após a operação de eliminação.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Preciso pagar o custo por esta retenção adicional de 14 dias?

Não, esta retenção adicional de 14 dias é livre de custos como parte da funcionalidade soft-delete.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso realizar uma operação de restauro quando os meus dados estão em estado de exclusão suave?

Não, é necessário desembolsar o recurso suave apagado para restaurar. A operação undelete trará o recurso de volta à proteção Stop com o **estado de conservação** dos dados onde pode restaurar a qualquer ponto no tempo. O coletor de lixo continua parado neste estado.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>As minhas fotos vão seguir o mesmo ciclo de vida que os meus pontos de recuperação no cofre?

Sim.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso voltar a ativar as cópias de segurança programadas para um recurso apagado?

Undelete seguido de uma operação retomada protegerá o recurso novamente. A operação de currículo associa uma política de backup para desencadear as cópias de segurança programadas com o período de retenção selecionado. Além disso, o coletor de lixo funciona assim que a operação do currículo terminar. Se desejar efetuar uma restauração a partir de um ponto de recuperação que já passou da data de validade, é aconselhável fazê-lo antes de desencadear a operação do currículo.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso apagar o meu cofre se houver itens suaves apagados no cofre?

O cofre dos Serviços de Recuperação não pode ser apagado se houver itens de reserva em estado de apagação suave no cofre. Os itens apagados com soft-apagado são permanentemente eliminados 14 dias após a operação de eliminação. Se não puder esperar 14 dias, [desative a eliminação suave](#enabling-and-disabling-soft-delete), desative os itens apagados suaves e elimine-os novamente para ser eliminado permanentemente. Depois de se ter assegurado de que não existem itens protegidos e nenhum produto apagado macio, o cofre pode ser eliminado.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso eliminar os dados mais cedo do que os 14 dias de eliminação suave após a eliminação?

N.º Não é possível forçar a eliminação dos itens apagados. São apagados automaticamente após 14 dias. Esta funcionalidade de segurança está ativada para salvaguardar os dados com apoio de eliminações acidentais ou maliciosas.  Deve esperar 14 dias antes de realizar qualquer outra ação no artigo.  Os itens apagados não serão carregados.  Se precisar de reprotegir os itens marcados para eliminar suavemente no prazo de 14 dias num novo cofre, contacte o suporte da Microsoft.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de eliminação suave podem ser executadas em PowerShell ou CLI?

As operações de eliminação suave podem ser efetuadas utilizando o PowerShell. Atualmente, o CLI não é apoiado.

## <a name="next-steps"></a>Passos seguintes

- [Visão geral das funcionalidades de segurança no Azure Backup](security-overview.md)
