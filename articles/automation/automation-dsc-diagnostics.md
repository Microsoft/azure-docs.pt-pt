---
title: Reencaminhar a configuração de estado de automatização do Azure a comunicar dados para o Log Analytics
description: Este artigo demonstra como enviar Desired State Configuration (DSC) dados de configuração de estado de automatização do Azure para o Log Analytics para fornecer informações adicionais e gestão de relatórios.
services: automation
ms.service: automation
ms.component: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 62d201e608864949c86e52d8ac8fbf9ce8538f11
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631468"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-log-analytics"></a>Reencaminhar a configuração de estado de automatização do Azure a comunicar dados para o Log Analytics

A configuração de estado de automatização do Azure pode enviar dados de estado do nó Desired State Configuration (DSC) para a área de trabalho do Log Analytics. Estado de conformidade é visível no portal do Azure ou com o PowerShell, para nós e para recursos de DSC individuais em configurações de nó. Com o Log Analytics, pode:

- Obter as informações de conformidade para os nós geridos e recursos individuais
- Acionar um e-mail ou o alerta com base no estado de conformidade
- Escrever consultas avançadas nos seus nós gerenciados
- Correlacionar o estado de conformidade em contas de automatização
- Visualizar o histórico de conformidade do nó ao longo do tempo

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar seus relatórios de configuração de estado de automatização para o Log Analytics, tem de:

- Versão de Novembro 2016 ou posterior do [do Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Uma conta de Automatização do Azure. Para obter mais informações, consulte [introdução à automatização do Azure](automation-offering-get-started.md)
- Uma área de trabalho do Log Analytics com uma **automatização & controlo** oferta de serviço. Para obter mais informações, consulte [introdução ao Log Analytics](../log-analytics/log-analytics-get-started.md).
- Pelo menos um nó de configuração de estado de automatização do Azure. Para obter mais informações, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md)

## <a name="set-up-integration-with-log-analytics"></a>Configurar a integração com o Log Analytics

Para começar a importar dados do Azure Automation DSC para o Log Analytics, conclua os seguintes passos:

1. Inicie sessão na sua conta do Azure no PowerShell. Consulte [iniciar sessão com o Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0)
1. Obter o _ResourceId_ da sua conta de automatização ao executar o seguinte comando do PowerShell: (se tiver mais do que uma conta de automatização, escolha a _ResourceID_ para a conta que pretende configurar).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType 'Microsoft.Automation/automationAccounts'
  ```

1. Obter o _ResourceId_ da área de trabalho do Log Analytics, executando o seguinte comando do PowerShell: (se tiver mais do que uma área de trabalho, escolha a _ResourceID_ para a área de trabalho que pretende configurar).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
  ```

1. Execute o seguinte comando do PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` com o _ResourceId_ valores de cada um dos passos anteriores:

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories 'DscNodeStatus'
  ```

Se pretender parar a importação de dados de configuração de estado de automatização do Azure para o Log Analytics, execute o seguinte comando do PowerShell:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>Ver os registos de configuração de estado

Depois de configurar a integração com o Log Analytics para os seus dados de configuração de estado de automatização, um **pesquisa de registos** botão aparecerá no **nós de DSC** painel da sua conta de automatização. Clique nas **pesquisa de registos** botão para ver os registos de dados do nó DSC.

![Botão de pesquisa de registo](media/automation-dsc-diagnostics/log-search-button.png)

O **pesquisa de registos** é apresentado o painel e vir um **DscNodeStatusData** operação para cada nó de configuração de estado e um **DscResourceStatusData** operação para cada [ Recursos de DSC](/powershell/dsc/resources) chamado na configuração do nó aplicada para esse nó.

O **DscResourceStatusData** operação contém informações de erro para quaisquer recursos de DSC que falhou.

Clique em cada operação na lista para ver os dados para essa operação.

