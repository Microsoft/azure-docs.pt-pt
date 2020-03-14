---
title: Funcionalidades de segurança para ajudar a proteger as cargas de trabalho em nuvem
description: Aprenda a utilizar funcionalidades de segurança no Azure Backup para tornar as cópias de segurança mais seguras.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: f89d9ab3ef373ecd9a7d15ef4ec9b2109750f8de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248076"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Funcionalidades de segurança para ajudar a proteger cargas de trabalho em nuvem que usam Backup Azure

As preocupações com questões de segurança, como malware, ransomware e intrusão, estão a aumentar. Estas questões de segurança podem ser dispendiosas, tanto em termos de dinheiro como de dados. Para se proteger de tais ataques, o Azure Backup fornece agora funcionalidades de segurança para ajudar a proteger os dados de backup mesmo após a eliminação.

Uma dessas características é a eliminação suave. Com eliminação suave, mesmo que um ator malicioso apague a cópia de segurança de um VM (ou os dados de backup são acidentalmente eliminados), os dados de backup são retidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. A retenção adicional de 14 dias de dados de backup no estado de "soft delete" não incorre em nenhum custo para o cliente. O Azure também encripta todos os dados de reserva em repouso usando [encriptação](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) do Serviço de Armazenamento para proteger ainda mais os seus dados.

A proteção de eliminação suave para máquinas virtuais Azure está geralmente disponível.

>[!NOTE]
>A eliminação suave para o servidor SQL em Azure VM e eliminação suave para SAP HANA em cargas de trabalho VM Azure já está disponível na pré-visualização.<br>
>Para se inscrever na pré-estreia, escreva-nos na AskAzureBackupTeam@microsoft.com

## <a name="soft-delete"></a>Eliminação suave

### <a name="soft-delete-for-vms"></a>Eliminação suave para VMs

A eliminação suave para VMs protege as cópias de segurança dos seus VMs de eliminação não intencional. Mesmo depois de as cópias de segurança serem eliminadas, são preservadas em estado de eliminação suave por 14 dias adicionais.

> [!NOTE]
> A eliminação suave apenas protege os dados de backup eliminados. Se um VM for eliminado sem uma cópia de segurança, a função soft-delete não preservará os dados. Todos os recursos devem ser protegidos com o Azure Backup para garantir a plena resiliência.
>

### <a name="supported-regions"></a>Regiões suportadas

A soft delete é atualmente apoiada no Centro-Oeste dos EUA, Ásia Oriental, Canadá Central, Canadá Leste, França Central, França Sul, Coreia Central, Coreia do Sul, Reino Unido, Oeste, Austrália Leste, Austrália Sudeste, Norte da Europa, Oeste DOS EUA, OESTE DOS EUA, Centro dos EUA, Sul Leste asiático, Norte-Central dos EUA, Centro-Sul dos EUA, Japão Leste, Japão Oeste, Índia Sul, Índia Central, Índia Ocidental, Leste DOS EUA 2, Suíça Norte, Suíça Oeste, e todas as regiões nacionais.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Eliminação suave para VMs utilizando portal Azure

1. Para eliminar os dados de cópia de segurança de um VM, a cópia de segurança deve ser interrompida. No portal Azure, vá ao cofre dos serviços de recuperação, clique à direita no item de reserva e escolha parar de **reserva**.

   ![Screenshot dos itens de backup do portal Azure](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. Na janela seguinte, será-lhe dada a opção de eliminar ou reter os dados de cópia de segurança. Se escolher **eliminar os dados** de backup e, em seguida, parar a cópia de **segurança,** a cópia de segurança VM não será eliminada permanentemente. Em vez disso, os dados de backup serão conservados durante 14 dias no estado suave apagado. Se eliminar os dados de **backup,** um alerta de e-mail de exclusão é enviado para o ID de e-mail configurado informando o utilizador de que restam 14 dias de retenção prolongada para dados de backup. Além disso, um alerta de e-mail é enviado no dia 12 informando que faltam mais dois dias para ressuscitar os dados apagados. A eliminação é adiada até ao 15º dia, quando ocorrer a eliminação permanente e é enviado um alerta final de e-mail informando sobre a eliminação permanente dos dados.

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

Este gráfico de fluxo mostra os diferentes passos e estados de um item de reserva quando o Soft Delete está ativado:

![Ciclo de vida de artigo de backup eliminado suave](./media/backup-azure-security-feature-cloud/lifecycle.png)

Para mais informações, consulte a secção [perguntas frequentes](backup-azure-security-feature-cloud.md#frequently-asked-questions) abaixo.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Eliminação suave para VMs usando Azure Powershell

> [!IMPORTANT]
> A versão Az.RecoveryServices necessária para utilizar soft-delete utilizando o Azure PS é min 2.2.0. Use ```Install-Module -Name Az.RecoveryServices -Force``` para obter a versão mais recente.

Tal como descrito acima para o portal Azure, a sequência de passos é a mesma ao utilizar também o Azure Powershell.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Eliminar o item de backup utilizando o Azure Powershell

Elimine o item de backup utilizando o cmdlet de proteção ps de [deficientes-AzRecoveryServicesBackup.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0)

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

O 'DeleteState' do item de cópia de segurança passará de 'NotDeleted' para 'ToBeDeleted'. Os dados de cópia de segurança serão conservados durante 14 dias. Se pretender reverter a operação de eliminação, deve efetuar a eliminação do desmino.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Desfazer a operação de eliminação utilizando a Azure Powershell

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

O 'DeleteState' do item de cópia de segurança reverterá para 'NotDeleted'. Mas a proteção ainda está parada. Tem de [retomar o backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) para reativar a proteção.

### <a name="soft-delete-for-vms-using-rest-api"></a>Eliminação suave para VMs utilizando API REST

- Elimine as cópias de segurança utilizando a API REST como mencionado [aqui](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Se o utilizador desejar desfazer estas operações de eliminação, consulte as etapas [aqui](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)mencionadas .

## <a name="disabling-soft-delete"></a>Desativar suavemente apaga-se

A eliminação suave é ativada por padrão em cofres recém-criados para proteger os dados de backup de exclusões acidentais ou maliciosas.  Não é recomendada a desativação desta funcionalidade. A única circunstância em que deve considerar a desativação suave é se planeia mover os seus itens protegidos para um novo cofre, e não pode esperar os 14 dias necessários antes de eliminar e reproteger (como num ambiente de teste).) Só o dono do cofre pode desativar esta funcionalidade. Se desativar esta funcionalidade, todas as futuras eliminações de itens protegidos resultarão em remoção imediata, sem a capacidade de restaurar. Os dados de backup que existem em estado suave apagado antes de desativar esta funcionalidade permanecerão em estado suave apagado durante o período de 14 dias. Se pretender eliminá-las permanentemente imediatamente, então terá de os eliminar e apagar novamente para ser eliminado permanentemente.

### <a name="disabling-soft-delete-using-azure-portal"></a>Desativar soft delete usando portal Azure

Para desativar a eliminação suave, siga estes passos:

1. No portal Azure, vá ao seu cofre e, em seguida, vá ao **Definições** -> **Propriedades**.
2. No painel de propriedades, selecione **Definições** de Segurança -> **Atualização**.  
3. No painel de definições de segurança, em **Soft Delete,** selecione **Desativar**.

![Desativar a eliminação suave](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Desativar soft delete usando Azure Powershell

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

1. Siga os passos para [desativar a eliminação suave](#disabling-soft-delete).
2. No portal Azure, vá ao seu cofre, vá a **Backup Itens**e escolha o VM suave apagado.

   ![Escolha VM eliminado suavemente](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Selecione a opção **Não apagar**.

   ![Escolha Não excluir](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Uma janela aparecerá. **Selecione Undelete**.

   ![Selecione Undelete](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Escolha **eliminar os dados de backup** para eliminar permanentemente os dados de backup.

   ![Escolha eliminar dados de backup](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. Digite o nome do item de reserva para confirmar que pretende eliminar os pontos de recuperação.

   ![Digite o nome do artigo de reserva](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. Para eliminar os dados de cópia de segurança do artigo, **selecione Eliminar**. Uma mensagem de notificação permite-lhe saber que os dados de backup foram eliminados.

### <a name="using-azure-powershell"></a>Usando a Powershell Azure

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

## <a name="encryption"></a>Encriptação

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pela Microsoft

Os dados de backup são automaticamente encriptados utilizando encriptação do Armazenamento Azure. A encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade. Os dados são encriptados e desencriptados de forma transparente utilizando encriptação AES de 256 bits, uma das cifras de blocos mais fortes disponíveis, e é compatível com o FIPS 140-2. A encriptação azure Storage é semelhante à encriptação BitLocker no Windows.

Dentro do Azure, os dados em trânsito entre o armazenamento do Azure e o cofre estão protegidos por HTTPS. Estes dados permanecem na rede de espinha dorsal Azure.

Para mais informações, consulte [a encriptação do Armazenamento Azure para obter dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption). Consulte o [FaQ de Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) para responder a quaisquer perguntas que possa ter sobre encriptação.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Encriptação de dados de backup usando chaves geridas pelo cliente

Ao fazer backup das Máquinas Virtuais Azure, também tem a opção de encriptar os seus dados de backup no Cofre de Serviços de Recuperação utilizando as chaves de encriptação armazenadas no Cofre de Chaves Azure.

>[!NOTE]
>Esta funcionalidade encontra-se atualmente em uso precoce. Preencha [este inquérito](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) se pretender encriptar os seus dados de backup utilizando as chaves geridas pelo cliente. Note que a capacidade de utilização desta funcionalidade está sujeita à aprovação do serviço de backup Azure.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>Backup do VM de disco gerido encriptado usando chaves geridas pelo cliente

O Azure Backup permite-lhe fazer backup de Máquinas Virtuais Azure contendo discos encriptados utilizando chaves geridas pelo cliente. Para mais detalhes, consulte a [Encriptação de discos geridos com chaves geridas](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)pelo cliente .

### <a name="backup-of-encrypted-vms"></a>Backup de VMs encriptados

Pode fazer backup e restaurar as máquinas virtuais Windows ou Linux Azure (VMs) com discos encriptados utilizando o serviço de backup Azure. Para obter instruções, consulte [Back up e restaure as máquinas virtuais encriptadas com cópia](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)de segurança Azure .

## <a name="private-endpoints"></a>Pontos Finais Privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="other-security-features"></a>Outras funcionalidades de segurança

### <a name="protection-of-azure-backup-recovery-points"></a>Proteção dos pontos de recuperação de Backup Azure

As contas de armazenamento utilizadas pelos cofres dos serviços de recuperação estão isoladas e não podem ser acedidas pelos utilizadores para qualquer finalidade maliciosa. O acesso só é permitido através de operações de gestão de backup azure, como restaurar. Estas operações de gestão são controladas através do Controlo de Acesso baseado em Funções (RBAC).

Para mais informações, consulte use o [Controlo de Acesso baseado em funções para gerir os pontos](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)de recuperação de backup do Azure .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="for-soft-delete"></a>Para eliminar soft

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Preciso de ativar a funcionalidade de eliminação suave em todos os cofres?

Não, é construído e habilitado por padrão para todos os cofres dos serviços de recuperação.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>Posso configurar o número de dias para os quais os meus dados serão retidos em estado de eliminação suave após a eliminação da operação?

Não, é fixado a 14 dias de retenção adicional após a operação de eliminação.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Preciso pagar o custo por esta retenção adicional de 14 dias?

Não, esta retenção adicional de 14 dias vem gratuitamente de custos como parte da funcionalidade soft-delete.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Posso executar uma operação de restauro quando os meus dados estão em estado de eliminação suave?

Não, é necessário desapagar o recurso suave apagado para restaurar. A operação de exclusão trará o recurso de volta para a **proteção Stop com o estado** de retenção onde pode restaurar a qualquer momento. O colecionador de lixo continua em pausa neste estado.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>As minhas fotos seguirão o mesmo ciclo de vida que os meus pontos de recuperação no cofre?

Sim.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Como posso voltar a ativar as cópias de segurança programadas para um recurso apagado?

A eliminação seguida de retoma irá proteger novamente o recurso. Retomar a operação associa uma política de backup para desencadear as cópias de segurança programadas com o período de retenção selecionado. Além disso, o coletor de lixo funciona assim que a operação de retoma estiver concluída. Se desejar efetuar um restauro a partir de um ponto de recuperação que já ultrapasse a data de validade, é aconselhável fazê-lo antes de desencadear a operação de retoma.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Posso apagar o meu cofre se houver itens apagados macios no cofre?

O cofre dos Serviços de Recuperação não pode ser apagado se houver itens de reserva em estado de eliminação suave no cofre. Os artigos apagados são eliminados permanentemente 14 dias após a eliminação da operação. Se não puder esperar 14 dias, [desative suavemente,](#disabling-soft-delete)desapague os itens apagados macios e elimine-os novamente para ser eliminado permanentemente. Depois de garantir que não existem itens protegidos e não há itens apagados macios, o cofre pode ser apagado.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Posso apagar os dados mais cedo do que os 14 dias de eliminação suave após a eliminação?

Não. Não é possível forçar a eliminação dos itens apagados, estes são automaticamente apagados após 14 dias. Esta funcionalidade de segurança está ativada para salvaguardar os dados de back-up de exclusões acidentais ou maliciosas.  Deve esperar 14 dias antes de realizar qualquer outra ação no VM.  Os itens apagados não serão carregados.  Se precisar de reproteger os VMs marcados para apagar suavemente dentro de 14 dias para um novo cofre, contacte o suporte da Microsoft.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>As operações de eliminação suave podem ser realizadas em PowerShell ou CLI?

As operações de eliminação suave podem ser efetuadas através da [Powershell](#soft-delete-for-vms-using-azure-powershell). Atualmente, o CLI não é suportado.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>O soft delete é suportado para outras cargas de trabalho em nuvem, como o SQL Server em VMs Azure e SAP HANA em VMs Azure?

Não. Atualmente, o soft delete é suportado apenas para máquinas virtuais Azure.

## <a name="next-steps"></a>Passos Seguintes

- Leia sobre os controlos de [segurança para o Backup Azure](backup-security-controls.md).
