---
title: Modelo de dados de logs de Azure Monitor para o backup do Azure
description: Este artigo fala sobre detalhes do modelo de dados de logs de Azure Monitor para dados de backup do Azure.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466162"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados Log Analytics para dados de backup do Azure

Use o modelo de dados Log Analytics para criar alertas personalizados de Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de backup do Azure

Você pode usar os campos a seguir fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e painéis de acordo com seus requisitos.

### <a name="alert"></a>Alerta

Esta tabela fornece detalhes sobre os campos relacionados ao alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Text |Identificador exclusivo do alerta gerado |
| AlertType_s |Text |Tipo de alerta, por exemplo, backup |
| AlertStatus_s |Text |Status do alerta, por exemplo, ativo |
| AlertOccurrenceDateTime_s |Date/Time |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Text |Severidade do alerta, por exemplo, crítico |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo necessário para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Text         |Identificar se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se for um alerta consolidado          |
|AlertRaisedOn_s     |Text         |Tipo de entidade em que o alerta é gerado         |
|AlertCode_s     |Text         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Text         |Ação recomendada para resolver o alerta         |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de backup associado ao alerta |
| SchemaVersion_s |Text |Versão atual do esquema, por exemplo **v2** |
| State_s |Text |Estado atual do objeto de alerta, por exemplo, ativo, excluído |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, fileFolder ao qual este alerta pertence |
| OperationName |Text |Nome da operação atual, por exemplo, alerta |
| Category |Text |Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| ProtectedContainerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao alerta (was ProtectedServerUniqueId_s em v1)|
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador exclusivo do recurso sobre o qual os dados são coletados. Por exemplo, uma ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre os campos relacionados ao item de backup.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de backup |
| BackupItemId_s |Text |Identificador do item de backup (este campo é apenas para o esquema v1) |
| BackupItemName_s |Text |Nome do item de backup |
| BackupItemFriendlyName_s |Text |Nome amigável do item de backup |
| BackupItemType_s |Text |Tipo de item de backup, por exemplo, VM, fileFolder |
| BackupItemProtectionState_s |Text |Estado de proteção do item de backup |
| BackupItemAppVersion_s |Text |Versão do aplicativo do item de backup |
| ProtectionState_s |Text |Estado de proteção atual do item de backup, por exemplo, protegido, ProtectionStopped |
| ProtectionGroupName_s |Text | Nome do grupo de proteção no qual o item de backup está protegido, para SC DPM e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Text |Se a proteção secundária está habilitada para o item de backup|
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **v2** |
| State_s |Text |Estado do objeto de item de backup, por exemplo, ativo, excluído |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, fileFolder ao qual este item de backup pertence |
| OperationName |Text |Nome da operação, por exemplo, BackupItem |
| Category |Text |Categoria de dados de diagnóstico enviados por push para logs de Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Recurso para o qual os dados são coletados, por exemplo, nome do cofre de serviços de recuperação |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |ID de recurso para dados que estão sendo coletados, por exemplo, ID de recurso do cofre de serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para dados que estão sendo coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para dados que estão sendo coletados |
| ResourceProvider |Text |Provedor de recursos para dados que estão sendo coletados, por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |Tipo do recurso para dados que estão sendo coletados, por exemplo, cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre as associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome desse evento, sempre é AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |ID exclusiva do item de backup |
| SchemaVersion_s |Text |Este campo denota a versão atual do esquema, é **v2** |
| State_s |Text |Estado atual do objeto de associação de item de backup, por exemplo, ativo, excluído |
| BackupManagementType_s |Text |Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| BackupItemSourceSize_s |Text | Tamanho de front-end do item de backup |
| BackupManagementServerUniqueId_s |Text | Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push para Log Analytics, é AzureBackupReport |
| OperationName |Text |Este campo representa o nome da operação atual-BackupItemAssociation |
| Resource |Text |Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| ProtectedContainerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao item de backup (foi ProtectedServerUniqueId_s em v1) |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre que contém o item de backup |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados estão sendo coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados estão sendo coletados |
| ResourceProvider |Text |Provedor de recursos para dados que estão sendo coletados, por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |O tipo do recurso para dados está sendo coletado, por exemplo, cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre as associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de backup do Azure no servidor de gerenciamento de backup          |
|BackupManagementServerVersion_s     |Text         |Versão do servidor de gerenciamento de backup|
|BackupManagementServerOSVersion_s    |Text            |Versão do so do servidor de gerenciamento de backup|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados estão sendo coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados estão sendo coletados |
| ResourceProvider |Text |Provedor de recursos para dados que estão sendo coletados, por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |O tipo do recurso para dados está sendo coletado, por exemplo, cofres |

