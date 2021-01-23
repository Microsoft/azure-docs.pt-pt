---
title: Dados de consulta no Azure Monitor utilizando o Azure Data Explorer (pré-visualização)
description: Utilize o Azure Data Explorer para realizar consultas de produtos cruzados entre o Azure Data Explorer, os espaços de trabalho do Log Analytics e as aplicações clássicas de Insights de Aplicações no Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: a31ef69d84f64e4bcaa46adac26a29d2cc367351
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731705"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dados de consulta no Azure Monitor utilizando o Azure Data Explorer (Preview)

O Azure Data Explorer suporta consultas de serviços cruzados entre Azure Data Explorer, [Application Insights (IA)](../app/app-insights-overview.md)e [Log Analytics (LA)](./data-platform-logs.md). Em seguida, pode consultar o seu espaço de trabalho Log Analytics/Application Insights utilizando ferramentas Azure Data Explorer e encaminhá-lo numa consulta de serviço cruzado. O artigo mostra como fazer uma consulta de serviço cruzado e como adicionar o espaço de trabalho Log Analytics/Application Insights à UI web do Azure Data Explorer Web UI.

As consultas de serviço cross-explorer Azure Data Explorer fluem: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::

> [!NOTE]
> * A capacidade de consultar os dados do Azure Monitor do Azure Data Explorer, quer diretamente a partir das ferramentas do cliente do Azure Data Explorer, quer indiretamente através de uma consulta num cluster Azure Data Explorer, encontra-se em modo de pré-visualização.
>* Contacte a equipa [de consulta de serviço cross](mailto:adxproxy@microsoft.com) com quaisquer perguntas.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Adicione um espaço de trabalho Log Analytics/Application Insights às ferramentas de cliente do Azure Data Explorer

1. Verifique se o cluster nativo do Azure Data Explorer (tal como o cluster *de ajuda)* aparece no menu esquerdo antes de ligar ao seu cluster Desalparado ou Insights de Aplicação.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Aglomerado nativo Azure Data Explorer.":::

 No Azure Data Explorer UI https://dataexplorer.azure.com/clusters) (, **selecione Add Cluster**.

2. Na janela **Add Cluster,** adicione o URL do cluster la ou IA.

    * Para LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Para a IA: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Selecione **Adicionar**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Adicione o cluster.":::
 
>[!NOTE]
>Se adicionar uma ligação a mais de um espaço de trabalho log analytics/application insights, dê a cada um um nome diferente. Caso contrário, todos terão o mesmo nome no painel esquerdo.

 Após a ligação ser estabelecida, o seu espaço de trabalho Log Analytics ou Application Insights aparecerá no painel esquerdo com o seu cluster Azure Data Explorer nativo.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics e Azure Data Explorer clusters.":::
 
> [!NOTE]
> O número de espaços de trabalho do Azure Monitor que podem ser mapeados está limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Criar consultas utilizando dados do Azure Monitor

Pode executar as consultas utilizando ferramentas de cliente que suportam consultas kusto, tais como: Kusto Explorer, Azure Data Explorer Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens, REST API.

