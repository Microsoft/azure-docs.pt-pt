---
title: Forward Azure Automation Configuração de relatórios de dados para registos do Monitor Azure
description: Este artigo demonstra como enviar dados de informação de configuração do Estado Desejado (DSC) da Configuração do Estado da Automação Azure para registos do Monitor Azure para fornecer informações e gestão adicionais.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 578fcf4cd03a2d4fc8400b9e84f53206750a588c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430725"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>Forward Azure Automation Configuração de relatórios de dados para registos do Monitor Azure

A Configuração do Estado da Automação Azure mantém os dados do estado do nó durante 30 dias.
Pode enviar dados de estado do nó para o seu espaço de trabalho Log Analytics se preferir reter estes dados por um período mais longo.
O estado de conformidade é visível no portal Azure ou com powerShell, para nós e para recursos DSC individuais em configurações de nós.
Com registos do Monitor Azure pode:

- Obtenha informações de conformidade para nós geridos e recursos individuais
- Desencadear um e-mail ou alerta com base no estado de conformidade
- Escreva consultas avançadas através dos seus nódosos geridos
- Correlacionar o estado de conformidade entre as contas da Automação
- Visualize o histórico de conformidade do seu nó ao longo do tempo

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para começar a enviar os seus relatórios de Configuração do Estado de Automação para os registos do Monitor Azure, precisa de:

- O lançamento de novembro de 2016 ou posterior lançamento da [Azure PowerShell](/powershell/azure/overview) (v2.3.0).
- Uma conta de Automatização do Azure. Para mais informações, consulte [Uma introdução à Automação Azure.](automation-intro.md)
- Um espaço de trabalho log Analytics com uma oferta de serviço de controlo de & automation. Para mais informações, consulte [Iniciar com Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
- Pelo menos um nó de configuração do Estado da Automação Azure. Para mais informações, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- O módulo [xDscDiagnostics,](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0) versão 2.7.0.0 ou superior. Para os passos de instalação, consulte [Troubleshoot Azure Automation Desired State Configuration](./troubleshoot/desired-state-configuration.md).

## <a name="set-up-integration-with-azure-monitor-logs"></a>Instale a integração com os registos do Monitor Azure

Para começar a importar dados do Azure Automation DSC para os registos do Monitor Azure, complete os seguintes passos:

1. Faça login na sua conta Azure na PowerShell. Consulte o Signin e o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
1. Obtenha o ID de recurso da sua conta Automation executando o seguinte cmdlet PowerShell. Se tiver mais de uma conta de automação, escolha o ID de recurso para a conta que pretende configurar.

   ```powershell
   # Find the ResourceId for the Automation account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. Obtenha o ID de recursos do seu espaço de trabalho Log Analytics executando o seguinte cmdlet PowerShell. Se tiver mais de um espaço de trabalho, escolha o ID de recurso para o espaço de trabalho que pretende configurar.

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. Executar o seguinte cmdlet PowerShell, substituindo `<AutomationResourceId>` e `<WorkspaceResourceId>` com os valores *ResourceId* de cada um dos passos anteriores.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

1. Se pretender parar de importar dados da Configuração do Estado da Automação Azure em registos do Monitor Azure, execute o seguinte cmdlet PowerShell.

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
   ```

## <a name="view-the-state-configuration-logs"></a>Ver os registos de Configuração do Estado

Depois de configurar a integração com os registos do Monitor Azure para os seus dados de Configuração do Estado de Automatização, pode vê-los selecionando **Registos** na secção **de Monitorização** na página de configuração do Estado (DSC).

![Registos](media/automation-dsc-diagnostics/automation-dsc-logs-toc-item.png)

O painel de Pesquisa de **Registos** abre com uma região de consulta ao seu recurso de conta Automation. Pode pesquisar os registos de Configuração do Estado para operações de DSC, pesquisando em registos do Monitor Azure. Os registos das operações da DSC estão guardados na tabela AzureDiagnostics. Por exemplo, para encontrar nós que não sejam compatíveis, escreva a seguinte consulta.

```AzureDiagnostics
| where Category == 'DscNodeStatus' 
| where OperationName contains 'DSCNodeStatusData'
| where ResultType != 'Compliant'
```
Detalhes de filtragem:

* Filtrar o *DscNodeStatusData* para devolver as operações para cada nó de Configuração do Estado.
* Filtrar o *DscResourceStatusData* para devolver as operações de cada recurso DSC chamado na configuração do nó aplicada a esse recurso. 
* Filtrar o *DscResourceStatusData* para devolver informações de erro para quaisquer recursos DSC que falhem.

Para saber mais sobre a construção de consultas de registo para encontrar dados, consulte a [visão geral das consultas de registo no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>Envie um e-mail quando uma verificação de conformidade de configuração do Estado falhar

Um dos nossos principais pedidos de cliente é para a capacidade de enviar um e-mail ou um texto quando algo corre mal com uma configuração DSC.

Para criar uma regra de alerta, comece por criar uma pesquisa de registo para os registos do relatório de Configuração do Estado que devem invocar o alerta. Clique no botão **+ Nova Regra** de Alerta para criar e configurar a regra de alerta.

1. A partir da página de visualização geral do espaço de trabalho Log Analytics, clique em **Registos**.
1. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`

   Se tiver configurado registos a partir de mais de uma conta Automation ou subscrição do seu espaço de trabalho, pode agrupar os seus alertas por subscrição e conta de Automação. Obtenha o nome da conta Automation a partir do campo Derecurso na pesquisa de DscNodeStatusData.
1. Para abrir o ecrã de **regra Criar,** clique + **Nova Regra** de Alerta no topo da página. 

Para obter mais informações sobre as opções para configurar o alerta, consulte [Criar uma regra de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Encontre recursos DSC falhados em todos os nós

Uma vantagem de utilizar os registos do Monitor Azure é que pode procurar verificações falhadas em nós.
Para encontrar todos os casos de recursos da DSC que falharam:

1. Na página de visualização geral do espaço de trabalho Log Analytics, clique em **Registos**.
1. Crie uma consulta de pesquisa de registo para o seu alerta digitando a seguinte pesquisa no campo de consulta:`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`

### <a name="view-historical-dsc-node-status"></a>Ver histórico estado do nó DSC

Para visualizar o histórico do seu estado do nó DSC ao longo do tempo, pode usar esta consulta:

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

Esta consulta exibe um gráfico do estado do nó ao longo do tempo.

## <a name="azure-monitor-logs-records"></a>Registos de registos do Azure Monitor

Os diagnósticos da Azure Automation criam duas categorias de registos nos registos do Monitor Azure:

* Dados do estado do nó (DscNodeStatusData)
* Dados do estado dos recursos (DscResourceStatusData)

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi decorrido. |
| OperationName |DscNodeStatusData. |
| Tipo de resultados |Se o nó é compatível. |
| NodeName_s |O nome do nó gerido. |
| NodeComplianceStatus_s |Se o nó é compatível. |
| DscReportStatus |Se a verificação de conformidade correu com sucesso. |
| Modo de Configuração | Como a configuração é aplicada ao nó. Os valores possíveis são: <ul><li>*Aplicação Apenas:* O DSC aplica a configuração e não faz mais nada a menos que uma nova configuração seja empurrada para o nó alvo ou quando uma nova configuração é retirada de um servidor. Após a aplicação inicial de uma nova configuração, a DSC não verifica a deriva de um estado previamente configurado. O DSC tenta aplicar a configuração até que tenha sucesso antes que o valor *ApplyOnly* entre em vigor. </li><li>*ApplyAndMonitor*: Este é o valor predefinido. O LCM aplica novas configurações. Após a aplicação inicial de uma nova configuração, se o nó alvo deriva do estado pretendido, a DSC relata a discrepância nos registos. O DSC tenta aplicar a configuração até que tenha sucesso antes que o valor *ApplyAndMonitor* entre em vigor.</li><li>*AplicaçãoAndAutoCorrect*: DSC aplica quaisquer novas configurações. Após a aplicação inicial de uma nova configuração, se o nó alvo deriva do estado pretendido, a DSC relata a discrepância nos registos e, em seguida, reaplica a configuração atual.</li></ul> |
| HostName_s | O nome do nó gerido. |
| IPAddress | O endereço IPv4 do nó gerido. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta Azure Automation. |
| Tenant_g | GUID que identifica o inquilino para o Chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| LastSeenTime_t |Data e hora em que o relatório foi visto pela última vez. |
| ReportStartTime_t |Data e hora quando o relatório foi iniciado. |
| ReportEndTime_t |Data e hora quando o relatório estiver concluído. |
| NumberOfResources_d |O número de recursos dSC chamados na configuração aplicada ao nó. |
| SourceSystem | A forma como os registos do Monitor Azure recolheram os dados. Sempre "Azure" para diagnósticos Azure. |
| ResourceId |Identificador da conta Azure Automation. |
| Descrição do resultado | A descrição desta operação. |
| SubscriptionId | O ID de subscrição Azure (GUID) para a conta Automation. |
| ResourceGroup | Nome do grupo de recursos para a conta Automation. |
| ResourceProvider | A MICROSOFT. A AUTOMAÇÃO. |
| ResourceType | AUTOMATIZAÇÃOCONTAS. |
| CorrelationId |GUID que é o identificador de correlação do relatório de conformidade. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriedade | Descrição |
| --- | --- |
| TimeGenerated |Data e hora quando a verificação de conformidade foi decorrido. |
| OperationName |DscResourceStatusData|
| Tipo de resultados |Se o recurso é compatível. |
| NodeName_s |O nome do nó gerido. |
| Categoria | DscNodeStatus. |
| Recurso | O nome da conta Azure Automation. |
| Tenant_g | GUID que identifica o inquilino para o Chamador. |
| NodeId_g |GUID que identifica o nó gerido. |
| DscReportId_g |GUID que identifica o relatório. |
| DscResourceId_s |O nome da instância de recursos dSC. |
| DscResourceName_s |O nome do recurso DSC. |
| DscResourceStatus_s |Se o recurso DSC está em conformidade. |
| DscModuleName_s |O nome do módulo PowerShell que contém o recurso DSC. |
| DscModuleVersion_s |A versão do módulo PowerShell que contém o recurso DSC. |
| DscConfigurationName_s |O nome da configuração aplicado ao nó. |
| ErrorCode_s | O código de erro se o recurso falhar. |
| ErrorMessage_s |A mensagem de erro se o recurso falhou. |
| DscResourceDuration_d |O tempo, em segundos, de que o recurso dSC funcionou. |
| SourceSystem | A forma como os registos do Monitor Azure recolheram os dados. Sempre *Azure* para diagnósticos Azure. |
| ResourceId |Especifica a conta De automação Azure. |
| Descrição do resultado | A descrição desta operação. |
| SubscriptionId | O ID de subscrição Azure (GUID) para a conta Automation. |
| ResourceGroup | Nome do grupo de recursos para a conta Automation. |
| ResourceProvider | A MICROSOFT. A AUTOMAÇÃO. |
| ResourceType | AUTOMATIZAÇÃOCONTAS. |
| CorrelationId |GUID que é a Identificação correlação do relatório de conformidade. |

## <a name="summary"></a>Resumo

Ao enviar os seus dados de Configuração do Estado de Automação para registos do Monitor De Saúde, pode obter uma melhor visão sobre o estado dos seus nós de Configuração do Estado de Automação por:

- Configuração de alertas para notificá-lo quando há um problema
- Utilizando vistas personalizadas e consultas de pesquisa para visualizar os resultados do seu livro de execução, o estado do trabalho do livro de corridas e outros indicadores ou métricas relacionados.

Os registos do Monitor Azure proporcionam uma maior visibilidade operacional aos dados da Configuração do Estado da Automação e podem ajudar a resolver os incidentes mais rapidamente.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, consulte a Configuração do Estado da [Automação Azure](automation-dsc-overview.md)
- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
- Para saber mais sobre como construir diferentes consultas de pesquisa e rever os registos de Configuração do Estado da Automação com registos do Monitor Azure, consulte as pesquisas de [registo nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md)
- Para saber mais sobre os registos do Monitor Do Azure e as fontes de recolha de dados, consulte a recolha de dados de armazenamento do Azure em dados de [registos do Monitor Do Azure](../azure-monitor/platform/collect-azure-metrics-logs.md)