### <a name="job"></a>Tarefa

Esta tabela fornece detalhes sobre os campos relacionados ao trabalho.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de backup |
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **v2** |
| State_s |Text |Estado atual do objeto de trabalho, por exemplo, ativo, excluído |
| BackupManagementType_s |Text |Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| OperationName |Text |Este campo representa o nome da operação atual-trabalho |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor, é AzureBackupReport |
| Resource |Text |Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao trabalho |
| ProtectedContainerUniqueId_s |Text | ID exclusiva para identificar o contêiner protegido no qual o trabalho é executado |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido |
| JobOperation_s |Text |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, configurar backup |
| JobStatus_s |Text |Status do trabalho concluído, por exemplo, concluído, com falha |
| JobFailureCode_s |Text |Cadeia de caracteres de código de falha devido a uma falha de trabalho |
| JobStartDateTime_s |Date/Time |Data e hora em que o trabalho começou a ser executado |
| BackupStorageDestination_s |Text |Destino do armazenamento de backup, por exemplo, nuvem, disco  |
| AdHocOrScheduledJob_s |Text | Campo para especificar se o trabalho é ad hoc ou agendado |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Text |ID exclusiva para identificar o trabalho |
| RecoveryJobDestination_s |Text | Destino de um trabalho de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |DateTime | A data, a hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobRPLocation_s |Text | O local onde o ponto de recuperação que está sendo recuperado foi armazenado|
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação|
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre os campos relacionados à política.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome desse evento, sempre é AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo denota a versão atual do esquema, é **v2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, ativo, excluído |
| BackupManagementType_s |Text ||Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual-política |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor, é AzureBackupReport |
| Resource |Text ||Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Text ||ID exclusiva para identificar a política |
| PolicyName_s |Text ||Nome da política definida |
| BackupFrequency_s |Text ||Frequência com que os backups são executados, por exemplo, diariamente, semanalmente |
| BackupTimes_s |Text ||Data e hora em que os backups são agendados |
| BackupDaysOfTheWeek_s |Text ||Dias da semana em que os backups foram agendados |
| RetentionDuration_s |Número inteiro ||Duração da retenção para backups configurados |
| DailyRetentionDuration_s |Número inteiro ||Duração total de retenção em dias para backups configurados |
| DailyRetentionTimes_s |Text ||Data e hora em que a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal ||Duração da retenção semanal total em semanas para backups configurados |
| WeeklyRetentionTimes_s |Text ||Data e hora quando a retenção semanal é configurada |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionados para retenção semanal |
| MonthlyRetentionDuration_s |Número decimal ||Duração total de retenção em meses para backups configurados |
| MonthlyRetentionTimes_s |Text ||Data e hora quando a retenção mensal está configurada |
| MonthlyRetentionFormat_s |Text ||Tipo de configuração para retenção mensal, por exemplo, diária para o dia, semanal para a semana com base |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionados para retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Semanas do mês em que a retenção mensal é configurada, por exemplo, a primeira, a última, etc. |
| YearlyRetentionDuration_s |Número decimal ||Duração total de retenção em anos para backups configurados |
| YearlyRetentionTimes_s |Text ||Data e hora quando a retenção anual é configurada |
| YearlyRetentionMonthsOfTheYear_s |Text ||Meses do ano selecionados para retenção anual |
| YearlyRetentionFormat_s |Text ||Tipo de configuração para retenção anual, por exemplo, diária para o dia, semanal para a semana com base | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datas do mês selecionadas para retenção anual |
| SynchronisationFrequencyPerDay_s |Número inteiro |v2|Número de vezes em um dia em que um backup de arquivo é sincronizado para SC DPM e MABS |
| DiffBackupFormat_s |Text |v2|Formato para backups diferenciais para SQL no backup de VM do Azure |
| DiffBackupTime_s |Time |v2|Tempo para backups diferenciais para SQL no backup de VM do Azure|
| DiffBackupRetentionDuration_s |Número decimal |v2|Duração da retenção para backups diferenciais para SQL no backup de VM do Azure|
| LogBackupFrequency_s |Número decimal |v2|Frequência de backups de log para SQL|
| LogBackupRetentionDuration_s |Número decimal |v2|Duração da retenção para backups de log para SQL no backup de VM do Azure|
| DiffBackupDaysofTheWeek_s |Text |v2|Dias da semana para backups diferenciais para SQL no backup de VM do Azure|
| SourceSystem |Text ||Sistema de origem dos dados atuais-Azure |
| ResourceId |Text ||Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text ||Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text ||Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text ||Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |

### <a name="policyassociation"></a>PolicyAssociation

Esta tabela fornece detalhes sobre associações de política com várias entidades.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome desse evento, sempre é AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo denota a versão atual do esquema, é **v2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, ativo, excluído |
| BackupManagementType_s |Text ||Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual-PolicyAssociation |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor, é AzureBackupReport |
| Resource |Text ||Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Text ||ID exclusiva para identificar a política |
| VaultUniqueId_s |Text ||ID exclusiva do cofre ao qual essa política pertence |
| BackupManagementServerUniqueId_s |Text |v2 |Campo para identificar exclusivamente o servidor de gerenciamento de backup no qual o item de backup está protegido, se aplicável        |
| SourceSystem |Text ||Sistema de origem dos dados atuais-Azure |
| ResourceId |Text ||Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text ||Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text ||Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text ||Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text ||Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |

### <a name="protected-container"></a>Contêiner protegido

Esta tabela fornece campos básicos sobre contêineres protegidos. (Was ProtectedServer em v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo para identificar exclusivamente um contêiner protegido |
| ProtectedContainerOSType_s |Text |Tipo de so do contêiner protegido |
| ProtectedContainerOSVersion_s |Text |Versão do sistema operacional do contêiner protegido |
| AgentVersion_s |Text |Número de versão do backup do agente ou do agente de proteção (no caso do SC DPM e MABS) |
| BackupManagementType_s |Text |Tipo de provedor para executar o backup. Por exemplo, IaaSVM, fileFolder |
| EntityState_s |Text |Estado atual do objeto de servidor protegido. Por exemplo, ativo, excluído |
| ProtectedContainerFriendlyName_s |Text |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Text |Nome do contêiner protegido |
| ProtectedContainerWorkloadType_s |Text |Tipo de backup do contêiner protegido. Por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Se o contêiner protegido está localizado no local ou no Azure |
| ProtectedContainerType_s |Text |Se o contêiner protegido é um servidor ou um contêiner |
| ProtectedContainerProtectionState_s’  |Text |Estado de proteção do contêiner protegido |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre os campos relacionados ao armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Armazenamento de backup na nuvem usado pelos backups, calculados com base no valor mais recente (este campo é apenas para o esquema v1)|
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas usadas para calcular o armazenamento de front-end na cobrança, calculada com base no valor mais recente |
| EventName_s |Text |Este campo representa o nome desse evento, sempre é AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo denota a versão atual do esquema, é **v2** |
| State_s |Text |Estado atual do objeto de armazenamento, por exemplo, ativo, excluído |
| BackupManagementType_s |Text |Tipo de provedor para o servidor executando o trabalho de backup, por exemplo, IaaSVM, fileFolder |
| OperationName |Text |Este campo representa o nome da operação atual-armazenamento |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor, é AzureBackupReport |
| Resource |Text |Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Text |ID exclusiva do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Text |A ID exclusiva do cofre para armazenamento é calculada |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |
| StorageUniqueId_s |Text |ID exclusiva usada para identificar a entidade de armazenamento |
| StorageType_s |Text |Tipo de armazenamento, por exemplo, nuvem, volume, disco |
| StorageName_s |Text |Nome da entidade de armazenamento, por exemplo E:\ |
| StorageTotalSizeInGBs_s |Text |Tamanho total do armazenamento, em GB, consumido pela entidade de armazenamento|

### <a name="storageassociation"></a>StorageAssociation

Esta tabela fornece campos básicos relacionados ao armazenamento que conectam o armazenamento a outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Text |ID exclusiva usada para identificar a entidade de armazenamento |
| SchemaVersion_s |Text |Este campo denota a versão atual do esquema, é **v2** |
| BackupItemUniqueId_s |Text |ID exclusiva usada para identificar o item de backup relacionado à entidade de armazenamento |
| BackupManagementServerUniqueId_s |Text |ID exclusiva usada para identificar o servidor de gerenciamento de backup relacionado à entidade de armazenamento|
| VaultUniqueId_s |Text |ID exclusiva usada para identificar o cofre relacionado à entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento alocado pelo item de backup correspondente no armazenamento correspondente do tipo disco|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre os campos relacionados ao cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome desse evento, sempre é AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo denota a versão atual do esquema, é **v2** |
| State_s |Text |Estado atual do objeto do cofre, por exemplo, ativo, excluído |
| OperationName |Text |Este campo representa o nome do cofre de operações atual |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviados por push aos logs de Azure Monitor, é AzureBackupReport |
| Resource |Text |Esse é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos serviços de recuperação |
| VaultUniqueId_s |Text |ID exclusiva do cofre |
| VaultName_s |Text |Nome do cofre |
| AzureDataCenter_s |Text |Data Center onde o cofre está localizado |
| StorageReplicationType_s |Text |Tipo de replicação de armazenamento para o cofre, por exemplo, georedundante |
| SourceSystem |Text |Sistema de origem dos dados atuais-Azure |
| ResourceId |Text |Identificador de recurso para dados que estão sendo coletados. Por exemplo, ID de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de assinatura do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| GrupoRecursos |Text |Grupo de recursos do recurso (por exemplo, Cofre dos serviços de recuperação) para os quais os dados são coletados |
| ResourceProvider |Text |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft. Recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são coletados. Por exemplo, cofres |

### <a name="backup-management-server"></a>Servidor de gerenciamento de backup

Esta tabela fornece campos básicos sobre servidores de gerenciamento de backup.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nome do servidor de gerenciamento de backup        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de backup do Azure no servidor de gerenciamento de backup          |
|BackupManagmentServerVersion_s     |Text         |Versão do servidor de gerenciamento de backup|
|BackupManagmentServerOSVersion_s     |Text            |Versão do so do servidor de gerenciamento de backup|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gerenciamento de backup, como MABS, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gerenciamento de backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Esta tabela especifica as cargas de trabalho às quais um volume está associado.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Text |ID exclusiva usada para identificar a entidade de armazenamento |
| BackupItemType_s |Text |As cargas de trabalho para as quais este volume é o armazenamento preferencial|

### <a name="protectedinstance"></a>ProtectedInstance

Esta tabela fornece campos relacionados a instâncias protegidas básicas.

| Campo | Tipo de Dados |Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|ID exclusiva usada para identificar o contêiner protegido para tudo, exceto VMs com backup usando o DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Contagem de instâncias protegidas para o item de backup associado ou o contêiner protegido nessa data e hora|

### <a name="recoverypoint"></a>RecoveryPoint

Esta tabela fornece campos básicos relacionados ao ponto de recuperação.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |ID exclusiva usada para identificar o item de backup para VMs com backup usando o DPM, MABS|
| OldestRecoveryPointTime_s |Text |Data e hora do ponto de recuperação mais antigo para o item de backup|
| OldestRecoveryPointLocation_s |Text |Local do ponto de recuperação mais antigo para o item de backup|
| LatestRecoveryPointTime_s |Text |Data e hora do último ponto de recuperação para o item de backup|
| LatestRecoveryPointLocation_s |Text |Local do último ponto de recuperação para o item de backup|

## <a name="next-steps"></a>Passos Seguintes

Depois de examinar o modelo de dados, você pode começar a [criar consultas personalizadas](../azure-monitor/learn/tutorial-logs-dashboards.md) em logs de Azure monitor para criar seu próprio painel.
