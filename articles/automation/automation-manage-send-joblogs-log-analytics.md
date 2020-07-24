---
title: Reencaminhar dados de tarefa da Automatização do Azure para os registos do Azure Monitor
description: Este artigo diz como enviar fluxos de trabalho de trabalho e runbook para registos do Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 05/22/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe6cbdbcb0cf5b5c28d34f2059a2b070b059566
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004754"
---
# <a name="forward-azure-automation-job-data-to-azure-monitor-logs"></a>Reencaminhar dados de tarefa da Automatização do Azure para os registos do Azure Monitor

A Azure Automation pode enviar o estado de trabalho do runbook e os fluxos de trabalho para o seu espaço de trabalho Log Analytics. Este processo não envolve a ligação do espaço de trabalho e é completamente independente. Os registos de trabalho e os fluxos de trabalho são visíveis no portal Azure, ou com o PowerShell, para trabalhos individuais e isso permite-lhe realizar investigações simples. Agora com registos do Azure Monitor pode:

* Obtenha informações sobre o estado dos seus trabalhos de Automação.
* Desembaraçar um e-mail ou alerta com base no seu estado de trabalho no runbook (por exemplo, falhado ou suspenso).
* Escreva consultas avançadas através dos seus fluxos de trabalho.
* Correlacionar empregos em todas as contas de Automação.
* Utilize vistas personalizadas e consultas de pesquisa para visualizar os resultados do seu runbook, o estado do trabalho do runbook e outros indicadores ou métricas de chaves relacionadas.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar os seus registos de Automação para registos do Azure Monitor, é necessário:

* O mais recente lançamento da [Azure PowerShell](/powershell/azure/).
* Uma área de trabalho do Log Analytics. Para obter mais informações, consulte [Começar com os registos do Azure Monitor](../azure-monitor/overview.md).
* O ID de recursos para a sua conta Azure Automation.

Utilize o seguinte comando para encontrar o ID de recurso para a sua conta Azure Automation:

```powershell-interactive
# Find the ResourceId for the Automation account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para encontrar o ID de recurso para o seu espaço de trabalho Log Analytics, execute o seguinte comando PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se tiver mais do que uma conta de Automação ou espaço de trabalho na saída dos comandos anteriores, pode encontrar o nome e outras propriedades relacionadas que fazem parte do ID completo do recurso da sua conta Demôm automação, executando o seguinte:

1. No portal Azure, selecione a sua conta Demôm automação na página **Contas de Automação.** 
2. Na página da conta de Automação selecionada, em **Definições de Conta**, selecione **Propriedades**.  
3. Na página **Propriedades,** note os detalhes apresentados abaixo.

    ![Propriedades de conta de automação](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="azure-monitor-log-records"></a>Registos de registos do Azure Monitor

Os diagnósticos de Automatização Azure criam dois tipos de registos em registos do Azure Monitor, marcados como `AzureDiagnostics` . As tabelas nas secções seguintes são exemplos de registos que a Azure Automation gera e os tipos de dados que aparecem nos resultados de pesquisa de registos.

### <a name="job-logs"></a>Registos de trabalhos

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o inquilino para quem ligou. |
| JobId_g |GUID que identifica o trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>- Novo<br>- Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Concluído |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs. |
| OperationName | O tipo de operação realizada em Azure. Para a Automação, o valor é Trabalho. |
| Recurso | O nome da conta Automation |
| SourceSystem | Sistema que os registos do Azure Monitor utilizam para recolher os dados. O valor é sempre Azure para diagnósticos Azure. |
| ResultDescription |O estado do resultado do trabalho. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído |
| CorrelationId |A correlação GUID do trabalho de runbook. |
| ResourceId |Identificação de recurso de conta Azure Automation do livro de contas. |
| SubscriptionId | A assinatura Azure GUID para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | O fornecedor de recursos. O valor é MICROSOFT. AUTOMAÇÃO. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

### <a name="job-streams"></a>Fluxos de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso |
| Tenant_g | GUID que identifica o inquilino para quem ligou. |
| JobId_g |GUID que identifica o trabalho do runbook. |
| ResultType |O estado do trabalho do runbook. Os valores possíveis são:<br>- Em curso |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams. |
| OperationName | Tipo de operação realizada em Azure. Para a Automação, o valor é Trabalho. |
| Recurso | O nome da conta Automation. |
| SourceSystem | Sistema que os registos do Azure Monitor utilizam para recolher os dados. O valor é sempre Azure para diagnósticos Azure. |
| ResultDescription |Descrição que inclui o fluxo de saída do livro de recortes. |
| CorrelationId |A correlação GUID do trabalho de runbook. |
| ResourceId |Identificação de recurso de conta Azure Automation do livro de contas. |
| SubscriptionId | A assinatura Azure GUID para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | O fornecedor de recursos. O valor é MICROSOFT. AUTOMAÇÃO. |
| ResourceType | O tipo de recurso. O valor é AUTOMATIONACCOUNTS. |

## <a name="set-up-integration-with-azure-monitor-logs"></a>Confiúdo de integração com registos do Azure Monitor

1. No seu computador, inicie o Windows PowerShell a partir do ecrã **Iniciar.**
2. Executar os seguintes comandos PowerShell e editar os valores para `$automationAccountId` e `$workspaceId` com os valores da secção anterior.

   ```powershell-interactive
   $workspaceId = "resource ID of the log analytics workspace"
   $automationAccountId = "resource ID of your Automation account"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Depois de executar este script, pode demorar uma hora até começar a ver registos em registos do Azure Monitor de novos `JobLogs` ou `JobStreams` escritos.

