---
title: Modelo de dados de logs do Azure Monitor
description: Neste artigo, conheça os detalhes do modelo de dados Do Log Analytics do Azure Monitor para os dados de Backup Azure.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 121117d20f5b9eb6e53da1b8884557fcfb46ff76
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501007"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics modelo de dados para dados de backup do Azure

Utilize o modelo de dados Log Analytics para criar alertas personalizados a partir do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Este modelo de dados está em referência ao Modo de Diagnóstico Azure de enviar eventos de diagnóstico para Log Analytics (LA). Para conhecer o modelo de dados do novo Modo Específico de Recursos, pode consultar o seguinte artigo: Modelo de [dados para Eventos](https://aka.ms/diagnosticsdatamodel) de Diagnóstico de Backup Azure

## <a name="using-azure-backup-data-model"></a>Utilização do modelo de dados de backup Azure

Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e dashboard de acordo com os seus requisitos.

### <a name="alert"></a>Alerta

Esta tabela fornece detalhes sobre campos relacionados com alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador único do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Texto |Estado do alerta, por exemplo, Ativo |
| AlertOccurrenceDateTime_s |Data/hora |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, Crítico |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo necessário para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Texto         |Identifique se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se se trata de um alerta consolidado          |
|AlertRaisedOn_s     |Texto         |Tipo de entidade em que o alerta é levantado         |
|AlertCode_s     |Texto         |Código para identificar exclusivamente um tipo de alerta         |
|RecommendedAction_s   |Texto         |Ação recomendada para resolver o alerta         |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de reserva associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo **V2** |
| State_s |Texto |Estado atual do objeto de alerta, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para executar backup, por exemplo, IaaSVM, FileFolder a que este alerta pertence |
| OperationName |Texto |Nome da operação atual, por exemplo, Alerta |
| Categoria |Texto |Categoria de dados de diagnóstico saqueados para os registos do Monitor Azure. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador único do servidor protegido associado ao alerta (Foi ProtectedServerUniqueId_s em V1)|
| VaultUniqueId_s |Texto |Identificador único do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador único para o recurso sobre quais os dados recolhidos. Por exemplo, um ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre campos relacionados com itens de reserva.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador único do item de reserva |
| BackupItemId_s |Texto |Identificador de item de backup (Este campo é apenas para v1 schema) |
| BackupItemName_s |Texto |Nome do item de reserva |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de reserva |
| BackupItemType_s |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| BackupItemProtectionState_s |Texto |Estado de Proteção do Item de Backup |
| BackupItemAppVersion_s |Texto |Versão de aplicação do item de backup |
| ProtectionState_s |Texto |Estado de proteção atual do item de reserva, por exemplo, Protegido, ProtectionStopped |
| ProtectionGroupName_s |Texto | Nome do Grupo de Proteção o Item de Backup está protegido em, para SC DPM, e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Texto |Se a proteção secundária está ativada para o item de reserva|
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado do objeto de cópia de segurança, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para executar backup, por exemplo, IaaSVM, FileFolder a que este item de reserva pertence |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de dados de diagnóstico saqueados para os registos do Monitor Azure. Sempre AzureBackupReport |
| Recurso |Texto |Recurso para o qual os dados são recolhidos, por exemplo, nome do cofre dos Serviços de Recuperação |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |ID de recursos para dados que estão a ser recolhidos, por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre de Serviços de Recuperação) para dados que estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre de Serviços de Recuperação) para dados que estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para dados que estão a ser recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para dados que estão a ser recolhidos, por exemplo, Cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre associações de artigos de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificação única do item de reserva |
| SchemaVersion_s |Texto |Este campo denota versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto de associação de itens de backup, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Texto | Tamanho frontal do item de reserva |
| BackupManagementServerUniqueId_s |Texto | Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para log Analytics, é AzureBackupReport |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador único do servidor protegido associado ao item de backup (Foi ProtectedServerUniqueId_s em V1) |
| VaultUniqueId_s |Texto |Identificador único do cofre contendo o item de reserva |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre de Serviços de Recuperação) para os quais os dados estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre de Serviços de Recuperação) para os quais os dados estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para dados que estão a ser recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |O tipo de recurso para dados está a ser recolhido, por exemplo, cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre associações de artigos de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Texto         |Nome do Servidor de Gestão de Backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Agente de Backup Azure no Servidor de Gestão de Backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do Servidor de Gestão de Backup|
|BackupManagementServerOSVersion_s    |Texto            |Versão OS do Servidor de Gestão de Backup|
|BackupManagementServerType_s     |Texto         |Tipo de Servidor de Gestão de Backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o Servidor de Gestão de Backup       |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre de Serviços de Recuperação) para os quais os dados estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre de Serviços de Recuperação) para os quais os dados estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para dados que estão a ser recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |O tipo de recurso para dados está a ser recolhido, por exemplo, cofres |

