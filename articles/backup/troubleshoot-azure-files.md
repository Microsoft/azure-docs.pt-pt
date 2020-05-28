---
title: Troubleshoot Azure partilha backup de partilha de ficheiros
description: Este artigo apresenta informações sobre a resolução de problemas que ocorrem ao proteger as suas partilhas de ficheiros do Azure.
ms.date: 02/10/2020
ms.topic: troubleshooting
ms.openlocfilehash: 3d04a60b8bab5ba764818eab341ac08836b0dfd1
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116740"
---
# <a name="troubleshoot-problems-while-backing-up-azure-file-shares"></a>Problemas de resolução de problemas ao apoiar ações de ficheiros da Azure

Este artigo fornece informações de resolução de problemas para resolver quaisquer problemas que encontre ao configurar as cópias de segurança ou restaurar as partilhas de ficheiros Azure utilizando o Serviço de Backup Azure.

## <a name="common-configuration-issues"></a>Problemas comuns de configuração

### <a name="could-not-find-my-storage-account-to-configure-backup-for-the-azure-file-share"></a>Não consegui encontrar a minha conta de armazenamento para configurar a cópia de segurança para a partilha de ficheiros Azure

- Aguarde até que a deteção esteja concluída.
- Verifique se alguma parte de ficheiro na conta de armazenamento já está protegida com outro cofre dos Serviços de Recuperação.

  >[!NOTE]
  >Todas as ações de ficheiros numa Conta de Armazenamento só podem ser protegidas sob um cofre dos Serviços de Recuperação. Pode usar [este script](scripts/backup-powershell-script-find-recovery-services-vault.md) para encontrar o cofre dos serviços de recuperação onde a sua conta de armazenamento está registada.

- Certifique-se de que a parte do ficheiro não está presente em nenhuma das Contas de Armazenamento não suportadas. Pode consultar a matriz de suporte para a cópia de segurança de partilha de [ficheiros Azure](azure-file-share-support-matrix.md) para encontrar contas de armazenamento suportadas.
- Verifique as definições de firewall da conta de armazenamento para garantir que a opção de permitir que os Serviços Microsoft fidedignos acedam à conta de armazenamento.

### <a name="error-in-portal-states-discovery-of-storage-accounts-failed"></a>Erro no portal afirma que descoberta de contas de armazenamento falhou

Se tiver uma subscrição de parceiro (ativada por CSP), ignore o erro. Se a sua subscrição não estiver ativada por CSP e as suas contas de armazenamento não puderem ser descobertas, suporte de contato.

### <a name="selected-storage-account-validation-or-registration-failed"></a>Validação ou registo de conta de armazenamento selecionado falhou

Tente o registo. Se o problema persistir, o suporte de contacto.

### <a name="could-not-list-or-find-file-shares-in-the-selected-storage-account"></a>Não poderia listar ou encontrar ações de ficheiro na conta de armazenamento selecionada

- Certifique-se de que a Conta de Armazenamento existe no Grupo de Recursos e não foi eliminada ou movida após a última validação ou registo no cofre.
- Certifique-se de que a partilha de ficheiros que procura proteger não foi apagada.
- Certifique-se de que a Conta de Armazenamento é uma conta de armazenamento suportada para cópia de segurança de partilha de ficheiros. Pode consultar a matriz de suporte para a cópia de segurança de partilha de [ficheiros Azure](azure-file-share-support-matrix.md) para encontrar contas de armazenamento suportadas.
- Verifique se a parte do ficheiro já está protegida no mesmo cofre dos Serviços de Recuperação.

### <a name="backup-file-share-configuration-or-the-protection-policy-configuration-is-failing"></a>A configuração da partilha de ficheiros de backup (ou a configuração da política de proteção) está a falhar

- Tente novamente a configuração para ver se o problema persiste.
- Certifique-se de que a partilha de ficheiros que pretende proteger não foi eliminada.
- Se estiver a tentar proteger várias partilhas de ficheiros ao mesmo tempo, e algumas das partilhas de ficheiros estiverem a falhar, tente configurar novamente a cópia de segurança para as partilhas de ficheiros falhadas.

