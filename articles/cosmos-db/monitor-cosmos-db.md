---
title: Monitorização Azure Cosmos DB [ Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 5056762dab18ae23980c7d3b3ebfbb3c3014fa56
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798698"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitorização Azure Cosmos DB

Quando se tem aplicações críticas e processos de negócio baseados nos recursos do Azure, pretende monitorizar esses recursos para a sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitorização gerados pelas bases de dados da Azure Cosmos e como pode utilizar as funcionalidades do Azure Monitor para analisar e alertar sobre estes dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
A Azure Cosmos DB cria dados de monitorização utilizando o [Azure Monitor,](../azure-monitor/overview.md) que é um serviço completo de monitorização de pilhas em Azure que fornece um conjunto completo de funcionalidades para monitorizar os seus recursos Azure, além de recursos em outras nuvens e no local.

Se ainda não está familiarizado com a monitorização dos serviços azure, comece com o artigo [Monitorde recursos Azure com o Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Configurar a recolha de dados
- Ferramentas padrão em Azure para analisar e alertar sobre dados de monitorização

As seguintes secções baseiam-se neste artigo descrevendo os dados específicos recolhidos do Azure Cosmos DB e fornecendo exemplos para configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Monitor Azure para Azure Cosmos DB

O Azure Monitor para o Azure Cosmos DB baseia-se na [funcionalidade de livros de livros do Azure Monitor](../azure-monitor/platform/workbooks-overview.md) e utiliza os mesmos dados de monitorização recolhidos para o Cosmos DB descritos nas secções abaixo. Utilize o Monitor Azure para uma visão do desempenho global, falhas, capacidade e saúde operacional de todos os seus recursos Azure Cosmos DB numa experiência interativa unificada, e aproveite as outras funcionalidades do Azure Monitor para análise detalhada e alerta. Para saber mais, consulte o explore Azure Monitor para o artigo [da Azure Cosmos DB.](../azure-monitor/insights/cosmosdb-insights-overview.md)

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucrodiferente. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Ver métricas de nível de operação para Azure Cosmos DB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Monitor** a partir da barra de navegação à esquerda e selecione **Métricas**.

   ![Painel de métricas no Monitor Azure](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Do painel de **métricas** > **Selecione um recurso** > escolha a **subscrição**necessária e **o grupo de recursos**. Para o **tipo de Recurso**, selecione contas **Azure Cosmos DB,** escolha uma das suas contas Azure Cosmos existentes e selecione **Apply**.

   ![Escolha uma conta Cosmos DB para ver métricas](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Em seguida, pode selecionar uma métrica da lista de métricas disponíveis. Pode selecionar métricas específicas para solicitar unidades, armazenamento, latência, disponibilidade, Cassandra, entre outros. Para conhecer em detalhe todas as métricas disponíveis nesta lista, consulte as [Métricas por artigo](monitor-cosmos-db-reference.md) de categoria. Neste exemplo, vamos selecionar **as unidades de Pedido** e **avg** como o valor de agregação.

   Além destes detalhes, também pode selecionar a gama de **tempo** e a **granularidade** do tempo das métricas. No máximo, pode ver métricas nos últimos 30 dias.  Depois de aplicar o filtro, é apresentado um gráfico com base no filtro. Pode ver o número médio de unidades de pedido consumidas por minuto durante o período selecionado.  

   ![Escolha uma métrica do portal Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Adicione filtros às métricas

Também pode filtrar métricas e o gráfico apresentado por um **nome**de recolha específico, Nome de **base de dados,** **OperaçãoTipo,** **Região**e **StatusCode**. Para filtrar as métricas, selecione **Adicionar filtro** e escolha a propriedade necessária, como **o OperationType** e selecione um valor como **O Query**. Em seguida, o gráfico exibe as unidades de pedido consumidas para a operação de consulta durante o período selecionado. As operações executadas através do procedimento Armazenado não estão registadas, pelo que não estão disponíveis na métrica OperationType.

![Adicione um filtro para selecionar a granularidade métrica](./media/monitor-cosmos-db/add-metrics-filter.png)

Pode agrupar métricas utilizando a opção **de divisão Apply.** Por exemplo, pode agrupar as unidades de pedido por tipo de operação e visualizar o gráfico para todas as operações de uma só vez, como mostra a seguinte imagem:

![Adicionar aplicar filtro de divisão](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Dados de monitorização recolhidos da Azure Cosmos DB

A Azure Cosmos DB recolhe os mesmos tipos de dados de monitorização que outros recursos Azure que são descritos na [Monitorização de dados a partir de recursos Azure.](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) Consulte a referência de dados de monitorização do [Azure Cosmos DB](monitor-cosmos-db-reference.md) para uma referência detalhada dos registos e métricas criados pela Azure Cosmos DB.

A página **de visão geral** no portal Azure para cada base de dados Azure Cosmos inclui uma breve visão do uso da base de dados, incluindo o seu pedido e o uso de faturação horária. Esta é uma informação útil, mas apenas uma pequena quantidade dos dados de monitorização disponíveis. Alguns destes dados são recolhidos automaticamente e disponíveis para análise assim que cria a base de dados, enquanto pode permitir a recolha adicional de dados com alguma configuração.

![Página de visão geral](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analisar dados métricos

A Azure Cosmos DB proporciona uma experiência personalizada para trabalhar com métricas. Consulte [as métricas DB monitor e depuração Azure Cosmos do Azure Monitor](cosmos-db-azure-monitor-metrics.md) para obter detalhes sobre a utilização desta experiência e para analisar diferentes cenários de Azure Cosmos DB.

Pode analisar métricas para O Azure Cosmos DB com métricas de outros serviços Azure usando o explorador de **Métricas,** abrindo métricas do menu **Azure Monitor.** Consulte [Começar com o Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre a utilização desta ferramenta. Todas as métricas para O Azure Cosmos DB estão no nome space **Cosmos DB métricas padrão**. Pode utilizar as seguintes dimensões com estas métricas ao adicionar um filtro a um gráfico:

- CollectionName
- DatabaseName
- OperaçãoTipo
- Região
- Código de Estado

## <a name="analyzing-log-data"></a>Analisar dados de registo
Os dados em Registos do Monitor Azure são armazenados em tabelas que cada tabela tem o seu próprio conjunto de propriedades únicas. A Azure Cosmos DB armazena dados nas seguintes tabelas.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar registos de recursos. Os registos de recursos da Azure Cosmos DB podem ser identificados com `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Tabela comum que armazena todos os registos do registo de atividades. 


> [!IMPORTANT]
> Ao selecionar **Registos** do menu Azure Cosmos DB, o Log Analytics é aberto com o âmbito de consulta definido para a base de dados atual do Azure Cosmos. Isto significa que as consultas de registo incluirão apenas dados desse recurso. Se pretender executar uma consulta que inclua dados de outras bases de dados ou dados de outros serviços do Azure, selecione **Registos** do menu **Do Monitor Do Azure.** Consulte o âmbito de consulta de registo e o intervalo de [tempo no Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) para obter mais detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Consultas de log da Azure Cosmos DB no Monitor Azure

Aqui estão algumas consultas que você pode entrar na barra de **pesquisa de Log** para ajudá-lo a monitorizar seus recipientes Azure Cosmos. Estas consultas funcionam com a [nova linguagem.](../log-analytics/log-analytics-log-search-upgrade.md)

Seguem-se as consultas que pode utilizar para o ajudar a monitorizar as suas bases de dados Azure Cosmos.

* Para consultar todos os registos de diagnóstico da Azure Cosmos DB por um período de tempo especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Para consultar todas as operações, agrupara-se por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar toda a atividade do utilizador, agrupado por recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitor Azure Cosmos DB programática

As métricas de nível de conta disponíveis no portal, tais como o uso do armazenamento de conta e o total de pedidos, não estão disponíveis através das APIs SQL. No entanto, pode obter dados de utilização ao nível da recolha utilizando as APIs SQL. Para recuperar os dados do nível de recolha, faça o seguinte:

* Para utilizar a API REST, [execute um GET na coleção](https://msdn.microsoft.com/library/mt489073.aspx). A informação de quota e utilização da recolha é devolvida nos cabeçalhos x-ms-resource-quota e x-ms-resource-use na resposta.

* Para utilizar o .NET SDK, utilize o método [DocumentClient.ReadDocumentCollectionAsync,](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) que devolve uma Resposta de [Recursos](https://msdn.microsoft.com/library/dn799209.aspx) que contém uma série de propriedades de utilização como **CollectionSizeUsage,** **DatabaseUsage,** **DocumentUsage,** e muito mais.

Para aceder a métricas adicionais, utilize o [SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)do Monitor Azure . As definições métricas disponíveis podem ser recuperadas através da chamada:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

As consultas para recuperar métricas individuais utilizam o seguinte formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Passos seguintes

- Consulte a referência de dados de monitorização do [Azure Cosmos DB](monitor-cosmos-db-reference.md) para uma referência dos registos e métricas criados pela Azure Cosmos DB.
- Consulte [a Monitorização dos recursos do Azure com o Monitor Azure](../azure-monitor/insights/monitor-azure-resource.md) para obter mais informações sobre a monitorização dos recursos do Azure.
