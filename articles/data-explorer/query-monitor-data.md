---
title: Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)
description: Neste tópico, consulte dados em Azure Monitor criando um proxy de Data Explorer do Azure para consultas entre produtos com Application Insights e Log Analytics
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e61f52282bcbc62a3eb069272cd7c1f3e329d3b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172694"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consultar dados em Azure Monitor usando o Data Explorer do Azure (versão prévia)

O cluster de proxy de Data Explorer do Azure (proxy ADX) é uma entidade que permite executar consultas entre produtos entre o Azure Data Explorer, [Application insights (ia)](/azure/azure-monitor/app/app-insights-overview)e [log Analytics (la)](/azure/azure-monitor/platform/data-platform-logs) no serviço [Azure monitor](/azure/azure-monitor/) . Você pode mapear Azure Monitor Log Analytics espaços de trabalho ou Application Insights aplicativos como um cluster de proxy. Você pode consultar o cluster de proxy usando as ferramentas de Data Explorer do Azure e consultá-lo em uma consulta entre clusters. O artigo mostra como se conectar a um cluster de proxy, adicionar um cluster de proxy ao Azure Data Explorer interface do usuário da Web e executar consultas em seus aplicativos de AI ou espaços de trabalho da LA do Azure Data Explorer.

O fluxo do proxy de Data Explorer do Azure: 

![Fluxo de proxy ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

> [!NOTE]
> O proxy ADX está no modo de visualização. Para habilitar esse recurso, entre em contato com a equipe do [ADXProxy](mailto:adxproxy@microsoft.com) .

## <a name="connect-to-the-proxy"></a>Conectar-se ao proxy

1. Verifique se o seu cluster nativo do Azure Data Explorer (como o cluster de *ajuda* ) aparece no menu à esquerda antes de se conectar ao seu Log Analytics ou Application insights cluster.

    ![Cluster nativo do ADX](media/adx-proxy/web-ui-help-cluster.png)

1. Na interface do usuário do data Explorer https://dataexplorer.azure.com/clusters) do Azure (, selecione **Adicionar cluster**.

1. Na janela **Adicionar cluster** :

    * Adicione a URL ao cluster LA ou ia. Por exemplo: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Selecione **Adicionar**.

    ![Adicionar cluster](media/adx-proxy/add-cluster.png)

    Se você adicionar uma conexão a mais de um cluster de proxy, dê a cada um deles um nome diferente. Caso contrário, eles terão o mesmo nome no painel esquerdo.

1. Depois que a conexão for estabelecida, seu cluster LA ou ia será exibido no painel esquerdo com seu cluster ADX nativo. 

    ![Log Analytics e clusters de Data Explorer do Azure](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Executar consultas

Você pode usar o Kusto Explorer, o ADX Web Explorer, o Jupyter Kqlmagic ou a API REST para consultar os clusters de proxy. 

> [!TIP]
> * O nome do banco de dados deve ter o mesmo nome que o recurso especificado no cluster de proxy. Os nomes diferenciam maiúsculas de minúsculas.
> * Em consultas entre clusters, certifique-se de que a nomenclatura de Application Insights aplicativos e espaços de trabalho do Log Analytics esteja correta.
>     * Se os nomes contiverem caracteres especiais, eles serão substituídos pela codificação de URL no nome do cluster de proxy. 
>     * Se os nomes incluírem caracteres que não atendem às [regras de nome de identificador KQL](/azure/kusto/query/schema-entities/entity-names), **-** eles serão substituídos pelo caractere de traço.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Consultar no cluster nativo do Data Explorer do Azure 

Execute consultas no cluster de Data Explorer do Azure (como a tabela *StormEvents* no cluster de *ajuda* ). Ao executar a consulta, verifique se o cluster nativo do Data Explorer do Azure está selecionado no painel esquerdo.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Consultar tabela StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Consultar em seu cluster LA ou ia

Ao executar consultas em seu cluster LA ou AL, verifique se seu cluster LA ou ia está selecionado no painel esquerdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Consultar espaço de trabalho](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Consultar o cluster LA ou ia do proxy ADX  

Ao executar consultas em seu cluster LA ou ia do proxy, verifique se o cluster nativo do ADX está selecionado no painel esquerdo. O exemplo a seguir demonstra uma consulta do espaço de trabalho LA usando o cluster ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Consulta do proxy de Data Explorer do Azure](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Consulta cruzada de cluster LA ou ia e o cluster ADX do proxy ADX 

Ao executar consultas entre clusters do proxy, verifique se o cluster nativo do ADX está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas `union`de cluster ADX (usando) com a la espaço de trabalho.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Consulta cruzada do proxy de Data Explorer do Azure](media/adx-proxy/cross-query-adx-proxy.png)

Usar o [ `join` operador](/azure/kusto/query/joinoperator), em vez de Union, pode exigir uma dica para executá-lo em um cluster nativo do Azure data Explorer (e não no proxy). 

## <a name="additional-syntax-examples"></a>Exemplos de sintaxe adicionais

As seguintes opções de sintaxe estão disponíveis ao chamar os clusters de Application Insights (ia) ou Log Analytics (LA):

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Banco de dados dentro de um cluster que contém apenas o recurso definido nesta assinatura (**recomendado para consultas entre clusters**) |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Cluster que contém todos os aplicativos/espaços de trabalho nesta assinatura    |     cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Cluster que contém todos os aplicativos/espaços de trabalho na assinatura e que são membros deste grupo de recursos    |   cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Cluster que contém apenas o recurso definido nesta assinatura      |    cluster(`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  cluster(`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

## <a name="next-steps"></a>Passos Seguintes

[Escrever consultas](write-queries.md)