### <a name="unable-to-delete-the-recovery-services-vault-after-unprotecting-a-file-share"></a>Incapaz de apagar o cofre dos Serviços de Recuperação depois de desproteger uma parte de ficheiro

No portal Azure, abra **as**suas contas de Armazenamento de  >  **Infraestruturas**de Backup vault e clique em  >  **Storage accounts** **Unregister** para remover as contas de armazenamento do cofre dos Serviços de Recuperação.

>[!NOTE]
>Um cofre de serviços de recuperação só pode ser apagado depois de desregistar todas as contas de armazenamento registadas no cofre.

## <a name="common-backup-or-restore-errors"></a>Erros comuns de backup ou restauro

### <a name="filesharenotfound--operation-failed-as-the-file-share-is-not-found"></a>FileShareNotFound- Operação falhada uma vez que a parte do ficheiro não é encontrada

Error Code: FileShareNotFound

Error Message: Operação falhou uma vez que a parte do ficheiro não é encontrada

Certifique-se de que a partilha de ficheiros que está a tentar proteger não foi apagada.

### <a name="usererrorfileshareendpointunreachable--storage-account-not-found-or-not-supported"></a>UserErrorFileShareEndpointUnreachable- Conta de armazenamento não encontrada ou não suportada

Error Code: UserErrorFileShareEndpointUnreachable

Error Message: Conta de armazenamento não encontrada ou não suportada

- Certifique-se de que a conta de armazenamento existe no Grupo de Recursos e não foi eliminada ou removida do Grupo de Recursos após a última validação.

- Certifique-se de que a conta de Armazenamento é uma conta de Armazenamento suportada para cópia de segurança de partilha de ficheiros.

### <a name="afsmaxsnapshotreached--you-have-reached-the-max-limit-of-snapshots-for-this-file-share-you-will-be-able-to-take-more-once-the-older-ones-expire"></a>AFSMaxSnapshotReached- Atingiu o limite máximo de instantâneos para esta partilha de ficheiros; você será capaz de tomar mais uma vez que os mais velhos expirar

Error Code: AFSMaxSnapshotReached

Error Message: Atingiu o limite máximo de instantâneos para esta partilha de ficheiros; poderá tomar mais quando os mais velhos expirarem.

- Este erro pode ocorrer quando cria várias cópias de segurança a pedido para uma partilha de ficheiros.
- Há um limite de 200 instantâneos por partilha de ficheiros, incluindo os tirados pela Azure Backup. As cópias de segurança (ou instantâneos) agendadas mais antigas são limpas automaticamente. As cópias de segurança a pedido (ou instantâneos) têm de ser eliminadas, caso seja atingido o limite máximo.

Elimine as cópias de segurança a pedido (instantâneos de partilhas de ficheiros do Azure) do portal de Ficheiros do Azure.

>[!NOTE]
> Perde os pontos de recuperação se eliminar imagens criadas pelo Azure Backup.

### <a name="usererrorstorageaccountnotfound--operation-failed-as-the-specified-storage-account-does-not-exist-anymore"></a>UserErrorStorageAccountNotFound- Operação falhou uma vez que a conta de armazenamento especificada já não existe

Error Code: UserErrorStorageAccountNotFound

Error Message: A operação falhou, uma vez que a conta de armazenamento especificada já não existe.

Certifique-se de que a conta de armazenamento ainda existe e não é eliminada.

### <a name="usererrordtsstorageaccountnotfound--the-storage-account-details-provided-are-incorrect"></a>UserErrorDTSStorageAccountNotFound- Os dados da conta de armazenamento fornecidos estão incorretos

Error Code: UserErrorDTSStorageAccount NotFound

Error Message: Os dados da conta de armazenamento fornecidos estão incorretos.

Certifique-se de que a conta de armazenamento ainda existe e não é eliminada.

### <a name="usererrorresourcegroupnotfound--resource-group-doesnt-exist"></a>UserErrorResourceNotFound- Grupo de recursos não existe