### <a name="job"></a>Tarefa

Esta tabela fornece detalhes sobre campos relacionados com o trabalho.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de reserva |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado atual do objeto de trabalho, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Job |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador único do servidor protegido associou o trabalho |
| ProtectedContainerUniqueId_s |Texto | Identificação única para identificar o recipiente protegido em que o trabalho é executado |
| VaultUniqueId_s |Texto |Identificador único do cofre protegido |
| JobOperation_s |Texto |Operação para a qual o trabalho é executado, por exemplo, Backup, Restaurar, Configurar Backup |
| JobStatus_s |Texto |Estado do trabalho acabado, por exemplo, Concluído, Falhado |
| JobFailureCode_s |Texto |Cadeia de código de falha por causa da falha de emprego aconteceu |
| JobStartDateTime_s |Data/hora |Data e hora quando o trabalho começou a funcionar |
| BackupStorageDestination_s |Texto |Destino de armazenamento de backup, por exemplo, Cloud, Disk  |
| AdHocOrScheduledJob_s |Texto | Campo para especificar se o trabalho é Ad Hoc ou Agendado |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Texto |Identificação única para identificar o trabalho |
| RecoveryJobDestination_s |Texto | Destino de um trabalho de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |DateTime | A data, hora em que o ponto de recuperação que está sendo recuperado foi criado |
| RecoveryJobRPLocation_s |Texto | O local onde o ponto de recuperação que está a ser recuperado foi armazenado|
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação|
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre os domínios relacionados com as políticas.

| Campo | Tipo de Dados | Versões Aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota versão atual do esquema, é **V2** |
| State_s |Texto ||Estado atual do objeto político, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - Política |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure, é o AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||Id único para identificar a política |
| PolicyName_s |Texto ||Nome da política definida |
| BackupFrequency_s |Texto ||Frequência com que as cópias de segurança são executadas, por exemplo, diariamente, semanalmente |
| BackupTimes_s |Texto ||Data e hora quando os backups estão agendados |
| BackupDaysOfTheWeek_s |Texto ||Dias da semana em que os backups foram agendados |
| RetentionDuration_s |Número inteiro ||Duração da retenção para cópias de segurança configuradas |
| DailyRetentionDuration_s |Número inteiro ||Duração total da retenção em dias para cópias de segurança configuradas |
| DailyRetentionTimes_s |Texto ||Data e hora em que a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal ||Duração total da retenção semanal em semanas para cópias de segurança configuradas |
| WeeklyRetentionTimes_s |Texto ||Data e hora em que a retenção semanal é configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para retenção semanal |
| MonthlyRetentionDuration_s |Número decimal ||Duração total da retenção em meses para cópias de segurança configuradas |
| MonthlyRetentionTimes_s |Texto ||Data e hora em que a retenção mensal é configurada |
| MonthlyRetentionFormat_s |Texto ||Tipo de configuração para retenção mensal, por exemplo, diariamente para dia baseado, semanalmente para a semana |
| MonthlyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto ||Semanas do mês em que a retenção mensal é configurada, por exemplo, Primeiro, Último etc. |
| YearlyRetentionDuration_s |Número decimal ||Duração total da retenção em anos para cópias de segurança configuradas |
| YearlyRetentionTimes_s |Texto ||Data e hora em que a retenção anual é configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto ||Meses do ano selecionados para retenção anual |
| YearlyRetentionFormat_s |Texto ||Tipo de configuração para retenção anual, por exemplo, diariamente para dia baseado, semanalmente para a semana | |
| YearlyRetentionDaysOfTheMonth_s |Texto ||Datas do mês selecionadas para retenção anual |
| SynchronisationFrequencyPerDay_s |Número inteiro |v2|Número de vezes num dia uma cópia de segurança de ficheiros é sincronizada para SC DPM e MABS |
| DiffBackupFormat_s |Texto |v2|Formato para backups diferenciais para SQL em backup VM Azure |
| DiffBackupTime_s |Hora |v2|Tempo para backups diferenciais para SQL em Backup VM Azure|
| DiffBackupRetentionDuration_s |Número decimal |v2|Duração da retenção para backups diferenciais para SQL em Backup VM Azure|
| LogBackupFrequency_s |Número decimal |v2|Frequência para backups de registo para SQL|
| LogBackupRetentionDuration_s |Número decimal |v2|Duração da retenção para backups de registo para SQL em Backup VM Azure|
| DiffBackupDaysofTheWeek_s |Texto |v2|Dias da semana para backups diferenciais para SQL em Backup VM Azure|
| SourceSystem |Texto ||Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto ||Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto ||Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="policyassociation"></a>Associação política

