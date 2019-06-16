---
title: Modelo de dados do Monitor registos do Azure para o Azure Backup
description: Este fala de artigo sobre o Azure Monitor regista os detalhes do modelo de dados para dados de cópia de segurança do Azure.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61234977"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados do log Analytics para dados de cópia de segurança do Azure

Utilize o modelo de dados do Log Analytics para criar alertas personalizados a partir do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de cópia de segurança do Azure

Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar elementos visuais, consultas personalizadas e dashboards de acordo com os seus requisitos.

### <a name="alert"></a>Alerta

Esta tabela fornece detalhes sobre os campos de alertas relacionados.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Text |Identificador exclusivo do alerta gerado |
| AlertType_s |Text |Tipo de alerta, por exemplo, cópia de segurança |
| AlertStatus_s |Text |Estado do alerta, por exemplo, Active Directory |
| AlertOccurrenceDateTime_s |Data/Hora |Data e hora de criação de alerta |
| AlertSeverity_s |Text |Gravidade do alerta, por exemplo, crítico |
|AlertTimeToResolveInMinutes_s    | Number        |Tempo necessário para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Text         |Identificar se o alerta é um alerta de consolidada ou não         |
|CountOfAlertsConsolidated_s     |Number         |Número de alertas consolidados se se trata de um alerta de consolidado          |
|AlertRaisedOn_s     |Text         |Tipo de entidade que é gerado o alerta no         |
|AlertCode_s     |Text         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Text         |Ação recomendada para resolver o alerta         |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de cópia de segurança associado ao alerta |
| SchemaVersion_s |Text |Versão atual do esquema, por exemplo **V2** |
| State_s |Text |Estado atual do objeto alerta, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text |Tipo de fornecedor para a execução de FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este alerta pertence a |
| OperationName |Text |Nome da operação atual, por exemplo, alerta |
| Category |Text |Categoria de dados de diagnóstico enviados por push para os registos do Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao alerta |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador exclusivo para o recurso sobre quais dados são recolhidos. Por exemplo, um id de recurso de cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre os campos relacionados com o item de cópia de segurança.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de cópia de segurança |
| BackupItemId_s |Text |Identificador do item de cópia de segurança |
| BackupItemName_s |Text |Nome do item de cópia de segurança |
| BackupItemFriendlyName_s |Text |Nome amigável de item de cópia de segurança |
| BackupItemType_s |Text |Tipo de item de cópia de segurança, por exemplo, a VM, FileFolder |
| ProtectionState_s |Text |Estado de proteção atual do item de cópia de segurança, por exemplo, protegido, ProtectionStopped |
| ProtectionGroupName_s |Text | Nome do grupo de proteção do Item de cópia de segurança está protegido, do DPM de SC e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Text |Se a proteção secundária está ativada para o item de cópia de segurança|
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **V2** |
| State_s |Text |Estado do objeto de item de cópia de segurança, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text |Tipo de fornecedor para a execução de FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este item de cópia de segurança pertence a |
| OperationName |Text |Nome da operação, por exemplo, BackupItem |
| Category |Text |Categoria de dados de diagnóstico enviados por push para os registos do Azure Monitor. Sempre AzureBackupReport |
| Resource |Text |Recursos para que os dados são recolhidos, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Id de recurso para os dados recolhidos, por exemplo, recuperação cofre dos serviços de id de recurso |
| SubscriptionId |Text |Identificador de subscrição do recurso (para ex. Cofre dos serviços de recuperação) para dados que está a ser recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (para ex. Cofre dos serviços de recuperação) para dados que está a ser recolhidos |
| ResourceProvider |Text |Fornecedor de recursos de dados sendo coletados, por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para os dados sendo coletados, por exemplo, cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre as associações de item de cópia de segurança com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |Id exclusivo do item de cópia de segurança |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto de associação item de cópia de segurança, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Text | Tamanho front-end do item de cópia de segurança |
| BackupManagementServerUniqueId_s |Text | Campo para identificar exclusivamente o servidor de gestão de cópia de segurança do Item de cópia de segurança está protegido, se aplicável |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviadas para o Log Analytics, é AzureBackupReport |
| OperationName |Text |Este campo representa o nome da operação atual - BackupItemAssociation |
| Resource |Text |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Text |Identificador exclusivo para a política associada ao item de cópia de segurança |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associado ao item de cópia de segurança |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre que contenha o item de cópia de segurança |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de subscrição do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceProvider |Text |Fornecedor de recursos de dados sendo coletados, por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para os dados é recolhido, por exemplo, os cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre as associações de item de cópia de segurança com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |Nome do servidor de gestão de cópia de segurança        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de cópia de segurança do Azure no servidor de gestão de cópia de segurança          |
|BackupManagementServerVersion_s     |Text         |Versão do servidor de gestão de cópia de segurança|
|BackupManagementServerOSVersion_s    |Text            |Versão do SO do servidor de gestão de cópia de segurança|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gestão de cópia de segurança, como o DPM MABS, SC|
|BackupManagementServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gestão de cópia de segurança       |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de subscrição do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceProvider |Text |Fornecedor de recursos de dados sendo coletados, por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para os dados é recolhido, por exemplo, os cofres |

### <a name="job"></a>Tarefa

Esta tabela fornece detalhes sobre os campos relacionados com tarefas.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |Identificador exclusivo do item de cópia de segurança |
| SchemaVersion_s |Text |Versão do esquema, por exemplo, **V2** |
| State_s |Text |Estado atual do objeto tarefa, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Text |Este campo representa o nome da operação atual - tarefa |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Resource |Text |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Text |Identificador exclusivo do servidor protegido associados a tarefa |
| ProtectedContainerUniqueId_s |Text | Id exclusivo para identificar o contentor protegido, que a tarefa é executada em |
| VaultUniqueId_s |Text |Identificador exclusivo do cofre protegido |
| JobOperation_s |Text |Operação para o qual tarefa é executada por exemplo, cópia de segurança, restauro, configurar a cópia de segurança |
| JobStatus_s |Text |Estado do trabalho concluído, por exemplo, concluído, com falhas |
| JobFailureCode_s |Text |Cadeia de caracteres do código de falha devido a que ocorreu a falha da tarefa |
| JobStartDateTime_s |Data/Hora |Data e hora quando a tarefa em execução iniciada |
| BackupStorageDestination_s |Text |Destino de cópias de segurança, por exemplo, na Cloud, disco  |
| AdHocOrScheduledJob_s |Text | Campo para especificar se a tarefa é Ad Hoc ou agendada |
| JobDurationInSecs_s | Number |Duração total da tarefa em segundos |
| DataTransferredInMB_s | Number |Dados transferidos em MB para esta tarefa|
| JobUniqueId_g |Text |Id exclusivo para identificar a tarefa |
| RecoveryJobDestination_s |Text | Destino de uma tarefa de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |DateTime | A data, hora de criação do ponto de recuperação que está a ser recuperado |
| RecoveryJobRPLocation_s |Text | A localização onde o ponto de recuperação que está a ser recuperado foi armazenado|
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação|
| SubscriptionId |Text |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre os campos relacionados com a política.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text ||Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual - política |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Resource |Text ||Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Text ||Id exclusivo para identificar a política |
| PolicyName_s |Text ||Nome da política definida |
| BackupFrequency_s |Text ||Frequência com que as cópias de segurança são executadas, por exemplo, diariamente, semanalmente |
| BackupTimes_s |Text ||Data e hora quando estão agendadas cópias de segurança |
| BackupDaysOfTheWeek_s |Text ||Dias da semana quando tiverem sido agendadas as cópias de segurança |
| RetentionDuration_s |Número inteiro ||Duração da retenção de cópias de segurança configuradas |
| DailyRetentionDuration_s |Número inteiro ||Duração total de retenção em dias para cópias de segurança configurados |
| DailyRetentionTimes_s |Text ||Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal ||Duração da retenção semanal total em semanas para cópias de segurança configuradas |
| WeeklyRetentionTimes_s |Text ||Data e hora quando estiver configurada retenção semanal |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionada para a retenção semanal |
| MonthlyRetentionDuration_s |Número decimal ||Duração da retenção total nos meses para cópias de segurança configuradas |
| MonthlyRetentionTimes_s |Text ||Data e hora quando estiver configurada retenção mensal |
| MonthlyRetentionFormat_s |Text ||Tipo de configuração para uma retenção mensal, por exemplo, diária, dia, com base, semanalmente por semana com base em |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Dias da semana selecionada para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Semanas do mês quando retenção mensal estiver configurada, por exemplo, primeiro, último etc. |
| YearlyRetentionDuration_s |Número decimal ||Duração da retenção total nos anos para cópias de segurança configurados |
| YearlyRetentionTimes_s |Text ||Data e hora quando estiver configurada retenção anual |
| YearlyRetentionMonthsOfTheYear_s |Text ||Meses do ano selecionado para a retenção anual |
| YearlyRetentionFormat_s |Text ||Tipo de configuração de retenção anual, por exemplo, diária, dia, com base, semanalmente por semana com base em | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Datas do mês selecionado para a retenção anual |
| SynchronisationFrequencyPerDay_s |Número inteiro |v2|Número de vezes que um dia de que um backup de arquivos está sincronizado do DPM de SC e MABS |
| DiffBackupFormat_s |Text |v2|Formato para cópias de segurança diferenciais para o SQL na cópia de segurança de VM do Azure |
| DiffBackupTime_s |Hora |v2|Tempo para cópias de segurança diferenciais do SQL no Azure VM Backup|
| DiffBackupRetentionDuration_s |Número decimal |v2|Duração da retenção de cópias de segurança diferenciais do SQL no Azure VM Backup|
| LogBackupFrequency_s |Número decimal |v2|Frequência de cópias de segurança do registo de SQL|
| LogBackupRetentionDuration_s |Número decimal |v2|Duração da retenção de cópias de segurança do registo do SQL no Azure VM Backup|
| DiffBackupDaysofTheWeek_s |Text |v2|Dias da semana para cópias de segurança diferenciais do SQL no Azure VM Backup|
| SourceSystem |Text ||Sistema de origem de dados atuais - Azure |
| ResourceId |Text ||Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text ||Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text ||Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text ||Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="policyassociation"></a>PolicyAssociation