Código de erro: UserErrorResourceGroupNotFound

Error Message: Grupo de recursos não existe

Selecione um grupo de recursos existente ou crie um novo grupo de recursos.

### <a name="parallelsnapshotrequest--a-backup-job-is-already-in-progress-for-this-file-share"></a>ParallelSnapshotRequest- Já está em curso um trabalho de backup para esta partilha de ficheiros

Error Code: ParallelSnapshotRequest

Error Message: Já está em curso um trabalho de backup para esta partilha de ficheiros.

- A cópia de segurança da partilha de ficheiros não suporta pedidos de instantâneo paralelos contra a mesma partilha de ficheiros.

- Espere que o trabalho de reserva existente termine e tente de novo. Se não conseguir encontrar um trabalho de reserva no cofre dos Serviços de Recuperação, verifique outros cofres dos Serviços de Recuperação na mesma subscrição.

### <a name="filesharebackupfailedwithazurerprequestthrottling-filesharerestorefailedwithazurerprequestthrottling--file-share-backup-or-restore-failed-due-to-storage-service-throttling-this-may-be-because-the-storage-service-is-busy-processing-other-requests-for-the-given-storage-account"></a>FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling- File share backup ou restaurar falhou devido ao estrangulamento do serviço de armazenamento. Isto pode ser porque o serviço de armazenamento está ocupado processando outros pedidos para a conta de armazenamento dada

Código de Erro: FileshareBackupFailedWithAzureRpRequestThrottling/ FileshareRestoreFailedWithAzureRpRequestThrottling

Error Message: File share backup or restore failed due to storage service throttling. Tal poderá ocorrer porque o serviço de armazenamento está ocupado a processar outros pedidos para a conta de armazenamento em questão.

Experimente a operação de backup/restauro mais tarde.

### <a name="targetfilesharenotfound--target-file-share-not-found"></a>TargetFileShareNotFound- Partilha de ficheiros de destino não encontrada

Código de Erro: TargetFileShareNotFound

Error Message: A parte do ficheiro do alvo não foi encontrada.

- Certifique-se de que a Conta de Armazenamento selecionada existe e que a parte do ficheiro alvo não é eliminada.

- Certifique-se de que a Conta de Armazenamento é uma conta de armazenamento suportada para cópia de segurança de partilha de ficheiros.

### <a name="usererrorstorageaccountislocked--backup-or-restore-jobs-failed-due-to-storage-account-being-in-locked-state"></a>UserErrorStorageAccountIsLocked- Backup ou restauro de trabalhos falhados devido à conta de armazenamento estar em estado fechado

Error Code: UserErrorStorageAccountIsLocked

Error Message: Backup ou restaurar trabalhos falhados devido à conta de armazenamento estar em estado fechado.

Retire o bloqueio na Conta de Armazenamento ou utilize **o bloqueio** de apagar em vez de ler **o bloqueio** e volte a tentar a operação de backup ou restauro.

### <a name="datatransferservicecoflimitreached--recovery-failed-because-number-of-failed-files-are-more-than-the-threshold"></a>DataTransferServiceCoFLimitReached- A recuperação falhou porque o número de ficheiros falhados é superior ao limiar

Código de Erro: DataTransferServiceCoFLimitReached

Error Message: A recuperação falhou porque o número de ficheiros falhados é superior ao limiar.

- Os motivos de falha de recuperação estão listados num ficheiro (caminho fornecido nos detalhes do trabalho). Abordar as falhas e voltar a tentar a operação de restauro apenas para os ficheiros falhados.

- Razões comuns para restabelecer falhas de recuperação de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - um diretório com o mesmo nome que o ficheiro falhado existe no diretório dos pais.

### <a name="datatransferserviceallfilesfailedtorecover--recovery-failed-as-no-file-could-be-recovered"></a>DataTransferServiceAllFilesFailedToRecover- Recuperação falhou uma vez que nenhum ficheiro poderia ser recuperado

Código de Erro: DataTransferServiceAllFilesFailedToRecover

