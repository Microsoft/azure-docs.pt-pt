---
title: FAQ sobre como Fazer Cópias de Segurança de Ficheiros do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre como proteger as suas partilhas de ficheiros Azure com o serviço De backup Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: 1be509f3b82cece3afb1e728a19da4c4d9526195
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836112"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre a cópia de segurança de Ficheiros do Azure

Este artigo responde a questões comuns sobre a cópia de segurança de Ficheiros do Azure. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço de backup Azure no Microsoft Q&Uma página de [perguntas para discussão](https://docs.microsoft.com/answers/topics/azure-backup.html).

Para analisar rapidamente as secções neste artigo, utilize as ligações à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurar a tarefa de cópia de segurança dos Ficheiros do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Por que não posso ver algumas das minhas Contas de Armazenamento que quero proteger, que contêm ações válidas de ficheiros Azure?

Consulte a Matriz de [Suporte para o ficheiro Azure partilha backup](azure-file-share-support-matrix.md) para garantir que a conta de armazenamento pertence a um dos tipos de conta de armazenamento suportado. Também é possível que a Conta de Armazenamento que procura já esteja protegida ou registada com outro Cofre. [Desregistre a conta](manage-afs-backup.md#unregister-a-storage-account) de armazenamento do cofre para descobrir a Conta de Armazenamento em outros cofres para proteção.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Por que motivo não vejo algumas das minhas partilhas de ficheiros do Azure na Conta de Armazenamento quando estou a tentar configurar a cópia de segurança?

Verifique se a partilha de ficheiros do Azure já está protegida no mesmo cofre dos Serviços de Recuperação ou se foi eliminada recentemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Posso proteger as Partilhas de Ficheiros ligadas a um Grupo de Sincronização no Azure File Sync?

Sim. A proteção das ações de ficheiros Azure ligadas aos Sync Groups está ativada.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Quando estava a tentar criar cópias de segurança de partilhas de ficheiros, cliquei numa Conta de Armazenamento para ver que partilhas havia na mesma. No entanto, não os protegi. Como protejo estas ações com outro cofre?

Ao tentar fazer o backback, selecionando uma Conta de Armazenamento para descobrir as ações de ficheirodentro dela regista a Conta de Armazenamento com o cofre a partir do qual isso é feito. Se optar por proteger as partilhas de ficheiros com um cofre diferente, [desregilhe](manage-afs-backup.md#unregister-a-storage-account) a Conta de Armazenamento escolhida deste cofre.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Posso mudar o Cofre para o qual apoio as minhas ações de ficheiro?

Sim. No entanto, terá de parar a [proteção na parte](manage-afs-backup.md#stop-protection-on-a-file-share) do ficheiro do cofre conectado, [desregistar](manage-afs-backup.md#unregister-a-storage-account) esta Conta de Armazenamento e, em seguida, protegê-la de um cofre diferente.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Quantas partilhas de ficheiros do Azure posso proteger num Cofre?

Pode proteger as ações de ficheiros Azure de até 50 Contas de Armazenamento por Cofre. Também pode proteger até 200 partilhas de ficheiros do Azure num único cofre.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Posso proteger duas partilhas de ficheiros diferentes da mesma Conta de Armazenamento em cofres diferentes?

Não. As partilhas de ficheiros numa Conta de Armazenamento só podem ser protegidas pelo mesmo Cofre.

## <a name="backup"></a>Cópia de segurança

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>O que devo fazer se os meus reforços começarem a falhar devido ao limite máximo atingido erro?

Pode ter até 200 Instantâneos para uma partilha de ficheiros em qualquer altura. O limite inclui instantâneos tirados pelo Azure Backup, conforme definido pela sua política. Se as suas cópias de segurança começarem a falhar depois de atingirem o limite, apague as imagens on-demand para futuras cópias de segurança bem sucedidas.

## <a name="restore"></a>Restauro

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Posso recuperar uma partilha de ficheiros do Azure eliminada?

Quando uma parte do ficheiro Azure é eliminada, é-lhe mostrada a lista de cópias de segurança que serão eliminadas e uma confirmação é solicitada. Atualmente, uma partilha de ficheiros Azure apagada não pode ser restaurada.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Posso restaurar a partir de cópias de segurança se tiver parado a proteção numa partilha de ficheiros do Azure?

Sim. Se tiver escolhido **Reter Dados de Cópia de Segurança** quando parou a proteção, poderá restaurar a partir de todos os pontos de restauro existentes.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se eu cancelar um trabalho de restauro em curso?

Se um trabalho de restauro em curso for cancelado, o processo de restauro para e todos os ficheiros restaurados antes do cancelamento, permaneça no destino configurado (localização original ou alternativa) sem quaisquer recuos.

## <a name="manage-backup"></a>Gerir backup

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Posso usar a PowerShell para configurar/gerir/restaurar cópias de segurança das ações do Ficheiro Azure?

Sim. Consulte a documentação detalhada [aqui](backup-azure-afs-automation.md).

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Posso aceder às fotos tiradas pelos Backups Azure e montá-las?

Todas as imagens tiradas pelo Azure Backup podem ser acedidas através da visualização de instantâneos no portal, PowerShell ou CLI. Para saber mais sobre o Azure Files partilhar fotos, consulte a [visão geral das imagens de partilha para Ficheiros Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual é a retenção máxima que posso configurar para cópias de segurança?

Consulte a [matriz](azure-file-share-support-matrix.md) de suporte para obter detalhes sobre a máxima retenção. O Azure Backup faz um cálculo em tempo real do número de instantâneos quando introduz os valores de retenção enquanto configura a política de backup. Assim que o número de instantâneos correspondentes aos seus valores de retenção definidos exceder 200, o portal mostrará um aviso solicitando-lhe que ajuste os seus valores de retenção. Isto é para que não exceda o limite máximo de instantâneos suportados pelo Azure Files para qualquer partilha de ficheiros em qualquer momento.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Qual é o impacto nos pontos de recuperação e instantâneos existentes quando modifico a política de backup para uma partilha de ficheiros Azure para passar de "Daily Policy" para "GFS Policy"?

Quando modifica uma política de backup diária à política gfs (adicionando retenção semanal/mensal/anual), o comportamento é o seguinte:

- **Retenção**: Se estiver a adicionar retenção semanal/mensal/anual como parte da modificação da política, todos os futuros pontos de recuperação criados como parte do backup programado serão marcados de acordo com a nova política. Todos os pontos de recuperação existentes continuarão a ser considerados como pontos de recuperação diários e, por isso, não serão marcados como semanal/mensalmente/ano.

- **Limpeza de snapshots e pontos de recuperação:**

  - Se a retenção diária for prolongada, a data de validade dos pontos de recuperação existentes é atualizada de acordo com o valor diário de retenção configurado na nova política.
  - Se a retenção diária for reduzida, os pontos de recuperação e instantâneos existentes são marcados para eliminação no próximo trabalho de limpeza, de acordo com o valor diário de retenção configurado na nova política, e depois eliminados.

Aqui está um exemplo de como isto funciona:

#### <a name="existing-policy-p1"></a>Política existente [P1]

|Tipo de retenção |Agenda |Retenção  |
|---------|---------|---------|
|Diário    |    Todos os dias às 20h    |  100 dias       |

#### <a name="new-policy-modified-p1"></a>Nova Política [P1 modificado]

| Tipo de retenção | Agenda                       | Retenção |
| -------------- | ------------------------------ | --------- |
| Diário          | Todos os dias às 21:00              | 50 dias   |
| Semanal         | No domingo, às 21h00.              | 3 semanas   |
| Mensalmente        | Na segunda-feira passada às 21h         | 1 mês   |
| Anual         | Em jan no terceiro domingo às 21:00 | 4 anos   |

#### <a name="impact"></a>Impacto

1. A data de validade dos pontos de recuperação existentes será ajustada de acordo com o valor diário de retenção da nova política: ou seja, 50 dias. Qualquer ponto de recuperação com mais de 50 dias será marcado para eliminação.

2. Os pontos de recuperação existentes não serão marcados como semanal/mensal/anualmente com base em novas políticas.

3. Todos os futuros backups serão desencadeados de acordo com a nova programação: isto é, às 21h.

4. A data de validade de todos os futuros pontos de recuperação estará alinhada com a nova política.

>[!NOTE]
>As mudanças de política afetarão apenas os pontos de recuperação criados no âmbito da execução programada de trabalho de backup. Para cópias de segurança a pedido, a retenção é determinada pelo **valor de retenção até** ao valor especificado no momento da obtenção de cópias de segurança.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Qual é o impacto nos pontos de recuperação existentes quando modifico uma política de GFS existente?

Quando uma nova política for aplicada em ações de ficheiros, todas as futuras cópias de segurança programadas serão tomadas de acordo com o calendário configurado na política modificada.  A retenção de todos os pontos de recuperação existentes está alinhada de acordo com os novos valores de retenção configurados. Assim, se a retenção for alargada, os pontos de recuperação existentes serão marcados para serem mantidos de acordo com a nova política. Se a retenção for reduzida, estão marcadas para limpeza no próximo trabalho de limpeza e depois eliminadas.

Aqui está um exemplo de como isto funciona:

#### <a name="existing-policy-p2"></a>Política existente [P2]

| Tipo de retenção | Agenda           | Retenção |
| -------------- | ------------------ | --------- |
| Diário          | Todos os dias às 20h | 50 dias   |
| Semanal         | Na segunda-feira às 20:00  | 3 semanas   |

#### <a name="new-policy-modified-p2"></a>Nova Política [P2 modificado]

| Tipo de retenção | Agenda               | Retenção |
| -------------- | ---------------------- | --------- |
| Diário          | Todos os dias às 21:00     | 10 dias   |
| Semanal         | Na segunda-feira às 21:00      | 2 semanas   |
| Mensalmente        | Na segunda-feira passada às 21h | 2 meses  |

#### <a name="impact-of-change"></a>Impacto da mudança

1. A data de validade dos pontos de recuperação diárias existentes será alinhada de acordo com o novo valor de retenção diária, ou seja, 10 dias. Assim, qualquer ponto de recuperação diária com mais de 10 dias será eliminado.

2. A data de validade dos pontos de recuperação semanais existentes será alinhada de acordo com o novo valor semanal de retenção, ou seja, duas semanas. Assim, qualquer ponto de recuperação semanal com mais de duas semanas será eliminado.

3. Os pontos de recuperação mensais só serão criados como parte de futuras cópias de segurança com base na nova configuração de políticas.

4. A data de validade de todos os futuros pontos de recuperação estará alinhada com a nova política.

>[!NOTE]
>As mudanças de política afetarão apenas os pontos de recuperação criados como parte do backup programado. Para cópias de segurança a pedido, a retenção é determinada pelo **valor de retenção até** ao valor especificado no momento da tomada de reserva.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outras áreas do Azure Backup, consulte algumas destas outras FAQs de backup:

- [FAQ sobre o cofre dos Serviços de Recuperação](backup-azure-backup-faq.md)
- [FAQ sobre as cópias de segurança de VMs do Azure](backup-azure-vm-backup-faq.md)
- [FAQ sobre o agente do Azure Backup](backup-azure-file-folder-backup-faq.md)
