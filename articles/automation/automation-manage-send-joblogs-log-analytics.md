---
title: Reencaminhar dados de tarefa da Automatização do Azure para os registos do Azure Monitor
description: Este artigo demonstra como enviar o status do trabalho e os fluxos de trabalho do runbook para Azure Monitor logs para fornecer informações e gerenciamento adicionais.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ff455ed355d4412bcf042208d2fd1e7a2a11b965
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186775"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Encaminhe o status do trabalho e os fluxos de trabalho da automação para os logs de Azure Monitor

A automação pode enviar o status do trabalho de runbook e fluxos de trabalho para seu espaço de trabalho Log Analytics. Esse processo não envolve a vinculação de espaço de trabalho e é completamente independente. Os logs de trabalho e os fluxos de trabalho são visíveis no portal do Azure, ou com o PowerShell, para trabalhos individuais e isso permite que você execute investigações simples. Agora, com os logs de Azure Monitor, você pode:

* Obter informações sobre as suas tarefas de Automatização.
* Dispare um email ou alerta com base no status do trabalho de runbook (por exemplo, com falha ou suspenso).
* Escrever consultas avançadas nos fluxos de tarefas.
* Correlacionar tarefas em contas de Automatização.
* Visualizar o seu histórico de tarefas ao longo do tempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar seus logs de automação para Azure Monitor logs, você precisa:

* A versão mais recente do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).
* Uma área de trabalho do Log Analytics. Para obter mais informações, consulte Introdução [aos logs de Azure monitor](../log-analytics/log-analytics-get-started.md).
* O ResourceId para sua conta de automação do Azure.

Para localizar o ResourceId da sua conta de automação do Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para localizar o ResourceId para seu espaço de trabalho Log Analytics, execute o seguinte PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se você tiver mais de uma conta de automação ou espaços de trabalho, na saída dos comandos anteriores, localize o *nome* que você precisa configurar e copie o valor de ResourceId.

Se você precisar localizar o *nome* da sua conta de automação, no portal do Azure selecione sua conta de automação na folha **conta de automação** e selecione **todas as configurações**. A partir do painel **Todas as definições**, em **Definições da Conta** selecione **Propriedades**.  No painel **Propriedades**, pode ter em atenção estes valores.<br> ![Propriedades](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)da conta de automação.

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs de Azure Monitor

1. Em seu computador, inicie o **Windows PowerShell** na tela **inicial** .
2. Execute o PowerShell a seguir e edite o valor para `[your resource id]` e `[resource id of the log analytics workspace]` com os valores da etapa anterior.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Depois de executar esse script, pode levar uma hora antes de começar a ver registros em Azure Monitor logs de novo JobLogs ou JobStreams sendo gravados.

Para ver os logs, execute a seguinte consulta na pesquisa de logs do log Analytics:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar configuração

Para confirmar que sua conta de automação está enviando logs para seu espaço de trabalho do Log Analytics, verifique se os diagnósticos estão configurados corretamente na conta de automação usando o PowerShell a seguir:

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, verifique se:

* Em *logs*, o valor de *habilitado* é *true*.
* O valor de *workspaceid* é definido como o ResourceId do seu espaço de trabalho log Analytics.

## <a name="azure-monitor-log-records"></a>Azure Monitor registros de log

O diagnóstico da automação do Azure cria dois tipos de registros em logs de Azure Monitor e são marcados como **AzureDiagnostics**. As consultas a seguir usam a linguagem de consulta atualizada para Azure Monitor logs. Para obter informações sobre consultas comuns entre a linguagem de consulta herdada e a nova linguagem de consulta do Azure Kusto visite [a página de dicas de nova linguagem de consulta do Azure Kusto](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language)

### <a name="job-logs"></a>Logs de trabalho

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>-Novo<br>-Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>-Concluído |
| Category | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs. |
| OperationName | Especifica o tipo de operação efetuada no Azure. Para a automação, o valor é trabalho. |
| Resource | Nome da conta de automação |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResultDescription |Descreve o estado do resultado do trabalho do runbook. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica a ID de recurso da conta de automação do Azure do runbook. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Fluxos de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |Quem iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>\- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso |
| Tenant_g | GUID que identifica o locatário do chamador. |
| JobId_g |GUID que é o Id do trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>-Em andamento |
| Category | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams. |
| OperationName | Especifica o tipo de operação efetuada no Azure. Para a automação, o valor é trabalho. |
| Resource | Nome da conta de automação |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResultDescription |Inclui o fluxo de saída do runbook. |
| CorrelationId |GUID que é o Id de Correlação do trabalho do runbook. |
| ResourceId |Especifica a ID de recurso da conta de automação do Azure do runbook. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a conta de automação. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Exibindo logs de automação em logs de Azure Monitor

Agora que você começou a enviar seus logs de trabalho de automação para Azure Monitor logs, vamos ver o que você pode fazer com esses logs dentro de logs de Azure Monitor.

Para ver os logs, execute a seguinte consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Enviar um email quando um trabalho de runbook falhar ou for suspenso
Uma das principais perguntas do cliente é a capacidade de enviar um email ou um texto quando algo der errado com um trabalho de runbook.

Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros de trabalho de runbook que devem invocar o alerta. Clique no botão **alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **Exibir logs**.
2. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta: `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`Você também pode agrupar pelo RunbookName usando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se você configurar logs de mais de uma conta de automação ou assinatura para seu espaço de trabalho, poderá agrupar seus alertas por conta de automação e assinatura. O nome da conta de automação pode ser encontrado no campo recurso na pesquisa de JobLogs.
3. Para abrir a tela **criar regra** , clique em **+ nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [log Alerts in Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Localizar todos os trabalhos que foram concluídos com erros
Além de alertar sobre falhas, você pode encontrar quando um trabalho de runbook tem um erro de não finalização. Nesses casos, o PowerShell produz um fluxo de erro, mas os erros de não encerramento não fazem com que seu trabalho seja suspenso ou falhe.

1. No espaço de trabalho Log Analytics, clique em **logs**.
2. No campo de consulta, digite `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` e, em seguida, clique no botão **Pesquisar** .

### <a name="view-job-streams-for-a-job"></a>Exibir fluxos de trabalho para um trabalho
Quando estiver depurando um trabalho, talvez você também queira examinar os fluxos de trabalho. A consulta a seguir mostra todos os fluxos de um único trabalho com o GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Exibir status do trabalho histórico
Por fim, talvez você queira Visualizar o histórico do trabalho ao longo do tempo. Você pode usar essa consulta para procurar o status de seus trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de status do trabalho histórico de Log Analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Remover configurações de diagnóstico

Para remover a configuração de diagnóstico da conta de automação, execute os seguintes comandos:

```powershell-interactive
$automationAccountId = "[resource id of your automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Resumo

Ao enviar o status do trabalho de automação e transmitir dados para Azure Monitor logs, você pode obter uma melhor percepção do status de seus trabalhos de automação:
+ Configurar alertas para notificá-lo quando houver um problema.
+ Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas de chave relacionados.

Os logs de Azure Monitor fornecem maior visibilidade operacional para seus trabalhos de automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Passos Seguintes

* Para obter ajuda para solução de problemas Log Analytics, consulte [Solucionando problemas por que log Analytics não está mais coletando dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de trabalho de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md).
* Para entender como criar e recuperar mensagens de saída e de erro de runbooks, consulte [saída e mensagens de runbook](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
* Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, consulte [visão geral sobre coleta de dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md).

