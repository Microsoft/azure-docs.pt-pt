---
title: 'Tutorial: monitorizar registos e métricas do Azure Firewall'
description: Neste tutorial, vai aprender a ativar e gerir registos e métricas do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 2befbf66733430e6077f5e5ff3044c30a77b7e5c
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958988"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Tutorial: Monitorizar métricas e registos de Firewall do Azure

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall. Com as métricas, pode ver os contadores de desempenho no portal. 

Pode aceder a alguns destes registos através do portal. Os registos podem ser enviados para [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e os Hubs de eventos e analisados no Azure Monitor registos ou através de ferramentas diferentes, como o Excel e o Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Ativar o registo através do portal do Azure
> * Ativar registo com o PowerShell
> * Ver e analisar o registo de atividades
> * Ver e analisar os registos de regras de rede e de aplicação
> * Ver métricas

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, deve ler as [métricas e registos do Azure Firewall](logs-and-metrics.md) para uma descrição geral dos registos de diagnóstico e métricas disponíveis para o Azure Firewall.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Ativar o registo de diagnósticos através do portal do Azure

Pode demorar alguns minutos até que os dados sejam apresentados nos seus registos, depois de concluir este procedimento para ativar o registo de diagnósticos. Se não vir nada inicialmente, verifique novamente passado alguns minutos.

1. No portal do Azure, abra o grupo de recursos da firewall e clique na firewall.
2. Em **Monitorização**, clique em **Registos de diagnóstico**.

   No Azure Firewall, estão disponíveis dois registos específicos do serviço:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Para iniciar a recolha de dados, clique em **Ativar os diagnósticos**.
4. A página **Definições de diagnóstico** fornece as definições para os registos de diagnóstico. 
5. Neste exemplo, registos de Monitor do Azure armazena os logs, por isso escreva **análise do log de Firewall** para o nome.
6. Clique em **Enviar para o Log Analytics** para configurar a sua área de trabalho. Também pode utilizar os hubs de eventos e uma conta de armazenamento para guardar os registos de diagnóstico.
7. Em **Log Analytics**, clique em **Configurar**.
8. Na página Áreas de Trabalho do Log Analytics, clique em **Criar Nova Área de Trabalho**.
9. Na página **Área de trabalho do Log Analytics**, escreva **firewall-oms** como o novo nome da **Área de trabalho do Log Analytics**.
10. Selecione a sua subscrição, utilize o grupo de recursos de firewall existente (**Test-FW-RG**), selecione **E.U.A. Leste** como localização e selecione o escalão de preço **Gratuito**.
11. Clique em **OK**.
   ![Iniciar o processo de configuração][1] As áreas de trabalho do OMS são agora referidas como áreas de trabalho do Log Analytics.  
12. Em **Registo**, clique em **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** para recolher registos para as regras de aplicação e de rede.
   ![Guardar as definições de diagnóstico][2]
13. Clique em **Guardar**.

## <a name="enable-logging-with-powershell"></a>Ativar registo com o PowerShell

O registo de atividades é ativado automaticamente para todos os recursos do Resource Manager. O registo de diagnósticos tem de estar ativado para iniciar a recolha dos dados disponíveis através desses registos.

Para ativar o registo de diagnósticos, utilize os seguintes passos:

1. Anote o ID de recurso da conta de armazenamento, onde os dados de registo são armazenados. Este valor é do formulário: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>*.

   Pode utilizar qualquer conta de armazenamento na sua subscrição. Pode utilizar o portal do Azure para encontrar estas informações. As informações que estão localizadas na página do recurso **Propriedade**.

2. Anote o ID de recurso da Firewall para o qual o registo está ativado. Este valor é do formulário: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/azureFirewalls/\<Nome da firewall\>*.

   Pode utilizar o portal para encontrar estas informações.

3. Ative o registo de diagnósticos com o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Os registos de diagnóstico não necessitam de uma conta de armazenamento separada. A utilização do armazenamento para registo do acesso e do desempenho incorre em encargos de serviços.

## <a name="view-and-analyze-the-activity-log"></a>Ver e analisar o registo de atividades

Pode ver e analisar os dados de registo de atividades através de um dos seguintes métodos:

* **Ferramentas do Azure**: Obter informações de registo de Atividades através do Azure PowerShell, a CLI do Azure, a API de REST do Azure ou o portal do Azure. As instruções passo-a-passo para cada método estão detalhadas no artigo [Operações de atividades com o Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI**: Se ainda não tiver uma [Power BI](https://powerbi.microsoft.com/pricing) conta, pode experimentar gratuitamente. Ao utilizar o [pacote de conteúdos de Registos de Atividades do Azure para o Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), pode analisar os seus dados com dashboards pré-configurados que podem ser utilizados tal como estão ou personalizados.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Ver e analisar os registos de regras de rede e de aplicação

[Registos de Monitor do Azure](../azure-monitor/insights/azure-networking-analytics.md) recolhe os ficheiros de registo de eventos e de contador. Inclui visualizações e capacidades de pesquisa poderosas para analisar os seus registos.

Para consultas de exemplo de análise de registo de Firewall do Azure, veja [amostras do Firewall do Azure log analytics](log-analytics-samples.md).

Também pode ligar à sua conta de armazenamento e obter as entradas de registo JSON para os registos de acesso e desempenho. Depois de transferir os ficheiros JSON, pode convertê-los em CSV e visualizá-los no Excel, Power BI ou qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com os conceitos básicos do Visual Studio para alterar os valores de constantes e variáveis em C#, pode utilizar as [ferramentas de conversor de registo](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="view-metrics"></a>Ver métricas
Navegue até ao Azure Firewall, em **Monitorização**, clique em **Métricas**. Para ver os valores disponíveis, selecione a lista pendente **MÉTRICA**.

## <a name="next-steps"></a>Passos Seguintes

Agora que configurou a firewall para recolher registos, pode explorar os registos do Azure Monitor para ver os seus dados.

> [!div class="nextstepaction"]
> [Monitorização de soluções no Azure Monitor registos da rede](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
