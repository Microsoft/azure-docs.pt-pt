---
title: Reencaminhar dados de tarefa da automatização do Azure para registos do Azure Monitor
description: Este artigo demonstra como enviar estado da tarefa e o runbook fluxos de trabalho para registos do Azure do Azure Monitor para fornecer informações adicionais e gestão.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 10497d40dcf67fb18d40eba02ec9e95c45be097b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820863"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Reencaminhar o estado da tarefa e fluxos de trabalho de automatização para registos do Azure Monitor

Automatização pode enviar as runbook fluxos de estado e a tarefa de trabalho para a área de trabalho do Log Analytics. Este processo não envolve a ligação de área de trabalho e é completamente independente. Registos de trabalhos e fluxos de trabalho são visíveis no portal do Azure ou com o PowerShell, para tarefas individuais e Isto permite-lhe realizar investigações simples. Agora com os registos do Azure Monitor, pode:

* Obter informações sobre as suas tarefas de Automatização.
* Acionador de um e-mail ou o alerta com base no seu estado de tarefa de runbook (por exemplo, falha ou suspenso).
* Escrever consultas avançadas nos fluxos de tarefas.
* Correlacionar tarefas em contas de Automatização.
* Visualizar o seu histórico de tarefas ao longo do tempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implementação

Para começar a enviar os registos de automatização para registos do Azure Monitor, é necessário:

* Versão de Novembro 2016 ou posterior do [do Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Uma área de trabalho do Log Analytics. Para obter mais informações, consulte [começar a utilizar com os registos do Azure Monitor](../log-analytics/log-analytics-get-started.md). 
* O ResourceId para a sua conta de automatização do Azure.

Para localizar o ResourceId para a sua conta de automatização do Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para localizar o ResourceId sua área de trabalho do Log Analytics, execute o PowerShell seguinte:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se tiver mais do que um contas de automatização ou encontrar áreas de trabalho, a saída dos comandos anteriores, o *Name* tem de configurar e copie o valor para *ResourceId*.

Se precisa localizar o *Name* da sua conta de automatização no portal do Azure, selecione a conta de automatização da **conta de automatização** painel e selecione **todas as definições** . A partir do painel **Todas as definições**, em **Definições da Conta** selecione **Propriedades**.  No painel **Propriedades**, pode ter em atenção estes valores.<br> ![Propriedades da conta de automatização](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os registos do Azure Monitor

1. No seu computador, inicie **Windows PowerShell** partir do **iniciar** ecrã.
2. Execute o PowerShell seguinte e editar o valor para o `[your resource id]` e `[resource id of the log analytics workspace]` com os valores do passo anterior.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Depois de executar este script, pode demorar uma hora antes de começar a ver registos nos registos do Azure Monitor do novo JobLogs ou JobStreams que estão sendo gravados.

Para ver os registos, execute a seguinte consulta na pesquisa de registos do log analytics: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar configuração

Para confirmar a sua conta de automatização está a enviar registos para a área de trabalho do Log Analytics, verifique se diagnóstico está corretamente configurado na conta de automatização utilizando o PowerShell seguinte:

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Na saída Certifique-se de que:

* Sob *Logs*, o valor de *ativado* é *verdadeiro*.
* O valor de *WorkspaceId* está definido como o ResourceId dos sua área de trabalho do Log Analytics.

## <a name="azure-monitor-log-records"></a>Registros de logs de Monitor do Azure

Cria dois tipos de registos no Azure Monitor registos de diagnóstico da automatização do Azure e é etiquetado como **AzureDiagnostics**. As consultas seguintes utilizam a linguagem de consulta atualizado nos registos do Azure Monitor. Para obter informações sobre consultas comuns entre o idioma de consulta legada e a nova linguagem de consulta de Kusto do Azure, visite [herdados para a nova folha de referência rápida de linguagem de consulta de Kusto do Azure](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Registos da tarefa

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>-Novo<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>-Concluída |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs. |
| OperationName | Especifica o tipo de operação efetuada no Azure. Para a automatização, o valor é o trabalho. |
| Recurso | Nome da conta de automatização |
| SourceSystem | Como o Azure Monitor registos recolhidos os dados. Sempre *Azure* para obter um diagnóstico do Azure. |
| ResultDescription |Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica o id de recurso de conta de automatização do Azure do runbook. |
| SubscriptionId | A subscrição do Azure Id (GUID) da conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Fluxos de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>-Em curso |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams. |
| OperationName | Especifica o tipo de operação efetuada no Azure. Para a automatização, o valor é o trabalho. |
| Recurso | Nome da conta de automatização |
| SourceSystem | Como o Azure Monitor registos recolhidos os dados. Sempre *Azure* para obter um diagnóstico do Azure. |
| ResultDescription |Inclui o fluxo de saída do runbook. |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica o id de recurso de conta de automatização do Azure do runbook. |
| SubscriptionId | A subscrição do Azure Id (GUID) da conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualizar registos de automatização nos registos do Azure Monitor

Agora que iniciou a enviar os registos da tarefa de automatização para registos do Azure Monitor, vejamos o que pode fazer com estes registos no interior de registos do Azure Monitor.

Para ver os registos, execute a seguinte consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um e-mail quando uma tarefa de runbook falha ou suspende
Um dos principais clientes pede destina-se a capacidade de enviar um e-mail ou uma mensagem de texto quando algo dá errado com uma tarefa de runbook.   

Para criar uma regra de alerta, começa criando uma pesquisa de registos para os registos de tarefa de runbook que deve invocar o alerta. Clique nas **alerta** botão para criar e configurar a regra de alerta.

1. Na página de descrição geral de área de trabalho do Log Analytics, clique em **ver registos**.
2. Crie uma consulta de pesquisa de registo para o alerta, escrevendo a seguinte pesquisa no campo de consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`  Também pode agrupar pelo RunbookName utilizando: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se configurar os registos de mais do que uma subscrição ou conta de automatização à área de trabalho, pode agrupar os alertas por conta de automatização e subscrição. Nome da conta de automatização pode ser encontrado no campo de recurso na pesquisa de JobLogs.
3. Para abrir o **criar regra** ecrã, clique em **+ nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções de configuração do alerta, consulte [alertas de registo no Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Encontrar todas as tarefas que foram concluídos com erros
Para além de alertas sobre falhas, pode encontrar quando uma tarefa de runbook tem um erro de não terminação. Nestes casos PowerShell produz uma sequência de erro, mas os erros de não terminação não fazer com que o seu trabalho a suspender ou falhar.    

1. Na área de trabalho do Log Analytics, clique em **registos**.
2. No campo de consulta, escreva `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` e, em seguida, clique nas **pesquisa** botão.

### <a name="view-job-streams-for-a-job"></a>Fluxos de trabalho de vista de uma tarefa
Quando estiver depurando um trabalho, também poderá examinar os fluxos de trabalho. A seguinte consulta mostra todos os fluxos para uma única tarefa com o GUID 2ebd22ea-e05e-4eb9 - 9D 76-d73cbd4356e0:   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Ver o estado de tarefa históricos
Por fim, pode querer visualizar o histórico de tarefas ao longo do tempo. Pode utilizar esta consulta para procurar o estado das suas tarefas ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Gráfico de estado de tarefa históricos do log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Remover definições de diagnóstico

Para remover a definição de diagnóstico da conta de automatização, execute os seguintes comandos:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Resumo

Ao enviar os dados de estado e o fluxo da tarefa de automatização para registos do Azure Monitor, pode obter mais idéias sobre o estado das suas tarefas de automatização por:
+ Configuração de alertas para notificá-lo quando existe um problema.
+ Utilizar vistas personalizadas e consultas de pesquisa para visualizar os resultados do runbook, estado da tarefa de runbook e outras associadas principais indicadores ou métricas.  

Registos de Monitor do Azure fornece maior visibilidade operacional para as tarefas de automatização e pode ajudar a incidentes de endereço mais rápidos.  

## <a name="next-steps"></a>Passos Seguintes
* Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos da tarefa de automatização com os registos do Azure Monitor, consulte [pesquisas de registos nos registos do Azure Monitor](../log-analytics/log-analytics-log-searches.md).
* Para compreender como criar e recuperar mensagens de erro e de saída a partir de runbooks, veja [Runbook de saída e mensagens](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
* Para saber mais sobre os registos do Azure Monitor e origens de recolha de dados, veja [descrição geral de registos de dados do armazenamento do Azure a recolher no Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).