Error Message: A recuperação falhou, uma vez que nenhum ficheiro poderia ser recuperado.

- Os motivos de falha de recuperação estão listados num ficheiro (caminho fornecido nos detalhes do trabalho). Resolva as falhas e repita a operação de restauro apenas para os ficheiros com falhas.

- Razões comuns para restabelecer falhas de recuperação de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - um diretório com o mesmo nome que o ficheiro falhado existe no diretório dos pais.

### <a name="usererrordtssourceurinotvalid---restore-fails-because-one-of-the-files-in-the-source-does-not-exist"></a>UserErrorDTSSourceUriNotValid - Restaurar falha porque um dos ficheiros na fonte não existe

Código de Erro: DataTransferServiceSourceurinotValid

Error Message: Restaurar falha porque um dos ficheiros na fonte não existe.

- Os itens selecionados não estão presentes nos dados do ponto de recuperação. Para recuperar os ficheiros, forneça a lista de ficheiros correta.
- O instantâneo da partilha de ficheiros que corresponde ao ponto de recuperação foi eliminado manualmente. Selecione outro ponto de recuperação e repita a operação de restauro.

### <a name="usererrordtsdestlocked--a-recovery-job-is-in-process-to-the-same-destination"></a>UserErrorDTSDestLocked- Um trabalho de recuperação está em processo para o mesmo destino

Código de Erro: UserErrorDTSDestLocked

Error Message: Um trabalho de recuperação está em processo para o mesmo destino.

- A cópia de segurança da partilha de ficheiros não suporta a recuperação paralela à mesma parte do ficheiro alvo.

- Aguarde que a recuperação existente termine e tente novamente. Se não conseguir encontrar um emprego de recuperação no cofre dos Serviços de Recuperação, verifique outros cofres dos Serviços de Recuperação na mesma subscrição.

### <a name="usererrortargetfilesharefull--restore-operation-failed-as-target-file-share-is-full"></a>UserErrorTargetFileShareFull- Restaurar a operação falhada à medida que a partilha de ficheiros alvo está completa

Código de erro: UserErrorTargetTargetShareFull

Error Message: Restaurar a operação falhou à medida que a parte do ficheiro alvo está completa.

Aumente a quota de tamanho da partilha de ficheiros-alvo para acomodar os dados de restauro e volte a tentar a operação de restauro.

### <a name="usererrortargetfilesharequotanotsufficient--target-file-share-does-not-have-sufficient-storage-size-quota-for-restore"></a>UserErrorTargetTargetShareShareNotEnough- A partilha de ficheiros target não tem quota de tamanho de armazenamento suficiente para restaurar

Código de erro: UserErrorTargetFileShareShareQuotaNotEnough

Error Message: Target File share não tem quota de tamanho de armazenamento suficiente para restaurar

Aumentar a quota de tamanho da partilha de ficheiros-alvo para acomodar os dados de restauro e voltar a tentar a operação

### <a name="file-sync-prerestorefailed--restore-operation-failed-as-an-error-occurred-while-performing-pre-restore-operations-on-file-sync-service-resources-associated-with-the-target-file-share"></a>Sincronização de ficheiros Pré-RestaurarFalhada- Restaurar a operação falhou como um erro ocorreu enquanto executava operações de pré-restauração nos recursos do Serviço de Sincronização de Ficheiros associados à parte do ficheiro alvo

Código de erro: Sincronização de ficheiros pré-restaurar Falhou

Error Message: Restaurar a operação falhou como ocorreu um erro durante a realização de operações de pré-restauração nos recursos do Serviço de Sincronização de Ficheiros associados à parte do ficheiro alvo.

Tente restaurar os dados mais tarde. Se o problema persistir, contacte o suporte da Microsoft.

### <a name="azurefilesyncchangedetectioninprogress--azure-file-sync-service-change-detection-is-in-progress-for-the-target-file-share-the-change-detection-was-triggered-by-a-previous-restore-to-the-target-file-share"></a>A deteção de alterações do Serviço de Sincronização de Ficheiros AzureSyncInProgress- Azure está em andamento para a parte de ficheiro alvo. A deteção de alterações foi desencadeada por um restauro anterior à parte do ficheiro alvo

