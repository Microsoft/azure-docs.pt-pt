---
title: Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs
description: Este artigo demonstra como enviar dados de relatório de DSC (configuração de estado desejado) da configuração de estado de automação do Azure para Azure Monitor logs para fornecer informações e gerenciamento adicionais.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5905afdb9832f32e837dc4496e4a951fca41b8b0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243556"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Encaminhe os dados do relatório de configuração de estado da automação do Azure para Azure Monitor logs

A configuração de estado da automação do Azure retém os dados de status do nó por 30 dias.
Você pode enviar dados de status do nó para seu espaço de trabalho do Log Analytics se preferir manter esses dados por um período mais longo.
O status de conformidade é visível no portal do Azure ou com o PowerShell, para nós e para recursos individuais de DSC em configurações de nó.
Com os logs de Azure Monitor, você pode:

- Obter informações de conformidade para nós gerenciados e recursos individuais
- Disparar um email ou alerta com base no status de conformidade
- Escrever consultas avançadas em seus nós gerenciados
- Correlacione o status de conformidade entre contas de automação
- Visualizar o histórico de conformidade do nó ao longo do tempo

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de configuração de estado de automação para logs de Azure Monitor, você precisa:

- A versão de novembro de 2016 ou posterior do [Azure PowerShell](/powershell/azure/overview) (v 2.3.0).
- Uma conta de Automatização do Azure. Para obter mais informações, consulte [introdução com a automação do Azure](automation-offering-get-started.md)
- Um espaço de trabalho Log Analytics com uma oferta de serviço de **controle de & de automação** . Para obter mais informações, consulte Introdução [aos logs de Azure monitor](../log-analytics/log-analytics-get-started.md).
- Pelo menos um nó de configuração de estado de automação do Azure. Para obter mais informações, consulte [integração de máquinas para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md)
- O módulo [xDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) , versão 2.7.0.0 ou superior. Para obter as etapas de instalação, consulte [Exibir logs de DSC em seu nó](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Configurar a integração com os logs de Azure Monitor

Para começar a importar dados do Azure DSC de Automação para Azure Monitor logs, conclua as seguintes etapas:

1. Faça logon em sua conta do Azure no PowerShell. Consulte [fazer logon com Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. Obtenha o _ResourceId_ da sua conta de automação executando o seguinte comando do PowerShell: (se você tiver mais de uma conta de automação, escolha o _ResourceId_ para a conta que você deseja configurar).

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha o _ResourceId_ do seu espaço de trabalho do log Analytics executando o seguinte comando do PowerShell: (se você tiver mais de um espaço de trabalho, escolha o _ResourceId_ para o espaço de trabalho que deseja configurar).

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Execute o seguinte comando do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` pelos valores _ResourceId_ de cada uma das etapas anteriores:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

Se você quiser parar de importar dados da configuração de estado da automação do Azure para logs de Azure Monitor, execute o seguinte comando do PowerShell:

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Exibir os logs de configuração de estado

Depois de configurar a integração com os logs de Azure Monitor para os dados de configuração de estado de automação, um botão de **pesquisa de log** será exibido na folha **nós DSC** da sua conta de automação. Clique no botão **pesquisa de logs** para exibir os logs dos dados do nó DSC.

![Botão pesquisa de log](media/automation-dsc-diagnostics/log-search-button.png)

A folha **pesquisa de logs** é aberta e você vê uma operação **DscNodeStatusData** para cada nó de configuração de estado e uma operação **DscResourceStatusData** para cada [recurso de DSC](/powershell/scripting/dsc/resources/resources) chamado na configuração de nó aplicada a esse nó.

A operação **DscResourceStatusData** contém informações de erro para todos os recursos de DSC que falharam.

Clique em cada operação na lista para ver os dados dessa operação.

Você também pode exibir os logs pesquisando em logs de Azure Monitor.
Consulte [Localizar dados usando pesquisas de log](../log-analytics/log-analytics-log-searches.md).
Digite a seguinte consulta para localizar seus logs de configuração de Estado: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Você também pode restringir a consulta pelo nome da operação. Por exemplo: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um email quando uma verificação de conformidade de configuração de estado falhar

Uma das nossas principais solicitações de clientes é a capacidade de enviar um email ou um texto quando algo der errado com uma configuração de DSC.

Para criar uma regra de alerta, você começa criando uma pesquisa de log para os registros de relatório de configuração de estado que devem invocar o alerta. Clique no botão **+ nova regra de alerta** para criar e configurar a regra de alerta.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **logs**.
1. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se você tiver configurado logs de mais de uma conta de automação ou assinatura para seu espaço de trabalho, poderá agrupar seus alertas por conta de automação e assinatura.
   O nome da conta de automação pode ser derivado do campo de recurso na pesquisa de DscNodeStatusData.
1. Para abrir a tela **criar regra** , clique em **+ nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções para configurar o alerta, consulte [criar uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontrar recursos de DSC com falha em todos os nós

Uma vantagem de usar os logs de Azure Monitor é que você pode procurar verificações com falha nos nós.
Para localizar todas as instâncias de recursos de DSC que falharam.

1. Na página Visão geral do espaço de trabalho Log Analytics, clique em **logs**.
1. Crie uma consulta de pesquisa de logs para seu alerta digitando a seguinte pesquisa no campo de consulta: `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Exibir status do nó de DSC do histórico

Por fim, talvez você queira Visualizar o histórico de status do nó DSC ao longo do tempo.
Você pode usar essa consulta para procurar o status do status do nó DSC ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Isso exibirá um gráfico do status do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registros de logs de Azure Monitor

O diagnóstico da automação do Azure cria duas categorias de registros em logs de Azure Monitor.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora em que a verificação de conformidade foi executada. |
| OperationName |DscNodeStatusData |
| resultType |Se o nó está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| NodeComplianceStatus_s |Se o nó está em conformidade. |
| DscReportStatus |Se a verificação de conformidade foi executada com êxito. |
| ConfigurationMode | Como a configuração é aplicada ao nó. Os valores possíveis são __"ApplyOnly"__ , __"ApplyandMonitior"__ e __"ApplyandAutoCorrect"__ . <ul><li>__ApplyOnly__: o DSC aplica a configuração e não faz nada além disso, a menos que uma nova configuração seja enviada por push ao nó de destino ou quando uma nova configuração for retirada de um servidor. Após a aplicação inicial de uma nova configuração, a DSC não verifica se há descompasso de um Estado previamente configurado. O DSC tenta aplicar a configuração até que ela seja bem-sucedida antes de __ApplyOnly__ entrar em vigor. </li><li> __ApplyAndMonitor__: esse é o valor padrão. O LCM aplica quaisquer novas configurações. Após a aplicação inicial de uma nova configuração, se o nó de destino se desconectar do estado desejado, a DSC relatará a discrepância nos logs. O DSC tenta aplicar a configuração até que ela seja bem-sucedida antes de __ApplyAndMonitor__ entrar em vigor.</li><li>__ApplyAndAutoCorrect__: o DSC aplica-se a novas configurações. Após a aplicação inicial de uma nova configuração, se o nó de destino se desconectar do estado desejado, a DSC relatará a discrepância nos logs e reaplicará a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerenciado. |
| IP | O endereço IPv4 do nó gerenciado. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automação do Azure. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| NodeId_g |GUID que identifica o nó gerenciado. |
| DscReportId_g |GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora em que o relatório foi exibido pela última vez. |
| ReportStartTime_t |Data e hora em que o relatório foi iniciado. |
| ReportEndTime_t |Data e hora em que o relatório foi concluído. |
| NumberOfResources_d |O número de recursos DSC chamados na configuração aplicada ao nó. |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResourceId |Especifica a conta de automação do Azure. |
| resultDescription | A descrição para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O. INICIA |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é a ID de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora em que a verificação de conformidade foi executada. |
| OperationName |DscResourceStatusData|
| resultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerenciado. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automação do Azure. |
| Tenant_g | GUID que identifica o locatário do chamador. |
| NodeId_g |GUID que identifica o nó gerenciado. |
| DscReportId_g |GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância de recurso de DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém o recurso de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém o recurso de DSC. |
| DscConfigurationName_s |O nome da configuração aplicada ao nó. |
| ErrorCode_s | O código de erro se o recurso falhou. |
| ErrorMessage_s |A mensagem de erro se o recurso falhou. |
| DscResourceDuration_d |O tempo, em segundos, que o recurso DSC foi executado. |
| SourceSystem | Como os logs de Azure Monitor coletaram os dados. Sempre *Azure* para diagnóstico do Azure. |
| ResourceId |Especifica a conta de automação do Azure. |
| resultDescription | A descrição para esta operação. |
| SubscriptionId | A ID da assinatura do Azure (GUID) para a conta de automação. |
| ResourceGroup | Nome do grupo de recursos para a conta de automação. |
| ResourceProvider | O. INICIA |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é a ID de correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar seus dados de configuração de estado de automação para logs de Azure Monitor, você pode obter uma melhor percepção do status de seus nós de configuração de estado de automação:

- Configurando alertas para notificá-lo quando houver um problema
- Usando exibições personalizadas e consultas de pesquisa para visualizar os resultados do runbook, o status do trabalho de runbook e outros indicadores ou métricas de chave relacionados.

Os logs de Azure Monitor fornecem maior visibilidade operacional para os dados de configuração do estado de automação e podem ajudar a resolver incidentes mais rapidamente.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral, consulte [configuração de estado da automação do Azure](automation-dsc-overview.md)
- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [cmdlets de configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
- Para saber mais sobre como construir diferentes consultas de pesquisa e examinar os logs de configuração de estado de automação com logs de Azure Monitor, consulte [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre os logs de Azure Monitor e fontes de coleta de dados, confira [visão geral sobre como coletar dados do armazenamento do Azure em logs de Azure monitor](../azure-monitor/platform/collect-azure-metrics-logs.md)
