---
title: Modelo de dados para eventos de diagnóstico de backup do Azure
description: Esse modelo de dados está em referência ao modo específico de recurso de envio de eventos de diagnóstico para Log Analytics (LA).
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 86416d0d74296069878aa7f33b549102a52ea488
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281080"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Modelo de dados para eventos de diagnóstico de backup do Azure

## <a name="coreazurebackup"></a>CoreAzureBackup

Esta tabela fornece informações sobre entidades de backup de núcleo, como cofres e itens de backup.

| **Campo**                         | **Tipo de dados** | **Descrição**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Texto          | Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação. |
| OperationName                     | Texto          | Este campo representa o nome da operação atual-BackupItem, BackupItemAssociation ou ProtectedContainer. |
| Categoria                          | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor. Por exemplo, CoreAzureBackup. |
| AgentVersion                      | Texto          | Número de versão do backup do agente ou do agente de proteção (no caso do SC DPM e MABS) |
| AzureBackupAgentVersion           | Texto          | Versão do agente de backup do Azure no servidor de gerenciamento de backup |
| AzureDataCenter                   | Texto          | Data Center em que o cofre está localizado                       |
| BackupItemAppVersion              | Texto          | Versão do aplicativo do item de backup                       |
| BackupItemFriendlyName            | Texto          | Nome amigável do item de backup                             |
| BackupItemName                    | Texto          | Nome do item de backup                                      |
| BackupItemProtectionState         | Texto          | Estado de proteção do item de backup                          |
| BackupItemFrontEndSize            | Texto          | Tamanho de front-end do item de backup                            |
| BackupItemType                    | Texto          | Tipo de item de backup. Por exemplo: VM, fileFolder             |
| BackupItemUniqueId                | Texto          | Identificador exclusivo do item de backup                         |
| BackupManagementServerType        | Texto          | Tipo de servidor de gerenciamento de backup, como em MABS, SC DPM     |
| BackupManagementServerUniqueId    | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup      |
| BackupManagementType              | Texto          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| BackupManagementServerName        | Texto          | Nome do servidor de gerenciamento de backup                         |
| BackupManagementServerOSVersion   | Texto          | Versão do so do servidor de gerenciamento de backup                   |
| BackupManagementServerVersion     | Texto          | Versão do servidor de gerenciamento de backup                      |
| LatestRecoveryPointLocation       | Texto          | Local do último ponto de recuperação para o item de backup    |
| LatestRecoveryPointTime           | DateTime      | Data e hora do último ponto de recuperação para o item de backup   |
| OldestRecoveryPointLocation       | Texto          | Local do ponto de recuperação mais antigo para o item de backup    |
| OldestRecoveryPointTime           | DateTime      | Data e hora do último ponto de recuperação para o item de backup   |
| PolicyUniqueId                    | Texto          | ID exclusiva para identificar a política                             |
| ProtectedContainerFriendlyName    | Texto          | Nome amigável do servidor protegido                        |
| ProtectedContainerLocation        | Texto          | Se o contêiner protegido está localizado no local ou no Azure |
| ProtectedContainerName            | Texto          | Nome do contêiner protegido                            |
| ProtectedContainerOSType          | Texto          | Tipo de so do contêiner protegido                          |
| ProtectedContainerOSVersion       | Texto          | Versão do sistema operacional do contêiner protegido                        |
| ProtectedContainerProtectionState | Texto          | Estado de proteção do contêiner protegido                  |
| ProtectedContainerType            | Texto          | Se o contêiner protegido é um servidor ou um contêiner  |
| ProtectedContainerUniqueId        | Texto          | ID exclusiva usada para identificar o contêiner protegido para tudo, exceto VMs com backup usando o DPM, MABS |
| ProtectedContainerWorkloadType    | Texto          | Tipo de backup do contêiner protegido. Por exemplo, IaaSVMContainer |
| NomeGrupoProteção               | Texto          | Nome do grupo de proteção no qual o item de backup está protegido, para SC DPM e MABS, se aplicável |
| ResourceGroupName                 | Texto          | Grupo de recursos do recurso (por exemplo, cofre dos serviços de recuperação) para dados que estão sendo coletados |
| SchemaVersion                     | Texto          | Este campo denota a versão atual do esquema, é **v2** |
| SecondaryBackupProtectionState    | Texto          | Se a proteção secundária está habilitada para o item de backup  |
| Estado                             | Texto          | Estado do objeto de item de backup. Por exemplo, ativo, excluído |
| StorageReplicationType            | Texto          | Tipo de replicação de armazenamento para o cofre. Por exemplo, georedundante |
| SubscriptionId                    | Texto          | Identificador de assinatura do recurso (por exemplo, cofre dos serviços de recuperação) para o qual os dados são coletados |
| VaultName                         | Texto          | Nome do cofre                                            |
| VaultTags                         | Texto          | Marcas associadas ao recurso do cofre                    |
| VaultUniqueId                     | Texto          | Identificador exclusivo do cofre                             |
| SourceSystem                      | Texto          | Sistema de origem dos dados atuais-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Esta tabela fornece detalhes sobre os campos relacionados ao alerta.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Nome da operação atual. Por exemplo, alerta            |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupAlerts |
| AlertCode                      | Texto          | Código para identificar exclusivamente um tipo de alerta                     |
| AlertConsolidationStatus       | Texto          | Identificar se o alerta é um alerta consolidado ou não         |
| AlertOccurrenceDateTime        | DateTime      | Data e hora em que o alerta foi criado                     |
| AlertRaisedOn                  | Texto          | Tipo de entidade em que o alerta é gerado                        |
| AlertSeverity                  | Texto          | Severidade do alerta. Por exemplo, crítico                 |
| AlertStatus                    | Texto          | Status do alerta. Por exemplo, ativo                     |
| AlertTimeToResolveInMinutes    | Número        | Tempo necessário para resolver um alerta. Em branco para alertas ativos.     |
| AlertType                      | Texto          | Tipo de alerta. Por exemplo, backup                           |
| AlertUniqueId                  | Texto          | Identificador exclusivo do alerta gerado                    |
| BackupItemUniqueId             | Texto          | Identificador exclusivo do item de backup associado ao alerta |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| CountOfAlertsConsolidated      | Número        | Número de alertas consolidados se for um alerta consolidado  |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| Recomendado              | Texto          | Ação recomendada para resolver o alerta                      |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **v2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, ativo, excluído |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusiva usada para identificar o cofre relacionado ao alerta    |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais-Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Esta tabela fornece campos relacionados a instâncias protegidas básicas.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Nome da operação, por exemplo ProtectedInstance         |
| Categoria                       | Texto          | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Texto          | ID exclusiva do item de backup                                 |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| ProtectedContainerUniqueId     | Texto          | ID exclusiva para identificar o contêiner protegido no qual o trabalho é executado |
| ProtectedInstanceCount         | Texto          | Contagem de instâncias protegidas para o item de backup associado ou o contêiner protegido nessa data e hora |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **v2**            |
| Estado                          | Texto          | Estado do objeto de item de backup, por exemplo, ativo, excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado à instância protegida |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais-Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Esta tabela fornece detalhes sobre os campos relacionados ao trabalho.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual-trabalho    |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Texto          | Campo para especificar se o trabalho é ad hoc ou agendado           |
| BackupItemUniqueId             | Texto          | ID exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId | Texto          | ID exclusiva usada para identificar o servidor de gerenciamento de backup relacionado à entidade de armazenamento |
| BackupManagementType           | Texto          | Tipo de provedor para executar o backup, por exemplo, IaaSVM, fileFolder ao qual este alerta pertence |
| DataTransferredInMB            | Número        | Dados transferidos em MB para este trabalho                          |
| JobDurationInSecs              | Número        | Duração total do trabalho em segundos                                |
| JobFailureCode                 | Texto          | Cadeia de caracteres de código de falha devido a uma falha de trabalho    |
| JobOperation                   | Texto          | Operação para a qual o trabalho é executado, por exemplo, backup, restauração, configurar backup |
| JobOperationSubType            | Texto          | Subtipo da operação de trabalho. Por exemplo, ' log ', no caso do trabalho de backup de log |
| JobStartDateTime               | DateTime      | Data e hora em que o trabalho começou a ser executado                       |
| Estado da Tarefa                      | Texto          | Status do trabalho concluído, por exemplo, concluído, com falha   |
| JobUniqueId                    | Texto          | ID exclusiva para identificar o trabalho                                |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| RecoveryJobDestination         | Texto          | Destino de um trabalho de recuperação, onde os dados são recuperados   |
| RecoveryJobRPDateTime          | DateTime      | A data, a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobLocation            | Texto          | O local onde o ponto de recuperação que está sendo recuperado foi armazenado |
| RecoveryLocationType           | Texto          | Tipo do local de recuperação                                |
| SchemaVersion                  | Texto          | Versão atual do esquema, por exemplo **v2**            |
| Estado                          | Texto          | Estado atual do objeto de alerta, por exemplo, ativo, excluído |
| VaultUniqueId                  | Texto          | Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais-Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Esta tabela fornece detalhes sobre os campos relacionados à política.