Esta tabela fornece detalhes sobre associações políticas com várias entidades.

| Campo | Tipo de Dados | Versões Aplicáveis | Descrição |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota versão atual do esquema, é **V2** |
| State_s |Texto ||Estado atual do objeto político, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure, é o AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||Id único para identificar a política |
| VaultUniqueId_s |Texto ||Identificação única do cofre a que esta política pertence |
| BackupManagementServerUniqueId_s |Texto |v2 |Campo para identificar exclusivamente o Servidor de Gestão de Backup o Item de Backup está protegido através, se aplicável        |
| SourceSystem |Texto ||Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto ||Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto ||Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="protected-container"></a>Recipiente protegido

Esta tabela fornece campos básicos sobre recipientes protegidos. (Foi Protegido Server em v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texto | Campo para identificar exclusivamente um recipiente protegido |
| ProtectedContainerOSType_s |Texto |Tipo OS do recipiente protegido |
| ProtectedContainerOSVersion_s |Texto |Versão OS do recipiente protegido |
| AgentVersion_s |Texto |Número de versão de Agente Backup ou do Agente de Proteção (No caso de SC DPM e MABS) |
| BackupManagementType_s |Texto |Tipo de fornecedor para executar backup. Por exemplo, IaaSVM, FileFolder |
| EntityState_s |Texto |Estado atual do objeto de servidor protegido. Por exemplo, Ativo, Eliminado |
| ProtectedContainerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Texto |Nome do recipiente protegido |
| ProtectedContainerWorkloadType_s |Texto |Tipo de recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Texto |Se o contentor protegido está localizado no local ou em Azure |
| ProtectedContainerType_s |Texto |Se o recipiente protegido é um servidor, ou um recipiente |
| ProtectedContainerProtectionState_s’  |Texto |Estado de proteção do contentor protegido |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre campos relacionados com armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Armazenamento de backup em nuvem usado por backups, calculado com base no valor mais recente (Este campo é apenas para v1 schema)|
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas utilizadas para calcular o armazenamento frontend na faturação, calculada com base no valor mais recente |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor que faz trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificação única do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Texto |A identificação única do cofre para armazenamento é calculada |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |
| StorageUniqueId_s |Texto |Id único usado para identificar a entidade de armazenamento |
| StorageType_s |Texto |Tipo de Armazenamento, por exemplo Cloud, Volume, Disco |
| StorageName_s |Texto |Nome da entidade de armazenamento, por exemplo E:\ |
| StorageTotalSizeInGBs_s |Texto |Tamanho total do armazenamento, em GB, consumido por entidade de armazenamento|

### <a name="storageassociation"></a>StorageAssociation

Esta tabela fornece campos básicos relacionados com o armazenamento que ligam o armazenamento a outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Texto |Id único usado para identificar a entidade de armazenamento |
| SchemaVersion_s |Texto |Este campo denota versão atual do esquema, é **V2** |
| BackupItemUniqueId_s |Texto |Id único usado para identificar o item de backup relacionado com a entidade de armazenamento |
| BackupManagementServerUniqueId_s |Texto |ID único usado para identificar o servidor de gestão de backup relacionado com a entidade de armazenamento|
| VaultUniqueId_s |Texto |Identificação única usada para identificar o cofre relacionado com a entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho do armazenamento consumido pelo produto de reserva correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento atribuído pelo produto de reserva correspondente no armazenamento correspondente do tipo Disk|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre campos relacionados com o cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota versão atual do esquema, é **V2** |
| State_s |Texto |Estado atual do objeto do cofre, por exemplo, Ativo, Eliminado |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure, é o AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Texto |Identificação única do cofre |
| VaultName_s |Texto |Nome do cofre |
| AzureDataCenter_s |Texto |Centro de dados onde o cofre está localizado |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema fonte dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para dados que estão a ser recolhidos. Por exemplo, ID de recurso de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre de Serviços de Recuperação) para os quais os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="backup-management-server"></a>Servidor de gestão de backup