Para ver os registos, execute a seguinte consulta na pesquisa de registo de registo de registo de registos de registos:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar configuração

Para confirmar que a sua conta Demômpa está a enviar registos para o seu espaço de trabalho Log Analytics, verifique se os diagnósticos estão corretamente configurados na conta Automação utilizando o seguinte comando PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, certifique-se de que:

* `Logs`Abaixo, o valor para é `Enabled` verdadeiro.
* `WorkspaceId`está definido para o `ResourceId` valor do seu espaço de trabalho Log Analytics.

## <a name="view-automation-logs-in-azure-monitor-logs"></a>Ver registos de automatização em registos do Monitor Azure

Agora que começou a enviar os seus registos de trabalho da Automação para registos do Azure Monitor, vamos ver o que pode fazer com estes registos dentro dos registos do Azure Monitor.

Para ver os registos, execute a seguinte consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envie um e-mail quando um trabalho de runbook falha ou suspende

Os passos seguintes mostram como configurar alertas no Azure Monitor para notificá-lo quando algo corre mal com um trabalho de runbook.

Para criar uma regra de alerta, comece por criar uma pesquisa de registo para os registos de trabalho do runbook que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. A partir da página de visão geral do espaço de trabalho do Log Analytics, clique em **Ver registos**.

2. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Também pode agrupar o nome do livro de corridas utilizando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se configurar registos de mais de uma conta de Automação ou subscrição do seu espaço de trabalho, pode agrupar os seus alertas por subscrição e conta Demôm automação. O nome da conta de automação pode ser encontrado no `Resource` campo na procura de `JobLogs` .

3. Para abrir o ecrã **de regras Criar,** clique em **Nova Regra de Alerta** no topo da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [os alertas de registo em Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Encontre todos os trabalhos que tenham sido concluídos com erros

Além de alertar sobre falhas, pode encontrar quando um trabalho de runbook tem um erro de não rescisão. Nestes casos, a PowerShell produz um fluxo de erro, mas os erros de não rescisão não fazem com que o seu trabalho suspenda ou falhe.

1. No seu espaço de trabalho Log Analytics, clique em **Registars**.

2. No campo de consulta, escreva `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g` .

3. Clique no botão **Procurar.**

### <a name="view-job-streams-for-a-job"></a>Ver fluxos de emprego para um trabalho

Quando estás a depurar um emprego, também podes querer ver os fluxos de emprego. A seguinte consulta mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Ver estado histórico do trabalho

Finalmente, talvez queira visualizar o seu histórico de trabalho ao longo do tempo. Pode utilizar esta consulta para procurar o estado dos seus trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de estado histórico de trabalho de log analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="remove-diagnostic-settings"></a>Remover as definições de diagnóstico

Para remover a definição de diagnóstico da conta Automação, execute o seguinte comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="next-steps"></a>Passos seguintes

* Para aprender a construir consultas de pesquisa e rever os registos de trabalho da Automação com registos do Azure Monitor, consulte [as pesquisas de Registo nos registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Para compreender a criação e recuperação de mensagens de saída e erro a partir de runbooks, consulte [a saída do livro de execução do Monitor](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbook, como monitorizar os trabalhos de runbook, e outros detalhes técnicos, consulte [a execução do Runbook na Azure Automation](automation-runbook-execution.md).
* Para saber mais sobre os registos do Azure Monitor e as fontes de recolha de dados, consulte [a recolha de dados de armazenamento do Azure monitor na visão geral dos registos do Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).
* Para ajudar a resolver problemas no Log Analytics, consulte [a resolução de problemas por que o Log Analytics já não está a recolher dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
