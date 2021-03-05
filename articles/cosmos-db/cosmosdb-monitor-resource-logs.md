---
title: Monitorize os dados do DB do Azure Cosmos utilizando as definições de Diagnóstico Azure
description: Saiba como utilizar as definições de Diagnóstico Azure para monitorizar o desempenho e disponibilidade de dados armazenados em Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.openlocfilehash: 1e551fc12da5e25ba54df5a6a38a49b76f7c376e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181878"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorizar os dados do Azure Cosmos DB com as definições de diagnóstico no Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

As definições de diagnóstico no Azure são utilizadas para recolher registos de recursos. Os registos de recursos Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. Estes registos são capturados por pedido e também são referidos como "registos de data plane". Alguns exemplos das operações do plano de dados incluem eliminar, inserir e lerFeed. O conteúdo destes registos varia consoá-lo por tipo de recurso.

As métricas da plataforma e os registos de Atividade são recolhidos automaticamente, enquanto que deve criar uma definição de diagnóstico para recolher registos de recursos ou reencaizá-los para fora do Azure Monitor. Pode ligar a definição de diagnóstico para as contas da Azure Cosmos utilizando os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue para a sua conta Azure Cosmos. Abra o painel de **definições de diagnóstico** e, em seguida, selecione Adicione a opção **de definição de diagnóstico.**

1. No painel de **definições de Diagnóstico,** preencha o formulário com os seguintes detalhes: 

    * **Nome**: Introduza um nome para os registos a criar.

    * Pode armazenar os registos para **arquivar numa conta de armazenamento,** transmitir para um centro de **eventos** ou **enviar para registar analíticos**

1. Quando cria uma definição de diagnóstico, especifica qual a categoria de registos a recolher. As categorias de registos suportados pela Azure Cosmos DB são listadas abaixo, juntamente com o registo de amostras recolhido por eles:

 * **DataPlaneRequests**: Selecione esta opção para registar pedidos de back-end nas contas API SQL em Azure Cosmos DB. As propriedades-chave a notar são: `Requestcharge` , , , , , e `statusCode` `clientIPaddress` `partitionID` `resourceTokenPermissionId` `resourceTokenPermissionMode` .

   ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
   ```
   
   Utilize a seguinte consulta para obter registos correspondentes a pedidos de plano de dados:
  
   ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   ```

* **MongoRequests**: Selecione esta opção para registar pedidos iniciados pelo utilizador a partir da parte frontal para servir pedidos à API da Azure Cosmos DB para a MongoDB. Este tipo de registo não está disponível para outras contas API. As principais propriedades a notar são: `Requestcharge` `opCode` . . Quando ativar os MongoRequests em registos de diagnóstico, certifique-se de desligar os DataPlaneRequests. Veria um registo de cada pedido feito na API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```
  
  Utilize a seguinte consulta para obter registos correspondentes aos pedidos da MongoDB:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="MongoRequests"
  ```

* **CassandraRequests**: Selecione esta opção para registar pedidos iniciados pelo utilizador a partir da parte frontal para servir pedidos à API da Azure Cosmos DB para a Cassandra. Este tipo de registo não está disponível para outras contas API. As principais propriedades a notar `operationName` `requestCharge` são, `piiCommandText` . . Quando ativar cassandraRequests em registos de diagnóstico, certifique-se de desligar os DataPlaneRequests. Veria um registo de cada pedido feito na API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```
   
  Utilize a seguinte consulta para obter registos correspondentes aos pedidos da Cassandra:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="CassandraRequests"
  ```

* **GremlinRequests**: Selecione esta opção para registar pedidos iniciados pelo utilizador a partir da parte frontal para servir pedidos à API da Azure Cosmos DB para a Gremlin. Este tipo de registo não está disponível para outras contas API. As principais propriedades a notar são `operationName` `requestCharge` e. Quando ativar os GremlinRequests nos registos de diagnóstico, certifique-se de desligar os DataPlaneRequests. Veria um registo de cada pedido feito na API.

  ```json
  { "time": "2021-01-06T19:36:58.2554534Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "GremlinRequests", "operationName": "eval", "properties": {"activityId": "b16bd876-0e5c-4448-90d1-7f3134c6b5ff", "errorCode": "200", "duration": "9.6036", "requestCharge": "9.059999999999999", "databaseName": "GraphDemoDatabase", "collectionName": "GraphDemoContainer", "authorizationTokenType": "PrimaryMasterKey", "address": "98.225.2.189", "estimatedDelayFromRateLimitingInMilliseconds": "0", "retriedDueToRateLimiting": "False", "region": "Australia East", "requestLength": "266", "responseLength": "364", "userAgent": "<empty>"}}
  ```
  
  Utilize a seguinte consulta para obter registos correspondentes aos pedidos da Gremlin:
  
  ```kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="GremlinRequests"
  ```

