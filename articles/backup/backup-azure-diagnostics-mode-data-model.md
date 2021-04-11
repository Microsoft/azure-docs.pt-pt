---
title: Azure Monitor regista modelo de dados
description: Neste artigo, conheça os dados do modelo de dados do Azure Monitor Log Analytics para os dados do Azure Backup.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 2682bf0483b38c50e64bf4ec5255c89f931ab0f0
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078915"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Registar modelo de dados do Analytics para dados de backup do Azure

Utilize o modelo de dados Log Analytics para criar alertas personalizados a partir do Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> * Este modelo de dados faz referência ao Modo Azure Diagnostics de enviar eventos de diagnóstico para Log Analytics (LA). Para conhecer o modelo de dados para o novo Modo Específico de Recurso, pode consultar o seguinte artigo: [Data Model for Azure Backup Diagnostic Events](./backup-azure-reports-data-model.md)
> * Para criar visualizações de [relatórios personalizados,](backup-reports-system-functions.md) recomenda-se a utilização de funções do sistema nos registos do Azure Monitor em vez de trabalhar com as tabelas em bruto listadas abaixo.

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de backup Azure

Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e dashboard de acordo com os seus requisitos.

### <a name="alert"></a>Alerta

Esta tabela fornece detalhes sobre campos relacionados com alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador único do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Texto |Estado do alerta, por exemplo, Ativo |
| AlertOccurrenceDateTime_s |Data/Hora |Data e hora quando o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, Critical |
|AlertTimeToResolveInMinutes_s    | Número        |Tempo tomado para resolver um alerta. Em branco para alertas ativos.         |
|AlertConsolidationStatus_s   |Texto         |Identifique se o alerta é um alerta consolidado ou não         |
|CountOfAlertsConsolidated_s     |Número         |Número de alertas consolidados se for um alerta consolidado          |
|AlertRaisedOn_s     |Texto         |Tipo de entidade em que o alerta é levantado         |
|AlertCode_s     |Texto         |Código para identificar de forma única um tipo de alerta         |
|RecommendedAction_s   |Texto         |Ação recomendada para resolver o alerta         |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de backup associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo **V2** |
| State_s |Texto |Estado atual do objeto de alerta, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para realizar backup, por exemplo, IaaSVM, FileFolder a que este alerta pertence |
| OperationName |Texto |Nome da operação atual, por exemplo, Alerta |
| Categoria |Texto |Categoria de dados de diagnóstico empurrados para registos do Monitor Azure. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador único do servidor protegido associado ao alerta (foi ProtectedServerUniqueId_s em V1)|
| VaultUniqueId_s |Texto |Identificador único do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador único para o recurso sobre os dados recolhidos. Por exemplo, um ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem

Esta tabela fornece detalhes sobre campos relacionados com artigos de backup.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| BackupItemId_s |Texto |Identificador de item de backup (Este campo é apenas para esquema v1) |
| BackupItemName_s |Texto |Nome do item de backup |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de backup |
| BackupItemType_s |Texto |Tipo de artigo de backup, por exemplo, VM, FileFolder |
| BackupItemProtectionState_s |Texto |Estado de proteção do item de reserva |
| BackupItemAppVersion_s |Texto |Versão de aplicação do item de backup |
| ProtectionState_s |Texto |Estado de proteção atual do item de backup, por exemplo, Protegido, ProtecçãoS cobertas |
| ProtectionGroupName_s |Texto | Nome do Grupo de Proteção O item de backup está protegido em, para SC DPM, e MABS, se aplicável|
| SecondaryBackupProtectionState_s |Texto |Se a proteção secundária está ativada para o item de backup|
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado do objeto de item de backup, por exemplo, Ative, Deleted |
| BackupManagementType_s |Texto |Tipo de fornecedor para realizar backup, por exemplo, IaaSVM, FileFolder a que este item de backup pertence |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de dados de diagnóstico empurrados para registos do Monitor Azure. Sempre AzureBackupReport |
| Recurso |Texto |Recurso para o qual os dados são recolhidos, por exemplo, nome do cofre dos Serviços de Recuperação |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |ID de recursos para dados recolhidos, por exemplo, ID de recursos de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre dos Serviços de Recuperação) para recolha de dados |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre dos Serviços de Recuperação) para recolha de dados |
| ResourceProvider |Texto |Fornecedor de recursos para dados recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados recolhidos, por exemplo, Cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation

Esta tabela fornece detalhes sobre associações de artigos de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |ID exclusivo do item de backup |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **V2.** |
| State_s |Texto |Estado atual do objeto de associação de artigos de backup, por exemplo, Ative, Deleted |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| BackupItemSourceSize_s |Texto | Tamanho frontal do item de backup |
| BackupManagementServerUniqueId_s |Texto | Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para o Log Analytics. É AzureBackupReport |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedContainerUniqueId_s |Texto |Identificador único do servidor protegido associado ao item de backup (foi ProtectedServerUniqueId_s em V1) |
| VaultUniqueId_s |Texto |Identificador único do cofre contendo o item de reserva |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre dos Serviços de Recuperação) para o qual os dados estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre dos Serviços de Recuperação) para o qual os dados estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para dados recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para dados está a ser recolhido, por exemplo, Cofres |

### <a name="backupmanagementserver"></a>BackupManagementServer

Esta tabela fornece detalhes sobre associações de artigos de backup com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
|BackupManagementServerName_s     |Texto         |Nome do Servidor de Gestão de Cópias de Segurança        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Agente de Backup Azure no Servidor de Gestão de Cópias de Segurança          |
|BackupManagementServerVersion_s     |Texto         |Versão do Servidor de Gestão de Cópias de Segurança|
|BackupManagementServerOSVersion_s    |Texto            |Versão OS do Servidor de Gestão de Cópias de Segurança|
|BackupManagementServerType_s     |Texto         |Tipo de Servidor de Gestão de Cópias de Segurança, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o Servidor de Gestão de Cópias de Segurança       |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (para ex. Cofre dos Serviços de Recuperação) para o qual os dados estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre dos Serviços de Recuperação) para o qual os dados estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para dados recolhidos, por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para dados está a ser recolhido, por exemplo, Cofres |

### <a name="job"></a>Tarefa

Esta tabela fornece detalhes sobre campos relacionados com o emprego.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V2** |
| State_s |Texto |Estado atual do objeto de trabalho, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Job |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador único do servidor protegido associou o trabalho |
| ProtectedContainerUniqueId_s |Texto | ID único para identificar o recipiente protegido em que o trabalho é executado |
| VaultUniqueId_s |Texto |Identificador único do cofre protegido |
| JobOperation_s |Texto |Operação para que trabalho é executado por exemplo, Backup, Restaurar, Configurar Backup |
| JobStatus_s |Texto |Estado do trabalho acabado, por exemplo, Concluído, Falhado |
| JobFailureCode_s |Texto |Cadeia de código de falha por causa da falha de trabalho |
| JobStartDateTime_s |Data/Hora |Data e hora quando o trabalho começou a correr |
| BackupStorageDestination_s |Texto |Destino do armazenamento de backup, por exemplo, Cloud, Disco  |
| AdHocOrScheduledJob_s |Texto | Campo para especificar se o trabalho é Ad Hoc ou Agendado |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Texto |ID único para identificar o trabalho |
| RecoveryJobDestination_s |Texto | Destino de um trabalho de recuperação, onde os dados são recuperados |
| RecoveryJobRPDateTime_s |DateTime | A data, hora em que o ponto de recuperação que está a ser recuperado foi criado |
| RecoveryJobRPLocation_s |Texto | O local onde o ponto de recuperação que está a ser recuperado foi armazenado|
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação|
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="policy"></a>Política

Esta tabela fornece detalhes sobre campos relacionados com políticas.

