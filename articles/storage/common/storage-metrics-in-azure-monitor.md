---
title: Métricas de armazenamento azure no Monitor Azure / Microsoft Docs
description: Conheça as novas métricas oferecidas pelo Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 73181222bf3f15bbbac24fc253eddfea1c57bc6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247100"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Métricas do Armazenamento do Azure no Azure Monitor

Com métricas no Armazenamento Azure, pode analisar as tendências de utilização, rastrear pedidos e diagnosticar problemas com a sua conta de armazenamento.

O Azure Monitor fornece interfaces unificadas de utilizador para monitorização em diferentes serviços Azure. Para mais informações, consulte [o Monitor Azure.](../../monitoring-and-diagnostics/monitoring-overview.md) O Azure Storage integra o Azure Monitor enviando dados métricos para a plataforma Azure Monitor.

## <a name="access-metrics"></a>Métricas de acesso

O Monitor Azure fornece várias formas de aceder às métricas. Pode aceder-lhes a partir do [portal Azure,](https://portal.azure.com)das APIs do Monitor Azure (REST e .NET) e de soluções de análise como os Centros de Eventos. Para mais informações, consulte [As Métricas do Monitor Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As métricas são ativadas por padrão, e você pode aceder aos últimos 93 dias de dados. Se precisar de reter dados por um período mais longo, pode arquivar dados de métricas numa conta de Armazenamento Azure. Isto está configurado em [definições](../../azure-monitor/platform/platform-logs-overview.md) de diagnóstico no Monitor Azure.

### <a name="access-metrics-in-the-azure-portal"></a>Métricas de acesso no portal Azure

Pode monitorizar as métricas ao longo do tempo no portal Azure. O exemplo que se segue mostra como visualizar **as Transações** ao nível da conta.

![screenshot de métricas de acesso no portal Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão desejado. O exemplo seguinte mostra como visualizar **as Transações** ao nível da conta numa operação específica, selecionando valores para a dimensão **do nome API.**

![screenshot de aceder métricas com dimensão no portal Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Métricas de acesso com a API REST

O Monitor Azure fornece [APIs REST](/rest/api/monitor/) para ler definição métrica e valores. Esta secção mostra-lhe como ler as métricas de armazenamento. O ID de recursos é usado em todos os APIS REST. Para mais informações, por favor leia a compreensão do ID de recursos para serviços no Armazenamento.

O exemplo que se segue mostra como usar o [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comando para simplificar os testes com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Lista de definição métrica de nível de conta com a API REST

O exemplo que se segue mostra como enumerar a definição métrica ao nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Se pretender listar as definições métricas para blob, tabela, ficheiro ou fila, deve especificar diferentes IDs de recursos para cada serviço com a API.

A resposta contém a definição métrica no formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Leia os valores métricos ao nível da conta com a API REST

O exemplo que se segue mostra como ler dados métricos ao nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Em exemplo acima, se quiser ler valores métricos para blob, tabela, arquivo ou fila, deve especificar diferentes IDs de recursos para cada serviço com a API.

A seguinte resposta contém valores métricos no formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Métricas de acesso com o .NET SDK

O Azure Monitor fornece [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler definição métrica e valores. O [código da amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Você precisa `0.18.0-preview` usar ou versão posterior para métricas de armazenamento. O ID de recurso é utilizado em .NET SDK. Para mais informações, por favor leia a compreensão do ID de recursos para serviços no Armazenamento.

O exemplo que se segue mostra como utilizar o Azure Monitor .NET SDK para ler as métricas de armazenamento.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Lista de definição métrica de nível de conta com o .NET SDK

O exemplo que se segue mostra como enumerar a definição métrica ao nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Se pretender listar as definições métricas para blob, tabela, ficheiro ou fila, deve especificar diferentes IDs de recursos para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Leia os valores métricos com o .NET SDK

O exemplo que se `UsedCapacity` segue mostra como ler dados a nível de conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Em exemplo acima, se quiser ler valores métricos para blob, tabela, arquivo ou fila, deve especificar diferentes IDs de recursos para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Leia os valores métricos multidimensionais com o .NET SDK

Para métricas multidimensionais, é necessário definir o filtro de meta dados se quiser ler dados métricos sobre o valor de dimensão específico.

O exemplo que se segue mostra como ler dados métricos sobre a multidimensionada métrica que suporta a multidimensão:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Compreensão do ID de recursos para serviços em Armazenamento Azure

O ID de recursos é um identificador único de um recurso em Azure. Quando utilizar a API DO MONITOR Azure para ler definições ou valores de métricas, deve utilizar o ID de recurso para o recurso em que pretende operar. O modelo de ID de recurso segue este formato:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

O armazenamento fornece métricas tanto ao nível da conta de armazenamento como ao nível de serviço com o Monitor Azure. Por exemplo, pode recuperar métricas para apenas armazenamento Blob. Cada nível tem o seu próprio ID de recursos, que é usado para recuperar as métricas para apenas esse nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recursos para uma conta de armazenamento

O seguinte mostra o formato para especificar o ID de recurso para uma conta de armazenamento.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID de recursos para os serviços de armazenamento

O seguinte mostra o formato para especificar o ID de recurso para cada um dos serviços de armazenamento.

* ID de recurso de serviço blob
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID de recurso de serviço de mesa
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID de serviço de serviço de fila
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID de recurso de serviço de arquivo
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de recursos na API REST Do Monitor Azure

O seguinte mostra o padrão utilizado ao chamar a API REST DO Monitor Azure.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Métricas de capacidade
Os valores das métricas de capacidade são enviados para o Monitor Azure a cada hora. Os valores são refrescados diariamente. O grão de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O grão de tempo suportado para todas as métricas de capacidade é de uma hora (PT1H).

O Armazenamento Azure fornece as seguintes métricas de capacidade no Monitor Azure.

### <a name="account-level"></a>Nível de conta

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Capacidade Utilizada | A quantidade de armazenamento utilizada pela conta de armazenamento. Para as contas de armazenamento standard, é a soma da capacidade utilizada pelos blobs, tabelas, ficheiros e filas. Para as contas de armazenamento premium e contas de armazenamento de blobs, é o mesmo que BlobCapacity. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blobs

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total do armazenamento blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensões: **BlobType**e **BlobTier** [(Definição)](#metrics-dimensions) |
| Contagem de contentores    | O número de contentores na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento utilizado pelo Índice Hierárquico ADLS Gen2 <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Table Storage

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Capacidade de Mesa | A quantidade de armazenamento de mesa utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de tabelas   | O número de tabelas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de tabelas | O número de entidades de mesa na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Capacidade de fila | A quantidade de armazenamento de fila utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de filas   | O número de filas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de mensagens de fila | O número de mensagens de fila não expiradas na conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de ficheiros

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Capacidade de Arquivo | A quantidade de armazenamento de ficheiros utilizada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| Contagem de Ficheiros   | O número de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de ações de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em todos os pedidos de uma conta de armazenamento do Azure Storage para o Monitor Azure. No caso de nenhuma atividade na sua conta de armazenamento, não haverá dados sobre métricas de transação no período. Todas as métricas de transação estão disponíveis tanto a nível de conta como de serviço (armazenamento blob, armazenamento de mesa, Ficheiros Azure e armazenamento de fila). O grão de tempo define o intervalo de tempo que os valores métricos são apresentados. Os grãos de tempo suportados para todas as métricas de transação são PT1H e PT1M.

O Azure Storage fornece as seguintes métricas de transação no Monitor Azure.

| Nome métrico | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions)<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui saída sugou para um cliente externo do Armazenamento Azure, bem como para a saída dentro do Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milliseconds <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. <br/><br/> Unidade: Milliseconds <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao dividir o valor do total dos pedidos faturáveis pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada. <br/><br/> Unidade: Por cento <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 99.99 |

## <a name="metrics-dimensions"></a>Dimensões métricas

O Armazenamento Azure suporta as dimensões seguintes para métricas no Monitor Azure.

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de bolha apenas para as métricas blob. Os valores suportados são **BlockBlob,** **PageBlob**e **Azure Data Lake Storage.** Append Blob está incluído no BlockBlob. |
| **BlobTier** | O armazenamento Azure oferece diferentes níveis de acesso, que lhe permitem armazenar dados de objetos blob da forma mais rentável. Veja mais na [categoria de blob de armazenamento Azure](../blobs/storage-blob-storage-tiers.md). Os valores suportados incluem: <br/> <li>**Hot**: Hot tier</li> <li>**Cool**: Cool tier</li> <li>**Arquivo**: Nível de arquivo</li> <li>**Premium**: Nível premium para bloco blob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Tipos de nível para a página premium blob</li> <li>**Standard**: Tipo de nível para página padrão Blob</li> <li>**Untiered**: Tier type para uso geral v1 conta de armazenamento</li> |
| **GeoTipo** | Transação do cluster primário ou secundário. Os valores disponíveis incluem **Primário** e **Secundário.** Aplica-se ao Read Access Geo Redundant Storage (RA-GRS) ao ler objetos de inquilino secundário. |
| **Tipo de resposta** | Tipo de resposta a transações. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: Todos os outros erros do lado do servidor, exceto os descritos </li> <li>**ServerBusyError**: Pedido autenticado que devolveu um código de estado HTTP 503. </li> <li>**ServerTimeoutError**: Pedido autenticado cronometrado que devolveu um código de estado HTTP 500. O tempo limite excedido ocorreu devido a um erro de servidor. </li> <li>**AutorizaçãoErro**: Pedido autenticado que falhou devido ao acesso não autorizado de dados ou a uma falha de autorização. </li> <li>**Error de rede**: Pedido autenticado que falhou devido a erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes da expiração do tempo limite. </li>  <li>**ClientAccountBandwidthThrottlingError**: O pedido é estrangulado na largura de banda para exceder os limites de [escalabilidade da conta](scalability-targets-standard-account.md)de armazenamento .</li><li>**ClientAccountRequestThrottlingError**: O pedido é acelerado na taxa de pedido por exceder [os limites](scalability-targets-standard-account.md)de escalabilidade da conta de armazenamento .<li>**ClientThrottlingError**: Outro erro de estrangulamento do lado do cliente. ClientAccountBandwidthThrottlingError e ClientAccountRequestThrottlingError estão excluídos.</li> <li>**ClientTimeoutError**: Pedido autenticado cronometrado que devolveu um código de estado HTTP 500. Se o tempo limite da rede do cliente ou do pedido estiver definido como um valor inferior ao esperado pelo serviço de armazenamento, trata-se de um tempo limite esperado. Caso contrário, é reportado como um ServerTimeoutError.</li> </li> <li>**ClientOtherError**: Todos os outros erros do lado do cliente, exceto os descritos. </li> <li>**Sucesso**: Pedido bem sucedido</li> <li> **SucessoWithThrottling**: Pedido bem sucedido quando um cliente SMB é estrangulado na primeira tentativa(s) mas consegue após tentativas.</li> |
| **Nome Api** | O nome da operação. Por exemplo: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Para todos os nomes da operação, consulte [o documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autenticação** | Tipo de autenticação utilizado em transações. Os valores disponíveis incluem: <br/> <li>**Chave de conta**: A transação é autenticada com a chave da conta de armazenamento.</li> <li>**SAS**: A transação é autenticada com assinaturas de acesso partilhado.</li> <li>**OAuth**: A transação é autenticada com fichas de acesso OAuth.</li> <li>**Anónimo**: A transação é solicitada anonimamente. Não inclui pedidos de pré-voo.</li> <li>**AnonymousPreflight**: A transação é pedido de pré-voo.</li> |

Para as dimensões de suporte das métricas, é necessário especificar o valor de dimensão para ver os valores métricos correspondentes. Por exemplo, se olhar para o valor **das Transações** para respostas bem sucedidas, precisa de filtrar a dimensão **ResponseType** com **sucesso**. Ou se olhar para o valor **BlobCount** para Block Blob, precisa de filtrar a dimensão **BlobType** com **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade do serviço das métricas antigas

As métricas antigas estão disponíveis em paralelo com as métricas geridas pelo Azure Monitor. O suporte mantém o mesmo até que o Armazenamento Azure termine o serviço em métricas antigas.

## <a name="faq"></a>FAQ

**As novas métricas suportam a conta de Armazenamento Clássico?**

Não, as novas métricas no Azure Monitor apenas suportam as contas de armazenamento do Gestor de Recursos Azure. Se quiser utilizar métricas nas contas de Armazenamento, precisa de migrar para a conta de Armazenamento do Gestor de Recursos Azure. Consulte [migrate para Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**O Armazenamento Azure suporta métricas para discos geridos ou discos não geridos?**

Não, a Azure Compute suporta as métricas nos discos. Consulte o [artigo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) para mais detalhes.

**Como mapear e migrar métricas clássicas com novas métricas?**

Você pode encontrar mapeamento detalhado entre métricas clássicas e novas métricas na migração de [métricas de armazenamento azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Passos seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
