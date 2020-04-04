---
title: Reencaminhar dados de tarefa da Automatização do Azure para os registos do Azure Monitor
description: Este artigo demonstra como enviar o estado de trabalho e os fluxos de trabalho do livro de recortes para os registos do Azure Monitor para fornecer informações e gestão adicionais.
services: automation
ms.subservice: process-automation
ms.date: 02/05/2019
ms.topic: conceptual
ms.openlocfilehash: 54f77f55a127cd712d43419eb6a85fd5d93a478c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652166"
---
# <a name="forward-job-status-and-job-streams-from-automation-to-azure-monitor-logs"></a>Estado de trabalho avançado e fluxos de trabalho da Automação para os registos do Monitor Azure

A automatização pode enviar o estado do trabalho do livro de recortes e os fluxos de trabalho para o seu espaço de trabalho Log Analytics. Este processo não envolve a ligação do espaço de trabalho e é completamente independente. Os registos de emprego e os fluxos de emprego são visíveis no portal Azure, ou com powerShell, para trabalhos individuais e isso permite-lhe realizar investigações simples. Agora com os registos do Monitor Azure pode:

* Obtenha informações sobre os seus trabalhos de Automação.
* Desencadear um e-mail ou alerta com base no estado do seu trabalho no livro de recortes (por exemplo, falhado ou suspenso).
* Escreva consultas avançadas através dos seus fluxos de trabalho.
* Correlacionar os empregos através das contas da Automação.
* Visualiza o teu histórico de trabalho ao longo do tempo.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-deployment-considerations"></a>Pré-requisitos e considerações de implantação

Para começar a enviar os seus registos de Automação para os registos do Monitor Azure, precisa de:

* O mais recente lançamento do [Azure PowerShell.](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/)
* Uma área de trabalho do Log Analytics. Para mais informações, consulte [Iniciar com registos do Monitor Azure](../log-analytics/log-analytics-get-started.md).
* O ID de recursos para a sua conta De automação Azure.

Utilize o seguinte comando para encontrar o ID de recurso para a sua conta De automação Azure:

