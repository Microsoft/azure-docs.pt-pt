---
title: Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem
description: Saiba como usar recursos de segurança no backup do Azure para tornar os backups mais seguros.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: e3da4778a82cd5eb50fbb82c7f9f00cf6c6f1a85
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829634"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Recursos de segurança para ajudar a proteger cargas de trabalho de nuvem que usam o backup do Azure

As preocupações em relação a problemas de segurança, como software maligno, ransomware e intrusão, estão a aumentar. Estes problemas de segurança podem ser dispendiosos, tanto em termos de dinheiro como de dados. Para se proteger contra tais ataques, o backup do Azure agora fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão. Um desses recursos é exclusão reversível. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua o backup de uma VM (ou os dados de backup sejam excluídos acidentalmente), os dados de backup são mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Esses 14 dias adicionais de retenção de dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para o cliente. O Azure também criptografa todos os dados de backup em repouso usando [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) para proteger ainda mais seus dados.

> [!NOTE]
> A exclusão reversível só protege os dados de backup excluídos. Se uma VM for excluída sem um backup, o recurso de exclusão reversível não preservará os dados. Todos os recursos devem ser protegidos com o backup do Azure para garantir a resiliência completa.
>

## <a name="soft-delete"></a>Eliminação de forma recuperável

### <a name="supported-regions"></a>Regiões suportadas

A exclusão reversível tem suporte no momento no EUA Central ocidental, Ásia Oriental, Canadá central, leste do Canadá, França central, sul da França, Coreia central, Coreia do Sul, Sul do Reino Unido, Oeste do Reino Unido, leste da Austrália, leste do Sul da Austrália, Europa Setentrional, oeste dos EUA, oeste dos EUA 2, EUA Central, sul Ásia Oriental, norte EUA Central, Sul EUA Central, leste do Japão, oeste do Japão, sul da Índia, Índia central, Índia ocidental, leste dos EUA 2, Norte da Suíça, Oeste da Suíça e todas as regiões nacionais.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Exclusão reversível para VMs usando portal do Azure