* **QueryRuntimeStatistics**: Selecione esta opção para registar o texto de consulta que foi executado. Este tipo de registo está disponível apenas para contas API SQL.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecione esta opção para registar as estatísticas das teclas de partição. Isto está atualmente representado com o tamanho de armazenamento (KB) das teclas de partição. Consulte os [problemas de resolução de problemas utilizando a](#diagnostic-queries) secção de consultas de Diagnóstico Azure deste artigo. Por exemplo, consultas que usam "PartitionKeyStatistics". O registo é emitido contra as três primeiras chaves de partição que ocupam a maioria do armazenamento de dados. Este registo contém dados como ID de subscrição, nome da região, nome da base de dados, nome da recolha, chave de partição e tamanho de armazenamento em KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Este registo informa o consumo agregado por segundo de chaves de partição. Atualmente, a Azure Cosmos DB reporta chaves de partição apenas para contas de API SQL e para operações de leitura/escrita de pontos e procedimentos armazenados. outros APIs e tipos de operação não são suportados. Para outras APIs, a coluna da chave de partição na tabela de registo de diagnóstico estará vazia. Este registo contém dados como ID de subscrição, nome da região, nome da base de dados, nome da recolha, chave de partição, tipo de operação e taxa de pedido. Consulte os [problemas de resolução de problemas utilizando a](#diagnostic-queries) secção de consultas de Diagnóstico Azure deste artigo. Por exemplo, consultas que usam "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Este registo contém detalhes sobre operações de plano de controlo como criar uma conta, adicionar ou remover uma região, atualizar as definições de replicação de conta, etc. Este tipo de registo está disponível para todos os tipos de API que incluem SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Pedidos**: Selecione esta opção para recolher dados métricos da Azure Cosmos DB para os destinos na definição de diagnóstico. Estes são os mesmos dados recolhidos automaticamente em Azure Metrics. Recolher dados métricos com registos de recursos para analisar ambos os tipos de dados em conjunto e enviar dados métricos fora do Azure Monitor.

Para obter informações detalhadas sobre como criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell, consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma em artigo Azure.](../azure-monitor/essentials/diagnostic-settings.md)


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Problemas de resolução de problemas com consultas de diagnóstico

1. Como consultar as operações que estão a demorar mais de 3 milissegundos a executar:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Como consultar o agente utilizador que está a executar as operações:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Como consultar as operações de longo prazo:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Como obter estatísticas-chave de partição para avaliar o desvio entre as 3 principais divisórias para uma conta de base de dados:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. Como obter os pedidos de consultas caras?

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

1. Como descobrir que operações estão a tomar a maior parte do RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. Como obter todas as consultas que estão consumindo mais do que 100 RU/s juntas com dados de **DataPlaneRequests** e **QueryRunTimeStatistics**.

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

1. Como obter as acusações de pedido e a duração da execução de uma consulta?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. Como obter a distribuição para diferentes operações?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual é a produção máxima que uma divisória consumiu?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Como obter a informação sobre as chaves de partição ru/s consumo por segundo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Como obter a taxa de pedido de uma chave de partição específica

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Como obter as chaves de partição superior com a maioria ru/s consumidas num período específico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Como obter os registos das chaves de partição cujo tamanho de armazenamento é superior a 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 8000000
   ```

1. Como obter latências de replicação P99 ou P50 para operações, taxa de pedido ou duração da resposta?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Como obter registos do ControlPlane?
 
   Lembre-se de ligar a bandeira como descrito no artigo [de acesso de metadados baseado em chave para desativar](audit-control-plane-logs.md#disable-key-based-metadata-write-access) e executar as operações utilizando a Azure PowerShell, o Azure CLI ou O Gestor de Recursos Azure.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Passos seguintes

* [Monitor Azure para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitore e depure com métricas em Azure Cosmos DB](use-metrics.md)