| Campo | Tipo de Dados | Versões Aplicáveis | Description |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema. É **V2.** |
| State_s |Texto ||Estado atual do objeto político, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - Política |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. É AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID único para identificar a política |
| PolicyName_s |Texto ||Nome da política definida |
| BackupFrequency_s |Texto ||Frequência com que os backups são executados, por exemplo, diariamente, semanalmente |
| BackupTimes_s |Texto ||Data e hora quando estão agendadas cópias de segurança |
| BackupDaysOfTheWeek_s |Texto ||Dias da semana em que os backups foram agendados |
| RetentionDuration_s |Número Inteiro ||Duração de retenção para backups configurados |
| DailyRetentionDuration_s |Número Inteiro ||Duração total da retenção em dias para cópias de segurança configuradas |
| DailyRetentionTimes_s |Texto ||Data e hora em que a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número Decimal ||Duração total da retenção semanal em semanas para backups configurados |
| WeeklyRetentionTimes_s |Texto ||Data e hora em que a retenção semanal é configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para retenção semanal |
| MonthlyRetentionDuration_s |Número Decimal ||Duração total da retenção em meses para cópias de segurança configuradas |
| MonthlyRetentionTimes_s |Texto ||Data e hora em que a retenção mensal é configurada |
| MonthlyRetentionFormat_s |Texto ||Tipo de configuração para retenção mensal, por exemplo, diária para base diurna, semanalmente para a semana |
| MonthlyRetentionDaysOfTheWeek_s |Texto ||Dias da semana selecionados para retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto ||Semanas do mês em que a retenção mensal é configurada, por exemplo, First, Last |
| YearlyRetentionDuration_s |Número Decimal ||Duração total da retenção em anos para cópias de segurança configuradas |
| YearlyRetentionTimes_s |Texto | | Data e hora em que a retenção anual é configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto | | Meses do ano selecionados para retenção anual |
| YearlyRetentionFormat_s |Texto ||Tipo de configuração para retenção anual, por exemplo, diária para base diurna, semanalmente para a semana |
| YearlyRetentionDaysOfTheMonth_s |Texto ||Datas do mês selecionadas para retenção anual |
| SynchronisationFrequencyPerDay_s |Número Inteiro |v2|Número de vezes num dia uma cópia de segurança de ficheiros é sincronizada para SC DPM e MABS |
| DiffBackupFormat_s |Texto |v2|Formato para backups diferenciais para SQL em backup VM Azure |
| DiffBackupTime_s |Hora |v2|Tempo para backups diferenciais para SQL em Backup VM Azure|
| DiffBackupRetentionDuration_s |Número Decimal |v2|Duração de retenção para backups diferenciais para SQL em Backup VM Azure|
| LogBackupFrequency_s |Número Decimal |v2|Frequência para backups de registo para SQL|
| LogBackupRetentionDuration_s |Número Decimal |v2|Duração de retenção para backups de registo para SQL em Backup VM Azure|
| DiffBackupDaysofTheWeek_s |Texto |v2|Dias da semana para backups diferenciais para SQL em Azure VM Backup|
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto ||Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto ||Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="policyassociation"></a>PolíticaAssociação

Esta tabela fornece detalhes sobre associações políticas com várias entidades.

| Campo | Tipo de Dados | Versões Aplicáveis | Description |
| --- | --- | --- | --- |
| EventName_s |Texto ||Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto ||Este campo denota a versão atual do esquema. É **V2.** |
| State_s |Texto ||Estado atual do objeto político, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto ||Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto ||Este campo representa o nome da operação atual - PolíticaAssociação |
| Categoria |Texto ||Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. É AzureBackupReport |
| Recurso |Texto ||Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto ||ID único para identificar a política |
| VaultUniqueId_s |Texto ||ID único do cofre a que esta política pertence |
| BackupManagementServerUniqueId_s |Texto |v2 |Campo para identificar exclusivamente o Servidor de Gestão de Cópia de Segurança O Item de Cópia de Segurança está protegido, se aplicável        |
| SourceSystem |Texto ||Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto ||Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto ||Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto ||Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto ||Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto ||Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="protected-container"></a>Recipiente Protegido

Esta tabela fornece campos básicos sobre recipientes protegidos. (Foi ProtegidoServer em v1)

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Texto | Campo para identificar exclusivamente um Recipiente Protegido |
| ProtectedContainerOSType_s |Texto |Tipo de SO do Recipiente Protegido |
| ProtectedContainerOSVersion_s |Texto |Versão SO do Recipiente Protegido |
| AgentVersion_s |Texto |Número de versão do Agente Backup ou do Agente de Proteção (No caso do SC DPM e MABS) |
| BackupManagementType_s |Texto |Tipo de fornecedor para realizar backup. Por exemplo, IaaSVM, FileFolder |
| EntityState_s |Texto |Estado atual do objeto do servidor protegido. Por exemplo, Ative, Eliminado |
| ProtectedContainerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| ProtectedContainerName_s |Texto |Nome do Recipiente Protegido |
| ProtectedContainerWorkloadType_s |Texto |Tipo de recipiente protegido apoiado. Por exemplo, IaaSVMContainer |
| ProtectedContainerLocation_s |Texto |Se o Contentor Protegido está localizado no local ou em Azure |
| ProtectedContainerType_s |Texto |Se o Recipiente Protegido é um servidor ou um recipiente |
| ProtectedContainerProtectionState_s'  |Texto |Estado de proteção do contentor protegido |

