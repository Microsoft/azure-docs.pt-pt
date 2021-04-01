---
title: FAQ sobre como Fazer Cópias de Segurança de Ficheiros do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre como proteger as suas partilhas de ficheiros Azure com o serviço de Backup Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: e2b6afb25e189ee2848f25c0ba59d843baf37090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91940840"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre a cópia de segurança de Ficheiros do Azure

Este artigo responde a questões comuns sobre a cópia de segurança de Ficheiros do Azure. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço Azure Backup na [página de perguntas do Microsoft Q&Uma página de perguntas para discussão](/answers/topics/azure-backup.html).

Para analisar rapidamente as secções neste artigo, utilize as ligações à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurar a tarefa de cópia de segurança dos Ficheiros do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Por que não posso ver algumas das minhas contas de armazenamento que quero proteger, que contêm ações válidas do ficheiro Azure?

Consulte a [Matriz de Suporte para a Azure para garantir](azure-file-share-support-matrix.md) que a conta de armazenamento pertence a um dos tipos de conta de armazenamento suportado. Também é possível que a Conta de Armazenamento que procura já esteja protegida ou registada noutro Cofre. [Desagregar a conta](manage-afs-backup.md#unregister-a-storage-account) de armazenamento do cofre para descobrir a Conta de Armazenamento em outros cofres para proteção.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Por que motivo não vejo algumas das minhas partilhas de ficheiros do Azure na Conta de Armazenamento quando estou a tentar configurar a cópia de segurança?

Verifique se a partilha de ficheiros do Azure já está protegida no mesmo cofre dos Serviços de Recuperação ou se foi eliminada recentemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Posso proteger as Partilhas de Ficheiros ligadas a um Grupo de Sincronização no Azure File Sync?

Sim. Está ativada a proteção das ações de ficheiros Azure ligadas a Grupos de Sincronização.

### <a name="when-trying-to-back-up-file-shares-i-selected-a-storage-account-to-discover-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Ao tentar fazer o back up de ações de ficheiros, selecionei uma Conta de Armazenamento para descobrir as ações de ficheiros nela. No entanto, não os protegi. Como posso proteger estas ações com qualquer outro cofre?

Ao tentar fazer o back up, selecionar uma Conta de Armazenamento para descobrir ações de ficheiros no seu interior regista a Conta de Armazenamento com o cofre a partir do qual isto é feito. Se optar por proteger as partilhas de ficheiros com um cofre diferente, [não registe](manage-afs-backup.md#unregister-a-storage-account) a Conta de Armazenamento escolhida a partir deste cofre.

### <a name="why-cant-i-change-the-vault-to-configure-backup-for-the-file-share"></a>Por que não posso mudar o cofre para configurar a cópia de segurança para a partilha de ficheiros?

Se a conta de armazenamento já estiver registada com um cofre ou outras ações de ficheiro na conta de armazenamento estão protegidas através de um cofre, não lhe é dada a opção de alterá-la. Todas as ações de ficheiros numa conta de armazenamento só podem ser protegidas pelo mesmo cofre. Se quiser mudar o cofre, terá de parar a [proteção de todas as ações](manage-afs-backup.md#stop-protection-on-a-file-share) de ficheiros na conta de armazenamento do cofre conectado, [desagregar](manage-afs-backup.md#unregister-a-storage-account) a Conta de Armazenamento e, em seguida, escolher um cofre diferente para proteção.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Posso mudar o Cofre para o qual apoio as minhas ações?

Sim. No entanto, terá de parar a [proteção da parte do ficheiro](manage-afs-backup.md#stop-protection-on-a-file-share) do cofre conectado, de não [registar](manage-afs-backup.md#unregister-a-storage-account) esta Conta de Armazenamento e, em seguida, protegê-la de um cofre diferente.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Posso proteger duas partilhas de ficheiros diferentes da mesma Conta de Armazenamento em cofres diferentes?

N.º As partilhas de ficheiros numa Conta de Armazenamento só podem ser protegidas pelo mesmo Cofre.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>O que devo fazer se os meus backups começarem a falhar devido ao limite máximo atingido?

Pode ter até 200 Instantâneos para uma partilha de ficheiros em qualquer altura. O limite inclui instantâneos tirados pelo Azure Backup, conforme definido pela sua política. Se as suas cópias de segurança começarem a falhar depois de atingirem o limite, elimine as imagens On-Demand para futuras cópias de segurança bem sucedidas.

## <a name="restore"></a>Restauro

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Posso recuperar uma partilha de ficheiros do Azure eliminada?

Se a partilha de ficheiros estiver no estado de apagamento suave, é necessário desembolsar primeiro a parte do ficheiro para executar a operação de restauro. A operação undelete trará a partilha de ficheiros para o estado ativo onde você pode restaurar a qualquer ponto no tempo. Para aprender a desdelear a sua partilha de ficheiros, visite [este link](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share) ou veja o Script de Partilha [de Ficheiros Undelete](./scripts/backup-powershell-script-undelete-file-share.md). Se a partilha de ficheiros for permanentemente eliminada, não poderá restaurar o conteúdo e as imagens.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Posso restaurar a partir de cópias de segurança se tiver parado a proteção numa partilha de ficheiros do Azure?

Sim. Se tiver escolhido **Reter Dados de Cópia de Segurança** quando parou a proteção, poderá restaurar a partir de todos os pontos de restauro existentes.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se eu cancelar um trabalho de restauro?

Se uma função de restauro em curso for cancelada, o processo de restauro para e todos os ficheiros restaurados antes do cancelamento, permaneçam no destino configurado (localização original ou alternativa) sem qualquer reversão.

## <a name="manage-backup"></a>Gerir backup

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Posso utilizar o PowerShell para configurar/gerir/restaurar as cópias de segurança das ações do Ficheiro Azure?

Sim. Consulte a documentação detalhada [aqui.](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Posso aceder às fotos tiradas pelo Azure Backups e montá-las?

Todas as imagens tiradas pelo Azure Backup podem ser acedidas através da visualização de imagens no portal, PowerShell ou CLI. Para saber mais sobre os Ficheiros Azure, consulte [a visão geral das fotos de partilha para ficheiros Azure](../storage/files/storage-snapshots-files.md).

### <a name="what-happens-after-i-move-a-backed-up-file-share-to-a-different-subscription"></a>O que acontece depois de mudar uma parte de ficheiro suspensa para uma subscrição diferente?

Uma vez que uma partilha de ficheiros é transferida para uma subscrição diferente, é considerada como uma nova partilha de ficheiros pela Azure Backup. Estes são os passos recomendados:
 
Cenário: Digamos que tem uma partilha de *ficheiros FS1* na subscrição *S1* e está protegido usando o cofre *V1.* Agora quer mover a sua parte do ficheiro para a subscrição *S2*.
 
1.  Mover a conta de armazenamento e a partilha de ficheiros desejadas (FS1) para a subscrição diferente (S2).
2.  No cofre V1, desencadeie a proteção de paragem com a eliminação da operação de dados para FS1.
3.  Desagregar a conta de armazenamento que hospeda FS1 do cofre V1.
4.  Reconfigure a cópia de segurança para o FS1, agora transferida para S2, com um cofre (V2) na assinatura S2. 
 
Note que depois de reconfigurar a cópia de segurança com v2, as imagens que foram tiradas com V1 deixarão de ser geridas pela Azure Backup. Por isso, terá de apagar as fotos manualmente de acordo com os seus requisitos.

### <a name="can-i-move-my-backed-up-file-share-to-a-different-resource-group"></a>Posso mover a minha parte de ficheiros de apoio para um grupo de recursos diferente?
 
Sim, pode mover a sua partilha de ficheiros com apoio para um grupo de recursos diferente. No entanto, terá de reconfigurar a cópia de segurança para a partilha de ficheiros, uma vez que será tratada como um novo recurso pela Azure Backup. Além disso, os instantâneos que foram criados antes do movimento do grupo de recursos deixarão de ser geridos por backup do Azure. Por isso, terá de apagar as fotos manualmente de acordo com os seus requisitos.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual é a retenção máxima que posso configurar para reforços?

Consulte a [matriz de suporte](azure-file-share-support-matrix.md) para obter detalhes sobre a retenção máxima. O Azure Backup faz um cálculo em tempo real do número de instantâneos quando introduz os valores de retenção enquanto configura a política de backup. Assim que o número de instantâneos correspondentes aos valores de retenção definidos exceder 200, o portal apresentará um aviso solicitando-lhe que ajuste os seus valores de retenção. Isto é para que não exceda o limite do número máximo de instantâneos suportados pelos Ficheiros Azure para qualquer partilha de ficheiros em qualquer momento.

### <a name="what-is-the-impact-on-existing-recovery-points-and-snapshots-when-i-modify-the-backup-policy-for-an-azure-file-share-to-switch-from-daily-policy-to-gfs-policy"></a>Qual é o impacto nos pontos de recuperação e instantâneos existentes quando modifico a política de Backup para uma partilha de ficheiros Azure para passar de "Política Diária" para "Política GFS"?

Quando modifica uma política diária de backup para a política GFS (adicionando retenção semanal/mensal/anual), o comportamento é o seguinte:

- **Retenção**: Se adicionar retenção semanal/mensal/anual como parte da modificação da política, todos os futuros pontos de recuperação criados como parte do backup programado serão marcados de acordo com a nova política. Todos os pontos de recuperação existentes continuarão a ser considerados como pontos de recuperação diários e, por isso, não serão marcados como semanais/mensais/ano.

- **Snapshots e limpeza de pontos de recuperação:**

  - Se a retenção diária for prolongada, a data de validade dos pontos de recuperação existentes é atualizada de acordo com o valor diário de retenção configurado na nova política.
  - Se a retenção diária for reduzida, os pontos de recuperação e instantâneos existentes são marcados para eliminação no próximo trabalho de limpeza, de acordo com o valor de retenção diário configurado na nova política, e depois eliminados.

Aqui está um exemplo de como isto funciona:

#### <a name="existing-policy-p1"></a>Política existente [P1]

|Tipo de Retenção |Agenda |Retenção  |
|---------|---------|---------|
|Diário    |    Todos os dias às 20h    |  100 dias       |

#### <a name="new-policy-modified-p1"></a>Nova Política [P1 modificado]

| Tipo de Retenção | Agenda                       | Retenção |
| -------------- | ------------------------------ | --------- |
| Diário          | Todos os dias às 21h              | 50 dias   |
| Semanal         | No domingo, às 21h00              | 3 semanas   |
| Mensalmente        | Na última segunda-feira às 21h         | 1 mês   |
| Anualmente         | Em jan no terceiro domingo às 21h | 4 anos   |

#### <a name="impact"></a>Impacto

1. A data de validade dos pontos de recuperação existentes será ajustada de acordo com o valor diário de retenção da nova política: ou seja, 50 dias. Então qualquer ponto de recuperação com mais de 50 dias será marcado para a eliminação.

2. Os pontos de recuperação existentes não serão marcados como semanais/mensais/ano com base em novas políticas.

3. Todos os futuros backups serão desencadeados de acordo com o novo horário: isto é, às 21h.

4. A data de validade de todos os futuros pontos de recuperação estará alinhada com a nova política.

>[!NOTE]
>As mudanças de política afetarão apenas os pontos de recuperação criados como parte da execução de trabalho de backup programada. Para cópias de segurança a pedido, a retenção é determinada pelo valor **Retenuso Till** especificado no momento da tomada de backup.

### <a name="what-is-the-impact-on-existing-recovery-points-when-i-modify-an-existing-gfs-policy"></a>Qual é o impacto nos pontos de recuperação existentes quando modifico uma política de GFS existente?

Quando uma nova política é aplicada em ações de ficheiros, todas as futuras cópias de segurança programadas serão tomadas de acordo com o calendário configurado na política modificada.  A retenção de todos os pontos de recuperação existentes está alinhada de acordo com os novos valores de retenção configurados. Assim, se a retenção for prolongada, os pontos de recuperação existentes serão marcados para serem mantidos de acordo com a nova política. Se a retenção for reduzida, estão marcadas para limpeza no próximo trabalho de limpeza e depois apagadas.

Aqui está um exemplo de como isto funciona:

#### <a name="existing-policy-p2"></a>Política existente [P2]

| Tipo de Retenção | Agenda           | Retenção |
| -------------- | ------------------ | --------- |
| Diário          | Todos os dias às 20h | 50 dias   |
| Semanal         | Segunda-feira às 20:00  | 3 semanas   |

#### <a name="new-policy-modified-p2"></a>Nova Política [P2 modificado]

| Tipo de Retenção | Agenda               | Retenção |
| -------------- | ---------------------- | --------- |
| Diário          | Todos os dias às 21h     | 10 dias   |
| Semanal         | Segunda-feira às 21h      | 2 semanas   |
| Mensalmente        | Na última segunda-feira às 21h | 2 meses  |

#### <a name="impact-of-change"></a>Impacto da mudança

1. A data de validade dos pontos de recuperação diários existentes será alinhada de acordo com o novo valor diário de retenção, ou seja, 10 dias. Assim, qualquer ponto de recuperação diário com mais de 10 dias será eliminado.

2. A data de validade dos pontos de recuperação semanais existentes será alinhada de acordo com o novo valor semanal de retenção, ou seja, duas semanas. Assim, qualquer ponto de recuperação semanal com mais de duas semanas será eliminado.

3. Os pontos de recuperação mensais só serão criados como parte de futuras cópias de segurança com base na nova configuração da política.

4. A data de validade de todos os futuros pontos de recuperação estará alinhada com a nova política.

>[!NOTE]
>As mudanças de política afetarão apenas os pontos de recuperação criados como parte do backup programado. Para cópias de segurança a pedido, a retenção é determinada pelo valor **Retenuso Till** especificado no momento da tomada do backup.

## <a name="next-steps"></a>Passos seguintes

- [Problemas de resolução de problemas ao mesmo tempo que apoiam as ações de ficheiros da Azure](troubleshoot-azure-files.md)
