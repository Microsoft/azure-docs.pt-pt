---
title: Resolução de problemas Azure ficheiros backup
description: Este artigo apresenta informações sobre a resolução de problemas que ocorrem ao proteger as suas partilhas de ficheiros do Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 4c934d2295fa702425e8df0a03636b9f9208cfa4
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515078"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Problemas de resolução de problemas ao mesmo tempo que apoiam as ações de ficheiros da Azure

Este artigo fornece informações de resolução de problemas para resolver quaisquer problemas que encontre enquanto configura a cópia de segurança ou restaura as ações de ficheiros Azure utilizando o Serviço de Backup Azure.

## <a name="common-configuration-issues"></a>Questões de configuração comuns

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Não consegui encontrar a minha conta de armazenamento para configurar a cópia de segurança para a partilha de ficheiros Azure

- Aguarde até que a deteção esteja concluída.
- Verifique se alguma parte do ficheiro na conta de armazenamento já está protegida com outro cofre dos Serviços de Recuperação.

  >[!NOTE]
  >Todas as ações de ficheiros numa Conta de Armazenamento só podem ser protegidas sob um cofre dos Serviços de Recuperação. Pode utilizar [este script](scripts/backup-powershell-script-find-recovery-services-vault.md) para encontrar o cofre dos Serviços de Recuperação onde a sua conta de armazenamento está registada.

- Certifique-se de que a partilha de ficheiros não está presente em nenhuma das contas de armazenamento não suportadas. Pode consultar a [matriz de suporte para a partilha de ficheiros Azure](azure-file-share-support-matrix.md) para encontrar contas de armazenamento suportadas.
- Certifique-se de que o comprimento combinado do nome da conta de armazenamento e o nome do grupo de recursos não excedem 84 caracteres no caso de novas contas de Armazenamento e 77 caracteres no caso de contas de armazenamento clássicas.
- Verifique as definições de firewall da conta de armazenamento para garantir que a opção de permitir que os Serviços Microsoft confiáveis acedam à conta de armazenamento está ativada.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Um erro no portal indica uma falha na deteção das contas de armazenamento.

Se tiver uma subscrição de parceiro (ativada por CSP), ignore o erro. Se a sua subscrição não estiver ativada pelo CSP e as suas contas de armazenamento não puderem ser descobertas, contacte o suporte.

### <a name="selected-storage-account-validation-or-registration-failed"></a>A validação ou registo de conta de armazenamento selecionados falhou

Recandidutar o registo. Se o problema persistir, contacte o suporte.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Não foi possível listar ou encontrar ações de ficheiro na conta de armazenamento selecionada

- Certifique-se de que a Conta de Armazenamento existe no Grupo de Recursos e não foi apagada ou movida após a última validação ou registo no cofre.
- Certifique-se de que a partilha de ficheiros que procura proteger não foi eliminada.
- Certifique-se de que a Conta de Armazenamento é uma conta de armazenamento suportada para a cópia de segurança da partilha de ficheiros. Pode consultar a [matriz de suporte para a partilha de ficheiros Azure](azure-file-share-support-matrix.md) para encontrar contas de armazenamento suportadas.
- Verifique se a partilha de ficheiros já está protegida no mesmo cofre dos Serviços de Recuperação.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>A configuração de partilha de ficheiros de backup (ou a configuração da política de proteção) está a falhar

- Relemisse a configuração para ver se o problema persiste.
- Certifique-se de que a partilha de ficheiros que pretende proteger não foi eliminada.
- Se estiver a tentar proteger várias ações de ficheiros de uma só vez, e algumas das ações dos ficheiros estiverem a falhar, tente configurar a cópia de segurança para as ações de ficheiros falhadas novamente.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Incapaz de apagar o cofre dos Serviços de Recuperação depois de desprotegir uma partilha de ficheiros

No portal Azure, abra as suas contas **de** Armazenamento  >  **de Infraestruturas de Backup** do Cofre  >  . Selecione **Unregister** para remover as contas de armazenamento do cofre dos Serviços de Recuperação.

>[!NOTE]
>Um cofre dos Serviços de Recuperação só pode ser apagado depois de não registar todas as contas de armazenamento registadas no cofre.

## <a name="common-backup-or-restore-errors"></a>Erros comuns de backup ou restauro

