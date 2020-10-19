---
title: FAQ - Backup Up Azure VMs
description: Neste artigo, descubra respostas a perguntas comuns sobre o backup dos VMs Azure com o serviço Azure Backup.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: f318d785fdfa5b72050bdd805ecfe801d307b9a7
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172834"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Perguntas frequentes-Back up Azure VMs

Este artigo responde a perguntas comuns sobre o backup dos VMs Azure com o serviço [Azure Backup.](./backup-overview.md)

## <a name="backup"></a>Cópia de segurança

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Que imagens VM podem ser ativadas para backup quando as crio?

Quando criar um VM, pode ativar a cópia de segurança para VMs que [executam sistemas operativos suportados.](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="why-initial-backup-is-taking-lot-of-time-to-complete"></a>Por que motivo a conclusão da cópia de segurança inicial está a demorar muito tempo?

A cópia de segurança inicial é sempre uma cópia de segurança completa e dependerá do tamanho dos dados e quando a cópia de segurança for processada. <br>
Para melhorar o desempenho de backup ver, [backup boas práticas;](./backup-azure-vms-introduction.md#best-practices) [Considerações de backup](./backup-azure-vms-introduction.md#backup-and-restore-considerations) e [desempenho de backup](./backup-azure-vms-introduction.md#backup-performance)<br>
Apesar de o tempo total das cópias de segurança incrementais ser inferior a 24 horas, este poderá não ser o caso para a primeira cópia de segurança.

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>O custo de reserva está incluído no custo do VM?

Não. Os custos de backup são separados dos custos de um VM. Saiba mais sobre [os preços de backup da Azure.](https://azure.microsoft.com/pricing/details/backup/)

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Que permissões são necessárias para permitir a cópia de segurança para um VM?

Se for um contribuinte VM, pode ativar a cópia de segurança no VM. Se estiver a utilizar uma função personalizada, necessita das seguintes permissões para ativar a cópia de segurança no VM:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionInt/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Se o cofre dos Serviços de Recuperação e o VM tiverem diferentes grupos de recursos, certifique-se de que tem permissões de escrita no grupo de recursos para o cofre dos Serviços de Recuperação.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Um trabalho de reserva a pedido usa o mesmo horário de retenção que os backups programados?

Não. Especifique o intervalo de retenção para um trabalho de backup a pedido. Por padrão, é retido por 30 dias quando disparado do portal.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Ativei recentemente o Azure Disk Encryption em algumas VMs. As minhas cópias de segurança vão continuar a funcionar?

Forneça permissões para que o Azure Backup aceda ao Cofre de Chaves. Especificar as permissões no PowerShell tal como descrito na secção **de backup Enable** na documentação [Azure Backup PowerShell.](backup-azure-vms-automation.md)

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Emigrei discos VM para discos geridos. As minhas cópias de segurança vão continuar a funcionar?

Sim, os reforços funcionam perfeitamente. Não há necessidade de reconfigurar nada.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Por que motivo não vejo a minha VM no assistente de Configuração de Cópias de Segurança?

O feiticeiro só lista VMs na mesma região que o cofre, e isso já não está a ser apoiado.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>O meu VM está desligado. Um trabalho de reserva ou de reserva?

Sim. As cópias de segurança funcionam quando uma máquina é desligada. O ponto de recuperação é marcado como consistente acidente.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Posso cancelar uma tarefa de cópia de segurança em curso?

Sim. Pode cancelar o trabalho de reserva num estado **de tomada de fotos.** Não pode cancelar um emprego se a transferência de dados do instantâneo estiver em andamento.

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Consegui bloquear o grupo de recursos criado pelo Azure Backup Service (por exemplo, `AzureBackupRG_<geo>_<number>` ). As minhas cópias de segurança vão continuar a funcionar?

Se bloquear o grupo de recursos criado pelo Serviço de Backup Azure, as cópias de segurança começarão a falhar, uma vez que existe um limite máximo de 18 pontos de restauro.

Retire a fechadura e limpe a recolha do ponto de restauro desse grupo de recursos para que as futuras cópias de segurança seja bem sucedida. [Siga estes passos](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) para remover a recolha do ponto de restauro.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>O Azure Backup suporta discos geridos por SSD padrão?

Sim, o Azure Backup suporta [discos geridos SSD padrão.](https://docs.microsoft.com/azure/virtual-machines/disks-types#standard-ssd)

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Podemos fazer um VM com um disco ativado por Write Accelerator (WA)?

As imagens não podem ser tiradas no disco ativado pela WA. No entanto, o serviço de Backup Azure pode excluir o disco ativado por WA da cópia de segurança.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Tenho um VM com discos write Accelerator (WA) e SAP HANA instalados. Como é que eu recuo?

O Azure Backup não pode fazer backup do disco ativado pela WA, mas pode excluí-lo da cópia de segurança. No entanto, a cópia de segurança não fornecerá consistência na base de dados porque a informação no disco ativado pela WA não é apoiada. Pode fazer backup de discos com esta configuração se quiser a cópia de segurança do disco do sistema operativo e a cópia de segurança dos discos que não estão ativados por WA.

O Azure Backup fornece uma solução de backup de streaming para bases de dados SAP HANA com um RPO de 15 minutos. É backint certificado pela SAP para fornecer um suporte de reserva nativo aproveitando as APIs nativas da SAP HANA. Saiba mais [sobre o backup das bases de dados SAP HANA em VMs Azure](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Qual é o atraso máximo que posso esperar na hora de início de reserva a partir do tempo de reserva programado que estarei na minha política de backup VM?

A cópia de segurança programada será acionada dentro de 2 horas da hora de reserva programada. Por exemplo, se 100 VMs têm a sua hora de início de reserva agendada para as 2:00 da manhã, então às 4:00 da manhã, o mais tardar, todos os 100 VMs terão o seu trabalho de reserva em andamento. Se as cópias de segurança programadas tiverem sido interrompidas devido a uma paragem e retomadas ou novamente experimentadas, então a cópia de segurança pode começar fora desta janela programada de duas horas.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Qual é o intervalo mínimo permitido de retenção para um ponto de reserva diário?

A política de backup da Azure Virtual Machine suporta um intervalo mínimo de retenção de sete dias até 9999 dias. Qualquer modificação a uma política de backup VM existente com menos de sete dias exigirá uma atualização para cumprir o intervalo mínimo de retenção de sete dias.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>O que acontece se eu mudar o caso do nome do meu VM ou do meu grupo de recursos VM?

Se alterar a caixa (para cima ou para baixo) do seu grupo de recursos VM ou VM, o caso do nome do item de cópia de segurança não se alterará. No entanto, espera-se este comportamento de Reserva Azure. A mudança de caso não aparecerá no item de backup, mas é atualizada no backend.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Posso fazer o back up ou restaurar discos seletivos ligados a um VM?

O Azure Backup suporta agora a cópia de segurança seletiva do disco e restaura utilizando a solução de backup da Máquina Virtual Azure. Para obter mais informações, consulte [a cópia de segurança seletiva do disco e restaure para VMs Azure](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>As identidades geridas são preservadas se uma mudança de inquilino ocorrer durante o backup?

Se [ocorrerem alterações](/azure/devops/organizations/accounts/change-azure-ad-connection) no inquilino, é obrigado a desativar e a reativar [identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) para fazer com que as cópias de segurança voltem a funcionar.

## <a name="restore"></a>Restauro

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Como decido se restabelece apenas discos ou um VM completo?

Pense numa restauração VM como uma opção de criação rápida para um Azure VM. Esta opção altera nomes de discos, contentores utilizados pelos discos, endereços IP públicos e nomes de interface de rede. A mudança mantém recursos únicos quando um VM é criado. O VM não é adicionado a um conjunto de disponibilidade.

Pode utilizar a opção de disco de restauro se quiser:

- Personalize o VM que é criado. Por exemplo, mude o tamanho.
- Adicione definições de configuração que não estavam lá no momento da cópia de segurança.
- Controle a convenção de nomeação de recursos que são criados.
- Adicione o VM a um conjunto de disponibilidade.
- Adicione qualquer outro ajuste que tenha de ser configurado utilizando o PowerShell ou um modelo.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Posso restaurar cópias de segurança de discos VM não geridos depois de atualizar para discos geridos?

Sim, pode usar cópias de segurança tiradas antes de os discos serem migrados de não geridos para geridos.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Como posso restaurar uma VM para um ponto de restauro antes de a VM ter sido migrada para discos geridos?

O processo de restauro permanece o mesmo. Se o ponto de recuperação for de um ponto no tempo em que a VM tinha discos não geridos, pode [restaurar os discos como não geridos](tutorial-restore-disk.md#unmanaged-disks-restore). Se o VM tivesse gerido discos, então pode [restaurar os discos como discos geridos](tutorial-restore-disk.md#managed-disk-restore). Em seguida, pode [criar um VM a partir desses discos.](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk)

[Saiba mais](backup-azure-vms-automation.md#restore-an-azure-vm) sobre fazer isto na PowerShell.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Se o restauro não criar o VM, o que acontece com os discos incluídos no restauro?

No caso de uma restauração gerida do VM, mesmo que a criação de VM falhe, os discos continuarão a ser restaurados.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Posso restaurar um VM que foi apagado?

Sim. Mesmo que apague o VM, pode ir ao item de backup correspondente no cofre e restaurar a partir de um ponto de recuperação.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Como posso restaurar um VM para os mesmos conjuntos de disponibilidade?

Para VMs Azure de Disco Gerido, restaurar os conjuntos de disponibilidade é ativado fornecendo uma opção no modelo enquanto restaura como discos geridos. Este modelo tem o parâmetro de entrada chamado **Availability sets**.

### <a name="how-do-we-get-faster-restore-performances"></a>Como é que vamos obter performances de restauro mais rápidas?

[A](backup-instant-restore-capability.md) capacidade de restauro instantâneo ajuda com cópias de segurança mais rápidas e restauros instantâneos a partir dos instantâneos.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>O que acontece quando mudamos as definições do cofre para o VM encriptado?

Depois de alterar as definições do cofre de chaves para o VM encriptado, as cópias de segurança continuarão a funcionar com o novo conjunto de detalhes. No entanto, após o restauro de um ponto de recuperação antes da mudança, terá que restaurar os segredos num cofre chave antes de poder criar o VM a partir dele. Para mais informações, consulte este [artigo.](./backup-azure-restore-key-secret.md)

Operações como o secret/key roll-over não requerem este passo e o mesmo cofre chave pode ser usado após a restauração.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Posso aceder ao VM uma vez restaurado devido a um VM ter quebrado a relação com o controlador de domínio?

Sim, acede ao VM uma vez restaurado devido a um VM ter quebrado a relação com o controlador de domínio. Para mais informações, consulte este [artigo](./backup-azure-arm-restore-vms.md#post-restore-steps)

### <a name="why-restore-operation-is-taking-long-time-to-complete"></a>Por que motivo a conclusão da operação de restauro está a demorar muito tempo?

O tempo total de restauro depende das operações de Entrada/saída por segundo (IOPS) e do rendimento da conta de armazenamento. O tempo total de restauro pode ser afetado se a conta de armazenamento do alvo for carregada com outras operações de leitura e escrita de aplicações. Para melhorar o funcionamento da restauração, selecione uma conta de armazenamento que não esteja carregada com outros dados da aplicação.

## <a name="manage-vm-backups"></a>Gerir cópias de segurança de VMs

### <a name="what-happens-if-i-modify-a-backup-policy"></a>O que acontece se eu modificar uma política de reserva?

O VM é apoiado utilizando as definições de programação e retenção na política modificada ou nova.

- Se a retenção for alargada, os pontos de recuperação existentes são marcados e mantidos de acordo com a nova política.
- Se a retenção for reduzida, os pontos de recuperação são marcados para poda no próximo trabalho de limpeza e subsequentemente eliminados.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Como posso mover um VM apoiado pela Azure Backup para um grupo de recursos diferente?

1. Pare temporariamente a cópia de segurança e retenha os dados de backup.
2. Para mover máquinas virtuais configuradas com backup Azure, faça os seguintes passos:

   1. Encontre a localização da sua máquina virtual.
   2. Encontre um grupo de recursos com o seguinte padrão de nomeação: `AzureBackupRG_<location of your VM>_1` . Por exemplo, *AzureBackupRG_westus2_1*
   3. No portal Azure, verifique **os tipos ocultos do Show**.
   4. Encontre o recurso com o tipo **Microsoft.Compute/restorePointCollections** que tenha o padrão de nomeação `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Elimine este recurso. Esta operação elimina apenas os pontos de recuperação instantâneos, não os dados de retenção no cofre.
   6. Depois de concluída a operação de eliminação, pode mover a sua máquina virtual.

3. Mova o VM para o grupo de recursos-alvo.
4. Retoma o apoio.

Pode restaurar o VM dos pontos de restauro disponíveis que foram criados antes da operação de movimento.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>O que acontece depois de mudar um VM para um grupo de recursos diferente?

Uma vez que um VM é transferido para um grupo de recursos diferente, é um novo VM no que diz respeito a Azure Backup.

Depois de mover o VM para um novo grupo de recursos, pode reprotegir o VM no mesmo cofre ou num cofre diferente. Uma vez que este é um novo VM para Azure Backup, você será cobrado separadamente.

Os antigos pontos de restauro do VM estarão disponíveis para restauro, se necessário. Se não precisar destes dados de backup, pode parar de proteger os seus antigos VM com dados de eliminação.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Existe um limite para o número de VMs que pode ser associado à mesma política de backup?

Sim, há um limite de 100 VMs que podem ser associados à mesma política de backup do portal. Recomendamos que para mais de 100 VMs, crie várias políticas de backup com o mesmo horário ou horário diferente.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Como posso ver as definições de retenção para os meus backups?

Atualmente, pode ver as definições de retenção a um nível de backup item (VM) com base na política de backup que é atribuída ao VM.

Uma forma de visualizar as definições de retenção para as suas cópias de segurança é navegar para o [painel de instrumentos](./backup-azure-manage-vms.md#view-vms-on-the-dashboard) de reserva para o seu VM, no portal Azure. A seleção do link para a sua política de backup ajuda-o a visualizar a duração de retenção de todos os pontos de retenção diários, semanais, mensais e anualmente associados ao VM.

Também pode utilizar [o Backup Explorer](./monitor-azure-backup-with-backup-explorer.md) para visualizar as definições de retenção de todos os seus VMs dentro de um único painel de vidro. Navegue para o Backup Explorer a partir de qualquer cofre dos Serviços de Recuperação, vá ao separador **Itens de Reserva** e selecione a Vista Avançada para ver informações detalhadas de retenção para cada VM.