```powershell-interactive
# Find the ResourceId for the Automation Account
Get-AzResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Para encontrar o ID de recursos para o seu espaço de trabalho Log Analytics, execute o seguinte comando PowerShell:

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Get-AzResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Se tiver mais de uma conta de Automação ou espaço de trabalho na saída dos comandos anteriores, encontre o nome que precisa para configurar e copiar o valor para o ID do recurso.

1. No portal Azure, selecione a sua conta De automação a partir da lâmina da **conta Automation** e selecione Todas **as definições**. 
2. A partir da lâmina **de todas as definições,** em definições de **conta,** selecione **Propriedades**.  
3. Na lâmina **Propriedades,** note estes valores.<br> ![Propriedades da](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)conta de automação.


## <a name="azure-monitor-log-records"></a>Registos de registos do Monitor Azure

Os diagnósticos da Azure Automation criam dois tipos de `AzureDiagnostics`registos em registos do Monitor Azure, marcados como . As tabelas nas secções seguintes são exemplos de registos que a Azure Automation gera e os tipos de dados que aparecem nos resultados de pesquisa de registo.

### <a name="job-logs"></a>Registos de trabalhos

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| Tenant_g | GUID que identifica o inquilino para quem liga. |
| JobId_g |GUID que identifica o trabalho do livro. |
| Tipo de resultados |O estado do trabalho do runbook. Os valores possíveis são:<br>- Novo<br>- Criado<br>- Iniciado<br>- Parado<br>- Suspenso<br>- Falhado<br>- Concluído |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobLogs. |
| OperationName | O tipo de operação realizada em Azure. Para automação, o valor é Job. |
| Recurso | O nome da conta Automation |
| SourceSystem | Sistema que os registos do Azure Monitor utilizam para recolher os dados. O valor é sempre Azure para diagnósticos Azure. |
| Descrição do resultado |O estado do resultado do trabalho do livro de corridas. Os valores possíveis são:<br>- Trabalho iniciado<br>- Trabalho falhado<br>- Trabalho Concluído |
| CorrelationId |A correlação GUID do trabalho do livro de corridas. |
| ResourceId |A identificação de recursos da conta Azure Automation do livro de execução. |
| SubscriptionId | A assinatura Azure GUID para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | O fornecedor de recursos. O valor é MICROSOFT. A AUTOMAÇÃO. |
| ResourceType | O tipo de recurso. O valor é AUTOMAÇÃOCONTAS. |

### <a name="job-streams"></a>Fluxos de trabalho
| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora da execução do trabalho do runbook. |
| RunbookName_s |O nome do runbook. |
| Caller_s |O chamador que iniciou a operação. Os valores possíveis são um endereço de e-mail ou o sistema para trabalhos agendados. |
| StreamType_s |O tipo de fluxo de trabalho. Os valores possíveis são:<br>- Em Curso<br>- Saída<br>- Aviso<br>- Erro<br>- Depuração<br>- Verboso |
| Tenant_g | GUID que identifica o inquilino para quem liga. |
| JobId_g |GUID que identifica o trabalho do livro. |
| Tipo de resultados |O estado do trabalho do runbook. Os valores possíveis são:<br>- Em curso |
| Categoria | Classificação do tipo de dados. Para a Automatização, o valor é JobStreams. |
| OperationName | Tipo de operação realizada em Azure. Para automação, o valor é Job. |
| Recurso | O nome da conta de Automação. |
| SourceSystem | Sistema que os registos do Azure Monitor utilizam para recolher os dados. O valor é sempre Azure para diagnósticos Azure. |
| Descrição do resultado |Descrição que inclui o fluxo de saída do livro de execução. |
| CorrelationId |A correlação GUID do trabalho do livro de corridas. |
| ResourceId |A identificação de recursos da conta Azure Automation do livro de execução. |
| SubscriptionId | A assinatura Azure GUID para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | O fornecedor de recursos. O valor é MICROSOFT. A AUTOMAÇÃO. |
| ResourceType | O tipo de recurso. O valor é AUTOMAÇÃOCONTAS. |

## <a name="setting-up-integration-with-azure-monitor-logs"></a>Criação de integração com registos do Monitor Azure

1. No seu computador, inicie o Windows PowerShell a partir do ecrã **Iniciar.**
2. Executar os seguintes comandos PowerShell e `[your resource ID]` `[resource ID of the log analytics workspace]` editar o valor para e com os valores da secção anterior.

   ```powershell-interactive
   $workspaceId = "[resource ID of the log analytics workspace]"
   $automationAccountId = "[resource ID of your Automation account]"

   Set-AzDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled 1
   ```

Depois de executar este script, pode demorar uma hora até começar a `JobLogs` `JobStreams` ver registos em registos do Monitor Azure de novos ou a serem escritos.

Para ver os registos, faça a seguinte consulta na pesquisa de registo sessão de análise de registo:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Verificar a configuração

Para confirmar que a sua conta Deautomaestá a enviar registos para o seu espaço de trabalho Log Analytics, verifique se os diagnósticos estão corretamente configurados na conta Automation utilizando o seguinte comando PowerShell.

```powershell-interactive
Get-AzDiagnosticSetting -ResourceId $automationAccountId
```

Na saída, certifique-se de que:

* Abaixo, `Logs`o `Enabled` valor para é verdade.
* `WorkspaceId`está definido `ResourceId` para o valor para o seu espaço de trabalho Log Analytics.

## <a name="viewing-automation-logs-in-azure-monitor-logs"></a>Visualização de registos de automação em registos do Monitor Azure

Agora que começou a enviar os seus registos de trabalho da Automation para os registos do Monitor Azure, vamos ver o que pode fazer com estes registos dentro dos registos do Monitor Azure.

Para ver os registos, faça a seguinte consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envie um e-mail quando um trabalho de livro de recortes falhar ou suspender

Um dos principais clientes pede é a capacidade de enviar um e-mail ou um texto quando algo corre mal com um trabalho de livro de corridas.

Para criar uma regra de alerta, comece por criar uma pesquisa de registo para os registos de trabalho do livro de execução que devem invocar o alerta. Clique no botão **Alerta** para criar e configurar a regra de alerta.

1. A partir da página de visualização geral do espaço de trabalho Log Analytics, clique em **ver registos**.
2. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`<br><br>Também pode agrupar pelo nome do livro de corridas utilizando:`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`

   Se configurar registos a partir de mais de uma conta Automation ou subscrição do seu espaço de trabalho, pode agrupar os seus alertas por subscrição e conta de Automação. O nome da conta `Resource` de automação `JobLogs`pode ser encontrado no campo na procura de .