Esta tabela fornece campos básicos sobre servidores de gestão de backup.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagementServerName_s     |Texto         |Nome do Servidor de Gestão de Backup        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Agente de Backup Azure no Servidor de Gestão de Backup          |
|BackupManagementServerVersion_s     |Texto         |Versão do Servidor de Gestão de Backup|
|BackupManagementServerOSVersion_s     |Texto            |Versão OS do Servidor de Gestão de Backup|
|BackupManagementServerType_s     |Texto         |Tipo de Servidor de Gestão de Backup, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o Servidor de Gestão de Backup       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Este quadro especifica a carga de trabalho(s) a que um volume está associado.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Texto |Id único usado para identificar a entidade de armazenamento |
| BackupItemType_s |Texto |As cargas de trabalho para as quais este volume é o armazenamento preferido|

### <a name="protectedinstance"></a>ProtectedInstance

Esta tabela fornece campos básicos protegidos relacionados com instâncias.

| Campo | Tipo de Dados |Versões Aplicáveis | Descrição |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texto |v2|Id único usado para identificar o item de backup para VMs apoiado usando DPM, MABS|
| ProtectedContainerUniqueId_s |Texto |v2|Id único usado para identificar o recipiente protegido para tudo, exceto VMs apoiados usando DPM, MABS|
| ProtectedInstanceCount_s |Texto |v2|Contagem de instâncias protegidas para o item de reserva associado ou recipiente protegido nessa data-data|

### <a name="recoverypoint"></a>Ponto de recuperação

Esta tabela fornece campos básicos de recuperação relacionados com pontos de recuperação.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Texto |Id único usado para identificar o item de backup para VMs apoiado usando DPM, MABS|
| OldestRecoveryPointTime_s |Texto |Hora da data do ponto de recuperação mais antigo para o item de reserva|
| OldestRecoveryPointLocation_s |Texto |Localização do ponto de recuperação mais antigo para o item de reserva|
| LatestRecoveryPointTime_s |Texto |Data do último ponto de recuperação para o item de backup|
| LatestRecoveryPointLocation_s |Texto |Localização do último ponto de recuperação para o item de backup|

## <a name="sample-kusto-queries"></a>Amostra de Consultas Kusto

Abaixo estão algumas amostras para ajudá-lo a escrever consultas sobre os dados de Backup Azure que residem na tabela de Diagnósticos Azure:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Todos os trabalhos de apoio falhados

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Todos os empregos de backup azure vm bem-sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Todos os trabalhos de backup de log SQL bem sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Todos os empregos bem sucedidos do agente azure backup

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>Passos Seguintes

Assim que rever o modelo de dados, pode começar a [criar consultas personalizadas](../azure-monitor/learn/tutorial-logs-dashboards.md) em registos do Monitor Azure para construir o seu próprio dashboard.
