---
title: Monitorização Azure Cosmos DB / Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: c04d61c6381304382261219f23e99401465590ca
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099390"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitorização Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Quando você tem aplicações críticas e processos de negócio contando com recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelas bases de dados do Azure Cosmos e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

Pode monitorizar os seus dados com métricas do lado do cliente e do servidor. Ao utilizar métricas do lado do servidor, pode monitorizar os dados armazenados no Azure Cosmos DB com as seguintes opções:

* **Monitor do portal DB Azure Cosmos:** Pode monitorizar com as métricas disponíveis no separador **Métricas** da conta Azure Cosmos. As métricas deste separador incluem níveis de produção, armazenamento, disponibilidade, latência, consistência e métricas de nível do sistema. Por padrão, estas métricas têm um período de retenção de 7 dias. Para saber mais, consulte os [dados de monitorização recolhidos na secção DB do Azure Cosmos](#monitoring-from-azure-cosmos-db) deste artigo.

* **Monitorize com métricas no monitor Azure:** Pode monitorizar as métricas da sua conta Azure Cosmos e criar dashboards a partir do Monitor Azure. O Azure Monitor recolhe as métricas DB do Azure Cosmos por padrão, não tem configurar nada explicitamente. Estas métricas são recolhidas com granularidade de um minuto, a granularidade pode variar em função da métrica que escolher. Por padrão, estas métricas têm um período de retenção de 30 dias. A maioria das métricas que estão disponíveis a partir das opções anteriores também estão disponíveis nestas métricas. Os valores de dimensão para as métricas como o nome do recipiente são insensíveis ao caso. Por isso, tens de usar a comparação caso-insensível ao fazer comparações de cordas sobre estes valores de dimensão. Para saber mais, consulte a secção de [dados métricos](#analyze-metric-data) analisar este artigo.

* **Monitor com registos de diagnóstico no Monitor Azure:** Pode monitorizar os registos da sua conta Azure Cosmos e criar dashboards a partir do Monitor Azure. A telemetria, como eventos e vestígios que ocorrem numa segunda granularidade, são armazenados como troncos. Por exemplo, se a produção de um recipiente for alterada, as propriedades de uma conta Cosmos são alteradas estes eventos são capturas dentro dos registos. Pode analisar estes registos executando consultas nos dados recolhidos. Para saber mais, consulte a secção de [dados](#analyze-log-data) de registo de comentários deste artigo.

* **Monitorize programáticamente com SDKs:** Pode monitorizar a sua conta Azure Cosmos programáticamente utilizando os .NET, Java, Python, Node.js SDKs e os cabeçalhos em REST API. Para saber mais, consulte a secção programática do [Monitor Azure Cosmos deste](#monitor-cosmosdb-programmatically) artigo.

A imagem a seguir mostra diferentes opções disponíveis para monitorizar a conta DB do Azure Cosmos através do portal Azure:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opções de monitorização disponíveis no portal Azure" border="false":::

Ao utilizar o Azure Cosmos DB, do lado do cliente pode recolher os dados para a cobrança de pedidos, ID de atividade, informações de exceção/stack de vestígios, código de estado/sub-estado HTTP, cadeia de diagnóstico para depurar qualquer problema que possa ocorrer. Esta informação também é necessária se precisar de contactar a equipa de apoio da Azure Cosmos DB.  

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

A Azure Cosmos DB cria dados de monitorização utilizando [o Azure Monitor,](../azure-monitor/overview.md) que é um serviço completo de monitorização de pilhas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local.

Se ainda não está familiarizado com a monitorização dos serviços Azure, comece com o artigo [Monitorizar os recursos do Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

* O que é o Azure Monitor?
* Custos associados à monitorização
* Dados de monitorização recolhidos em Azure
* Recolha de dados configurado
* Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo, descrevendo os dados específicos recolhidos a partir da Azure Cosmos DB e fornecendo exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Monitor Azure para Azure Cosmos DB

O Azure Monitor for Azure Cosmos DB baseia-se na [funcionalidade de livros de Azure Monitor](../azure-monitor/platform/workbooks-overview.md) e utiliza os mesmos dados de monitorização recolhidos para o Cosmos DB descritos nas secções abaixo. Utilize o Azure Monitor para uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos DB Azure Cosmos numa experiência interativa unificada, e aproveite as outras características do Azure Monitor para análise e alerta detalhada. Para saber mais, consulte o Explore Azure Monitor para o artigo [DB da Azure Cosmos.](../azure-monitor/insights/cosmosdb-insights-overview.md)

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Monitor de dados recolhidos do portal DB Azure Cosmos

A Azure Cosmos DB recolhe os mesmos tipos de dados de monitorização que outros recursos Azure que são descritos na [monitorização de dados a partir de recursos Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Consulte [a referência de dados de monitorização do Azure Cosmos DB](monitor-cosmos-db-reference.md) para uma referência detalhada dos registos e métricas criados pela Azure Cosmos DB.

A página **geral** no portal Azure para cada base de dados Azure Cosmos inclui uma breve visão da utilização da base de dados, incluindo o seu pedido e o uso da faturação horária. Esta é uma informação útil, mas apenas uma pequena quantidade dos dados de monitorização disponíveis. Alguns destes dados são recolhidos automaticamente e disponíveis para análise assim que criar a base de dados, enquanto pode ativar a recolha adicional de dados com alguma configuração.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Análise de dados métricos

A Azure Cosmos DB proporciona uma experiência personalizada para trabalhar com métricas.

Pode analisar métricas para Azure Cosmos DB com métricas de outros serviços Azure usando o explorador de Métricas abrindo **métricas** a partir do menu **Azure Monitor.** Consulte [o Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta. Todas as métricas para Azure Cosmos DB estão no nome **cosmos DB métricas padrão** . Pode utilizar as seguintes dimensões com estas métricas ao adicionar um filtro a um gráfico:

* CollectionName
* DatabaseName
* Tipo de Operação
* Região
* Código de Estado

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Ver métricas de nível de operação para Azure Cosmos DB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** da barra de navegação à esquerda e selecione **Métricas** .

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

1. A partir do painel **métrica >** **Selecione um recurso** > escolha a **subscrição** necessária e o **grupo de recursos** . Para o **tipo de recurso** , selecione contas **DB Azure Cosmos** , escolha uma das suas contas Azure Cosmos existentes e selecione **Apply** .

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

1. Em seguida, pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra, entre outras. Para saber em detalhe sobre todas as métricas disponíveis nesta lista, consulte as [Métricas por](monitor-cosmos-db-reference.md) artigo de categoria. Neste exemplo, vamos selecionar **unidades de Pedido** e **Avg** como o valor de agregação.

   Além destes detalhes, também pode selecionar a **gama de tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

### <a name="add-filters-to-metrics"></a>Adicione filtros às métricas

Também pode filtrar métricas e o gráfico apresentado por um **Nome** de Coleção específico, **DataName,** **OperationType,** **Região** e **StatusCode.** Para filtrar as métricas, **selecione Adicionar filtro** e escolha a propriedade necessária, como o **OperationType,** e selecione um valor como **Consulta.** Em seguida, o gráfico apresenta as unidades de pedido consumidas para a operação de consulta durante o período selecionado. As operações executadas através do procedimento armazenado não são registadas, pelo que não estão disponíveis ao abrigo da métrica OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

Pode agrupar métricas utilizando a opção **de divisão De aplicar.** Por exemplo, pode agrupar as unidades de pedido por tipo de operação e ver o gráfico para todas as operações de uma só vez, como mostrado na imagem seguinte:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Opções de monitorização disponíveis no portal Azure":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Análise de dados de registo

Os dados em Registos monitores Azure são armazenados em tabelas que cada mesa tem o seu próprio conjunto de propriedades únicas. A Azure Cosmos DB armazena dados nas seguintes tabelas.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar registos de recursos. Os registos de recursos da Azure Cosmos DB podem ser identificados com `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Tabela comum que armazena todos os registos do registo de atividades.

> [!IMPORTANT]
> Quando seleciona **Logs** do menu DB Azure Cosmos, o Log Analytics é aberto com o âmbito de consulta definido para a base de dados Azure Cosmos atual. Isto significa que as consultas de registo só incluirão dados desse recurso. Se pretender executar uma consulta que inclua dados de outras bases de dados ou dados de outros serviços Azure, selecione **Registos** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) para obter mais detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Consultas Azure Cosmos DB Log Analytics em Azure Monitor

Aqui ficam algumas consultas que pode introduzir na barra **de pesquisa de Registo** para ajudá-lo a monitorizar os seus contentores Azure Cosmos. Estas consultas funcionam com a [nova linguagem.](../azure-monitor/log-query/log-query-overview.md)

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar as suas bases de dados Azure Cosmos.

* Para consultar todos os registos de diagnóstico da Azure Cosmos DB por um período de tempo especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Para consulta de todas as operações, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consulta de toda a atividade do utilizador, agrupado por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitor Azure Cosmos DB programático

As métricas de nível de conta disponíveis no portal, como o uso de armazenamento de conta e o total de pedidos, não estão disponíveis através das APIs SQL. No entanto, pode obter dados de utilização ao nível da recolha utilizando as APIs SQL. Para obter dados de nível de recolha, faça o seguinte:

* Para utilizar a API REST, [execute um GET na coleção.](/rest/api/cosmos-db/get-a-collection) A informação de quota e utilização da coleção é devolvida nos cabeçalhos x-ms-resource-quota e x-ms-resource-use-use na resposta.

* Para utilizar o método .NET SDK, utilize o método [DocumentClient.ReadDocumentCollectionAsync,](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) que devolve uma Resposta de [Recursos](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) que contém uma série de propriedades de utilização como **CollectionSizeUsage,** **DatabaseUsage,** **DocumentUsage** , e muito mais.

Para aceder a métricas adicionais, utilize o [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). As definições métricas disponíveis podem ser recuperadas chamando:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Para recuperar métricas individuais, utilize o seguinte formato:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Para saber mais, consulte o artigo [da Azure monitoring REST API.](../azure-monitor/platform/rest-api-walkthrough.md)

## <a name="next-steps"></a>Passos seguintes

* Consulte [a referência de dados de monitorização do Azure Cosmos DB](monitor-cosmos-db-reference.md) para uma referência dos registos e métricas criados pela Azure Cosmos DB.
* Consulte [os recursos de Monitor Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter informações sobre a monitorização dos recursos do Azure.