Código de Erro: AzureFileSyncChangeDetectionInProgress

Error Message: A deteção de alteração do Serviço de Sincronização de Ficheiros Azure está em andamento para a parte do ficheiro alvo. A deteção de alterações foi desencadeada por um restauro anterior à parte do ficheiro alvo.

Use uma parte de ficheiro de alvo diferente. Em alternativa, pode esperar que a deteção de alteração do Serviço de Sincronização de Ficheiros Azure seja concluída para a parte do ficheiro alvo antes de voltar a tentar a restauração.

### <a name="usererrorafsrecoverysomefilesnotrestored--one-or-more-files-could-not-be-recovered-successfully-for-more-information-check-the-failed-file-list-in-the-path-given-above"></a>UserErrorAFSRecoverySomeFilesNotRestored- Um ou mais ficheiros não puderam ser recuperados com sucesso. Para mais informações, consulte a lista de ficheiros falhada no caminho acima indicado

Error Code: UserErrorAFSRecoverySomeFilesNotRestored

Error Message: Um ou mais ficheiros não puderam ser recuperados com sucesso. Para obter mais informações, verifique a lista de ficheiros com falhas no caminho indicado acima.

- Os motivos de falha de recuperação estão listados no ficheiro (caminho fornecido nos detalhes do Trabalho). Aborde as razões e volte a tentar restabelecer a operação de restauro apenas para os ficheiros falhados.
- Razões comuns para restabelecer falhas de recuperação de ficheiros:

  - ficheiros que falharam estão atualmente em uso
  - um diretório com o mesmo nome que o ficheiro falhado existe no diretório dos pais.

### <a name="usererrorafssourcesnapshotnotfound--azure-file-share-snapshot-corresponding-to-recovery-point-cannot-be-found"></a>UserErrorAFSSourceSnapshotNotFound- Não é possível encontrar instantâneo de partilha de ficheiros Do Lado Do Utilizador, correspondente ao ponto de recuperação

Error Code: UserErrorAFSSourceSnapshotNotFound

Error Message: Não é possível encontrar instantâneo de partilha de ficheiros Azure correspondente ao ponto de recuperação

- Certifique-se de que o instantâneo de partilha de ficheiros, correspondente ao ponto de recuperação que está a tentar utilizar para a recuperação, ainda existe.

  >[!NOTE]
  >Se eliminar um instantâneo de partilha de ficheiros criado pela Azure Backup, os pontos de recuperação correspondentes tornam-se inutilizáveis. Recomendamos que não apague instantâneos para garantir a recuperação garantida.

- Tente selecionar outro ponto de restauro para recuperar os seus dados.

### <a name="usererroranotherrestoreinprogressonsametarget--another-restore-job-is-in-progress-on-the-same-target-file-share"></a>UserErrorAnotherRestoreInProgressOnSameTarget- Outro trabalho de restauro está em andamento na mesma parte de ficheiro-alvo

Error Code: UserErrorAnotherRestoreInProgressOnSameTarget

Error Message: Outro trabalho de restauro está em andamento na mesma parte de ficheiro-alvo

Use uma parte de ficheiro de alvo diferente. Em alternativa, pode cancelar ou esperar que o outro restauro esteja concluído.

## <a name="common-modify-policy-errors"></a>Erros políticos comuns modificam

### <a name="bmsusererrorconflictingprotectionoperation--another-configure-protection-operation-is-in-progress-for-this-item"></a>BMSUserErrorConflictingProtectionOperation- Outra operação de proteção configurada está em andamento para este item

Error Code: BMSUserErrorConflictingProtectionOperation

Error Message: Outra operação de proteção configurada está em andamento para este item.

Aguarde que a operação política modificada anterior termine e retente mais tarde.

### <a name="bmsusererrorobjectlocked--another-operation-is-in-progress-on-the-selected-item"></a>BMSUserErrorObjectLocked- Outra operação está em andamento no item selecionado

Error Code: BMSUserErrorObjectLocked