| **Campo**                       | **Tipo de dados**  | **Descrição**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Texto           | Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| OperationName                   | Texto           | Nome da operação, por exemplo, política ou PolicyAssociation |
| Categoria                        | Texto           | Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Texto           | Dias da semana em que os backups foram agendados            |
| BackupFrequency                 | Texto           | Frequência com que os backups são executados. Por exemplo, diário, semanal |
| BackupManagementType            | Texto           | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| BackupManagementServerUniqueId  | Texto           | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| Inatividade                     | Texto           | Data e hora em que os backups são agendados                     |
| DailyRetentionDuration          | Número inteiro   | Duração total de retenção em dias para backups configurados      |
| DailyRetentionTimes             | Texto           | Data e hora em que a retenção diária foi configurada            |
| DiffBackupDaysOfTheWeek         | Texto           | Dias da semana para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupFormat                | Texto           | Formato para backups diferenciais para SQL no backup de VM do Azure   |
| DiffBackupRetentionDuration     | Número decimal | Duração da retenção para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupTime                  | Hora           | Tempo para backups diferenciais para SQL no backup de VM do Azure     |
| LogBackupFrequency              | Número decimal | Frequência de backups de log para SQL                            |
| LogBackupRetentionDuration      | Número decimal | Duração da retenção para backups de log para SQL no backup de VM do Azure |
| MonthlyRetentionDaysOfTheMonth  | Texto           | Semanas do mês em que a retenção mensal é configurada.  Por exemplo, primeiro, último, etc. |
| MonthlyRetentionDaysOfTheWeek   | Texto           | Dias da semana selecionados para retenção mensal              |
| MonthlyRetentionDuration        | Texto           | Duração total de retenção em meses para backups configurados    |
| MonthlyRetentionFormat          | Texto           | Tipo de configuração para retenção mensal. Por exemplo, diariamente com base no dia, semanal para a semana com base |
| MonthlyRetentionTimes           | Texto           | Data e hora quando a retenção mensal está configurada           |
| MonthlyRetentionWeeksOfTheMonth | Texto           | Semanas do mês em que a retenção mensal é configurada.   Por exemplo, primeiro, último, etc. |
| PolicyName                      | Texto           | Nome da política definida                                   |
| PolicyUniqueId                  | Texto           | ID exclusiva para identificar a política                             |
| PolicyTimeZone                  | Texto           | Fuso horário no qual os campos de hora da política são especificados nos logs |
| RetentionDuration               | Texto           | Duração da retenção para backups configurados                    |
| RetentionType                   | Texto           | Tipo de retenção                                            |
| SchemaVersion                   | Texto           | Este campo denota a versão atual do esquema, é **v2** |
| Estado                           | Texto           | Estado atual do objeto de política. Por exemplo, ativo, excluído |
| SynchronisationFrequencyPerDay  | Número inteiro   | Número de vezes em um dia em que um backup de arquivo é sincronizado para SC DPM e MABS |
| VaultUniqueId                   | Texto           | ID exclusiva do cofre ao qual essa política pertence          |
| WeeklyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para retenção semanal               |
| WeeklyRetentionDuration         | Número decimal | Duração da retenção semanal total em semanas para backups configurados |
| WeeklyRetentionTimes            | Texto           | Data e hora quando a retenção semanal é configurada            |
| YearlyRetentionDaysOfTheMonth   | Texto           | Datas do mês selecionadas para retenção anual             |
| YearlyRetentionDaysOfTheWeek    | Texto           | Dias da semana selecionados para retenção anual               |
| YearlyRetentionDuration         | Número decimal | Duração total de retenção em anos para backups configurados     |
| YearlyRetentionFormat           | Texto           | Tipo de configuração para retenção anual, por exemplo, diária para o dia, semanal para a semana com base |
| YearlyRetentionMonthsOfTheYear  | Texto           | Meses do ano selecionados para retenção anual             |
| YearlyRetentionTimes            | Texto           | Data e hora quando a retenção anual é configurada            |
| YearlyRetentionWeeksOfTheMonth  | Texto           | Semanas do mês selecionadas para retenção anual             |
| SourceSystem                    | Texto           | Sistema de origem dos dados atuais-Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