### <a name="storage"></a>Armazenamento

Esta tabela fornece detalhes sobre campos relacionados com armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número Decimal |Armazenamento de backup em nuvem utilizado por backups, calculado com base no valor mais recente (Este campo é apenas para esquema v1)|
| ProtectedInstances_s |Número Decimal |Número de casos protegidos utilizados para calcular o armazenamento de frontend na faturação, calculado com base no valor mais recente |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **V2.** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Ativo, Eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID único do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Texto |ID único do cofre para armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |
| StorageUniqueId_s |Texto |ID único usado para identificar a entidade de armazenamento |
| StorageType_s |Texto |Tipo de Armazenamento, por exemplo Cloud, Volume, Disco |
| StorageName_s |Texto |Nome da entidade de armazenamento, por exemplo E:\ |
| StorageTotalSizeInGBs_s |Texto |Tamanho total do armazenamento, em GB, consumido por entidade de armazenamento|

### <a name="storageassociation"></a>ArmazenamentoAssociação

Esta tabela fornece campos básicos relacionados com armazenamento que ligam o armazenamento a outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- |  --- |
| StorageUniqueId_s |Texto |ID único usado para identificar a entidade de armazenamento |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **V2.** |
| BackupItemUniqueId_s |Texto |ID exclusivo usado para identificar o item de backup relacionado com a entidade de armazenamento |
| BackupManagementServerUniqueId_s |Texto |ID exclusivo usado para identificar o servidor de gestão de backup relacionado com a entidade de armazenamento|
| VaultUniqueId_s |Texto |ID único usado para identificar o cofre relacionado com a entidade de armazenamento|
| StorageConsumedInMBs_s |Número|Tamanho do armazenamento consumido pelo item de backup correspondente no armazenamento correspondente |
| StorageAllocatedInMBs_s |Número |Tamanho do armazenamento atribuído pelo item de backup correspondente no armazenamento correspondente do tipo Disco|

### <a name="vault"></a>Cofre

Esta tabela fornece detalhes sobre campos relacionados com o cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento. É sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo denota a versão atual do esquema. É **V2.** |
| State_s |Texto |Estado atual do objeto do cofre, por exemplo, Ativo, Eliminado |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico empurrados para os registos do Monitor Azure. É AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão a ser recolhidos, mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Texto |ID único do cofre |
| VaultName_s |Texto |Nome do cofre |
| AzureDataCenter_s |Texto |Centro de dados onde o cofre está localizado |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recursos para recolha de dados. Por exemplo, ID de recurso de cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos Serviços de Recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para os quais os dados são recolhidos. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, Cofres |

### <a name="backup-management-server"></a>Servidor de gestão de backup

Esta tabela fornece campos básicos sobre servidores de gestão de cópias de segurança.

|Campo  |Tipo de Dados  | Descrição  |
|---------|---------|----------|
|BackupManagementServerName_s     |Texto         |Nome do Servidor de Gestão de Cópias de Segurança        |
|AzureBackupAgentVersion_s     |Texto         |Versão do Agente de Backup Azure no Servidor de Gestão de Cópias de Segurança          |
|BackupManagementServerVersion_s     |Texto         |Versão do Servidor de Gestão de Cópias de Segurança|
|BackupManagementServerOSVersion_s     |Texto            |Versão OS do Servidor de Gestão de Cópias de Segurança|
|BackupManagementServerType_s     |Texto         |Tipo de Servidor de Gestão de Cópias de Segurança, como MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Texto         |Campo para identificar exclusivamente o Servidor de Gestão de Cópias de Segurança       |

### <a name="preferredworkloadonvolume"></a>Trabalho Preferido SobreVolume

Esta tabela especifica a carga de trabalho(s) a volume a que está associada.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| StorageUniqueId_s |Texto |ID único usado para identificar a entidade de armazenamento |
| BackupItemType_s |Texto |As cargas de trabalho para as quais este volume é o armazenamento preferido|

