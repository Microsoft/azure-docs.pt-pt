---
title: Falhas de backup de resolução de problemas na cópia de segurança do disco Azure
description: Saiba como resolver falhas de backup no Azure Disk Backup
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 855c6c5b19b10bdb699a25f89ebc29001b7941ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737732"
---
# <a name="troubleshooting-backup-failures-in-azure-disk-backup-in-preview"></a>Falhas de backup de resolução de problemas na cópia de segurança do disco Azure (na pré-visualização)

>[!IMPORTANT]
>A Azure Disk Backup está em pré-visualização sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para disponibilidade da região, consulte a [matriz de suporte.](disk-backup-support-matrix.md)
>
>[Preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) para se inscrever para a pré-visualização.

Este artigo fornece informações sobre backup e restaurar problemas enfrentados com Azure Disk. Para obter mais informações sobre a disponibilidade da região de backup do [Disco Azure,](disk-backup-overview.md) cenários e limitações apoiados, consulte a matriz de [suporte](disk-backup-support-matrix.md).

## <a name="common-issues-faced-with-azure-disk-backup"></a>Questões comuns confrontadas com a cópia de segurança do disco Azure

### <a name="error-code-usererrorsnapshotrgsubscriptionmismatch"></a>Código de erro: UserErRSnapshotRGSubscriptionMismatch

Error Message: Subscrição inválida selecionada para a loja de dados snapshot

Ação recomendada: Os discos e as imagens de disco são armazenados na mesma subscrição. Pode escolher qualquer grupo de recursos para armazenar as imagens do disco dentro da subscrição. Selecione a mesma subscrição que a do disco de origem. Para mais informações, consulte a [matriz de suporte.](disk-backup-support-matrix.md)

### <a name="error-code-usererrorsnapshotrgnotfound"></a>Código de erro: UserErRSnapshotRGNotFound

Error Message: Não foi possível efetuar a operação uma vez que o Grupo de Recursos da Loja de Dados Instantâneos não existe.

