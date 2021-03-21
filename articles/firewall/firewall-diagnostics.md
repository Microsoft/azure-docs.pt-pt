---
title: Monitorar registos e métricas do Azure Firewall
description: Neste artigo, aprende-se a ativar e gerir registos e métricas do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 52c6ef9edfc42bf1ad3b3279e0fa4e19b4cf502c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98788269"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorar registos e métricas do Azure Firewall

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall. Com as métricas, pode ver os contadores de desempenho no portal.

Pode aceder a alguns destes registos através do portal. Os registos podem ser enviados para [registos do Monitor Azure,](../azure-monitor/insights/azure-networking-analytics.md)Centros de Armazenamento e Evento e analisados em registos do Azure Monitor ou por diferentes ferramentas como o Excel e o Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ler [registos e métricas do Azure Firewall](logs-and-metrics.md) para uma visão geral dos registos de diagnóstico e métricas disponíveis para a Azure Firewall.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Ativar o registo de diagnósticos através do portal do Azure

Pode demorar alguns minutos até que os dados sejam apresentados nos seus registos, depois de concluir este procedimento para ativar o registo de diagnósticos. Se não vir nada inicialmente, verifique novamente passado alguns minutos.

1. No portal Azure, abra o seu grupo de recursos de firewall e selecione a firewall.
2. Em **Monitorização**, selecione **Definições de diagnóstico**.

   Para a Azure Firewall, estão disponíveis quatro registos específicos do serviço:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selecione **Adicionar definição de diagnóstico**. A página **Definições de diagnóstico** fornece as definições para os registos de diagnóstico.
5. Neste exemplo, os registos do Azure Monitor armazenam os registos, por isso **digite a análise do registo de Firewall** para o nome.
6. Em **Log**, selecione **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule**, **AzureFirewallThreatIntelLog** e **AzureFirewallDnsProxy** para recolher os registos.
7. Selecione **Enviar para Registar Analytics** para configurar o seu espaço de trabalho.
8. Selecione a sua subscrição.
9. Selecione **Guardar**.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>Ativar a sessão de diagnóstico utilizando o PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. O registo de diagnósticos tem de estar ativado para iniciar a recolha dos dados disponíveis através desses registos.

Para ativar o registo de diagnóstico com o PowerShell, utilize os seguintes passos:

1. Note o seu ID de recursos do espaço de trabalho log Analytics, onde os dados de registo são armazenados. Este valor é do formulário: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>` .

   Pode utilizar qualquer espaço de trabalho na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações. A informação está localizada na página **propriedades** de recursos.

2. Anote o ID de recurso da Firewall para o qual o registo está ativado. Este valor é do formulário: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Pode utilizar o portal para encontrar estas informações.

3. Ativar o registo de diagnóstico para todos os registos e métricas utilizando o seguinte cmdlet PowerShell:

   ```powershell
   $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
   }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Ativar o registo de diagnóstico utilizando o CLI Azure

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. O registo de diagnósticos tem de estar ativado para iniciar a recolha dos dados disponíveis através desses registos.

Para ativar o registo de diagnóstico com o Azure CLI, utilize os seguintes passos:

1. Note o seu ID de recursos do espaço de trabalho log Analytics, onde os dados de registo são armazenados. Este valor é do formulário: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Pode utilizar qualquer espaço de trabalho na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações. A informação está localizada na página **propriedades** de recursos.

2. Anote o ID de recurso da Firewall para o qual o registo está ativado. Este valor é do formulário: `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>` .

   Pode utilizar o portal para encontrar estas informações.

3. Ativar o registo de diagnóstico para todos os registos e métricas utilizando o seguinte comando Azure CLI:

   ```azurecli-interactive
   az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode ver e analisar os dados de registo de atividades através de um dos seguintes métodos:

* **Ferramentas do Azure**: recuperar informações de registo de atividades através do Azure PowerShell, a CLI do Azure, a API REST do Azure ou o portal do Azure. As instruções passo-a-passo para cada método estão detalhadas no artigo [Operações de atividades com o Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI**: se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), pode experimentá-lo gratuitamente. Ao utilizar o [pacote de conteúdos de Registos de Atividades do Azure para o Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), pode analisar os seus dados com dashboards pré-configurados que podem ser utilizados tal como estão ou personalizados.
* **Azure Sentinel**: Pode ligar os registos do Azure Firewall ao Azure Sentinel, permitindo-lhe visualizar dados de registo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a sua investigação. O conector de dados Azure Firewall em Azure Sentinel está atualmente em pré-visualização pública. Para obter mais informações, consulte [os dados do Azure Firewall.](../sentinel/connect-azure-firewall.md)

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Ver e analisar os registos de regras de rede e de aplicação

[Os registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) recolhem os ficheiros de registo de contador e de evento. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Para consultas de amostras de registo de registo Azure Firewall, consulte [amostras de análise de registo de registos do Azure Firewall](./firewall-workbook.md).

[O Azure Firewall Workbook](firewall-workbook.md) fornece uma tela flexível para a análise de dados do Azure Firewall. Pode usá-lo para criar relatórios visuais ricos dentro do portal Azure. Você pode aceder a várias Firewalls implantadas em Azure, e combiná-las em experiências interativas unificadas.

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="view-metrics"></a>Ver métricas
Navegue por uma Firewall Azure, em **Métricas selecionadas** **de Monitorização** . Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste a tua firewall para recolher registos, podes explorar registos do Azure Monitor para visualizar os teus dados.

[Monitorize registos usando o Livro de Trabalho da Firewall Azure](firewall-workbook.md)

[Soluções de monitorização de rede nos registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)