Esta tabela fornece detalhes sobre os campos relacionados ao armazenamento.

| **Campo**                      | **Tipo de dados** | **Descrição**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Texto          | Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| OperationName                  | Texto          | Este campo representa o nome da operação atual-armazenamento ou StorageAssociation |
| Categoria                       | Texto          | Este campo representa a categoria de dados de diagnóstico enviados por push para logs de Azure Monitor-AddonAzureBackupStorage |
| BackupItemUniqueId             | Texto          | ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS |
| BackupManagementServerUniqueId | Texto          | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| BackupManagementType           | Texto          | Tipo de provedor para o servidor executando o trabalho de backup. Por exemplo, IaaSVM, fileFolder |
| PreferredWorkloadOnVolume      | Texto          | Carga de trabalho para a qual este volume é o armazenamento preferencial      |
| ProtectedContainerUniqueId     | Texto          | Identificador exclusivo do servidor protegido associado ao alerta |
| SchemaVersion                  | Texto          | Versão do esquema. Por exemplo, **v2**                   |
| Estado                          | Texto          | Estado do objeto de item de backup. Por exemplo, ativo, excluído |
| StorageAllocatedInMBs          | Número        | Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do tipo disco |
| StorageConsumedInMBs           | Número        | Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageName                    | Texto          | Nome da entidade de armazenamento. Por exemplo, E:\                      |
| StorageTotalSizeInGBs          | Texto          | Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento     |
| StorageType                    | Texto          | Tipo de armazenamento, por exemplo, nuvem, volume, disco             |
| StorageUniqueId                | Texto          | ID exclusiva usada para identificar a entidade de armazenamento                |
| VaultUniqueId                  | Texto          | ID exclusiva usada para identificar o cofre relacionado à entidade de armazenamento |
| VolumeFriendlyName             | Texto          | Nome amigável do volume de armazenamento                          |
| SourceSystem                   | Texto          | Sistema de origem dos dados atuais-Azure                    |

## <a name="next-steps"></a>Passos seguintes

- [Saiba como enviar dados de diagnóstico para Log Analytics](https://aka.ms/AA6il6r)