### <a name="protectedinstance"></a>Proteção

Esta tabela fornece campos básicos protegidos relacionados com casos.

| Campo | Tipo de Dados |Versões Aplicáveis | Description |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Texto |v2|ID único usado para identificar o item de backup para VMs apoiado usando DPM, MABS|
| ProtectedContainerUniqueId_s |Texto |v2|ID único usado para identificar o recipiente protegido para tudo, exceto VMs apoiados usando DPM, MABS|
| ProtectedInstanceCount_s |Texto |v2|Contagem de instâncias protegidas para o item de backup associado ou recipiente protegido nessa data|

### <a name="recoverypoint"></a>Ponto de Recuperação

Esta tabela fornece campos básicos relacionados com pontos de recuperação.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| BackupItemUniqueId_s |Texto |ID único usado para identificar o item de backup para VMs apoiado usando DPM, MABS|
| OldestRecoveryPointTime_s |Texto |Data do ponto de recuperação mais antigo para o item de backup|
| OldestRecoveryPointLocation_s |Texto |Localização do ponto de recuperação mais antigo para o item de backup|
| LatestRecoveryPointTime_s |Texto |Data do último ponto de recuperação para o item de backup|
| LatestRecoveryPointLocation_s |Texto |Localização do último ponto de recuperação para o item de backup|

## <a name="sample-kusto-queries"></a>Amostra de Consultas Kusto

Abaixo estão algumas amostras para ajudá-lo a escrever consultas sobre dados de Backup Azure que residem na tabela Azure Diagnostics:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Todos os trabalhos de reserva falhados

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Todos os trabalhos de backup da Azure VM

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

- Todos os trabalhos de backup de registos sql bem sucedidos

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

- Todos os trabalhos bem sucedidos de agente de backup da Azure

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

## <a name="v1-schema-vs-v2-schema"></a>Esquema V1 vs V2

Anteriormente, os dados de diagnóstico do Agente de Backup Azure e da cópia de segurança Azure VM foram enviados para a tabela Azure Diagnostics num esquema referido como ***esquema V1** _. Posteriormente, novas colunas foram adicionadas para apoiar outros cenários e cargas de trabalho, e os dados de diagnóstico foram empurrados num novo esquema referido como _*_esquema V2_**.  

Por razões de retrocompatibilidade, os dados de diagnóstico do Agente de Backup Azure e da cópia de segurança Azure VM são atualmente enviados para a tabela Azure Diagnostics no esquema V1 e V2 (com o esquema V1 agora em rota de depreciação). Pode identificar quais os registos em Log Analytics que são de esquema V1 filtrando registos de SchemaVersion_s=="V1" nas suas consultas de registo.

Consulte a terceira coluna 'Descrição' no modelo de [dados](#using-azure-backup-data-model) acima descrito para identificar quais colunas pertencem apenas ao esquema V1.

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>Modificar as suas consultas para usar o esquema V2

Como o esquema V1 está em um caminho de depreciação, é recomendado usar apenas o esquema V2 em todas as suas consultas personalizadas sobre dados de diagnóstico de backup Azure. Abaixo está um exemplo de como atualizar as suas consultas para remover a dependência do esquema V1:

1. Identifique se a sua consulta está a usar qualquer campo que seja aplicável apenas ao esquema V1. Assuma que tem uma consulta para listar todos os itens de backup e os seus servidores protegidos associados da seguinte forma:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    A consulta acima utiliza o campo ProtectedServerUniqueId_s, que só é aplicável ao esquema V1. O equivalente ao esquema V2 deste campo é ProtectedContainerUniqueId_s (consulte as tabelas acima). O campo BackupItemUniqueId_s é aplicável mesmo ao esquema V2 e o mesmo campo pode ser utilizado nesta consulta.

2. Atualize a consulta para utilizar os nomes do campo de esquema V2. É uma prática recomendada para usar o filtro **onde SchemaVersion_s="V2"** em todas as suas consultas, de modo que apenas os registos correspondentes ao esquema V2 são analisados pela consulta:

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>Passos seguintes

Uma vez que reveja o modelo de dados, pode começar [a criar consultas personalizadas](../azure-monitor/visualize/tutorial-logs-dashboards.md) nos registos do Azure Monitor para construir o seu próprio dashboard.
