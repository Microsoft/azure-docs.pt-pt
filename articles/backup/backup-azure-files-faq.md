---
title: FAQ sobre como Fazer Cópias de Segurança de Ficheiros do Azure
description: Este artigo dá detalhes sobre como proteger as partilhas de ficheiros do Azure.
services: backup
author: dcurwin
ms.author: dacurwin
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 0c9e68a14aa18726a751709e99953d494c62308e
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275531"
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre a cópia de segurança de Ficheiros do Azure
Este artigo responde a questões comuns sobre a cópia de segurança de Ficheiros do Azure. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para analisar rapidamente as secções neste artigo, utilize as ligações à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurar a tarefa de cópia de segurança dos Ficheiros do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Por que motivo não vejo algumas das minhas Contas de Armazenamento que quero proteger, que contêm partilhas de ficheiros do Azure válidas? <br/>
Durante a pré-visualização, a Cópia de Segurança das Partilhas de ficheiros do Azure não suporta todos os tipos de Contas de Armazenamento. Consulte a lista [aqui](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) para ver a lista das contas de armazenamento suportadas. Também é possível que a Conta de Armazenamento que procura já esteja protegida ou registada noutro Cofre. [Anular o registo](troubleshoot-azure-files.md#configuring-backup) do cofre para detetar a Conta de Armazenamento noutros Cofres para proteção.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Por que motivo não vejo algumas das minhas partilhas de ficheiros do Azure na Conta de Armazenamento quando estou a tentar configurar a cópia de segurança? <br/>
Verifique se a partilha de ficheiros do Azure já está protegida no mesmo cofre dos Serviços de Recuperação ou se foi eliminada recentemente.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Posso proteger as Partilhas de Ficheiros ligadas a um Grupo de Sincronização no Azure File Sync? <br/>
Sim. A proteção das Partilhas de Ficheiros do Azure ligadas a Grupos de Sincronização está ativada e faz parte da Pré-visualização pública.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Quando estava a tentar criar cópias de segurança de partilhas de ficheiros, cliquei numa Conta de Armazenamento para ver que partilhas havia na mesma. No entanto, não as protegi. Como posso proteger estas partilhas de ficheiros com qualquer outro Cofre?
Ao tentar criar cópias de segurança, selecionar uma Conta de Armazenamento para ver que partilhas de ficheiros há na mesma regista essa Conta no Cofre a partir do qual as cópias estão a ser feitas. Se optar por proteger as partilhas de ficheiros com outro cofre, [Anule o registo](troubleshoot-azure-files.md#configuring-backup) da Conta de Armazenamento escolhida nesse Cofre.

### <a name="can-i-change-the-vault-to-which-i-backup-my-file-shares"></a>Posso alterar o Cofre no qual crio as cópias de segurança das minhas partilhas de ficheiros?
Sim. No entanto, terá de [Parar a Proteção](backup-azure-files.md#stop-protecting-an-azure-file-share) no Cofre ligado, [Anular o registo](troubleshoot-azure-files.md#configuring-backup) dessa Conta de Armazenamento e, em seguida, protegê-la noutro Cofre.

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Em que áreas geográficas posso criar cópias de segurança de partilhas de Ficheiros do Azure <br/>
A cópia de segurança para partilhas de Ficheiros do Azure encontra-se atualmente em Pré-visualização e está disponível apenas nas seguintes áreas geográficas:
- Leste da Austrália (AE)
- Sudeste da Austrália (ASE)
- Sul do Brasil (BRS)
- Canadá Central (CNC)
- Leste do Canadá (CE)
- EUA Central (CUS)
- Ásia Oriental (EA)
- EUA Leste (EUS)
- EUA Leste 2 (EUS2)
- Leste do Japão (JPE)
- Oeste do Japão (JPW)
- Índia Central (INC)
- Índia do Sul (INS)
- KR do Sul Central (KRC)
- Sul KR do Sul (KRS)
- EUA Centro-Norte (NCUS)
- Europa do Norte (NE)
- EUA Centro-Sul (SCUS)
- Sudeste Asiático (SEA)
- Sul do Reino Unido (UKS)
- Oeste do Reino Unido (UKW)
- Europa Ocidental (WE)
- EUA Oeste (WUS)
- EUA Centro-Oeste (WCUS)
- EUA Oeste 2 (WUS 2)
- US Gov Arizona (UGA)
- US Gov Texas (UGT)
- US Gov Virgínia (UGV)

Envie uma mensagem para [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) se precisar de a utilizar numa área geográfica específica que não esteja listada acima.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Quantas partilhas de ficheiros do Azure posso proteger num Cofre?<br/>
Durante a pré-visualização, pode proteger partilhas de ficheiros do Azure de um máximo de 50 Contas de Armazenamento por Cofre. Também pode proteger até 200 partilhas de ficheiros do Azure num único cofre.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Posso proteger duas partilhas de ficheiros diferentes da mesma Conta de Armazenamento em cofres diferentes?
Não. As partilhas de ficheiros numa Conta de Armazenamento só podem ser protegidas pelo mesmo Cofre.

## <a name="backup"></a>Cópia de segurança

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Quantas cópias de segurança A Pedido posso fazer por partilha de ficheiros? <br/>
Pode ter até 200 Instantâneos para uma partilha de ficheiros em qualquer altura. O limite inclui instantâneos tirados pelo Azure Backup, conforme definido pela sua política. Se as cópias de segurança começarem a falhar depois de atingirem o limite, elimine os pontos de restauro A Pedido para obter futuras cópias de segurança com êxito.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Depois de ativar as Redes Virtuais na minha Conta de Armazenamento, a Cópia de Segurança das partilhas de ficheiros na conta começou a falhar. Porquê?
A cópia de segurança de partilhas de ficheiros do Azure não suporta Contas de Armazenamento que tenham Redes Virtuais ativadas. Desative as Redes Virtuais nas Contas de Armazenamento para permitir cópias de segurança bem-sucedidas.

## <a name="restore"></a>Restauro

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Posso recuperar uma partilha de ficheiros do Azure eliminada? <br/>
Quando uma partilha de ficheiros do Azure é eliminada, é-lhe apresentada a lista de cópias de segurança que serão eliminadas e é-lhe pedida confirmação. Não é possível restaurar uma partilha de ficheiros do Azure eliminada.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Posso restaurar a partir de cópias de segurança se tiver parado a proteção numa partilha de ficheiros do Azure? <br/>
Sim. Se tiver escolhido **Reter Dados de Cópia de Segurança** quando parou a proteção, poderá restaurar a partir de todos os pontos de restauro existentes.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontece se cancelar uma tarefa de restauro em curso?
Se uma tarefa de restauro em curso é cancelada, o pára de processo de restauro e todos os ficheiros restaurados antes do cancelamento, permanecem no destino configurado (localização original ou alternativa) sem qualquer reversões. 


## <a name="manage-backup"></a>Gerir a Cópia de Segurança

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares-br"></a>Posso utilizar o PowerShell para configurar/gerir/restaurar cópias de segurança de partilhas de ficheiros do Azure? <br/>
Sim. Consulte a documentação detalhada [aqui](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Posso aceder aos instantâneos realizados pelas Cópias de Segurança do Azure e montá-los? <br/>
Todos os Instantâneos tirados pelo Azure Backup podem ser acedidos ao utilizar a opção Ver Instantâneos no portal, no PowerShell ou na CLI. Para saber mais sobre os instantâneos de partilha de Ficheiros do Azure, veja [Descrição geral de instantâneos de partilha de Ficheiros do Azure (pré-visualização)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Qual é a retenção máxima que posso configurar para as Cópias de Segurança? <br/>
Cópia de segurança para partilhas de ficheiros do Azure oferece a capacidade de configurar as políticas de retenção de cópia de segurança para 180 dias. No entanto, utilizar o [opção "cópia de segurança a pedido", no PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup), pode manter um ponto de recuperação até mesmo há 10 anos.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>O que acontece quando altero a Política de cópias de segurança de uma partilha de ficheiros do Azure? <br/>
Quando é aplicada uma política nova a uma ou mais partilhas de ficheiros, a agenda e a retenção da política nova são seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para que estejam em conformidade com a política nova. Se for reduzida, são marcados para eliminação no trabalho de limpeza seguinte.

## <a name="see-also"></a>Consulte também
Esta informação é apenas sobre a cópia de segurança de Ficheiros do Azure. Para obter mais informações sobre outras áreas do Azure Backup, veja algumas destas outras FAQs sobre Cópias de Segurança:
-  [FAQ sobre o cofre dos Serviços de Recuperação](backup-azure-backup-faq.md)
-  [FAQ sobre as cópias de segurança de VMs do Azure](backup-azure-vm-backup-faq.md)
-  [FAQ sobre o agente do Azure Backup](backup-azure-file-folder-backup-faq.md)
