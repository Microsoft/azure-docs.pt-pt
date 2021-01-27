---
title: Integre-se com registos do Monitor Azure
description: Este artigo diz como enviar dados de relatório de relatório de configuração do estado desejado da Azure Automation State Configuração para registos do Azure Monitor.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 34bbf34d53c44dcef7b8e128a93ee64201423c3e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897042"
---
# <a name="integrate-with-azure-monitor-logs"></a>Integre-se com registos do Monitor Azure

A Azure Automation State Configuration retém dados de estado do nó durante 30 dias. Pode enviar dados de estado do nó para o seu espaço de trabalho Log Analytics se preferir reter estes dados por um período mais longo. O estado de conformidade é visível no portal Azure ou com o PowerShell, para nós e para recursos DSC individuais em configurações de nó. 

Os registos do Azure Monitor proporcionam uma maior visibilidade operacional aos dados de Configuração do Estado da Automação e podem ajudar a resolver os incidentes mais rapidamente. Com registos do Monitor Azure pode:

- Obtenha informações de conformidade para nós geridos e recursos individuais.
- Desencadeie um e-mail ou alerta com base no estado de conformidade.
- Escreva consultas avançadas através dos seus nós geridos.
- Correlacionar o estado de conformidade nas contas de Automação.
- Utilize vistas personalizadas e consultas de pesquisa para visualizar os resultados do seu runbook, o estado do trabalho do runbook e outros indicadores ou métricas de chaves relacionadas.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar os seus relatórios de Configuração do Estado da Automação para os registos do Azure Monitor, é necessário:

- O lançamento de novembro de 2016 ou posterior da [Azure PowerShell](/powershell/azure/) (v2.3.0).
- Uma conta de Automatização do Azure. Para mais informações, consulte [Uma introdução à Azure Automation.](automation-intro.md)
- Um espaço de trabalho Log Analytics com uma oferta de serviço de Controlo de & de Automação. Para obter mais informações, consulte [Começar com o Log Analytics no Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).
- Pelo menos um nó de configuração do estado da Azure Automation. Para obter mais informações, consulte [máquinas de embarque para gestão através da Azure Automation State Configuration](automation-dsc-onboarding.md).
- O módulo [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versão 2.7.0.0 ou maior. Para etapas de instalação, consulte a configuração de [estado desejada da Automatização de Azure de Resolução](./troubleshoot/desired-state-configuration.md)de Problemas .

## <a name="set-up-integration-with-azure-monitor-logs"></a>Confiúdo de integração com registos do Azure Monitor

Para começar a importar dados da Azure Automation State Configuration para registos Azure Monitor, complete os seguintes passos:

1. Faça login na sua conta Azure em PowerShell. Ver [Iniciar sação com Azure PowerShell](/powershell/azure/authenticate-azureps).
1. Obtenha o ID de recursos da sua conta Automation executando o seguinte cmdlet PowerShell. Se tiver mais do que uma conta de automação, escolha o ID de recurso para a conta que pretende configurar.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha o ID de recursos do seu espaço de trabalho Log Analytics executando o seguinte cmdlet PowerShell. Se tiver mais do que um espaço de trabalho, escolha o ID de recursos para o espaço de trabalho que pretende configurar.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Executar o cmdlet PowerShell seguinte, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` com os `ResourceId` valores de cada um dos passos anteriores.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se pretender parar de importar dados da Configuração do Estado da Automação Azure para registos do Azure Monitor, execute o seguinte cmdlet PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Ver os registos de configuração do Estado

Depois de configurar a integração com os registos do Monitor Azure para os seus dados de Configuração do Estado da Automação, pode vê-los selecionando **Registos** na secção **de Monitorização** no painel esquerdo da página de configuração do Estado (DSC).

![Registos](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

O painel de pesquisa de log abre com uma região de consulta scopeed para o seu recurso de conta Automation. Pode pesquisar os registos de Configuração do Estado para operações de DSC pesquisando nos registos do Azure Monitor. Os registos das operações da DSC estão guardados na `AzureDiagnostics` tabela. Por exemplo, para encontrar nós que não estejam em conformidade, digite a seguinte consulta.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```

Detalhes da filtragem:

* Filtrar `DscNodeStatusData` para devolver as operações de retorno de cada nó de configuração do Estado.
* Filtrar `DscResourceStatusData` para devolver as operações de cada recurso DSC chamado na configuração do nó aplicado a esse recurso. 
* Filtrar `DscResourceStatusData` para devolver informações de erro para quaisquer recursos DSC que falhem.

Para saber mais sobre a construção de consultas de registo para encontrar dados, consulte [a visão geral das consultas de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Envie um e-mail quando uma verificação de conformidade de configuração do Estado falhar

Um dos nossos principais pedidos de clientes é a capacidade de enviar um e-mail ou um texto quando algo corre mal com uma configuração DSC.

Para criar uma regra de alerta, comece por criar uma pesquisa de registo para os registos de relatórios de configuração do Estado que devem invocar o alerta. Clique no botão **Nova Regra de Alerta** para criar e configurar a regra de alerta.

1. A partir da página de visão geral do espaço de trabalho do Log Analytics, clique em **Registars**.
1. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:  `Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se tiver configurado registos de mais de uma conta de Automação ou subscrição do seu espaço de trabalho, pode agrupar os seus alertas por subscrição e conta Demôm automação. Obtém o nome da conta Automation do `Resource` campo na procura dos `DscNodeStatusData` registos.
1. Para abrir o ecrã **de regras Criar,** clique em **Nova Regra de Alerta** no topo da página. 

Para obter mais informações sobre as opções para configurar o alerta, consulte [Criar uma regra de alerta.](../azure-monitor/platform/alerts-metric.md)

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontre recursos DSC falhados em todos os nós

Uma vantagem de utilizar registos do Azure Monitor é que pode procurar verificações falhadas através de nós. Para encontrar todas as instâncias de recursos da DSC que falharam:

1. Na página de visão geral do espaço de trabalho do Log Analytics, clique em **Registos**.
1. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:  `Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Ver estado histórico do nó DSC

Para visualizar o histórico de estado do nó DSC ao longo do tempo, pode utilizar esta consulta:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Esta consulta apresenta um gráfico do estado do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registos de registos do Monitor Azure

Os diagnósticos da Azure Automation criam duas categorias de registos em registos do Azure Monitor:

* Dados de estado do nó `DscNodeStatusData` ()
* Dados de estado de recursos `DscResourceStatusData` ()

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade correu. |
| OperationName |`DscNodeStatusData`. |
| ResultType |Valor que indica se o nó está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| NodeComplianceStatus_s |Valor de estado que especifica se o nó está em conformidade. |
| DscReportStatus |Valor do estado indicando se a verificação de conformidade correu com sucesso. |
| ConfiguraçãoMode | O modo utilizado para aplicar a configuração no nó. Os valores possíveis são: <ul><li>`ApplyOnly`: O DSC aplica a configuração e não faz mais nada a menos que uma nova configuração seja empurrada para o nó-alvo ou quando uma nova configuração é retirada de um servidor. Após a aplicação inicial de uma nova configuração, o DSC não verifica se deriva de um estado previamente configurado. A DSC tenta aplicar a configuração até que seja bem sucedida antes que o `ApplyOnly` valor produza efeito. </li><li>`ApplyAndMonitor`: Este é o valor predefinido. O LCM aplica quaisquer novas configurações. Após a aplicação inicial de uma nova configuração, se o nó-alvo derivar do estado pretendido, a DSC reporta a discrepância nos registos. A DSC tenta aplicar a configuração até que seja bem sucedida antes que o `ApplyAndMonitor` valor produza efeito.</li><li>`ApplyAndAutoCorrect`: O DSC aplica quaisquer novas configurações. Após a aplicação inicial de uma nova configuração, se o nó-alvo derivar do estado pretendido, o DSC reporta a discrepância nos registos e, em seguida, reaplica a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerido. |
| IPAddress | O endereço IPv4 do nó gerido. |
| Categoria | `DscNodeStatus`. |
| Recurso | O nome da conta Azure Automation. |
| Tenant_g | GUID que identifica o inquilino para quem ligou. |
| NodeId_g | GUID que identifica o nó gerido. |
| DscReportId_g | GUID que identifica o relatório. |
| LastSeenTime_t | Data e hora em que o relatório foi visto pela última vez. |
| ReportStartTime_t | Data e hora quando o relatório foi iniciado. |
| ReportEndTime_t | Data e hora quando o relatório foi concluído. |
| NumberOfResources_d | O número de recursos DSC chamados na configuração aplicada ao nó. |
| SourceSystem | O sistema de origem que identifica como os registos do Azure Monitor recolheram os dados. Sempre `Azure` para diagnósticos Azure. |
| ResourceId |O identificador de recursos da conta Azure Automation. |
| ResultDescription | A descrição dos recursos para esta operação. |
| SubscriptionId | O ID de assinatura Azure (GUID) para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | A MICROSOFT. AUTOMAÇÃO. |
| ResourceType | CONTAGEM DE AUTOMAÇÃO. |
| CorrelationId | Um GUID que é o identificador de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade correu. |
| OperationName |`DscResourceStatusData`.|
| ResultType |Se o recurso está em conformidade. |
| NodeName_s |O nome do nó gerido. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta Azure Automation. |
| Tenant_g | GUID que identifica o inquilino para quem ligou. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância de recursos do DSC. |
| DscResourceName_s |O nome do recurso DSC. |
| DscResourceStatus_s |Se o recurso DSC está em conformidade. |
| DscModuleName_s |O nome do módulo PowerShell que contém o recurso DSC. |
| DscModuleVersion_s |A versão do módulo PowerShell que contém o recurso DSC. |
| DscConfigurationName_s |O nome da configuração aplicada ao nó. |
| ErrorCode_s | O código de erro se o recurso falhar. |
| ErrorMessage_s |A mensagem de erro se o recurso falhar. |
| DscResourceDuration_d |O tempo, em segundos, que o recurso DSC correu. |
| SourceSystem | Como os registos do Azure Monitor recolheram os dados. Sempre `Azure` para diagnósticos Azure. |
| ResourceId |O identificador da conta da Azure Automation. |
| ResultDescription | A descrição desta operação. |
| SubscriptionId | O ID de assinatura Azure (GUID) para a conta Automation. |
| ResourceGroup | O nome do grupo de recursos para a conta Automation. |
| ResourceProvider | A MICROSOFT. AUTOMAÇÃO. |
| ResourceType | CONTAGEM DE AUTOMAÇÃO. |
| CorrelationId |GUID que é a identificação de correlação do relatório de conformidade. |

## <a name="next-steps"></a>Próximos passos

- Para obter uma visão geral, consulte [a visão geral da configuração do Estado da Automação Azure](automation-dsc-overview.md).
- Para começar, consulte [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para saber mais sobre como construir diferentes consultas de pesquisa e rever os registos de Configuração do Estado da Automação com registos do Monitor Azure, consulte [as pesquisas de Registo nos registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
- Para saber mais sobre os registos do Azure Monitor e as fontes de recolha de dados, consulte [a recolha de dados de armazenamento do Azure monitor na visão geral dos registos do Azure Monitor](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace).