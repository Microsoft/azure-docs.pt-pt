---
title: Monitorar dados de Azure Cosmos DB usando as configurações de diagnóstico do Azure
description: Saiba como usar as configurações de diagnóstico do Azure para monitorar o desempenho e a disponibilidade dos dados armazenados no Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 670797eb833b0a145a18e20c6bba711ca11609bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483287"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorar dados de Azure Cosmos DB usando as configurações de diagnóstico no Azure

As configurações de diagnóstico no Azure são usadas para coletar logs de recursos. Os logs de recursos do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação desse recurso. Esses logs são capturados por solicitação e também são chamados de "logs do plano de dados". Alguns exemplos das operações do plano de dados incluem excluir, inserir e readFeed. O conteúdo estes registos varia consoante o tipo de recurso.

As métricas de plataforma e os logs de atividade são coletados automaticamente, enquanto você deve criar uma configuração de diagnóstico para coletar logs de recursos ou encaminhá-los fora do Azure Monitor. Você pode ativar a configuração de diagnóstico para contas do Azure Cosmos usando as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue até sua conta do Azure Cosmos. Abra o painel **configurações de diagnóstico** e selecione a opção **Adicionar configuração de diagnóstico** .

1. No painel **configurações de diagnóstico** , preencha o formulário com os seguintes detalhes: 

    * **Nome**: introduza um nome para os registos criar.

    * Você pode armazenar os logs para **arquivar em uma conta de armazenamento**, **transmitir para um hub de eventos** ou **Enviar para log Analytics**

1. Ao criar uma configuração de diagnóstico, você especifica qual categoria de logs coletar. As categorias de logs com suporte pelo Azure Cosmos DB estão listadas abaixo, juntamente com o log de exemplo coletado por eles:

 * **DataPlaneRequests**: Selecione esta opção para registrar solicitações de back-end para todas as APIs, que incluem as contas SQL, Graph, MongoDB, Cassandra e API de Tabela no Azure Cosmos DB. As propriedades de chave a serem observadas são: `Requestcharge`, `statusCode`, `clientIPaddress`e `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecione esta opção para registrar solicitações iniciadas pelo usuário do front-end para atender a solicitações para a API do Azure Cosmos DB para MongoDB, esse tipo de log não está disponível para outras contas de API. As solicitações do MongoDB serão exibidas em MongoRequests, bem como DataPlaneRequests. As propriedades de chave a serem observadas são: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selecione esta opção para registrar o texto da consulta que foi executado. Esse tipo de log está disponível somente para contas da API do SQL.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecione esta opção para registrar as estatísticas das chaves de partição. Atualmente, isso é representado com o tamanho do armazenamento (KB) das chaves de partição. Consulte a seção [Solucionando problemas usando consultas de diagnóstico do Azure](#diagnostic-queries) deste artigo. Por exemplo, consultas que usam "PartitionKeyStatistics". O log é emitido em relação às três primeiras chaves de partição que ocupam a maior parte do armazenamento de dados. Esse log contém dados como a ID da assinatura, o nome da região, o nome do banco de dado, o nome da coleção, a chave de partição e o tamanho do armazenamento em KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: esse log relata o consumo de ru/s agregado por segundo de chaves de partição. Atualmente, Azure Cosmos DB relata chaves de partição somente para contas de API do SQL e para operações de ponto de leitura/gravação e procedimento armazenado. Não há suporte para outros tipos de operação e APIs. Para outras APIs, a coluna de chave de partição na tabela de log de diagnóstico estará vazia. Esse log contém dados como a ID da assinatura, o nome da região, o nome do banco, o nome da coleção, a chave de partição, o tipo de operação e o encargo da solicitação. Consulte a seção [Solucionando problemas usando consultas de diagnóstico do Azure](#diagnostic-queries) deste artigo. Por exemplo, consultas que usam "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: esse log contém detalhes sobre as operações do plano de controle, como criar uma conta, adicionar ou remover uma região, atualizar as configurações de replicação da conta, etc. Esse tipo de log está disponível para todos os tipos de API que incluem SQL (Core), MongoDB, Gremlin, Cassandra, API de Tabela.

* **Solicitações**: Selecione esta opção para coletar dados de métrica de Azure Cosmos DB para os destinos na configuração de diagnóstico. Esses são os mesmos dados coletados automaticamente nas métricas do Azure. Coletar dados de métrica com logs de recursos para analisar os dois tipos de dados juntos e enviar dados de métricas fora do Azure Monitor.

Para obter informações detalhadas sobre como criar uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell, consulte [criar configuração de diagnóstico para coletar logs e métricas de plataforma no artigo do Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a id="diagnostic-queries"></a>Solucionar problemas com consultas de diagnóstico

1. Como obter os encargos de solicitação para consultas caras?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Como descobrir quais operações estão assumindo a maior parte de RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Como obter a distribuição para operações diferentes?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual é a taxa de transferência máxima que uma partição fornece?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Como obter as informações sobre o consumo de RU/s de chaves de partição por segundo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Como obter a cobrança de solicitação para uma chave de partição específica

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Como obter as principais chaves de partição com a maioria das RU/s consumidas em um período específico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Como obter os logs para as chaves de partição cujo tamanho de armazenamento é maior que 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Como obter estatísticas de chave de partição para avaliar a distorção entre as três principais partições para a conta do banco de dados?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Passos seguintes

* [Azure Monitor para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
