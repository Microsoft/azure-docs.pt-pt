---
title: Modelo de dados do Monitor registos do Azure para o Azure Backup
description: Este fala de artigo sobre o Azure Monitor regista os detalhes do modelo de dados para dados de cópia de segurança do Azure.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.openlocfilehash: b17e7548a19543add6274243d64ede3b61544c52
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819570"
---
# <a name="azure-monitor-logs-data-model-for-azure-backup-data"></a>O Azure Monitor faz o modelo de dados para dados de cópia de segurança do Azure
Utilize o modelo de dados de registos do Azure Monitor para criar relatórios. Com o modelo de dados, pode criar consultas personalizadas e dashboards ou personalizar os dados de cópia de segurança do Azure, no entanto, o que quiser.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de cópia de segurança do Azure
Pode utilizar os seguintes campos fornecidos como parte do modelo de dados para criar elementos visuais, consultas personalizadas e dashboards de acordo com os seus requisitos.

### <a name="alert"></a>Alerta
Esta tabela fornece detalhes sobre os campos de alertas relacionados.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador exclusivo do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, cópia de segurança |
| AlertStatus_s |Texto |Estado do alerta, por exemplo, Active Directory |
| AlertOccurrenceDateTime_s |Data/Hora |Data e hora de criação de alerta |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, crítico |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item de cópia de segurança associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo **V1** |
| State_s |Texto |Estado atual do objeto alerta, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para a execução de FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este alerta pertence a |
| OperationName |Texto |Nome da operação atual, por exemplo, alerta |
| Categoria |Texto |Categoria de dados de diagnóstico enviados por push para os registos do Azure Monitor. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao alerta |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador exclusivo para o recurso sobre quais dados são recolhidos. Por exemplo, um id de recurso de cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="backupitem"></a>BackupItem
Esta tabela fornece detalhes sobre os campos relacionados com o item de cópia de segurança.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item de cópia de segurança |
| BackupItemId_s |Texto |Identificador do item de cópia de segurança |
| BackupItemName_s |Texto |Nome do item de cópia de segurança |
| BackupItemFriendlyName_s |Texto |Nome amigável de item de cópia de segurança |
| BackupItemType_s |Texto |Tipo de item de cópia de segurança, por exemplo, a VM, FileFolder |
| ProtectedServerName_s |Texto |Nome do servidor protegido para o qual o item de cópia de segurança pertence a |
| ProtectionState_s |Texto |Estado de proteção atual do item de cópia de segurança, por exemplo, protegido, ProtectionStopped |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V1** |
| State_s |Texto |Estado do objeto de item de cópia de segurança, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para a execução de FileFolder de cópia de segurança, por exemplo, IaaSVM, para que este item de cópia de segurança pertence a |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de dados de diagnóstico enviados por push para os registos do Azure Monitor. Sempre AzureBackupReport |
| Recurso |Texto |Recursos para que os dados são recolhidos, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Id de recurso para os dados recolhidos, por exemplo, recuperação cofre dos serviços de id de recurso |
| SubscriptionId |Texto |Identificador de subscrição do recurso (para ex. Cofre dos serviços de recuperação) para dados que está a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre dos serviços de recuperação) para dados que está a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos de dados sendo coletados, por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para os dados sendo coletados, por exemplo, cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation
Esta tabela fornece detalhes sobre as associações de item de cópia de segurança com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Id exclusivo do item de cópia de segurança |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação item de cópia de segurança, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Identificador exclusivo para a política associada ao item de cópia de segurança |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao item de cópia de segurança |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre que contenha o item de cópia de segurança |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (para ex. Cofre dos serviços de recuperação) para o qual os dados estão a ser recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos de dados sendo coletados, por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para os dados é recolhido, por exemplo, os cofres |