3. Para abrir o ecrã de **regra Criar,** clique + **Nova Regra** de Alerta no topo da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [alertas de registo no Azure](../azure-monitor/platform/alerts-unified-log.md).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Encontre todos os empregos que tenham concluído com erros

Além de alertar sobre falhas, pode descobrir quando um trabalho de livro tem um erro não terminante. Nestes casos, a PowerShell produz um fluxo de erros, mas os erros não terminadores não fazem com que o seu trabalho suspenda ou falhe.

1. No seu espaço de trabalho Log Analytics, clique em **Registos**.
2. No campo de consulta, escreva `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`.
3. Clique no botão **'Procurar'.**

### <a name="view-job-streams-for-a-job"></a>Ver fluxos de trabalho para um trabalho

Quando se está a depurar um emprego, talvez queira ver os fluxos de emprego. A seguinte consulta mostra todos os fluxos para um único trabalho com GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0:

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Ver histórico estado de trabalho

Finalmente, talvez queiras visualizar o teu histórico de emprego ao longo do tempo. Pode usar esta consulta para procurar o estado dos seus trabalhos ao longo do tempo.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`
<br> ![Gráfico de estado histórico de trabalho de log analytics](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="removing-diagnostic-settings"></a>Remoção de definições de diagnóstico

Para remover a definição de diagnóstico da conta Automation, execute o seguinte comando:

```powershell-interactive
$automationAccountId = "[resource ID of your Automation account]"

Remove-AzDiagnosticSetting -ResourceId $automationAccountId
```

## <a name="summary"></a>Resumo

Ao enviar o seu estado de trabalho automatione e transmitir dados para registos do Monitor Azure, pode obter uma melhor visão sobre o estado dos seus trabalhos de Automação através de:
+ Configuração de alertas para notificá-lo quando há um problema.
+ Utilizando vistas personalizadas e consultas de pesquisa para visualizar os resultados do seu livro de execução, o estado do trabalho do livro de corridas e outros indicadores ou métricas relacionados.

Os registos do Azure Monitor proporcionam uma maior visibilidade operacional aos seus trabalhos de Automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Passos seguintes

* Para ajudar a resolver problemas no Log Analytics, consulte [a resolução de problemas porque é que o Log Analytics já não está a recolher dados](../azure-monitor/platform/manage-cost-storage.md#troubleshooting-why-log-analytics-is-no-longer-collecting-data).
* Para saber mais sobre como construir diferentes consultas de pesquisa e rever os registos de trabalho da Automation com registos do Monitor Azure, consulte as pesquisas de [registo em registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md).
* Para entender como criar e recuperar mensagens de saída e erro a partir de livros de execução, consulte a saída do Livro de [Ensaios e as mensagens](automation-runbook-output-and-messages.md).
* Para saber mais sobre a execução de runbooks, como monitorizar tarefas de runbooks e outros detalhes técnicos, veja [Acompanhar uma tarefa de runbook](automation-runbook-execution.md).
* Para saber mais sobre os registos do Monitor Do Azure e as fontes de recolha de dados, consulte a Recolha de dados de [armazenamento do Azure em toda a visão geral dos registos do Monitor Do Azure.](../azure-monitor/platform/collect-azure-metrics-logs.md)

