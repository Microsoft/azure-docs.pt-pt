---
title: Consultar dados no Azure Monitor com o Explorador de dados do Azure (pré-visualização)
description: Neste tópico, consultar dados no Azure Monitor através da criação de um proxy do Explorador de dados do Azure para o produto cruzado consultas com o Application Insights e o Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800214"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados no Azure Monitor com o Explorador de dados do Azure (pré-visualização)

O cluster de proxy do Explorador de dados do Azure (ADX Proxy) é uma entidade que lhe permite realizar consultas de produto cruzado entre o Explorador de dados do Azure, [Application Insights (IA)](/azure/azure-monitor/app/app-insights-overview), e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) no [Do azure Monitor](/azure/azure-monitor/) serviço. Pode mapear as áreas de trabalho do Log Analytics do Azure Monitor ou de aplicações do Application Insights como um cluster de proxy. Em seguida, pode consultar o cluster de proxy com as ferramentas do Explorador de dados do Azure e fazer referência a ele numa consulta de cluster entre. O artigo mostra como ligar a um cluster de proxy, adicionar um cluster de proxy a IU da Web do Explorador de dados do Azure e executar consultas em relação a suas aplicações de IA ou áreas de trabalho LA a partir do Explorador de dados do Azure.

O fluxo de proxy do Explorador de dados do Azure: 

![Fluxo de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O Proxy de ADX está no modo de pré-visualização. Para ativar esta funcionalidade, entre em contato com o [ADXProxy](mailto:adxproxy@microsoft.com) equipe.

## <a name="connect-to-the-proxy"></a>Ligar ao proxy

1. Verifique se o seu cluster nativo do Explorador de dados do Azure (por exemplo, *ajudar* cluster) aparece no menu da esquerda, antes de ligar ao seu cluster do Log Analytics ou o Application Insights.

    ![Cluster de nativo ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Na IU do Explorador de dados do Azure (https://dataexplorer.azure.com/clusters), selecione **Cluster adicionar**.

1. Na **Cluster adicionar** janela:

    * Adicione o URL para o cluster de LA ou IA. Por exemplo: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se adicionar uma ligação a mais de um cluster de proxy, dar a cada um nome diferente. Caso contrário, todas terão o mesmo nome no painel esquerdo.

1. Depois da ligação é estabelecida, o seu cluster LA ou IA aparecerá no painel da esquerda com o seu cluster ADX nativo. 

    ![Clusters do log Analytics e o Explorador de dados do Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Pode utilizar o Explorador de Kusto, web ADX Explorer, Jupyter Kqlmagic ou REST API para consultar os clusters de proxy. 

> [!TIP]
> * Nome da base de dados deve ter o mesmo nome que o recurso especificado no cluster de proxy. Os nomes são maiúsculas de minúsculas.
> * No cluster consultas em várias, certifique-se de que o [de nomenclatura de aplicações e áreas de trabalho](#application-insights-app-and-log-analytics-workspace-names) está correto.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Consultar no cluster do Explorador de dados do Azure nativo 

Executar consultas no seu cluster do Explorador de dados do Azure (como *StormEvents* da tabela na *ajudar* cluster). Ao executar a consulta, certifique-se de que o seu cluster do Explorador de dados do Azure nativo é selecionado no painel esquerdo.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Consultar a tabela de StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Consultas em relação ao seu cluster LA ou IA

Ao executar consultas no seu cluster LA ou AL, certifique-se de que o seu cluster LA ou IA está selecionado no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Área de trabalho LA de consulta](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Consultar o seu cluster LA ou de AI do ADX proxy  

Quando executar consultas no seu cluster de LA ou de AI do proxy, certifique-se de que o cluster nativo ADX está selecionado no painel esquerdo. O exemplo seguinte demonstra uma consulta de área de trabalho de LA utilizando o cluster ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Consultar a partir do proxy do Explorador de dados do Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Em várias consultas de cluster de LA ou IA e o cluster ADX do ADX proxy 

Quando executa consultas de cluster cruzada do proxy, certifique-se de que o cluster nativo ADX está selecionado no painel esquerdo. Os exemplos seguintes demonstram combinar ADX tabelas de cluster (utilizar `union`) com a área de trabalho LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Passar a consulta de proxy do Explorador de dados do Azure](media/adx-proxy/cross-query-adx-proxy.png)

Utilizar o [ `join` operador](/azure/kusto/query/joinoperator), em vez de União, podem exigir uma sugestão para executá-lo num cluster nativo do Explorador de dados do Azure (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicionais

As seguintes opções de sintaxe estão disponíveis ao chamar os clusters do Application Insights (IA) ou o Log Analytics (LA):

|Descrição de sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de dados dentro de um cluster que contém apenas o recurso definido nesta subscrição (**recomendado para cruzada consultas de cluster**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster que contém todas as aplicações/áreas de trabalho nesta subscrição    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster que contém todas as aplicações/áreas de trabalho na subscrição e que são membros deste grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster que contém apenas o recurso definido nesta subscrição      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Aplicação do Application Insights e nomes de área de trabalho do Log Analytics

* Se os nomes de conter carateres especiais, eles estiverem substituídos pelo URL de codificação no nome do cluster de proxy. 
* Se os nomes incluem os carateres que não cumprem [regras de nome do identificador KQL](/azure/kusto/query/schema-entities/entity-names), o são substituídos pelo traço **-** caráter.

## <a name="next-steps"></a>Passos Seguintes

[Escrever consultas](write-queries.md)