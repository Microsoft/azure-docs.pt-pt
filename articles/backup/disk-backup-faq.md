---
title: Perguntas frequentes sobre backup de disco Azure
description: Obtenha respostas para perguntas frequentes sobre backup de disco Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734567"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Perguntas frequentes sobre backup de disco Azure (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade da região, consulte a [matriz de suporte.](disk-backup-support-matrix.md)
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

Este artigo responde frequentemente a perguntas sobre a cópia de segurança do disco Azure. Para obter mais informações sobre a disponibilidade da região de backup do [Disco Azure,](disk-backup-overview.md) cenários e limitações apoiados, consulte a matriz de [suporte](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Posso fazer o backup do disco utilizando a solução de Backup do Disco Azure se o mesmo disco for apoiado utilizando a cópia de segurança da máquina virtual Azure?

O Azure Backup oferece suporte lado a lado para a cópia de segurança do disco gerido utilizando a cópia de segurança do disco e as soluções [de backup Azure VM.](backup-azure-vms-introduction.md) Isto é útil quando você precisa de uma aplicação consistente de cópias de segurança de máquinas virtuais e também cópias de segurança mais frequentes do disco DE ou de um disco de dados específico, que são consistentes sem afetar o desempenho da aplicação de produção.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Como encontro o grupo de recursos instantâneos que usei para configurar a cópia de segurança para um disco?

No ecrã **De exemplo de cópia de segurança,** pode encontrar o campo de grupo de recursos instantâneos na secção **Essentials.** Pode pesquisar e selecionar a sua instância de backup do disco correspondente a partir do backup center ou do cofre de reserva.

![Campo de grupo de recursos instantâneos](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>O que é um grupo de recursos instantâneos?

O Azure Disk Backup oferece cópia de segurança de nível operacional para o disco gerido. Ou seja, os instantâneos que são criados durante as operações de backup programadas e a pedido são armazenados num grupo de recursos dentro da sua subscrição. O Azure Backup oferece restauro instantâneo porque as imagens incrementais são armazenadas dentro da sua subscrição. Este grupo de recursos é conhecido como o grupo de recursos instantâneos. Para mais informações, consulte a cópia de segurança do [Configure.](backup-managed-disks.md#configure-backup)

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Porque é que o grupo de recursos instantâneos deve estar na mesma subscrição que a do disco que está a ser apoiado?

Não é possível criar uma imagem incremental para um disco específico fora da subscrição do disco. Por isso, escolha o grupo de recursos dentro da mesma subscrição que a do disco a ser apoiado. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos geridos.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Por que preciso de fornecer tarefas de papel para poder configurar backups, realizar backups programados e a pedido, e restaurar operações?

O Azure Disk Backup utiliza a abordagem menos privilegiada para descobrir, proteger e restaurar os discos geridos nas suas subscrições. Para isso, o Azure Backup utiliza a identidade gerida do [cofre backup](backup-vault-overview.md) para aceder a outros recursos Azure. Um sistema atribuído à identidade gerida é restrito a um por recurso e está ligado ao ciclo de vida deste recurso. Pode conceder permissões à identidade gerida utilizando o controlo de acesso baseado em funções Azure (Azure RBAC). A identidade gerida é um principal de serviço de um tipo especial que só pode ser usado com recursos Azure. Saiba mais sobre [identidades geridas.](../active-directory/managed-identities-azure-resources/overview.md) Por predefinição, o cofre de backup não terá permissão para aceder ao disco para ser apoiado, criar instantâneos periódicos, eliminar instantâneos após o período de retenção e restaurar um disco a partir de cópia de segurança. Ao conceder explicitamente atribuições de papéis à identidade gerida do cofre de backup, você está no controle de gerir permissões para os recursos nas subscrições.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Porque é que a política de backup limita a duração da retenção?

A Azure Disk Backup utiliza instantâneos incrementais, que estão limitados a 200 instantâneos por disco. Para permitir que você assuma backups a pedido para além de backups programados, a política de backup limita o total de backups para 180. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos geridos.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Como funciona a frequência de apoio de hora a hora e de dia na política de backup?

O Azure Disk Backup oferece várias cópias de segurança por dia. Se necessitar de cópias de segurança mais frequentes, escolha a frequência de backup **hourly.** As cópias de segurança são programadas com base no intervalo **de tempo** selecionado. Por exemplo, se selecionar **De 4 em 4 horas,** as cópias de segurança são tomadas aproximadamente a cada 4 horas para que as cópias de segurança sejam distribuídas igualmente ao longo do dia. Se uma vez por dia a cópia de segurança for suficiente, então escolha a frequência de backup **Diária.** Na frequência de backup diária, pode especificar a hora do dia em que as suas cópias de segurança serão tomadas. É importante notar que a hora do dia indica a hora de início de backup e não a hora em que a cópia de segurança termina. O tempo necessário para completar a operação de backup depende de vários fatores, incluindo a taxa de churn entre backups consecutivos. No entanto, a cópia de segurança do Azure Disk é uma cópia de segurança sem agente que utiliza [imagens incrementais](../virtual-machines/disks-incremental-snapshots.md) que não afetam o desempenho da aplicação de produção.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Porque é que a definição de redundância do cofre de reserva não se aplica às cópias de segurança armazenadas no nível operacional (o grupo de recursos instantâneos)?

O Azure Backup utiliza [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) de discos geridos que armazenam apenas as alterações delta aos discos desde a última imagem no armazenamento standard de HDD, independentemente do tipo de armazenamento do disco-mãe. Para uma maior fiabilidade, os instantâneos incrementais são armazenados em Zone Redundant Storage (ZRS) por padrão em regiões que suportam ZRS. Atualmente, o Azure Disk Backup suporta cópias operacionais de discos geridos que não copiam as cópias de segurança para o armazenamento do cofre de backup. Assim, a definição de redundância de armazenamento de reserva do cofre de reserva não se aplica aos pontos de recuperação.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Posso usar o Backup Center para configurar backups e gerir casos de backup para discos Azure?

Sim, o Azure Disk Backup está integrado no [Backup Center,](backup-center-overview.md)que proporciona uma **única experiência de gestão unificada** em Azure para as empresas governarem, monitorizarem, operarem e analisarem backups em escala. Também pode usar o cofre de reserva para fazer backup, restaurar e gerir as instâncias de backup que estão protegidas dentro do cofre.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Por que preciso criar um cofre de reserva e não usar um cofre dos Serviços de Recuperação?

Um cofre de backup é uma entidade de armazenamento em Azure que abriga dados de backup para certas cargas de trabalho mais recentes que a Azure Backup suporta. Pode utilizar cofres de backup para conter dados de backup para vários serviços Azure, tais como Azure Database para servidores PostgreSQL, Discos Azure e cargas de trabalho mais recentes que o Azure Backup irá suportar. Os cofres de reserva facilitam a organização dos seus dados de backup, minimizando a sobrecarga de gestão. Consulte [os cofres de reserva](./backup-vault-overview.md) para saber mais.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>O disco pode ser apoiado e o cofre de reserva em diferentes subscrições?

Sim, o disco gerido por fontes a ser apoiado e o cofre de backup pode estar em diferentes subscrições.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>O disco pode ser apoiado e o cofre de reserva em diferentes regiões?

Não, atualmente o disco gerido pela fonte deve ser apoiado e o cofre de reserva deve estar na mesma região.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Posso restaurar um disco numa subscrição diferente?

Sim, pode restaurar o disco numa subscrição diferente da do disco gerido por fontes a partir do qual a cópia de segurança é tomada.

### <a name="can-i-back-up-multiple-disks-together"></a>Posso fazer o reforço de vários discos juntos?

Não, imagens pontuais de vários discos ligados a uma máquina virtual não são suportadas. Para obter mais informações, consulte a cópia de segurança do [Configure](backup-managed-disks.md#configure-backup) e para saber mais sobre limitações, consulte a [matriz de suporte](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Quais são as minhas opções para fazer o back up disks em várias subscrições?

Atualmente, a utilização do portal Azure para configurar a cópia de segurança dos discos está limitada a um máximo de 20 discos da mesma subscrição.

### <a name="what-is-a-target-resource-group"></a>O que é um grupo de recursos alvo?

Durante uma operação de restauro, pode escolher a subscrição e um grupo de recursos onde pretende restaurar o disco. O Azure Backup criará novos discos a partir do ponto de recuperação do grupo de recursos selecionado. Isto é referido como um grupo de recursos-alvo. Note que a identidade gerida do cofre de backup requer que a atribuição de funções no grupo de recursos-alvo seja capaz de executar a operação de restauro com sucesso. Para mais informações, consulte a [documentação de restauro.](restore-managed-disks.md)

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Quais são as permissões utilizadas pela Azure Backup durante a operação de backup e restauro?

Seguem-se as ações utilizadas na função **de Leitor de Backup de Disco** atribuído no **disco** a ser apoiado:

"Microsoft.Compute/disks/read"

"Microsoft.Compute/disks/startGetAccess/action"

Seguem-se as ações utilizadas na função **de contribuinte Snapshot de disco** atribuído no grupo de recursos **Snapshot:**

"Microsoft.Compute/snapshots/delete"

"Microsoft.Compute/snapshots/write"

"Microsoft.Compute/snapshots/read"

"Microsoft.Storage/storageAccounts/write"

"Microsoft.Storage/storageAccounts/read"

"Microsoft.Storage/storageAccounts/delete"

"Microsoft.Resources/subscrições/resourceGroups/read"

"Microsoft.Storage/storageAccounts/listkeys/action"

"Microsoft.Compute/snapshots/startGetAccess/action"

"Microsoft.Compute/snapshots/endGetAccess/action"

"Microsoft.Compute/disks/startGetAccess/action"

Seguem-se as ações utilizadas na função **do Operador de Restauro** de Disco atribuído ao Grupo de **Recursos-Alvo:**

"Microsoft.Compute/disks/write"

"Microsoft.Compute/disks/read"

"Microsoft.Resources/subscrições/resourceGroups/read"

>[!NOTE]
>As permissões sobre estas funções podem mudar no futuro, com base nas funcionalidades adicionadas pelo serviço Azure Backup.

## <a name="next-steps"></a>Próximos passos

- [Matriz de suporte da Cópia de Segurança de Discos do Azure](disk-backup-support-matrix.md)