Também pode ver os registos, [a procurar no Log Analytics. Ver [encontrar os dados com pesquisas de registos](../log-analytics/log-analytics-log-searches.md).
Escreva a consulta seguinte para localizar os registos de configuração de estado: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

Também pode diminuir a consulta pelo nome da operação. Por exemplo: `Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Enviar um e-mail quando ocorre uma falha de uma verificação de conformidade de configuração de estado

Uma das solicitações de nossos principais clientes é a capacidade enviar um e-mail ou uma mensagem de texto quando algo dá errado com uma configuração de DSC.

Para criar uma regra de alerta, começa criando uma pesquisa de registos para os registos de relatório de configuração de estado que deve invocar o alerta. Clique nas **+ nova regra de alerta** botão para criar e configurar a regra de alerta.

1. Na página Descrição geral do Log Analytics, clique em **pesquisa de registos**.
1. Crie uma consulta de pesquisa de registo para o alerta, escrevendo a seguinte pesquisa no campo de consulta:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se tiver configurado registos de mais do que uma subscrição ou conta de automatização à área de trabalho, pode agrupar os alertas por conta de automatização e subscrição.  
   Nome da conta de automatização pode derivar do campo de recurso na pesquisa de DscNodeStatusData.  
1. Para abrir o **criar regra** ecrã, clique em **+ nova regra de alerta** na parte superior da página. Para obter mais informações sobre as opções de configuração do alerta, consulte [criar um alerta rulelert](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontre os recursos de DSC com falhas em todos os nós

Uma das vantagens de utilizar o Log Analytics é que pode pesquisar por verificações falhadas em todos os nós.
Para localizar todas as instâncias de recursos de DSC que falhou.

1. Na página Descrição geral do Log Analytics, clique em **pesquisa de registos**.
1. Crie uma consulta de pesquisa de registo para o alerta, escrevendo a seguinte pesquisa no campo de consulta:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Ver estado histórico de nó DSC

Por fim, pode querer visualizar o histórico de estado do nó DSC ao longo do tempo.  
Pode utilizar esta consulta para procurar o estado do seu estado de nó DSC ao longo do tempo.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Esta ação apresenta um gráfico de estado do nó ao longo do tempo.

## <a name="log-analytics-records"></a>Registos do Log Analytics

Diagnóstico da automatização do Azure cria duas categorias de registos no Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscNodeStatusData |
| resultType |Se o nó está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| NodeComplianceStatus_s |Se o nó está em conformidade. |
| DscReportStatus |Se a verificação de conformidade foi executado com êxito. |
| ConfigurationMode | Como a configuração é aplicada ao nó. Os valores possíveis são __"ApplyOnly"__,__"ApplyandMonitior"__, e __"ApplyandAutoCorrect"__. <ul><li>__ApplyOnly__: aplica-se a configuração de DSC e não faz nada além disso, a menos que uma nova configuração é enviada por push para o nó de destino ou quando uma nova configuração é obtida a partir de um servidor. Depois de aplicativo inicial de uma nova configuração, DSC não verifica se desviam de um estado anteriormente configurado. DSC tenta aplicar a configuração, até que seja bem-sucedida __ApplyOnly__ entra em vigor. </li><li> __ApplyAndMonitor__: Este é o valor predefinido. O LCM aplica-se quaisquer configurações de novo. Após a aplicação inicial de uma configuração de novo, se o nó de destino drifts do estado pretendido, o DSC relatórios discrepância nos registos. DSC tenta aplicar a configuração, até que seja bem-sucedida __ApplyAndMonitor__ entra em vigor.</li><li>__ApplyAndAutoCorrect__: DSC aplica-se quaisquer configurações de novo. Depois de aplicativo inicial de uma nova configuração, se o nó de destino drifts do estado pretendido, DSC relatórios discrepância nos registos e, em seguida, volta a aplicar a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerido. |
| iPAddress | O endereço IPv4 do nó gerido. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automatização do Azure. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora quando o relatório foi última visualização. |
| ReportStartTime_t |Data e hora quando o relatório foi iniciado. |
| ReportEndTime_t |Data e hora quando o relatório concluído. |
| NumberOfResources_d |O número de recursos de DSC chamado na configuração aplicada ao nó. |
| SourceSystem | Como o Log Analytics recolhidos os dados. Sempre *Azure* para obter um diagnóstico do Azure. |
| ResourceId |Especifica a conta de automatização do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A subscrição do Azure Id (GUID) da conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é o Id de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi executada. |
| OperationName |DscResourceStatusData|
| resultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| Categoria | DscNodeStatus |
| Recurso | O nome da conta de automatização do Azure. |
| Tenant_g | GUID que identifica o inquilino para o chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância de recursos de DSC. |
| DscResourceName_s |O nome do recurso de DSC. |
| DscResourceStatus_s |Se o recurso de DSC está em conformidade. |
| DscModuleName_s |O nome do módulo do PowerShell que contém o recurso de DSC. |
| DscModuleVersion_s |A versão do módulo do PowerShell que contém o recurso de DSC. |
| DscConfigurationName_s |O nome da configuração aplicado ao nó. |
| ErrorCode_s | O código de erro se o recurso falhou. |
| ErrorMessage_s |A mensagem de erro se o recurso falhou. |
| DscResourceDuration_d |O tempo, em segundos, em que executou o recurso de DSC. |
| SourceSystem | Como o Log Analytics recolhidos os dados. Sempre *Azure* para obter um diagnóstico do Azure. |
| ResourceId |Especifica a conta de automatização do Azure. |
| ResultDescription | A descrição para esta operação. |
| SubscriptionId | A subscrição do Azure Id (GUID) da conta de automatização. |
| ResourceGroup | Nome do grupo de recursos para a conta de automatização. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID que é o Id de correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar os dados de configuração de estado de automatização para o Log Analytics, pode obter mais idéias sobre o estado dos seus nós de configuração de estado de automatização por:

- Configuração de alertas para notificá-lo quando existe um problema
- Utilizar vistas personalizadas e consultas de pesquisa para visualizar os resultados do runbook, estado da tarefa de runbook e outras associadas principais indicadores ou métricas.  

O log Analytics fornece maior visibilidade operacional aos seus dados de configuração de estado de automatização e pode ajudar aborde incidentes mais rapidamente.

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral, consulte [configuração de estado de automatização do Azure](automation-dsc-overview.md)
- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
- Para saber mais sobre como construir consultas de pesquisa diferentes e rever os registos de configuração de estado de automatização com o Log Analytics, consulte [pesquisas de registos no Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre o Log Analytics e origens de recolha de dados, consulte [dados de armazenamento do Azure recolha na descrição geral do Log Analytics](../log-analytics/log-analytics-azure-storage.md)