### <a name="job"></a>Tarefa
Esta tabela fornece detalhes sobre os campos relacionados com tarefas.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item de cópia de segurança |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V1** |
| State_s |Texto |Estado atual do objeto tarefa, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - tarefa |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associados a tarefa |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido |
| JobOperation_s |Texto |Operação para o qual tarefa é executada por exemplo, cópia de segurança, restauro, configurar a cópia de segurança |
| JobStatus_s |Texto |Estado do trabalho concluído, por exemplo, concluído, com falhas |
| JobFailureCode_s |Texto |Cadeia de caracteres do código de falha devido a que ocorreu a falha da tarefa |
| JobStartDateTime_s |Data/Hora |Data e hora quando a tarefa em execução iniciada |
| BackupStorageDestination_s |Texto |Destino de cópias de segurança, por exemplo, na Cloud, disco  |
| JobDurationInSecs_s | Number |Duração total da tarefa em segundos |
| DataTransferredInMB_s | Number |Dados transferidos em MB para esta tarefa|
| JobUniqueId_g |Texto |Id exclusivo para identificar a tarefa |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação|
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="policy"></a>Política
Esta tabela fornece detalhes sobre os campos relacionados com a política.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - política |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Id exclusivo para identificar a política |
| PolicyName_s |Texto |Nome da política definida |
| BackupFrequency_s |Texto |Frequência com que as cópias de segurança são executadas, por exemplo, diariamente, semanalmente |
| BackupTimes_s |Texto |Data e hora quando estão agendadas cópias de segurança |
| BackupDaysOfTheWeek_s |Texto |Dias da semana quando tiverem sido agendadas as cópias de segurança |
| RetentionDuration_s |Número inteiro |Duração da retenção de cópias de segurança configuradas |
| DailyRetentionDuration_s |Número inteiro |Duração total de retenção em dias para cópias de segurança configurados |
| DailyRetentionTimes_s |Texto |Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal |Duração da retenção semanal total em semanas para cópias de segurança configuradas |
| WeeklyRetentionTimes_s |Texto |Data e hora quando estiver configurada retenção semanal |
| WeeklyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionada para a retenção semanal |
| MonthlyRetentionDuration_s |Número decimal |Duração da retenção total nos meses para cópias de segurança configuradas |
| MonthlyRetentionTimes_s |Texto |Data e hora quando estiver configurada retenção mensal |
| MonthlyRetentionFormat_s |Texto |Tipo de configuração para uma retenção mensal, por exemplo, diária, dia, com base, semanalmente por semana com base em |
| MonthlyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionada para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto |Semanas do mês quando retenção mensal estiver configurada, por exemplo, primeiro, último etc. |
| YearlyRetentionDuration_s |Número decimal |Duração da retenção total nos anos para cópias de segurança configurados |
| YearlyRetentionTimes_s |Texto |Data e hora quando estiver configurada retenção anual |
| YearlyRetentionMonthsOfTheYear_s |Texto |Meses do ano selecionado para a retenção anual |
| YearlyRetentionFormat_s |Texto |Tipo de configuração de retenção anual, por exemplo, diária, dia, com base, semanalmente por semana com base em |
| YearlyRetentionDaysOfTheMonth_s |Texto |Datas do mês selecionado para a retenção anual |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="policyassociation"></a>PolicyAssociation
Esta tabela fornece detalhes sobre associações de política com várias entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| PolicyUniqueId_g |Texto |Id exclusivo para identificar a política |
| VaultUniqueId_s |Texto |Id exclusivo do cofre ao qual esta política pertence a |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="protectedserver"></a>ProtectedServer
Esta tabela fornece detalhes sobre os campos relacionados a servidores protegidos.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| ProtectedServerName_s |Texto |Nome do servidor protegido |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de servidor protegido, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServer |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido |
| RegisteredContainerId_s |Texto |O ID de contentor registado para cópia de segurança |
| ProtectedServerType_s |Texto |Tipo de servidor protegido, por exemplo, o Windows |
| ProtectedServerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| AzureBackupAgentVersion_s |Texto |Número de versão de versão do agente de cópia de segurança |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Esta tabela fornece detalhes sobre as associações de servidor protegido com outras entidades.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação servidor protegido, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServerAssociation |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido |
| VaultUniqueId_s |Texto |Id exclusivo do cofre ao qual este servidor protegido pertence a |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="storage"></a>Armazenamento
Esta tabela fornece detalhes sobre os campos relacionados com o armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |O armazenamento de cópia de segurança na cloud utilizados pelas cópias de segurança, calculadas com base no valor mais recente |
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas usada para calcular o armazenamento de front-end na faturação, calculada com base no valor mais recente |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Active Directory, eliminado |
| BackupManagementType_s |Texto |Tipo de fornecedor para o servidor fazendo a tarefa de cópia de segurança, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - armazenamento |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| ProtectedServerUniqueId_s |Texto |Id exclusivo do servidor protegido para o qual armazenamento é calculado |
| VaultUniqueId_s |Texto |Id exclusivo do cofre para o armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

### <a name="vault"></a>Cofre
Esta tabela fornece detalhes sobre os campos relacionados com o cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome deste evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do cofre, por exemplo, Active Directory, eliminado |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria de dados de diagnóstico enviadas para o Azure Monitor registos, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para os quais dados são recolhidos, que mostra o nome do cofre dos serviços de recuperação |
| VaultUniqueId_s |Texto |Id exclusivo do Cofre |
| VaultName_s |Texto |Nome do Cofre |
| AzureDataCenter_s |Texto |Onde está localizado o Cofre de centro de dados |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem de dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso de dados que está a ser recolhidos. Por exemplo, id de recurso do cofre dos serviços de recuperação |
| SubscriptionId |Texto |Identificador de subscrição do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceGroup |Texto |Grupo de recursos do recurso (ex. Cofre dos serviços de recuperação) para o qual os dados são recolhidos |
| ResourceProvider |Texto |Fornecedor de recursos para o qual os dados são recolhidos. Por exemplo, Microsoft. recoveryservices |
| ResourceType |Texto |Tipo de recurso para o qual os dados são recolhidos. Por exemplo, os cofres |

## <a name="next-steps"></a>Passos Seguintes
Após analisar o modelo de dados para a criação de relatórios do Azure Backup, pode começar [criar o dashboard](../azure-monitor/learn/tutorial-logs-dashboards.md) nos registos do Azure Monitor.
