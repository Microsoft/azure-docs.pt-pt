---
title: Dados de consulta no Azure Monitor utilizando o Azure Data Explorer (pré-visualização)
description: Utilize o Azure Data Explorer para realizar consultas de produtos cruzados entre o Azure Data Explorer, os espaços de trabalho do Log Analytics e as aplicações clássicas de Insights de Aplicações no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 8a503a5456fc28bd1b3ebb69c784fc59b3c6e7df
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92050077"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Dados de consulta no Azure Monitor utilizando o Azure Data Explorer (pré-visualização)
O cluster de procuração Azure Data Explorer permite-lhe realizar consultas de produtos cruzadas entre o Azure Data Explorer, os espaços de trabalho do Log Analytics e as aplicações clássicas de Insights de Aplicação no Azure Monitor. Pode mapear espaços de trabalho do Log Analytics no Azure Monitor ou aplicações clássicas de App Insights como clusters de procuração. Em seguida, pode consultar o cluster proxy usando ferramentas Azure Data Explorer e encaminhá-lo numa consulta de cluster transversal. O artigo mostra como ligar a um cluster proxy, adicionar um cluster proxy ao Azure Data Explorer Web UI, e executar consultas contra os seus espaços de trabalho Log Analytics ou aplicações clássicas de Insights de Aplicação do Azure Data Explorer.

O diagrama a seguir mostra o fluxo de procuração do Azure Data Explorer:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::


