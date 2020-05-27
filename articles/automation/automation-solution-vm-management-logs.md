---
title: Registos de consultas de VMs de arranque/paragem de automação Azure durante o horário de folga
description: Este artigo diz como usar o Monitor Azure para consultar os dados de registo gerados por VMs Start/Stop durante o horário de folga.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: de013b6ccd924f50ffe12fcba1285b121eece5f7
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827561"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Consultar registos a partir da solução Iniciar/Parar VMs fora do horário de expediente

A Azure Automation encaminha dois tipos de registos para o espaço de trabalho linked Log Analytics: registos de emprego e fluxos de trabalho. Este artigo analisa os dados disponíveis para [consulta](../azure-monitor/log-query/log-query-overview.md) no Monitor Azure.

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

A tabela seguinte fornece pesquisas de registo de amostras para registos de trabalho recolhidos por VMs start/stop durante o horário de folga.

|Consulta | Descrição|
|----------|----------|
|Encontre empregos para ScheduledStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para ScheduledStartStop_Parent de runbook que não tenham concluído com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para SequencedStartStop_Parent runbook que terminaram com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Encontre empregos para SequencedStartStop_Parent de runbook que não tenham concluído com sucesso | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Passos seguintes

* Para configurar a funcionalidade, consulte Os [VMs de paragem/arranque do Configure durante](automation-solution-vm-management-config.md)as horas de folga .
* Para obter informações sobre alertas de registo durante a implementação da funcionalidade, consulte Criar alertas de registo com o [Monitor Azure](../azure-monitor/platform/alerts-log.md).
* Para resolver os erros de funcionalidade, consulte [Os VMs Start/Stop de Troubleshoot durante as questões fora de horas](troubleshoot/start-stop-vm.md).