>[!NOTE]
>Consulte [este documento](./backup-rbac-rs-vault.md#minimum-role-requirements-for-the-azure-file-share-backup) para garantir que tem permissões suficientes para realizar operações de backup ou restauro.

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound- Operação falhou uma vez que a partilha de ficheiros não é encontrada

Código de erro: FileShareNotFound

Error Message: A operação falhou uma vez que a partilha de ficheiros não é encontrada

Certifique-se de que a partilha de ficheiros que está a tentar proteger não foi eliminada.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>Conta de armazenamento userErrorFileShareEndpointUnreachable-

Código de erro: UserErrorFileShareEndpointsunreachable

Error Message: Conta de armazenamento não encontrada ou não suportada

- Certifique-se de que a conta de armazenamento existe no Grupo de Recursos e não foi eliminada ou removida do Grupo de Recursos após a última validação.

- Certifique-se de que a conta de Armazenamento é uma conta de Armazenamento suportada para a cópia de segurança da partilha de ficheiros.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached- Atingiu o limite máximo de instantâneos para esta partilha de ficheiros; você será capaz de tomar mais uma vez que os mais velhos expiram

Código de erro: AFSMaxSnapshotReached

Error Message: Atingiu o limite máximo de instantâneos para esta partilha de ficheiros; poderá tomar mais quando os mais velhos expirarem.

- Este erro pode ocorrer quando cria várias cópias de segurança a pedido para uma partilha de ficheiros.
- Há um limite de 200 instantâneos por ação de ficheiro, incluindo os tirados pela Azure Backup. As cópias de segurança (ou instantâneos) agendadas mais antigas são limpas automaticamente. As cópias de segurança a pedido (ou instantâneos) têm de ser eliminadas, caso seja atingido o limite máximo.

Elimine as cópias de segurança a pedido (instantâneos de partilhas de ficheiros do Azure) do portal de Ficheiros do Azure.

>[!NOTE]
> Perde os pontos de recuperação se eliminar as imagens criadas por Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound- Operação falhou uma vez que a conta de armazenamento especificada já não existe

Código de erro: UserErrorStorageAccountNotFound

Error Message: A operação falhou uma vez que a conta de armazenamento especificada já não existe.

Certifique-se de que a conta de armazenamento ainda existe e não é apagada.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound- Os dados da conta de armazenamento fornecidos estão incorretos

Código de erro: UserErrorDTSStorageAccountNotFound

Error Message: Os dados da conta de armazenamento fornecidos estão incorretos.

Certifique-se de que a conta de armazenamento ainda existe e não é apagada.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>O grupo de recursos UserErrorResourceGroupNotFound não existe

Código de erro: UserErrorResourceGroupNotFound

Error Message: Grupo de recursos não existe

Selecione um grupo de recursos existente ou crie um novo grupo de recursos.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest- Um trabalho de backup já está em andamento para esta partilha de ficheiros

Código de erro: ParallelSnapshotRequest

Error Message: Já está em curso um trabalho de backup para esta partilha de ficheiros.

- A cópia de segurança da partilha de ficheiros não suporta pedidos de instantâneo paralelos contra a mesma partilha de ficheiros.

- Aguarde que o trabalho de reserva existente termine e tente de novo. Se não conseguir encontrar um trabalho de reserva no cofre dos Serviços de Recuperação, verifique outros cofres dos Serviços de Recuperação na mesma subscrição.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling- Cópia de segurança da partilha de ficheiros ou restauro falhado devido ao estrangulamento do serviço de armazenamento. Isto pode ser porque o serviço de armazenamento está ocupado processando outros pedidos para a conta de armazenamento dada

Código de erro: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Error Message: O ficheiro partilha a cópia de segurança ou a restauração falhou devido ao estrangulamento do serviço de armazenamento. Tal poderá ocorrer porque o serviço de armazenamento está ocupado a processar outros pedidos para a conta de armazenamento em questão.

Tente a operação de backup/restauro mais tarde.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound- Partilha de ficheiros alvo não encontrado

Código de erro: TargetFileShareNotFound

Error Message: A partilha de ficheiros de destino não foi encontrada.

- Certifique-se de que a conta de armazenamento selecionada existe e que a parte do ficheiro-alvo não é eliminada.

- Certifique-se de que a Conta de Armazenamento é uma conta de armazenamento suportada para a cópia de segurança da partilha de ficheiros.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked- Backup ou restaurar empregos falhados devido à conta de armazenamento estar em estado bloqueado

Código de erro: UserErrorStorageAccountIsLocked

Error Message: Os trabalhos de backup ou restauro falharam devido ao estado bloqueado da conta de armazenamento.

Retire o bloqueio na Conta de Armazenamento ou utilize o bloqueio de **exclusão** em vez de **ler o bloqueio** e voltar a tentar a cópia de segurança ou restaurar a operação.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached- Recuperação falhou porque o número de ficheiros falhados é mais do que o limiar

Código de erro: DataTransferServiceCofLimitEd

Error Message: A recuperação falhou porque o número de ficheiros falhados é mais do que o limiar.

- As razões de falha de recuperação estão listadas num ficheiro (caminho fornecido nos detalhes do trabalho). Resolva as falhas e redoça a operação de restauro apenas para os ficheiros falhados.

- Razões comuns para as falhas de restauro de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - existe um diretório com o mesmo nome que o ficheiro falhado no diretório dos pais.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover- Recuperação falhou uma vez que nenhum ficheiro poderia ser recuperado

Código de erro: DataTransferServiceServiceAllFilesFailedToRecover

Error Message: Recovery failed as no file could be recovery.

- As razões de falha de recuperação estão listadas num ficheiro (caminho fornecido nos detalhes do trabalho). Resolva as falhas e repita a operação de restauro apenas para os ficheiros com falhas.

- Razões comuns para as falhas de restauro de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - existe um diretório com o mesmo nome que o ficheiro falhado no diretório dos pais.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - Restaurar falha porque um dos ficheiros da fonte não existe

Código de erro: DataTransferServiceSourceUriNotValid

Error Message: O restauro falha porque um dos ficheiros da fonte não existe.

- Os itens selecionados não estão presentes nos dados do ponto de recuperação. Para recuperar os ficheiros, forneça a lista de ficheiros correta.
- O instantâneo da partilha de ficheiros que corresponde ao ponto de recuperação foi eliminado manualmente. Selecione outro ponto de recuperação e repita a operação de restauro.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked- Um trabalho de recuperação está em curso para o mesmo destino

Código de erro: UserErrorDTSDestLocked

Error Message: Um trabalho de recuperação está em curso para o mesmo destino.

- A cópia de segurança da partilha de ficheiros não suporta a recuperação paralela à mesma partilha de ficheiros alvo.

- Aguarde que a recuperação existente termine e tente novamente. Se não conseguir encontrar um emprego de recuperação no cofre dos Serviços de Recuperação, verifique outros cofres dos Serviços de Recuperação na mesma subscrição.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull- Operação de restauro falhou à medida que a partilha de ficheiros-alvo está completa

Código de erro: UserErrorTargetFileShareFull

Error Message: Restaurar a operação falhou à medida que a partilha de ficheiros-alvo está completa.

Aumente a quota de tamanho da parte do ficheiro alvo para acomodar os dados de restauro e reveja a operação de restauro.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>A quota de ficheiros TargetErrorTargetFileShareQuotaNotSufficient- Target não tem quota de tamanho de armazenamento suficiente para restauro

Código de erro: UserErrorTargetFileShareQuotaNotSufficient

Error Message: Target File share não tem quota de tamanho de armazenamento suficiente para restaurar

Aumente a quota de tamanho de ação de ficheiro alvo para acomodar os dados de restauro e re-tentar a operação

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>File Sync PreRestoreFailed- A operação de restauro falhou, uma vez que ocorreu um erro durante a realização de operações de pré-restauro nos recursos do Serviço de Sincronização de Ficheiros associados à partilha de ficheiros-alvo

Código de erro: Sincronização de ficheiros PreRestoreFailed

Error Message: Restaurar a operação falhou à medida que ocorreu um erro durante a realização de operações de pré-restauração nos recursos do Serviço de Sincronização de Ficheiros associados à partilha de ficheiros-alvo.

Tente restaurar os dados mais tarde. Se o problema persistir, contacte o suporte da Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>A deteção de alterações do Serviço de Sincronização de Ficheiros AzureFileFileSyncChangeProgress- Azure File Sync Está em andamento para a partilha de ficheiros-alvo. A deteção de alterações foi desencadeada por uma restauração anterior da parte do ficheiro alvo

Código de erro: AzureFileSyncChangeDetectionInProgress

Error Message: Azure File Sync Service detection is in progress for the target file share. A deteção de alterações foi desencadeada por uma restauração anterior da parte do ficheiro alvo.

Use uma partilha de ficheiros de alvo diferente. Em alternativa, pode esperar que a deteção do Serviço de Sincronização de Ficheiros Azure seja concluída para a partilha do ficheiro alvo antes de voltar a tentar a restauração.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored- Um ou mais ficheiros não puderam ser recuperados com sucesso. Para mais informações, verifique a lista de ficheiros falhados no caminho acima indicado

Código de erro: UserErrorAFSRecoverySomeFilesNotRestored

Error Message: Um ou mais ficheiros não puderam ser recuperados com sucesso. Para obter mais informações, verifique a lista de ficheiros com falhas no caminho indicado acima.

- As razões de falha de recuperação estão listadas no ficheiro (caminho fornecido nos detalhes do Trabalho). Aborde as razões e recandiduça a operação de restauro apenas para os ficheiros falhados.
- Razões comuns para as falhas de restauro de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - existe um diretório com o mesmo nome que o ficheiro falhado no diretório dos pais.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound- Azure file share snapshot correspondente ao ponto de recuperação não pode ser encontrado

Código de erro: UserErrorAFSSourceSnapshotNotFound

Error Message: Azure file share snapshot correspondente ao ponto de recuperação não pode ser encontrado

- Certifique-se de que o instantâneo de partilha de ficheiros, correspondente ao ponto de recuperação que está a tentar utilizar para recuperação, ainda existe.

  >[!NOTE]
  >Se eliminar uma imagem de partilha de ficheiros criada pela Azure Backup, os pontos de recuperação correspondentes tornam-se inutilizáveis. Recomendamos que não extinguam instantâneos para garantir uma recuperação garantida.

- Tente selecionar outro ponto de restauro para recuperar os seus dados.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget- Outra função de restauro está em andamento na mesma partilha de ficheiros-alvo

Código de erro: UserErrorAnotherRestoreInProgressOnSameTarget

Error Message: Outra tarefa de restauro está em andamento na mesma partilha de ficheiros alvo

Use uma partilha de ficheiros de alvo diferente. Em alternativa, pode cancelar ou aguardar a conclusão do outro restauro.

## <a name="common-modify-policy-errors"></a>Erros de política comuns de modificação

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperação- Está em curso outra operação de proteção de configuração para este item

Código de erro: BMSUserErrorConflictingProtectionOperação

Error Message: Está em curso uma outra operação de proteção de configuração para este item.

Aguarde que a operação de modificação anterior termine e relemque mais tarde.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked- Outra operação está em andamento no item selecionado

Código de erro: BMSUserErrorObjectLocked

Error Message: Está em curso outra operação no item selecionado.

Aguarde que a outra operação em curso termine e relemisse mais tarde.

## <a name="common-soft-delete-related-errors"></a>Erros relacionados com a eliminação suave comum

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState- Este ponto de restauro não está disponível, uma vez que o instantâneo associado a este ponto está numa Partilha de Ficheiros que se encontra em estado de apagamento suave

Código de erro: UserErrorRestoreAFSInSoftDeleteState

Error Message: Este ponto de restauro não está disponível, uma vez que o instantâneo associado a este ponto está numa Partilha de Ficheiros que se encontra em estado de apagamento suave.

Não é possível efetuar uma operação de restauro quando a partilha de ficheiros está em estado de apagamento suave. Desempacoar a partilha de ficheiros do portal Files ou usando o [script Undelete](scripts/backup-powershell-script-undelete-file-share.md) e, em seguida, tente restaurar.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDelete-Pontos de restauro listados não estão disponíveis, uma vez que a parte de ficheiro associada que contém as imagens do ponto de restauro foi eliminada permanentemente

Código de erro: UserErrorRestoreAFSInDeleteState

Error Message: Os pontos de restauro listados não estão disponíveis, uma vez que a parte de ficheiro associada que contém as imagens do ponto de restauro foi eliminada permanentemente.

Verifique se a partilha de ficheiros com ressoou. Se estava em estado de eliminação suave, verifique se o período de retenção de eliminação suave acabou e não foi recuperado de volta. Em qualquer um destes casos, perderá todas as suas fotos permanentemente e não será capaz de recuperar os dados.

>[!NOTE]
> Recomendamos que não apague a partilha de ficheiros com retenida ou se estiver em estado de eliminação suave, desacompanhada antes do fim do período de retenção de eliminação suave, para evitar perder todos os seus pontos de restauro.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - Cópia de segurança falhou uma vez que a Azure File Share está em estado de apagamento suave

Código de erro: UserErrorBackupAFSInSoftDeleteState

Error Message: A cópia de segurança falhou à medida que a Azure File Share está em estado de apagamento suave

Desempacoar a partilha de ficheiros do **portal Ficheiros** ou utilizando o [script Undelete](scripts/backup-powershell-script-undelete-file-share.md) para continuar a cópia de segurança e impedir a eliminação permanente de dados.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState- Cópia de segurança falhou à medida que a partilha de ficheiros Azure associada é permanentemente eliminada

Código de erro: UserErrorBackupAFSInDeleteState

Error Message: A cópia de segurança falhou à medida que a partilha de ficheiros Azure associada é permanentemente eliminada

Verifique se a parte do ficheiro retrossativo é permanentemente eliminada. Se sim, pare a cópia de segurança para a partilha de ficheiros para evitar falhas repetidas de backup. Para aprender a parar a proteção consulte [stop Protection for Azure file share](./manage-afs-backup.md#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o backup das ações de ficheiros Azure, consulte:

- [Fazer cópia de segurança das partilhas de ficheiros do Azure](backup-afs.md)
- [Back up Azure file share FAQ](backup-azure-files-faq.yml)