> [!NOTE]
> O proxy Azure Data Explorer está em pré-visualização pública. [Ligue-se ao representante](#connect-to-the-proxy) para ativar a função de procuração para os seus clusters. 

## <a name="connect-to-the-proxy"></a>Ligue-se ao representante
Para ligar o seu espaço de trabalho Log Analytics ou a aplicação Clássica Application Insights, abra o[Azure Data Explorer Web UI](https://dataexplorer.azure.com/clusters). Verifique se o cluster nativo do Azure Data Explorer (tal como o cluster *de ajuda)* aparece no menu esquerdo antes de ligar ao seu cluster Desalparado ou Insights de Aplicação.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::

Clique **em Adicionar Cluster** e, em seguida, adicione o URL do cluster 'Log Analytics' ou Application Insights num dos seguintes formatos. 
    
* Para a análise de registos: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Para insights de aplicação: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Clique **em Adicionar** para fazer a ligação.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::
 
> [!NOTE]
> Se adicionar uma ligação a mais de um aglomerado de procuração, dê a cada um um nome diferente. Caso contrário, todos terão o mesmo nome no painel esquerdo.

Após a ligação ser estabelecida, o seu cluster De Insights de Registo ou Desaparente aparecerá no painel esquerdo com o seu cluster Azure Data Explorer nativo. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::
 
> [!NOTE]
> O número de espaços de trabalho do Azure Monitor que podem ser mapeados está limitado a 100.

## <a name="create-queries-using-azure-monitor-data"></a>Criar consultas utilizando dados do Azure Monitor

Pode executar as consultas utilizando ferramentas de cliente que suportam consultas kusto, tais como: Kusto Explorer, Azure Data Explorer Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, Lens e REST API.

> [!NOTE]
> A funcionalidade de procuração Azure Data Explorer é utilizada apenas para a recuperação de dados. Para obter mais informações, consulte [a capacidade de suporte da função.](#function-supportability)

> [!TIP]
> * O nome da base de dados deve ter o mesmo nome que o recurso especificado no cluster proxy. Os nomes são sensíveis às maiúsculas e minúsculas.
> * Nas consultas de cluster cross, certifique-se de que o nome de aplicações De Insights de Aplicação e espaços de trabalho log Analytics está correto.
>     * Se os nomes contiverem caracteres especiais, são substituídos por codificação de URL no nome do cluster proxy. 
>     * Se os nomes incluem caracteres que não cumprem [as regras do nome do identificador KQL,](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)são substituídos pelo personagem do **-** traço.

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Consulta direta do seu cluster de procuração log Analytics ou Application Insights

Executar consultas no seu cluster Log Analytics ou Application Insights. Verifique se o seu cluster está selecionado no painel esquerdo. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Consulta cruzada do seu cluster de procuração log analytics ou application insights e o cluster nativo Azure Data Explorer

Quando executar consultas de cluster cruzada a partir do proxy, verifique se o seu cluster nativo Azure Data Explorer é selecionado no painel esquerdo. Os exemplos a seguir demonstram a combinação de tabelas de clusterS Azure Data Explorer utilizando o operador [da união](/azure/data-explorer/kusto/query/unionoperator) com um espaço de trabalho Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
A utilização do [ `join` operador](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer), em vez de união, pode exigir uma [dica](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer#join-hints) para executá-lo num cluster nativo do Azure Data Explorer (e não no proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Junte os dados de um cluster Azure Data Explorer em um inquilino com um recurso Azure Monitor em outro

Consultas de inquilinos cruzadas não são apoiadas por Azure Data Explorer proxy. Você está inscrito em um único inquilino para executar a consulta abrangendo ambos os recursos.

Se o recurso Azure Data Explorer estiver no espaço de trabalho do Inquilino 'A' e do Log Analytics está no Arrendatário 'B' utilize um dos dois métodos seguintes:

- O Azure Data Explorer permite-lhe adicionar funções para os principais em diferentes inquilinos. Adicione o seu ID de utilizador no Inquilino 'B' como um utilizador autorizado no cluster Azure Data Explorer. Validar a propriedade *['TrustedExternalTenant'](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* no cluster Azure Data Explorer contém O Inquilino 'B'. Executar a consulta cruzada completamente no Inquilino 'B'.

- Utilize o [Farol](/azure/lighthouse/) para projetar o recurso Azure Monitor no Inquilino 'A'.

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ligue-se aos clusters Azure Data Explorer de diferentes inquilinos

O Kusto Explorer inscreve-o automaticamente no inquilino a que a conta de utilizador pertence originalmente. Para aceder a recursos em outros inquilinos com a mesma conta de utilizador, o `tenantId` tem de ser explicitamente especificado na cadeia de ligação: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Suporte de função

O cluster de procuração Azure Data Explorer suporta funções tanto para Log Analytics como para Insights de Aplicação. Esta capacidade permite que as consultas de cluster transversal referenciam diretamente uma função tabular do Monitor Azure.

Os seguintes comandos são suportados pelo representante:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

A imagem que se segue representa um exemplo de consulta de uma função tabular a partir da UI web Azure Data Explorer. Para utilizar a função, execute o nome na janela 'Consulta'.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Fluxo de procuração de explorador de dados Azure.":::
 
> [!NOTE]
> O Azure Monitor suporta apenas funções tabulares, que não suportam parâmetros.

## <a name="additional-syntax-examples"></a>Exemplos adicionais de sintaxe

As seguintes opções de sintaxe estão disponíveis quando se chama os clusters De Análise de Registo ou Insights de Aplicação:

|Descrição da sintaxe  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de dados dentro de um cluster que contém apenas o recurso definido nesta subscrição **(recomendado para consultas de cluster transversal)** |   cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ( ) | cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` ( )     |
| Cluster que contém todas as aplicações/espaços de trabalho nesta subscrição    |     cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>` ( )    |    cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>` ( )     |
|Cluster que contém todas as aplicações/espaços de trabalho na subscrição e são membros deste grupo de recursos    |   cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` ( )      |    cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` ( )      |
|Cluster que contém apenas o recurso definido nesta subscrição      |    cluster `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` ( )    |  cluster `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` ( )     |

## <a name="next-steps"></a>Passos seguintes

- Leia mais sobre a [estrutura de dados dos espaços de trabalho do Log Analytics e do Application Insights](data-platform-logs.md).
- Aprenda a [escrever consultas no Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/write-queries).