> [!NOTE]
> A capacidade de consulta de serviço cruzado é utilizada apenas para a recuperação de dados. Para obter mais informações, consulte [a capacidade de suporte da função.](#function-supportability)

> [!TIP]
> * O nome da base de dados deve ter o mesmo nome que o recurso especificado na consulta de serviço cruzado. Os nomes são sensíveis às maiúsculas e minúsculas.
> * Nas consultas de cluster cross, certifique-se de que o nome de aplicações De Insights de Aplicação e espaços de trabalho log Analytics está correto.
> * Se os nomes contiverem caracteres especiais, são substituídos por codificação de URL na consulta de serviço cruzado.
> * Se os nomes incluem caracteres que não cumprem [as regras do nome do identificador KQL,](/azure/data-explorer/kusto/query/schema-entities/entity-names)são substituídos pelo personagem do **-** traço.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Consulta direta no seu Log Analytics ou Application Insights espaços de trabalho a partir de ferramentas de clienteS Azure Data Explorer

Executar consultas nos seus espaços de trabalho Log Analytics ou Application Insights. Verifique se o seu espaço de trabalho está selecionado no painel esquerdo.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Consulta Log Analytics espaço de trabalho.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada do seu Log Analytics ou Application Insights e do cluster nativo Azure Data Explorer

Quando executar consultas de serviço de cluster cruzada, verifique se o seu cluster nativo Azure Data Explorer está selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de clusterS Azure Data Explorer [utilizando a união](/azure/data-explorer/kusto/query/unionoperator) com o espaço de trabalho Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Consulta de serviço cruzada do Azure Data Explorer.":::

A utilização do [ `join` operador](/azure/data-explorer/kusto/query/joinoperator), em vez de união, pode exigir que [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) a utilize num cluster nativo do Azure Data Explorer.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Junte os dados de um cluster Azure Data Explorer em um inquilino com um recurso Azure Monitor em outro

As consultas entre os inquilinos entre os serviços não são apoiadas. Você está inscrito em um único inquilino para executar a consulta abrangendo ambos os recursos.

Se o recurso Azure Data Explorer estiver no espaço de trabalho do Inquilino 'A' e do Log Analytics está no Arrendatário 'B' utilize um dos dois métodos seguintes:

1. O Azure Data Explorer permite-lhe adicionar funções para os principais em diferentes inquilinos. Adicione o seu ID de utilizador no Inquilino 'B' como um utilizador autorizado no cluster Azure Data Explorer. Validar a propriedade *['TrustedExternalTenant'](/powershell/module/az.kusto/update-azkustocluster)* no cluster Azure Data Explorer contém O Inquilino 'B'. Executar a consulta cruzada completamente no Inquilino 'B'.

2. Utilize o [Farol](../../lighthouse/index.yml) para projetar o recurso Azure Monitor no Inquilino 'A'.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ligue-se aos clusters Azure Data Explorer de diferentes inquilinos

O Kusto Explorer inscreve-o automaticamente no inquilino a que a conta de utilizador pertence originalmente. Para aceder a recursos em outros inquilinos com a mesma conta de utilizador, o `tenantId` tem de ser explicitamente especificado na cadeia de ligação: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Suporte de função

As consultas de serviço cross-service Azure Data Explorer suportam funções tanto para Insights de Aplicação como para Analítica de Registo.
Esta capacidade permite que as consultas de cluster transversal referenciam diretamente uma função tabular do Monitor Azure.
Os seguintes comandos são suportados com a consulta de serviço cruzado:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

A imagem que se segue representa um exemplo de consulta de uma função tabular a partir da UI web Azure Data Explorer.
Para utilizar a função, execute o nome na janela 'Consulta'.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Consultar uma função tabular a partir da Azure Data Explorer Web UI.":::

## <a name="additional-syntax-examples"></a>Exemplos adicionais de sintaxe

As seguintes opções de sintaxe estão disponíveis quando se chama os clusters De Análise de Registo ou Insights de Aplicação:

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de dados dentro de um cluster que contém apenas o recurso definido nesta subscrição **(recomendado para consultas de cluster transversal)** |   cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ( ) | cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` ( )     |
| Cluster que contém todas as aplicações/espaços de trabalho nesta subscrição    |     cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>` ( )    |    cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>` ( )     |
|Cluster que contém todas as aplicações/espaços de trabalho na subscrição e são membros deste grupo de recursos    |   cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` ( )      |    cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` ( )      |
|Cluster que contém apenas o recurso definido nesta subscrição      |    cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` ( )    |  cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` ( )     |

## <a name="next-steps"></a>Próximos passos

- Leia mais sobre a [estrutura de dados dos espaços de trabalho do Log Analytics e do Application Insights](data-platform-logs.md).
- Aprenda a [escrever consultas no Azure Data Explorer](/azure/data-explorer/write-queries).