Error Message: Outra operação está em curso no item selecionado.

Aguarde que a outra operação em curso termine e retente mais tarde.

Do ficheiro: troubleshoot-azure-files.md

## <a name="common-soft-delete-related-errors"></a>Erros relacionados de eliminação suave comum

### <a name="usererrorrestoreafsinsoftdeletestate--this-restore-point-is-not-available-as-the-snapshot-associated-with-this-point-is-in-a-file-share-that-is-in-soft-deleted-state"></a>UserErrorRestoreAFSInSoftDeleteState- Este ponto de restauro não está disponível, uma vez que o instantâneo associado a este ponto está numa Partilha de Ficheiros que se encontra em estado de soft-delete

Error Code: UserErrorRestoreAFSInSoftDeleteState

Error Message: Este ponto de restauro não está disponível, uma vez que o instantâneo associado a este ponto está numa Partilha de Ficheiros que se encontra em estado de eliminação suave.

Não é possível executar uma operação de restauro quando a parte do ficheiro está em estado suave apagado. Desapague a parte do ficheiro do portal Ficheiros ou utilize o [script Undelete](scripts/backup-powershell-script-undelete-file-share.md) e tente restaurar.

### <a name="usererrorrestoreafsindeletestate--listed-restore-points-are-not-available-as-the-associated-file-share-containing-the-restore-point-snapshots-has-been-deleted-permanently"></a>UserErrorRestoreAFSInDeleteState- Os pontos de restauro listados não estão disponíveis, uma vez que a parte de ficheiro associada que contém as imagens do ponto de restauro foi eliminada permanentemente

Error Code: UserErrorRestoreAFSInDeleteState

Error Message: Os pontos de restauro listados não estão disponíveis, uma vez que a parte de ficheiro associada que contém as imagens do ponto de restauro foi eliminada permanentemente.

Verifique se a parte do ficheiro de back-up foi eliminada. Se foi em estado apagado suavemente, verifique se o período de retenção de eliminação suave acabou e não foi recuperado de volta. Em qualquer um destes casos, perderá todas as suas fotos permanentemente e não será capaz de recuperar os dados.

>[!NOTE]
> Recomendamos que não elimine a parte do ficheiro de backed, ou se estiver em estado suave apagado, sem apagar antes que o período de retenção de eliminação suave termine, para evitar perder todos os seus pontos de restauro.

### <a name="usererrorbackupafsinsoftdeletestate---backup-failed-as-the-azure-file-share-is-in-soft-deleted-state"></a>UserErrorBackupAFSInSoftDeleteState - A cópia de segurança falhou à medida que a Partilha de Ficheiros Azure está em estado de soft-delete

Error Code: UserErrorBackupAFSInSoftDeleteState

Error Message: Backup failed as the Azure File Share is in soft-deleted state

Desapague a parte do ficheiro do **portal Ficheiros** ou utilizando o [script Undelete](scripts/backup-powershell-script-undelete-file-share.md) para continuar a cópia de segurança e evitar a eliminação permanente dos dados.

### <a name="usererrorbackupafsindeletestate--backup-failed-as-the-associated-azure-file-share-is-permanently-deleted"></a>UserErrorBackupAFSInDeleteState- Cópia de segurança falhou à medida que a partilha de ficheiros Azure associada é eliminada permanentemente

Error Code: UserErrorBackupAFSInDeleteState

Error Message: Backup failed as the Associated Azure File Share is permanently eliminado

Verifique se a parte do ficheiro backed é permanentemente eliminada. Se sim, pare a cópia de segurança para a partilha de ficheiros para evitar falhas repetidas de backup. Para aprender a parar a proteção ver [Stop Protection for Azure file share](https://docs.microsoft.com/azure/backup/manage-afs-backup#stop-protection-on-a-file-share)

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o backup de ações de ficheiros Azure, consulte:

- [Fazer cópia de segurança das partilhas de ficheiros do Azure](backup-afs.md)
- [Back up Azure partilha de ficheiros FAQ](backup-azure-files-faq.md)