Esta tabela fornece detalhes sobre associações de política com várias entidades.

| Campo | Tipo de Dados | Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Text ||Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text ||Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text ||Estado atual do objeto de política, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text ||Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Text ||Este campo representa o nome da operação atual - PolicyAssociation |
| Category |Text ||Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Resource |Text ||Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Text ||Id exclusivo para identificar a política |
| VaultUniqueId_s |Text ||Id exclusivo do cofre ao qual esta política pertence a |
| BackupManagementServerUniqueId_s |Text |v2 |Campo para identificar exclusivamente o servidor de gestão de cópia de segurança do Item de cópia de segurança está protegido, se aplicável        |
| SourceSystem |Text ||Sistema de origem de dados atuais - Azure |
| ResourceId |Text ||Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text ||Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text ||Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text ||Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="protected-container"></a>Contentor protegido

Esta tabela fornece campos básicos sobre contentores protegidos. (Era ProtectedServer no v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Campo para identificar exclusivamente um contentor protegido |
| ProtectedContainerOSType_s |Text |Tipo de SO do contentor protegido |
| ProtectedContainerOSVersion_s |Text |Versão do SO do contentor protegido |
| AgentVersion_s |Text |Número de versão de agente de cópia de segurança ou o agente de proteção (no caso de SC DPM e MABS) |
| BackupManagementType_s |Text |Tipo de fornecedor para a execução de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| EntityState_s |Text |Estado atual do objeto de servidor protegido, por exemplo, Active Directory, eliminado |
| ProtectedContainerFriendlyName_s |Text |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Text |Nome do contentor protegido |
| ProtectedContainerWorkloadType_s |Text |Tipo do contentor protegido backup IaaSVMContainer por exemplo, |
| ProtectedContainerLocation_s |Text |Se o contentor protegido está localizada no local ou no Azure |
| ProtectedContainerType_s |Text |Se o contentor protegido é um servidor ou um contentor |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre os campos relacionados com o armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |O armazenamento de cópia de segurança na cloud utilizados pelas cópias de segurança, calculadas com base no valor mais recente |
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas usada para calcular o armazenamento de front-end na faturação, calculada com base no valor mais recente |
| EventName_s |Text |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto de armazenamento, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Text |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Text |Este campo representa o nome da operação atual - armazenamento |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Resource |Text |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Text |Id exclusivo do servidor protegido para o qual armazenamento é calculado |
| VaultUniqueId_s |Text |Id exclusivo do cofre para o armazenamento é calculado |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="storageassociation"></a>StorageAssociation

Esta tabela fornece campos básicos relacionados com o armazenamento ao armazenamento de outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Text |Id exclusivo utilizado para identificar a entidade de armazenamento |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| BackupItemUniqueId_s |Text |Id exclusivo utilizado para identificar o item de cópia de segurança relacionados com a entidade de armazenamento |
| BackupManagementServerUniqueId_s |Text |Id exclusivo utilizado para identificar o servidor de gestão de cópia de segurança relacionados com a entidade de armazenamento|
| VaultUniqueId_s |Text |Id exclusivo utilizado para identificar o Cofre relacionados com a entidade de armazenamento|
| StorageConsumedInMBs_s |Number|Tamanho de armazenamento consumido pelo item de cópia de segurança correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Number |Tamanho de armazenamento alocado pelo item de cópia de segurança correspondente no armazenamento correspondente do tipo de disco|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre os campos relacionados com o cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Text |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Text |Este campo indica a versão atual do esquema, é **V2** |
| State_s |Text |Estado atual do objeto do cofre, por exemplo, Active Directory, eliminado |
| OperationName |Text |Este campo representa o nome da operação atual - Cofre |
| Category |Text |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Resource |Text |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| VaultUniqueId_s |Text |Id exclusivo do Cofre |
| VaultName_s |Text |Nome do Cofre |
| AzureDataCenter_s |Text |Onde está localizado o Cofre de centro de dados |
| StorageReplicationType_s |Text |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Text |Sistema de origem de dados atuais - Azure |
| ResourceId |Text |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Text |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Text |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Text |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Text |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="backup-management-server"></a>Servidor de gestão de cópia de segurança

Esta tabela fornece campos básicos sobre servidores de gestão de cópia de segurança.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |Nome do servidor de gestão de cópia de segurança        |
|AzureBackupAgentVersion_s     |Text         |Versão do agente de cópia de segurança do Azure no servidor de gestão de cópia de segurança          |
|BackupManagmentServerVersion_s     |Text         |Versão do servidor de gestão de cópia de segurança|
|BackupManagmentServerOSVersion_s     |Text            |Versão do SO do servidor de gestão de cópia de segurança|
|BackupManagementServerType_s     |Text         |Tipo de servidor de gestão de cópia de segurança, como o DPM MABS, SC|
|BackupManagmentServerUniqueId_s     |Text         |Campo para identificar exclusivamente o servidor de gestão de cópia de segurança       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Esta tabela especifica workload(s) que um Volume está associado.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Text |Id exclusivo utilizado para identificar a entidade de armazenamento |
| BackupItemType_s |Text |As cargas de trabalho para o qual este volume é o armazenamento preferido|

### <a name="protectedinstance"></a>ProtectedInstance

Esta tabela fornece campos relacionados do básico de instâncias protegidas.

| Campo | Tipo de Dados |Versões aplicáveis | Descrição |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Id exclusivo utilizado para identificar o item de cópia de segurança para VMs feita com o DPM, MABS|
| ProtectedContainerUniqueId_s |Text |v2|Id exclusivo utilizado para identificar o contentor protegido para tudo, exceto as VMs feita com o DPM, MABS|
| ProtectedInstanceCount_s |Text |v2|Contagem de instâncias protegidas para o item de cópia de segurança associado ou um contentor protegido nessa data e hora|

### <a name="recoverypoint"></a>RecoveryPoint

Esta tabela fornece recuperação básica do ponto de campos relacionados.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Id exclusivo utilizado para identificar o item de cópia de segurança para VMs feita com o DPM, MABS|
| OldestRecoveryPointTime_s |Text |Data hora do ponto de recuperação mais antigo para o item de cópia de segurança|
| OldestRecoveryPointLocation_s |Text |Localização do ponto de recuperação mais antigo para o item de cópia de segurança|
| LatestRecoveryPointTime_s |Text |Data hora do último ponto de recuperação para o item de cópia de segurança|
| LatestRecoveryPointLocation_s |Text |Localização do ponto de recuperação mais recente para o item de cópia de segurança|

## <a name="next-steps"></a>Passos Seguintes

Após analisar o modelo de dados, pode começar [criar consultas personalizadas](../azure-monitor/learn/tutorial-logs-dashboards.md) nos logs de Monitor do Azure para criar seu próprio dashboard.
