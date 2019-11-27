---
title: Azure Cosmos DB de monitoramento | Microsoft Docs
description: Saiba como monitorar o desempenho e a disponibilidade do Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 9a36b46d11657ef52051f8bf8df1e4944051da23
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454270"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB de monitoramento
Quando você tem aplicativos críticos e processos de negócios que dependem de recursos do Azure, você deseja monitorar esses recursos para sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitoramento gerados pelos bancos de dado do Azure Cosmos e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Azure Cosmos DB cria dados de monitoramento usando [Azure monitor](../azure-monitor/overview.md) que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar os recursos do Azure, além de recursos em outras nuvens e locais. 

Se você ainda não estiver familiarizado com o monitoramento dos serviços do Azure, comece com o artigo [monitorando os recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitorando dados coletados no Azure
- Configurando a coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

As seções a seguir se baseiam neste artigo descrevendo os dados específicos coletados de Azure Cosmos DB e fornecendo exemplos para configurar a coleta de dados e analisar esses dados com as ferramentas do Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor para Cosmos DB (versão prévia)
[Azure monitor para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) baseia-se no [recurso de pastas de trabalho do Azure monitor](../azure-monitor/app/usage-workbooks.md) e usa os mesmos dados de monitoramento coletados para Cosmos DB descritos nas seções abaixo. Use essa ferramenta para ver um modo de exibição do desempenho geral, falhas, capacidade e integridade operacional de todos os seus Azure Cosmos DB recursos em uma experiência interativa unificada e aproveite os outros recursos do Azure Monitor para análise e alertas detalhados. 

![Azure Monitor para Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Dados de monitoramento coletados do Azure Cosmos DB
Azure Cosmos DB coleta os mesmos tipos de dados de monitoramento que outros recursos do Azure, que são descritos em [monitoramento de dados de recursos do Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Veja [Azure Cosmos DB referência de dados de monitoramento](monitor-cosmos-db-reference.md) para obter uma referência detalhada dos logs e métricas criados por Azure Cosmos DB.

A página de **visão geral** no portal do Azure para cada banco de dados Cosmos do Azure inclui uma breve exibição do uso do banco de dados, incluindo sua solicitação e o uso de cobrança por hora. Essas são informações úteis, mas apenas uma pequena quantidade de dados de monitoramento disponíveis. Alguns desses dados são coletados automaticamente e disponíveis para análise assim que você cria o banco de dados, enquanto você pode habilitar a coleta de dado adicional com algumas configurações.

![Página de visão geral](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Definições de diagnóstico
As métricas de plataforma e o log de atividades são coletados automaticamente, mas você deve criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los fora do Azure Monitor. Consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../azure-monitor/platform/diagnostic-settings.md) para o processo detalhado para criar uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell.

Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar. As categorias para Azure Cosmos DB estão listadas abaixo, juntamente com os dados de exemplo.

 * **DataPlaneRequests**: Selecione esta opção para registrar solicitações de back-end para todas as APIs que incluem as contas SQL, Graph, MongoDB, Cassandra e API de Tabela no Azure Cosmos DB. As propriedades de chave a serem observadas são: Requestcharge, statusCode, clientIPaddress e PartitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário do front-end para atender a solicitações para a API do Azure Cosmos DB para MongoDB. As solicitações do MongoDB serão exibidas em MongoRequests, bem como DataPlaneRequests. As propriedades de chave a serem observadas são: Requestcharge, opCode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selecione esta opção para registrar o texto da consulta que foi executado. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecione esta opção para registrar as estatísticas das chaves de partição. Atualmente, isso é representado com o tamanho do armazenamento (KB) das chaves de partição. O log é emitido em relação às três primeiras chaves de partição que ocupam a maior parte do armazenamento de dados.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Solicitações de métrica**: Selecione esta opção para coletar dados de métrica de Azure Cosmos DB para os destinos na configuração de diagnóstico. Esses são os mesmos dados coletados automaticamente nas métricas do Azure. Coletar dados de métrica com logs de recursos para analisar os dois tipos de dados juntos e enviar dados de métricas fora do Azure Monitor.

## <a name="analyzing-metric-data"></a>Analisando dados de métrica
Azure Cosmos DB fornece uma experiência personalizada para trabalhar com métricas. Consulte [monitorar e depurar Azure Cosmos DB métricas de Azure monitor](cosmos-db-azure-monitor-metrics.md) para obter detalhes sobre como usar essa experiência e para analisar diferentes cenários de Azure Cosmos DB.

Você pode analisar métricas para Azure Cosmos DB com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **métricas** no menu **Azure monitor** . Consulte [introdução ao Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta. Todas as métricas para Azure Cosmos DB estão no namespace **Cosmos DB métricas padrão**. Você pode usar as seguintes dimensões com essas métricas ao adicionar um filtro a um gráfico:

- CollectionName
- databaseName
- OperationType
- Região
- StatusCode


## <a name="analyzing-log-data"></a>Analisando dados de log
Os dados em logs de Azure Monitor são armazenados em tabelas que cada tabela tem seu próprio conjunto de propriedades exclusivas. Azure Cosmos DB armazena dados nas tabelas a seguir.

| Tabela | Descrição |
|:---|:---|
| AzureDiagnostics | Tabela comum usada por vários serviços para armazenar logs de recursos. Os logs de recursos de Azure Cosmos DB podem ser identificados com `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Tabela comum que armazena todos os registros do log de atividades. 


> [!IMPORTANT]
> Quando você seleciona **logs** no menu Azure Cosmos DB, log Analytics é aberto com o escopo de consulta definido para o banco de dados atual do Azure Cosmos. Isso significa que as consultas de log incluirão apenas os dados desse recurso. Se você quiser executar uma consulta que inclua dados de outros bancos de dados ou de outros serviços do Azure, selecione **logs** no menu **Azure monitor** . Consulte [escopo de consulta de log e intervalo de tempo em Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) para obter detalhes.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics consultas no Azure Monitor

Aqui estão algumas consultas que você pode inserir na barra de pesquisa da **pesquisa de logs** para ajudá-lo a monitorar seus contêineres de Cosmos do Azure. Essas consultas funcionam com a [nova linguagem](../log-analytics/log-analytics-log-search-upgrade.md).

Veja a seguir as consultas que você pode usar para ajudá-lo a monitorar seus bancos de dados do Azure Cosmos.

* Para consultar todos os registos de diagnóstico do Azure Cosmos DB durante um período de tempo especificado:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Para consultar o 10 mais recentemente registados eventos:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Para consultar todas as operações, agrupadas por tipo de operação:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Para consultar todas as operações, agrupadas por recurso:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Para consultar todas as atividades de utilizador, agrupadas pelo recurso:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Para obter todas as consultas com mais de 100 RUs Unidas com dados de **DataPlaneRequests** e **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* A consulta para os quais operações mais demoram 3 milissegundos:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Para consultar para o qual o agente está a executar as operações:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Para consultar quando foram efetuadas as operações de longa execução:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Para obter estatísticas de chave de partição para avaliar a distorção entre as três principais partições para a conta de banco de dados:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorizar o Azure Cosmos DB através de programação
As métricas ao nível de conta disponíveis no portal, como a conta a utilização e total de pedidos de armazenamento, não estão disponíveis por meio das APIs de SQL. No entanto, pode recuperar dados de utilização ao nível da coleção com as APIs de SQL. Para obter dados de nível de coleção, faça o seguinte:

* Para usar a API REST, [Execute um get na coleção](https://msdn.microsoft.com/library/mt489073.aspx). As informações de quota e utilização para a coleção são devolvidas nos cabeçalhos x-ms-resource-quota e x-ms--a utilização de recursos na resposta.
* Para usar o SDK do .NET, use o método [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , que retorna um [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) que contém várias propriedades de uso, como **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**e muito mais.

Para acessar métricas adicionais, use o [SDK do Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Definições de métricas disponíveis podem ser obtidas chamando:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Consultas para obter métricas individuais, utilize o seguinte formato:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Passos Seguintes

- Veja [Azure Cosmos DB referência de dados de monitoramento](monitor-cosmos-db-reference.md) para obter uma referência dos logs e métricas criados por Azure Cosmos DB.
- Consulte [monitorando recursos do Azure com Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) para obter detalhes sobre como monitorar recursos do Azure.