1. Para excluir os dados de backup de uma VM, o backup deve ser interrompido. Na portal do Azure, vá para o cofre dos serviços de recuperação, clique com o botão direito do mouse no item de backup e escolha **parar backup**.

   ![Captura de tela de portal do Azure itens de backup](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela a seguir, você terá a opção de excluir ou reter os dados de backup. Se você escolher **excluir dados de backup** e **parar o backup**, o backup da VM não será excluído permanentemente. Em vez disso, os dados de backup serão mantidos por 14 dias no estado de exclusão reversível. Se **excluir dados de backup** for escolhido, um alerta de email de exclusão será enviado para a ID de email configurada informando ao usuário que 14 dias permanecem de retenção estendida para dados de backup. Além disso, um alerta por email é enviado no dia de 12 dias informando que há mais dois dias restantes para ressuscitar os dados excluídos. A exclusão é adiada até o dia 15, quando a exclusão permanente ocorre e um alerta de email final é enviado informando sobre a exclusão permanente dos dados.

   ![Captura de tela de portal do Azure, parar o backup Screen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Durante esses 14 dias, no cofre dos serviços de recuperação, a VM com exclusão reversível será exibida com um ícone vermelho de "exclusão reversível" ao lado dele.

   ![Captura de tela de portal do Azure, VM no estado de exclusão reversível](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Se algum item de backup excluído por software estiver presente no cofre, o cofre não poderá ser excluído nesse momento. Tente excluir o cofre depois que os itens de backup forem excluídos permanentemente e não houver nenhum item no estado de exclusão reversível no cofre.

4. Para restaurar a VM com exclusão reversível, primeiro ela deve ser restaurada. Para restaurar, escolha a VM com exclusão reversível e, em seguida, selecione a opção **restaurar**.

   ![Captura de tela de portal do Azure, restaurar VM](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Uma janela será exibida avisando que, se a exclusão for escolhida, todos os pontos de restauração da VM serão restaurados e estarão disponíveis para a execução de uma operação de restauração. A VM será retida em um estado "parar proteção com retenção de dados" com backups pausados e dados de backup retidos para sempre, sem nenhuma política de backup em vigor.

   ![Captura de tela de portal do Azure, confirmar restaurar VM](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   Neste ponto, você também pode restaurar a VM selecionando **restaurar VM** do ponto de restauração escolhido.  

   ![Captura de tela de portal do Azure, opção restaurar VM](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > O coletor de lixo só executará e limpará os pontos de recuperação expirados depois que o usuário executar a operação de **retomada de backup** .

5. Depois que o processo de reinicialização for concluído, o status retornará "parar backup com reter dados" e você poderá escolher **retomar backup**. A operação **retomar backup** retorna o item de backup no estado ativo, associado a uma política de backup selecionada pelo usuário que define os agendamentos de backup e retenção.

   ![Captura de tela de portal do Azure, opção retomar backup](./media/backup-azure-security-feature-cloud/resume-backup.png)

Este gráfico de fluxo mostra as diferentes etapas e Estados de um item de backup quando a exclusão reversível está habilitada:

![Ciclo de vida do item de backup excluído por software](./media/backup-azure-security-feature-cloud/lifecycle.png)

Para obter mais informações, consulte a seção [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) abaixo.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Exclusão reversível para VMs usando o Azure PowerShell

> [!IMPORTANT]
> A versão AZ. Recoveryservices necessária para usar a exclusão reversível usando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Conforme descrito acima para portal do Azure, a sequência de etapas é a mesma ao usar o Azure PowerShell também.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Excluir o item de backup usando o Azure PowerShell

Exclua o item de backup usando o cmdlet [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) do PS.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O ' Deletestate ' do item de backup será alterado de ' candeleted ' para ' ToBeDeleted '. Os dados de backup serão mantidos por 14 dias. Se você quiser reverter a operação de exclusão, a ação desfazer-excluir deverá ser executada.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazendo a operação de exclusão usando o Azure PowerShell

Primeiro, busque o item de backup relevante que está no estado de exclusão reversível (ou seja, prestes a ser excluído).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, execute a operação de desfazer exclusão usando o cmdlet [Undo-AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) do PS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

O ' Deletestate ' do item de backup será revertido para ' candeleted '. Mas a proteção ainda está parada. Você precisa [retomar o backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) para reabilitar a proteção.

### <a name="soft-delete-for-vms-using-rest-api"></a>Exclusão reversível para VMs usando a API REST

- Exclua os backups usando a API REST, conforme mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se o usuário quiser desfazer essas operações de exclusão, consulte as etapas mencionadas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).

## <a name="disabling-soft-delete"></a>Desabilitando a exclusão reversível

A exclusão reversível é habilitada por padrão em cofres recém-criados para proteger dados de backup de exclusões acidentais ou mal-intencionadas.  Não é recomendável desabilitar esse recurso. A única circunstância em que você deve considerar a desabilitação da exclusão reversível é se você está planejando mover seus itens protegidos para um novo cofre e não pode aguardar os 14 dias necessários antes de excluir e proteger novamente (como em um ambiente de teste). Somente um administrador de backup pode desabilitar esse recurso. Se você desabilitar esse recurso, todas as exclusões de itens protegidos resultarão na remoção imediata, sem a capacidade de restaurar. Dados de backup em estado de exclusão reversível antes de desabilitar esse recurso, permanecerão no estado de exclusão reversível. Se você quiser excluí-las permanentemente imediatamente, será necessário restaurar e excluí-las novamente para que sejam excluídas permanentemente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desabilitando a exclusão reversível usando o portal do Azure

Para desabilitar a exclusão reversível, siga estas etapas:

1. Na portal do Azure, acesse seu cofre e vá para **configurações** -> **Propriedades**.
2. No painel Propriedades, selecione **configurações de segurança** -> **atualização**.  
3. No painel configurações de segurança, em **exclusão reversível**, selecione **desabilitar**.

![Desabilitar exclusão reversível](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desabilitando a exclusão reversível usando o Azure PowerShell

> [!IMPORTANT]
> A versão AZ. Recoveryservices necessária para usar a exclusão reversível usando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Para desabilitar, use o cmdlet [set-AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) do PS.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Desabilitando a exclusão reversível usando a API REST

Para desabilitar a funcionalidade de exclusão reversível usando a API REST, consulte as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Excluindo permanentemente itens de backup com exclusão reversível

Dados de backup em estado de exclusão reversível antes de desabilitar esse recurso, permanecerão no estado de exclusão reversível. Se você quiser excluí-las permanentemente, desexclua-as e exclua-as novamente para que sejam excluídas permanentemente.

### <a name="using-azure-portal"></a>Utilizar o portal do Azure

Siga estes passos.

1. Siga as etapas para [desabilitar a exclusão reversível](#disabling-soft-delete).
2. No portal do Azure, acesse seu cofre, vá para **itens de backup**e escolha a VM com exclusão reversível.

![Escolher VM com exclusão reversível](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **restaurar**.

![Escolha restaurar](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. Uma janela será exibida. Selecione **restaurar**.

![Selecione restaurar](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **excluir dados de backup** para excluir permanentemente os dados de backup.

![Escolha excluir dados de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digite o nome do item de backup para confirmar que você deseja excluir os pontos de recuperação.

![Digite o nome do item de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Para excluir os dados de backup do item, selecione **excluir**. Uma mensagem de notificação permite que você saiba que os dados de backup foram excluídos.

### <a name="using-azure-powershell"></a>Usando o Azure PowerShell

Se os itens foram excluídos antes de a exclusão reversível ter sido desabilitada, eles estarão em um estado de exclusão reversível. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e, em seguida, executada novamente.

Identifique os itens que estão no estado de exclusão reversível.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Em seguida, inverta a operação de exclusão que foi executada quando a exclusão reversível foi habilitada.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Como a exclusão reversível agora está desabilitada, a operação de exclusão resultará na remoção imediata dos dados de backup.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Utilizar a API REST

Se os itens foram excluídos antes de a exclusão reversível ter sido desabilitada, eles estarão em um estado de exclusão reversível. Para excluí-los imediatamente, a operação de exclusão precisa ser revertida e, em seguida, executada novamente.

1. Primeiro, desfaça as operações de exclusão com as etapas mencionadas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data).
2. Em seguida, desabilite a funcionalidade de exclusão reversível usando a API REST usando as etapas mencionadas [aqui](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Em seguida, exclua os backups usando a API REST, conforme mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="encryption"></a>Encriptação

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas da Microsoft

Os dados de backup são criptografados automaticamente usando a criptografia de armazenamento do Azure. A criptografia protege seus dados e ajuda a atender aos compromissos de segurança e conformidade da organização. Os dados são criptografados e descriptografados de forma transparente usando a criptografia AES de 256 bits, uma das codificações de bloco mais fortes disponíveis e é compatível com o FIPS 140-2. A criptografia de armazenamento do Azure é semelhante à criptografia BitLocker no Windows.

No Azure, os dados em trânsito entre o armazenamento do Azure e o cofre são protegidos por HTTPS. Esses dados permanecem na rede de backbone do Azure.

Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte as [perguntas frequentes sobre o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a perguntas que você possa ter sobre criptografia.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Criptografia de dados de backup usando chaves gerenciadas pelo cliente

Ao fazer backup de máquinas virtuais do Azure, você também tem a opção de criptografar os dados de backup no cofre dos serviços de recuperação usando suas chaves de criptografia armazenadas no Azure Key Vault.

>[!NOTE]
>Este recurso está atualmente em uso inicial. Preencha [esta pesquisa](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) se desejar criptografar seus dados de backup usando chaves gerenciadas pelo cliente. Observe que a capacidade de usar esse recurso está sujeita à aprovação do serviço de backup do Azure.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Backup de VM de disco gerenciado criptografado usando chaves gerenciadas pelo cliente

O backup do Azure permite fazer backup de máquinas virtuais do Azure que contêm discos criptografados usando chaves gerenciadas pelo cliente. Para obter detalhes, consulte [criptografia de discos gerenciados com chaves gerenciadas pelo cliente](https://docs.microsoft.com//azure/virtual-machines/windows/disk-encryption#customer-managed-keys-public-preview).

### <a name="backup-of-encrypted-vms"></a>Backup de VMs criptografadas

Você pode fazer backup e restaurar máquinas virtuais (VMs) do Azure do Windows ou Linux com discos criptografados usando o serviço de backup do Azure. Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## <a name="other-security-features"></a>Outros recursos de segurança

### <a name="protection-of-azure-backup-recovery-points"></a>Proteção dos pontos de recuperação do backup do Azure

As contas de armazenamento usadas pelos cofres dos serviços de recuperação são isoladas e não podem ser acessadas por usuários para fins mal-intencionados. O acesso é permitido somente por meio de operações de gerenciamento de backup do Azure, como a restauração. Essas operações de gerenciamento são controladas pelo RBAC (controle de acesso baseado em função).

Para obter mais informações, consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação do backup do Azure](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="for-soft-delete"></a>Para exclusão reversível

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>É necessário habilitar o recurso de exclusão reversível em todos os cofres?

Não, ele é compilado e habilitado por padrão para todos os cofres dos serviços de recuperação.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Posso configurar o número de dias pelos quais meus dados serão retidos no estado de exclusão reversível após a conclusão da operação de exclusão?

Não, ele é corrigido para 14 dias de retenção adicional após a operação de exclusão.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>É necessário pagar o custo dessa retenção adicional de 14 dias?

Não, essa retenção adicional de 14 dias é gratuita por custo como parte da funcionalidade de exclusão reversível.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso executar uma operação de restauração quando meus dados estiverem no estado de exclusão reversível?

Não, você precisa restaurar o recurso de exclusão reversível a fim de restaurá-lo. A operação de desfazer exclusão levará o recurso de volta para o **estado parar proteção com manter dados** , em que você pode restaurar para qualquer ponto no tempo. O coletor de lixo permanece pausado nesse estado.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Meus instantâneos seguem o mesmo ciclo de vida dos meus pontos de recuperação no cofre?

Sim.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso disparar os backups agendados novamente para um recurso excluído de forma reversível?

Não excluir seguido pela operação de retomada irá proteger o recurso novamente. A operação de retomada associa uma política de backup para disparar os backups agendados com o período de retenção selecionado. Além disso, o coletor de lixo é executado assim que a operação de retomada é concluída. Se você quiser executar uma restauração de um ponto de recuperação após sua data de expiração, é recomendável fazer isso antes de disparar a operação de retomada.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso excluir meu cofre se houver itens com exclusão reversível no cofre?

O cofre dos serviços de recuperação não poderá ser excluído se houver itens de backup em estado de exclusão reversível no cofre. Os itens excluídos por software são excluídos permanentemente 14 dias após a operação de exclusão. Se você não puder esperar por 14 dias, [desabilite a exclusão reversível](#disabling-soft-delete), exclua os itens com exclusão reversível e exclua-os novamente para ser excluído permanentemente. Depois de garantir que não há itens protegidos e nenhum item com exclusão reversível, o cofre pode ser excluído.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso excluir os dados anteriores ao período de exclusão reversível de 14 dias após a exclusão?

Não. Não é possível forçar a exclusão dos itens excluídos por software, eles são excluídos automaticamente após 14 dias. Esse recurso de segurança é habilitado para proteger os dados de backup de exclusões acidentais ou mal-intencionadas.  Você deve aguardar por 14 dias antes de executar qualquer outra ação na VM.  Os itens excluídos de forma reversível não serão cobrados.  Se você precisar proteger novamente as VMs marcadas para exclusão reversível dentro de 14 dias para um novo cofre, entre em contato com o suporte da Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de exclusão reversível podem ser executadas no PowerShell ou na CLI?

As operações de exclusão reversível podem ser executadas usando o [PowerShell](#soft-delete-for-vms-using-azure-powershell). Atualmente, não há suporte para a CLI.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>A exclusão reversível tem suporte para outras cargas de trabalho de nuvem, como SQL Server em VMs do Azure e SAP HANA em VMs do Azure?

Não. Atualmente, há suporte para a exclusão reversível em máquinas virtuais do Azure.

## <a name="next-steps"></a>Passos seguintes

- Leia sobre os [controles de segurança para o backup do Azure](backup-security-controls.md).
