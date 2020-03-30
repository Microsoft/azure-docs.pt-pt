---
title: Dados de consulta no Monitor Azure com O Explorador de Dados Azure (Pré-visualização)
description: Neste tópico, consulta dados no Monitor Azure através da criação de um proxy Azure Data Explorer para consultas de produtos cruzados com Insights de Aplicação e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: c7e98c31c0db1db3051ad66df6526dcbddb265c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560427"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dados de consulta no Monitor Azure utilizando o Azure Data Explorer (Pré-visualização)

O cluster de proxy do Azure Data Explorer (ADX Proxy) é uma entidade que lhe permite realizar consultas de produtos cruzados entre o Azure Data Explorer, [Application Insights (IA)](/azure/azure-monitor/app/app-insights-overview)e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) no serviço [Azure Monitor.](/azure/azure-monitor/) Pode mapear espaços de trabalho do Azure Monitor Log Analytics ou aplicações de Apps Application Insights como clusters proxy. Em seguida, pode consultar o cluster proxy usando ferramentas do Azure Data Explorer e consulte-o numa consulta de cluster transversal. O artigo mostra como se conectar a um cluster proxy, adicionar um cluster proxy ao Azure Data Explorer Web UI, e executar consultas contra as suas aplicações de IA ou espaços de trabalho de LA do Azure Data Explorer.

O fluxo de procuração do Explorador de Dados Azure: 

![Fluxo de procuração ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O ADX Proxy encontra-se em modo de pré-visualização. [Ligue-se ao proxy](#connect-to-the-proxy) para ativar a função proxy ADX para os seus clusters. Contacte a equipa [DaDXProxy](mailto:adxproxy@microsoft.com) com quaisquer questões.

## <a name="connect-to-the-proxy"></a>Ligue-se ao representante

1. Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster de *ajuda)* aparece no menu esquerdo antes de se ligar ao seu cluster Log Analytics ou Application Insights.

    ![Aglomerado nativo ADX](media/adx-proxy/web-ui-help-cluster.png)

1. No Azure Data Explorerhttps://dataexplorer.azure.com/clusters)UI (, selecione **Adicionar Cluster**.

1. Na janela **Adicionar Cluster,** adicione o URL ao cluster LA ou IA. 
    
    * Para LA:`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para a IA:`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se adicionar uma ligação a mais de um aglomerado de procuração, dê a cada um um um nome diferente. Caso contrário, todos terão o mesmo nome no painel esquerdo.

1. Após a ligação ser estabelecida, o seu cluster de LA ou IA aparecerá no painel esquerdo com o seu cluster ADX nativo. 

    ![Log Analytics e clusters do Explorador de Dados Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Você pode executar as consultas usando ferramentas de cliente que suportam consultas Kusto, tais como: Kusto Explorer, ADX Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens, REST API.

> [!TIP]
> * O nome da base de dados deve ter o mesmo nome que o recurso especificado no cluster proxy. Os nomes são sensíveis ao caso.
> * Em consultas de cluster seletiva, certifique-se de que o nome de aplicações De Insights de Aplicação e espaços de trabalho de Log Analytics está correto.
>     * Se os nomes contiverem caracteres especiais, são substituídos por codificação de URL no nome do cluster proxy. 
>     * Se os nomes incluirem caracteres que não cumprem as regras **-** de [identificação kQL,](/azure/kusto/query/schema-entities/entity-names)são substituídos pelo carácter do traço.

### <a name="direct-query-from-your-la-or-ai-adx-proxy-cluster"></a>Consulta direta do seu cluster de Procuração ADX DE LA ou AI

Execute consultas no seu aglomerado de IA ou IA. Verifique se o seu cluster está selecionado no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Espaço de trabalho de La Consulta](media/adx-proxy/query-la.png)

### <a name="cross-query-of-your-la-or-ai-adx-proxy-cluster-and-the-adx-native-cluster"></a>Consulta cruzada do seu cluster de Procuração AI ou AI ADX e do cluster nativo ADX 

Quando executar consultas de cluster cruzada sacar em conjunto a partir do proxy, verifique se o seu cluster nativo ADX é selecionado no painel esquerdo. Os exemplos seguintes demonstram combinar tabelas `union`de cluster ADX (utilizando) com espaço de trabalho de LA.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

   [![Consulta cruzada do proxy do Explorador de Dados do Azure](media/adx-proxy/cross-query-adx-proxy.png)](media/adx-proxy/cross-query-adx-proxy.png#lightbox)

A [ `join` ](/azure/kusto/query/joinoperator)utilização do operador , [`hint`](/azure/kusto/query/joinoperator#join-hints) em vez de união, pode exigir um para executá-lo num cluster nativo do Azure Data Explorer (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos adicionais de sintaxe

As seguintes opções de sintaxe estão disponíveis quando se ligam para os clusters Application Insights (IA) ou Log Analytics (LA):

|Descrição da Sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de dados dentro de um cluster que contém apenas o recurso definido nesta subscrição **(recomendado para consultas de cluster seletivas)** |   cluster.`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` | cluster.`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`     |
| Cluster que contém todas as aplicações/espaços de trabalho nesta subscrição    |     cluster.`https://ade.applicationinsights.io/subscriptions/<subscription-id>`    |    cluster.`https://ade.loganalytics.io/subscriptions/<subscription-id>`     |
|Cluster que contém todas as aplicações/espaços de trabalho na subscrição e são membros deste grupo de recursos    |   cluster.`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`      |    cluster.`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`      |
|Cluster que contém apenas o recurso definido nesta subscrição      |    cluster.`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`    |  cluster.`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`     |

## <a name="next-steps"></a>Passos seguintes

[Escrever consultas](write-queries.md)
