---
title: Como consultar os registos da solução Start/Stop VMs
description: Este artigo descreve como consultar os dados de registo gerados pela solução Start/Stop VMs do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604731"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Como consultar os registos da solução Start/Stop VMs

A Azure Automation encaminha dois tipos de registos para o espaço de trabalho linked Log Analytics: registos de emprego e fluxos de trabalho. Estes dados estão disponíveis para [consulta](../azure-monitor/log-query/log-query-overview.md) no Monitor Azure.

## <a name="job-logs"></a>Registos de trabalhos

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs.|
|CorrelationId | GUID que é a Id correlação do trabalho do livro de corridas.|
|JobId | GUID que é a identificação do trabalho do livro de corridas.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para automação, o valor é Job.|
|resourceId | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O estado do trabalho do runbook. Os valores possíveis são:<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Bem-sucedido|
|resultDescription | Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído|
|RunbookName | Especifica o nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para automação, o valor é OpsManager|
|StreamType | Especifica o tipo de evento. Os valores possíveis são:<br>- Verboso<br>- Saída<br>- Erro<br>- Aviso|
|SubscriptionId | Especifica o ID de subscrição do trabalho.
|Hora | Data e hora da execução do trabalho do runbook.|

## <a name="job-streams"></a>Fluxos de trabalho

|Propriedade | Descrição|
|----------|----------|
|Autor da chamada |  Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados.|
|Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams.|
|JobId | GUID que é a identificação do trabalho do livro de corridas.|
|operationName | Especifica o tipo de operação efetuada no Azure. Para automação, o valor é Job.|
|ResourceGroup | Especifica o nome do grupo de recursos do trabalho do runbook.|
|resourceId | Especifica o ID de recurso em Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|ResourceProvider | Especifica o serviço do Azure que fornece os recursos que pode implementar e gerir. Para a Automatização, o valor é Automatização do Azure.|
|ResourceType | Especifica o tipo de recurso no Azure. Para a Automatização, o valor é a conta de Automatização associada ao runbook.|
|resultType | O resultado do trabalho do runbook no momento em que o evento foi gerado. Um valor possível é:<br>- InProgress|
|resultDescription | Inclui o fluxo de saída do runbook.|
|RunbookName | O nome do runbook.|
|SourceSystem | Especifica o sistema de origem dos dados submetidos. Para a Automação, o valor é OpsManager.|
|StreamType | O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Progresso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso|
|Hora | Data e hora da execução do trabalho do runbook.|

Quando efetua qualquer pesquisa de registo que retorne os registos da categoria de **JobLogs** ou **JobStreams,** pode selecionar a vista **JobLogs** ou **JobStreams,** que apresenta um conjunto de azulejos resumindo as atualizações devolvidas pela pesquisa.

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de trabalhos que esta solução recolhe.

|Consulta | Descrição|
|----------|----------|
|Encontre empregos para ScheduledStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para ScheduledStartStop_Parent de runbook que não tenham concluído com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para SequencedStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para SequencedStartStop_Parent de runbook que não tenham concluído com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Passos seguintes

Os **VMs de início/paragem durante a** solução off-hours não incluem um conjunto predefinido de alertas. Reveja os [alertas de registo Create](../azure-monitor/platform/alerts-log.md) com o Monitor Azure para aprender a criar alertas falhados de trabalho para apoiar os seus DevOps ou processos e procedimentos operacionais.