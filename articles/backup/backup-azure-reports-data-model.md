---
title: Modelo de dados para o backup do Azure
description: Este artigo fala sobre Power BI detalhes do modelo de dados para relatórios de backup do Azure.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: dacurwin
ms.openlocfilehash: f3db0e6a78eada52f2b5936f5194138d97ebcca6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074900"
---
# <a name="data-model-for-azure-backup-reports"></a>Modelo de dados para relatórios do Azure Backup

Este artigo descreve o modelo de dados Power BI usado para criar relatórios de backup do Azure. Usando esse modelo de dados, você pode filtrar relatórios existentes com base em campos relevantes e, mais importante, criar seus próprios relatórios usando tabelas e campos no modelo.

## <a name="creating-new-reports-in-power-bi"></a>Criando novos relatórios no Power BI

Power BI fornece recursos de personalização usando os quais você pode [criar relatórios usando o modelo de dados](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de backup do Azure

Você pode usar os campos a seguir fornecidos como parte do modelo de dados para criar relatórios e personalizar os relatórios existentes.

### <a name="alert"></a>Alerta

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao alerta.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Número inteiro |Número de alertas criados no período de tempo selecionado |
| %ActiveAlertsCreatedInPeriod |Percentagem |Percentual de alertas ativos no período de tempo selecionado |
| %CriticalAlertsCreatedInPeriod |Percentagem |Percentual de alertas críticos no período de tempo selecionado |
| AlertOccurrenceDate |Data |Data em que o alerta foi criado |
| AlertSeverity |Texto |Severidade do alerta. Por exemplo, crítico |
| AlertStatus |Texto |Status do alerta. Por exemplo, ativo |
| AlertType |Texto |Tipo do alerta gerado. Por exemplo, backup |
| AlertUniqueId |Texto |ID exclusiva do alerta gerado |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Número decimal |Tempo médio (em minutos) para resolver o alerta para o período de tempo selecionado |
| EntityState |Texto |Estado atual do objeto de alerta. Por exemplo, ativo, excluído |

### <a name="backup-item"></a>Item de backup

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao item de backup.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #BackupItems |Número inteiro |Número de itens de backup |
| #UnprotectedBackupItems |Número inteiro |Número de itens de backup interrompidos para proteção ou configurados para backups, mas backups não iniciados|
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| BackupItemFriendlyName |Texto |Nome amigável do item de backup |
| BackupItemId |Texto |ID do item de backup |
| BackupItemName |Texto |Nome do item de backup |
| BackupItemType |Texto |Tipo de item de backup. Por exemplo, VM, fileFolder |
| EntityState |Texto |Estado atual do objeto de item de backup. Por exemplo, ativo, excluído |
| LastBackupDateTime |Data/hora |Hora do último backup do item de backup selecionado |
| LastBackupState |Texto |Estado do último backup para o item de backup selecionado. Por exemplo, com êxito, com falha |
| LastSuccessfulBackupDateTime |Data/hora |Hora do último backup bem-sucedido para o item de backup selecionado |
| Proteçãostate |Texto |Estado de proteção atual do item de backup. Por exemplo, protegido, ProtectionStopped |

### <a name="calendar"></a>Calendário

Esta tabela fornece detalhes sobre os campos relacionados ao calendário.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| Data |Data |Data selecionada para filtrar dados |
| DateKey |Texto |Chave exclusiva para cada item de data |
| DayDiff |Número decimal |Diferença no dia para filtrar dados. Por exemplo, 0 indica dados do dia atual,-1 indica os dados de um dia anterior, 0 e-1 indicam dados para o dia atual e o anterior  |
| Mês |Texto |Mês do ano selecionado para filtrar dados, o mês começa no primeiro dia e termina no dia 31 |
| MonthDate | Data |Data do mês em que o mês termina, selecionado para filtrar dados |
| MonthDiff |Número decimal |Diferença no mês de filtragem de dados. Por exemplo, 0 indica os dados do mês atual,-1 indica os dados do mês anterior, 0 e-1 indicam dados para o mês atual e o anterior |
| Week (Semana) |Texto |Semana selecionada para filtrar dados, a semana começa no domingo e termina no sábado |
| WeekDate |Data |Data da semana quando a semana termina, selecionada para filtrar dados |
| WeekDiff |Número decimal |Diferença na semana para filtrar dados. Por exemplo, 0 indica os dados da semana atual,-1 indica os dados da semana anterior, 0 e-1 indicam dados para a semana atual e a anterior |
| Ano |Texto |Ano civil selecionado para filtrar dados |
| YearDate |Data |Data do ano em que o ano termina, selecionado para filtrar dados |

### <a name="job"></a>Tarefa

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao trabalho.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #JobsCreatedInPeriod |Número inteiro |Número de trabalhos criados no período de tempo selecionado |
| %FailuresForJobsCreatedInPeriod |Percentagem |Percentual de falhas gerais de trabalho no período de tempo selecionado |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Número decimal |valor do 80 º percentil de dados transferidos em MB para trabalhos de **backup** criados no período de tempo selecionado |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Número decimal |Tempo médio em minutos para trabalhos de **backup concluídos** criados no período de tempo selecionado |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Número decimal |Tempo médio em minutos para trabalhos de **restauração concluídos** criados no período de tempo selecionado |
| BackupStorageDestination |Texto |Destino do armazenamento de backup. Por exemplo, nuvem, disco  |
| EntityState |Texto |Estado atual do objeto de trabalho. Por exemplo, ativo, excluído |
| JobFailureCode |Texto |Cadeia de caracteres de código de falha devido a uma falha de trabalho |
| JobOperation |Texto |Operação para qual o trabalho é executado. Por exemplo, backup, restauração, configurar backup |
| JobStartDate |Data |Data em que o trabalho começou a ser executado |
| JobStartTime |Hora |Hora em que o trabalho começou a ser executado |
| Estado da Tarefa |Texto |Status do trabalho concluído. Por exemplo, concluído, com falha |
| JobUniqueId |Texto |ID exclusiva para identificar o trabalho |

### <a name="policy"></a>Política

Esta tabela fornece campos e agregações básicos em vários campos relacionados à política.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #Policies |Número inteiro |Número de políticas de backup existentes no sistema |
| #PoliciesInUse |Número inteiro |Número de políticas que estão sendo usadas atualmente para configurar backups |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| BackupDaysOfTheWeek |Texto |Dias da semana em que os backups foram agendados |
| BackupFrequency |Texto |Frequência com que os backups são executados. Por exemplo, diário, semanal |
| Inatividade |Texto |Data e hora em que os backups são agendados |
| DailyRetentionDuration |Número inteiro |Duração total de retenção em dias para backups configurados |
| DailyRetentionTimes |Texto |Data e hora em que a retenção diária foi configurada |
| EntityState |Texto |Estado atual do objeto de política. Por exemplo, ativo, excluído |
| MonthlyRetentionDaysOfTheMonth |Texto |Datas do mês selecionadas para retenção mensal |
| MonthlyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção mensal |
| MonthlyRetentionDuration |Número decimal |Duração total de retenção em meses para backups configurados |
| MonthlyRetentionFormat |Texto |Tipo de configuração para retenção mensal. Por exemplo, diariamente com base no dia, semanal para a semana com base |
| MonthlyRetentionTimes |Texto |Data e hora quando a retenção mensal está configurada |
| MonthlyRetentionWeeksOfTheMonth |Texto |Semanas do mês em que a retenção mensal é configurada. Por exemplo, primeiro, último etc. |
| PolicyName |Texto |Nome da política definida |
| PolicyUniqueId |Texto |ID exclusiva para identificar a política |
| RetentionType |Texto |Tipo de política de retenção. Por exemplo, diário, semanal, mensal, anual |
| WeeklyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção semanal |
| WeeklyRetentionDuration |Número decimal |Duração da retenção semanal total em semanas para backups configurados |
| WeeklyRetentionTimes |Texto |Data e hora quando a retenção semanal é configurada |
| YearlyRetentionDaysOfTheMonth |Texto |Datas do mês selecionadas para retenção anual |
| YearlyRetentionDaysOfTheWeek |Texto |Dias da semana selecionados para retenção anual |
| YearlyRetentionDuration |Número decimal |Duração total de retenção em anos para backups configurados |
| YearlyRetentionFormat |Texto |Tipo de configuração para retenção anual. Por exemplo, diariamente com base no dia, semanal para a semana com base |
| YearlyRetentionMonthsOfTheYear |Texto |Meses do ano selecionados para retenção anual |
| YearlyRetentionTimes |Texto |Data e hora quando a retenção anual é configurada |
| YearlyRetentionWeeksOfTheMonth |Texto |Semanas do mês em que a retenção anual é configurada. Por exemplo, primeiro, último etc. |

### <a name="protected-server"></a>Servidor protegido

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao servidor protegido.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #ProtectedServers |Número inteiro |Número de servidores protegidos |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| AzureBackupAgentOSType |Texto |Tipo de so do agente de backup do Azure |
| AzureBackupAgentOSVersion |Texto |Versão do so do agente de backup do Azure |
| AzureBackupAgentUpdateDate |Texto |Data quando o agente de backup do agente foi atualizado |
| AzureBackupAgentVersion |Texto |Número de versão da versão de backup do agente |
| BackupManagementType |Texto |Tipo de provedor para executar o backup. Por exemplo, IaaSVM, fileFolder |
| EntityState |Texto |Estado atual do objeto de servidor protegido. Por exemplo, ativo, excluído |
| ProtectedServerFriendlyName |Texto |Nome amigável do servidor protegido |
| ProtectedServerName |Texto |Nome do servidor protegido |
| ProtectedServerType |Texto |Tipo de backup do servidor protegido. Por exemplo, IaaSVMContainer |
| ProtectedServerName |Texto |Nome do servidor protegido ao qual pertence o item de backup |
| RegisteredContainerId |Texto |ID do contêiner registrado para backup |

### <a name="storage"></a>Storage

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao armazenamento.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #ProtectedInstances |Número decimal |Número de instâncias protegidas usadas para calcular o armazenamento de front-end na cobrança, calculadas com base no valor mais recente na hora selecionada |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| CloudStorageInMB |Número decimal |Armazenamento de backup na nuvem usado pelos backups, calculados com base no valor mais recente na hora selecionada |
| EntityState |Texto |Estado atual do objeto. Por exemplo, ativo, excluído |
| LastUpdatedDate |Data |Data em que a linha selecionada foi atualizada pela última vez |

### <a name="time"></a>Hora

Esta tabela fornece detalhes sobre os campos relacionados ao tempo.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| Hora |Hora |Hora do dia. Por exemplo, 1:00:00 PM |
| HourNumber |Número decimal |Número de hora no dia. Por exemplo, 13, 0 |
| Minuto |Número decimal |Minuto da hora |
| PeriodOfTheDay |Texto |Slot do período de tempo no dia. Por exemplo, 12-3 AM |
| Hora |Hora |Hora do dia. Por exemplo, 12:00:01 AM |
| TimeKey |Texto |Valor da chave para representar a hora |

### <a name="vault"></a>Cofre

Esta tabela fornece campos e agregações básicos em vários campos relacionados ao cofre.

| Campo | Tipo de Dados | Descrição |
| --- | --- | --- |
| #Vaults |Número inteiro |Número de cofres |
| AsOnDateTime |Data/hora |Hora de atualização mais recente para a linha selecionada |
| AzureDataCenter |Texto |Data Center onde o cofre está localizado |
| EntityState |Texto |Estado atual do objeto do cofre. Por exemplo, ativo, excluído |
| StorageReplicationType |Texto |Tipo de replicação de armazenamento para o cofre. Por exemplo, georedundante |
| SubscriptionId |Texto |ID da assinatura do cliente selecionado para gerar relatórios |
| VaultName |Texto |Nome do cofre |
| VaultTags |Texto |Marcas associadas ao cofre |

## <a name="next-steps"></a>Passos seguintes

Depois de examinar o modelo de dados para criar relatórios de backup do Azure, consulte os artigos a seguir para obter mais detalhes sobre como criar e exibir relatórios no Power BI.

* [Criando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Filtrando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
