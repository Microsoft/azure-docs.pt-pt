---
title: Monitor Ize os dados do Azure Cosmos DB utilizando as definições de Diagnóstico Azure
description: Saiba como utilizar as definições de Diagnóstico Azure para monitorizar o desempenho e disponibilidade de dados armazenados no Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521052"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitor De dados do Azure Cosmos DB utilizando definições de diagnóstico no Azure

As definições de diagnóstico em Azure são utilizadas para recolher registos de recursos. Os registos de recursos Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. Estes registos são capturados por pedido e também são referidos como "registos de planos de dados". Alguns exemplos das operações do avião de dados incluem apagar, inserir e lerFeed. O conteúdo destes registos varia por tipo de recurso.

As métricas da plataforma e os registos de Atividade são recolhidos automaticamente, ao passo que é necessário criar uma definição de diagnóstico para recolher registos de recursos ou encaminhar para fora do Monitor Azure. Pode ligar a definição de diagnóstico para as contas do Azure Cosmos utilizando os seguintes passos:

1. Assine no [portal Azure.](https://portal.azure.com)

1. Navegue para a sua conta Azure Cosmos. Abra o painel de **definições de diagnóstico** e, em seguida, selecione Adicionar a opção de **definição de diagnóstico.**

1. No painel de **definições de diagnóstico,** encha o formulário com os seguintes detalhes: 

    * **Nome**: Introduza um nome para os registos a criar.

    * Pode armazenar os registos para Arquivar numa conta de **armazenamento,** **transmitir para um centro de eventos** ou enviar para Log **Analytics**

1. Quando cria uma definição de diagnóstico, especifica qual a categoria de registos a recolher. As categorias de registos suportados pela Azure Cosmos DB estão listadas abaixo juntamente com o registo de amostras recolhido por eles:

 * **DataPlaneRequests**: Selecione esta opção para registar pedidos de back-end para todas as APIs, que incluem Contas SQL, Graph, MongoDB, Cassandra e Table API em contas Azure Cosmos DB. As principais propriedades `Requestcharge`a `statusCode` `clientIPaddress`notar `partitionID`são: , , e .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecione esta opção para registar pedidos iniciados pelo utilizador a partir da parte frontal para servir pedidos à API da Azure Cosmos DB para mongoDB. Este tipo de registo não está disponível para outras contas API. As principais propriedades `Requestcharge`a `opCode`notar são: . Quando ativa rumit os Registos de Diagnóstico, certifique-se de que desliga os DataPlaneRequests. Veria um registo para cada pedido feito na API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Selecione esta opção para registar pedidos iniciados pelo utilizador a partir da parte frontal para servir pedidos à API da Azure Cosmos DB para Cassandra. Este tipo de registo não está disponível para outras contas API. As principais propriedades `operationName`a `requestCharge` `piiCommandText`notar são, . Quando ativar os Pedidos de Cassandra em registos de diagnóstico, certifique-se de desligar os DataPlaneRequests. Veria um registo para cada pedido feito na API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **ConsultasEstatísticas :** Selecione esta opção para registar o texto de consulta que foi executado. Este tipo de registo está disponível apenas para contas SQL API.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecione esta opção para registar as estatísticas das teclas de partição. Isto está atualmente representado com o tamanho de armazenamento (KB) das teclas de partição. Consulte os problemas de resolução de problemas utilizando a secção de consultas de [Diagnóstico Azure](#diagnostic-queries) deste artigo. Por exemplo, consultas que utilizam "PartitionKeyStatistics". O registo é emitido contra as três primeiras teclas de partição que ocupam a maioria do armazenamento de dados. Este registo contém dados como ID de subscrição, nome da região, nome da base de dados, nome de recolha, chave de partição e tamanho de armazenamento em KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartilhakeyRUConsumo**: Este registo reporta o consumo agregado por segundo de CHAVES de partição. Atualmente, a Azure Cosmos DB reporta chaves de partição apenas para contas SQL API e para operações de leitura/escrita e procedimentos armazenados. não são suportados outros APIs e tipos de operação. Para outras APIs, a coluna da chave da partição na tabela de registos de diagnóstico estará vazia. Este registo contém dados como ID de subscrição, nome da região, nome da base de dados, nome de recolha, chave de partição, tipo de operação e cobrança de pedidos. Consulte os problemas de resolução de problemas utilizando a secção de consultas de [Diagnóstico Azure](#diagnostic-queries) deste artigo. Por exemplo, consultas que utilizam "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Este registo contém detalhes sobre operações de planode controlo como criar uma conta, adicionar ou remover uma região, atualizar as definições de replicação de conta, etc. Este tipo de log está disponível para todos os tipos de API que incluem SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Pedidos**: Selecione esta opção para recolher dados métricos do Azure Cosmos DB para os destinos na definição de diagnóstico. Estes são os mesmos dados recolhidos automaticamente nas Métricas Azure. Recolher dados métricos com registos de recursos para analisar ambos os tipos de dados em conjunto e enviar dados métricos para fora do Monitor Azure.

Para obter informações detalhadas sobre como criar uma definição de diagnóstico utilizando o portal Azure, CLI ou PowerShell, consulte [Criar definição de diagnóstico para recolher registos e métricas da plataforma no artigo do Azure.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Problemas de resolução de problemas com consultas de diagnóstico

1. Como obter os encargos de pedido para consultas caras?

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

1. Como descobrir que operações estão a tomar a maior parte dos RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Como obter a distribuição para diferentes operações?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Qual é a máxima entrada que uma partição consumiu?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Como obter a informação sobre as teclas de partição RU/s consumo por segundo?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Como obter a taxa de pedido para uma chave de partição específica

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Como obter as chaves de partição superior com a maioria dos RU/s consumidos num período específico? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Como obter os registos das teclas de partição cujo tamanho de armazenamento é superior a 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Como obter divisória Estatísticas chave para avaliar skew em todas as três divisórias para conta de base de dados?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Passos seguintes

* [Monitor Azure para Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitor e depuração com métricas em Azure Cosmos DB](use-metrics.md)
