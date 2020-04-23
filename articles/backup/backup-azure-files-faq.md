---
title: FAQ sobre como Fazer Cópias de Segurança de Ficheiros do Azure
description: Neste artigo, descubra respostas a perguntas comuns sobre como proteger as suas partilhas de ficheiros Azure com o serviço De backup Azure.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: b89e4a55bc8bc4ef2f4cdb50059537fe7708b6a6
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101166"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre a cópia de segurança de Ficheiros do Azure

Este artigo responde a questões comuns sobre a cópia de segurança de Ficheiros do Azure. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

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

Todas as imagens tiradas pelo Azure Backup podem ser acedidas através da visualização de instantâneos no portal, PowerShell ou CLI. Para saber mais sobre os instantâneos de partilha de Ficheiros do Azure, veja [Descrição geral de instantâneos de partilha de Ficheiros do Azure (pré-visualização)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Qual é a retenção máxima que posso configurar para cópias de segurança?

Consulte a [matriz](azure-file-share-support-matrix.md) de suporte para obter detalhes sobre a máxima retenção. O Azure Backup faz um cálculo em tempo real do número de instantâneos quando introduz os valores de retenção enquanto configura a política de backup. Assim que o número de instantâneos correspondentes aos seus valores de retenção definidos exceder 200, o portal mostrará um aviso solicitando-lhe que ajuste os seus valores de retenção. Isto é para que não exceda o limite máximo de instantâneos suportados pelo Azure Files para qualquer partilha de ficheiros em qualquer momento.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>O que acontece quando altero a Política de cópias de segurança de uma partilha de ficheiros do Azure?

Quando é aplicada uma política nova a uma ou mais partilhas de ficheiros, a agenda e a retenção da política nova são seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para que estejam em conformidade com a política nova. Se for reduzida, são marcados para eliminação no trabalho de limpeza seguinte.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre outras áreas do Azure Backup, consulte algumas destas outras FAQs de backup:

- [FAQ sobre o cofre dos Serviços de Recuperação](backup-azure-backup-faq.md)
- [FAQ sobre as cópias de segurança de VMs do Azure](backup-azure-vm-backup-faq.md)
- [FAQ sobre o agente do Azure Backup](backup-azure-file-folder-backup-faq.md)