Ação Recomendada: Crie o grupo de recursos e forneça as permissões necessárias. Para mais informações, consulte a cópia de segurança de [configuração.](backup-managed-disks.md#configure-backup)

### <a name="error-code-usererrormanageddisknotfound"></a>Código de erro: UserErrorManagedDiskNotFound

Error Message: Não foi possível executar a operação uma vez que o Disco Gerido já não existe.

Ação recomendada: As cópias de segurança continuarão a falhar, uma vez que o disco de origem pode ser eliminado ou transferido para um local diferente. Utilize o ponto de restauro existente para restaurar o disco se for eliminado por engano. Se o disco for movido para um local diferente, configuure a cópia de segurança para o disco.

### <a name="error-code-usererrornotenoughpermissionondisk"></a>Código de erro: UserErrorNotEnoughPermissionOnDisk

Error Message: O Serviço de Backup Azure requer permissões adicionais no Disco para fazer esta operação.

Ação recomendada: Conceda à identidade gerida do cofre de reserva as permissões apropriadas no disco. Consulte [a documentação](backup-managed-disks.md) para entender quais as permissões necessárias pela identidade gerida do Cofre de Reserva e como fornejá-la.

### <a name="error-code-usererrornotenoughpermissiononsnapshotrg"></a>Código de erro: UserErrorNotEnoughPermissionOnSnapshotRG

Error Message: O Serviço de Cópia de Segurança Azure requer permissões adicionais no Grupo de Recursos da Loja de Dados Instantâneos para fazer esta operação.

Ação Recomendada: Conceda à identidade gerida do cofre de reserva as permissões apropriadas no grupo de recursos da loja de dados instantânea. O grupo de recursos da loja de dados instantâneos é o local onde as imagens do disco são armazenadas. Consulte [a documentação](backup-managed-disks.md) para entender qual é o grupo de recursos, quais as permissões necessárias pela identidade gerida do Cofre de Reserva e como fornejá-la.

### <a name="error-code-usererrordiskbackupdiskormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupDiskOrMSIPermissionsNotPresent

Error Message: O disco inválido ou o Serviço de Backup Azure requer permissões adicionais no Disco para fazer esta operação

Ação recomendada: As cópias de segurança continuarão a falhar, uma vez que o disco de origem pode ser eliminado ou transferido para um local diferente. Utilize o ponto de restauro existente para restaurar o disco se for eliminado por engano. Se o disco for movido para um local diferente, configuure a cópia de segurança para o disco. Se o disco não for apagado ou movido, conceda à identidade gerida do cofre de reserva as permissões apropriadas no disco. Consulte [a documentação](backup-managed-disks.md) para entender quais as permissões necessárias pela identidade gerida do cofre de reserva e como fornejá-la.

### <a name="error-code-usererrordiskbackupsnapshotrgormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupSnapshotRGOrMSIPermissionsNotPresent

Error Message: Não foi possível executar a operação uma vez que o Grupo de Recursos da Loja de Dados Instantâneos já não existe. Ou o Serviço de Backup Azure requer permissões adicionais no Grupo de Recursos da Loja de Dados Instantâneos para fazer esta operação.

Ação Recomendada: Crie um grupo de recursos e conceda à identidade gerida do cofre de backup as permissões apropriadas no grupo de recursos da loja de dados instantânea. O grupo de recursos da loja de dados instantâneos é o local onde as imagens do disco são armazenadas. Consulte [a documentação](backup-managed-disks.md) para entender o que é o grupo de recursos, quais as permissões necessárias pela identidade gerida do cofre de reserva e como fornejá-la.

### <a name="error-code-usererrordiskbackupauthorizationfailed"></a>Código de erro: UserErrorDiskBackupAuthorizationFailed

Error Message: A identidade gerida pelo Cofre de Cópia de Segurança está a perder as permissões necessárias para fazer esta operação.

Ação recomendada: Conceda à identidade gerida do cofre de reserva as permissões apropriadas no disco a ser apoiada e no grupo de recursos da loja de dados instantânea onde as imagens são armazenadas. Consulte [a documentação](backup-managed-disks.md) para entender quais as permissões necessárias pela identidade gerida do cofre de reserva e como fornejá-la.

### <a name="error-code-usererrorsnapshotrgormsipermissionsnotpresent"></a>Código de erro: UserErrorSnapshotRGOrMSIPermissionsNotPresent

Error Message: Não foi possível executar a operação uma vez que o Grupo de Recursos da Loja de Dados Instantâneos já não existe. Ou, o Serviço de Backup Azure requer permissões adicionais no Grupo de Recursos da Loja de Dados Instantâneos para fazer esta operação.

Ação recomendada: Crie o grupo de recursos e conceda à identidade gerida do cofre de backup as permissões apropriadas no grupo de recursos da loja de dados instantânea. O grupo de recursos da loja de dados instantâneos é o local onde as imagens são armazenadas. Consulte [a documentação](backup-managed-disks.md) para entender o que é o grupo de recursos, quais as permissões necessárias pela identidade gerida do cofre de reserva e como fornejá-la.

### <a name="error-code-usererroroperationalstoreparametersnotprovided"></a>Código de erro: UserErrorOperationalStoreParametersNotProvided

Error Message: Não foi possível executar a operação uma vez que o parâmetro do Grupo de Recursos da Loja de Dados Instantâneos não é fornecido

Ação recomendada: Forneça o parâmetro do grupo de recursos da loja de dados instantânea. O grupo de recursos da loja de dados instantâneos é o local onde as imagens do disco são armazenadas. Para mais informações, consulte [a documentação.](backup-managed-disks.md)

### <a name="error-code-usererrorinvalidoperationalstoreresourcegroup"></a>Código de erro: UserErrorInvalidOperationalStoreResourceGroup

Error Message: Snapshot Data Store Resource Group fornecido é inválido

Ação Recomendada: Forneça um grupo de recursos válido para a loja de dados instantâneos. O grupo de recursos da loja de dados instantâneos é o local onde as imagens do disco são armazenadas. Para mais informações, consulte [a documentação.](backup-managed-disks.md)

### <a name="error-code-usererrordiskbackupdisktypenotsupported"></a>Código de erro: UserErrorDiskBackupDiskTypeNotSupported

Error Message: Tipo de disco não suportado

Ação recomendada: Consulte [a matriz de suporte](disk-backup-support-matrix.md) em cenários e limitações não suportados.

### <a name="error-code-usererrorsamenamediskalreadyexists"></a>Código de erro: UserErrorSameNameDiskAlreadyExists

Error Message: Não foi possível restaurar como um Disco com o mesmo nome já existe no grupo de recursos-alvo selecionado

Ação recomendada: Forneça um nome de disco diferente para restaurar. Para obter mais informações, consulte [Restaurar discos geridos Azure](restore-managed-disks.md).

### <a name="error-code-usererrorrestoretargetrgnotfound"></a>Código de erro: UserErrorRestoreTargetRGNotFound

Error Message: A operação falhou uma vez que o Grupo de Recursos-Alvo não existe.

Ação recomendada: Forneça um grupo de recursos válido para restaurar. Para obter mais informações, consulte [Restaurar discos geridos Azure](restore-managed-disks.md).

### <a name="error-code-usererrornotenoughpermissionontargetrg"></a>Código de erro: UserErrorNotEnoughPermissionOnTargetRG

Error Message: O Serviço de Backup Azure requer permissões adicionais no Grupo de Recursos Alvo para fazer esta operação.

Ação recomendada: Conceda à identidade gerida do cofre de reserva as permissões apropriadas no grupo de recursos-alvo. O grupo de recursos-alvo é o local selecionado onde o disco deve ser restaurado. Consulte a [documentação de restauro](restore-managed-disks.md) para entender quais as permissões necessárias pela identidade gerida do cofre de reserva e como forneca-la.

### <a name="error-code-usererrorsubscriptiondiskquotalimitreached"></a>Código de erro: UserErrorSubscriptionDiskQuotaLimitReached

Error Message: A operação falhou à medida que o limite máximo da quota de disco foi atingido na subscrição.

Ação recomendada: Consulte os limites de subscrição e serviço do [Azure e a documentação da quota](../azure-resource-manager/management/azure-subscription-service-limits.md) ou contacte o Microsoft Support para obter mais orientações.

### <a name="error-code-usererrordiskbackuprestorergormsipermissionsnotpresent"></a>Código de erro: UserErrorDiskBackupRestoreRGOrMSIPermissionsNotPresent

Error Message: A operação falhou uma vez que o Grupo de Recursos-Alvo não existe. Ou o Serviço de Backup Azure requer permissões adicionais no Grupo de Recursos Alvo para fazer esta operação.

Ação Recomendada: Forneça um grupo de recursos válido para restaurar e conceda à identidade gerida do cofre de reserva as permissões apropriadas no grupo de recursos-alvo. O grupo de recursos-alvo é o local selecionado onde o disco deve ser restaurado. Consulte a [documentação de restauro](restore-managed-disks.md) para entender quais as permissões necessárias pela identidade gerida do cofre de reserva e como forneca-la.

### <a name="error-code-usererrordeskeyvaultkeydisabled"></a>Código de erro: UserErrorDESKeyVaultKeyDisabled

Error Message: A chave do cofre utilizada para o conjunto de encriptação do disco não está ativada.

Ação recomendada: Ativar a chave do cofre utilizada para o conjunto de encriptação do disco. Consulte as limitações na [matriz de suporte.](disk-backup-support-matrix.md)

### <a name="error-code-usererrormsipermissionsnotpresentondes"></a>Código de Erro: UserErrorMSIPermissionsNotPresentOnDES

Error Message: O Serviço de Backup Azure necessita de permissão para aceder ao conjunto de encriptação do disco utilizado com o disco.

Ação recomendada: Fornecer ao leitor acesso à identidade gerida do cofre de reserva para o conjunto de encriptação do disco (DES).

### <a name="error-code-usererrordeskeyvaultkeynotavailable"></a>Código de erro: UserErrorDESKeyVaultKeyNotAvailable

Error Message: A chave do cofre utilizada para o conjunto de encriptação do disco não está disponível.

Ação recomendada: Certifique-se de que a chave do cofre utilizada para o conjunto de encriptação do disco não é desativada ou eliminada.

### <a name="error-code-usererrordisksnapshotnotfound"></a>Código de erro: UserErrorDiskSnapshotNotFound

Error Message: A imagem do disco para este ponto de restauro foi eliminada.

Ação recomendada: As imagens instantâneas são armazenadas no grupo de recursos da loja de dados instantâneos dentro da sua subscrição. É possível que o instantâneo relacionado com o ponto de restauro selecionado possa ter sido apagado ou movido deste grupo de recursos. Considere usar outro ponto de recuperação para restaurar. Além disso, siga as diretrizes recomendadas para a escolha do grupo de recursos Snapshot mencionado na [documentação de restauro](restore-managed-disks.md).

### <a name="error-code-usererrorsnapshotmetadatanotfound"></a>Código de erro: UserErrOrSnapshotMetadataNotFound

Error Message: Os metadados instantâneos do disco para este ponto de restauro foram eliminados

Ação recomendada: Considere utilizar outro ponto de recuperação para restaurar. Para mais informações, consulte a [documentação de restauro.](restore-managed-disks.md)

### <a name="error-code-backupagentpluginhostvalidateprotectionerror"></a>Código de erro: BackupAgentPluginHostValidateProtectionError

Error Message: A cópia de segurança do disco ainda não está disponível na região do Cofre de Cópia de Segurança, sob a qual está a ser tentada a Proteção de Configuração.

Ação recomendada: O Cofre de Apoio deve estar numa região apoiada por pré-visualização. Para disponibilidade da região consulte [a matriz de suporte.](disk-backup-support-matrix.md)

### <a name="error-code-usererrordppdatasourcealreadyhasbackupinstance"></a>Código de erro: UserErrorDppDatasourceAlreadyHasBackupInstance

Error Message: O disco que está a tentar configurar a cópia de segurança já está a ser protegido. O disco já está associado a uma instância de backup num cofre de reserva.

Ação recomendada: Este disco já está associado a uma instância de backup num cofre de reserva. Se quiser voltar a proteger este disco, elimine a instância de backup do cofre de reserva onde está atualmente protegido e re-proteja o disco em qualquer outro cofre.

### <a name="error-code-usererrordppdatasourcealreadyprotected"></a>Código de erro: UserErrorDppDatasourceAlreadyProtector

Error Message: O disco que está a tentar configurar a cópia de segurança já está a ser protegido. O disco já está associado a uma instância de backup num cofre de reserva.

Ação recomendada: Este disco já está associado a uma instância de backup num cofre de reserva. Se pretender voltar a proteger este disco, elimine a instância de cópia de segurança do cofre de reserva onde está atualmente protegido e re-proteja o disco em qualquer outro cofre.

### <a name="error-code-usererrormaxconcurrentoperationlimitreached"></a>Código de erro: UserErrorMaxConcurrentOperaçãoLimitada

Error Message: Não é possível iniciar a operação como número máximo de cópias de segurança simultâneas permitidas.

Ação recomendada: Aguarde até que a cópia de segurança anterior esteja concluída.

## <a name="next-steps"></a>Passos seguintes

- [Matriz de suporte da Cópia de Segurança de Discos do Azure](disk-backup-support-matrix.md)