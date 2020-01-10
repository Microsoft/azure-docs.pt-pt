---
title: Métricas de armazenamento do Azure no Azure Monitor | Microsoft Docs
description: Saiba mais sobre as novas métricas oferecidas de Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 62e2e3f1a80cef04dc778d5a1950cca97d79dcb0
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748373"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Métricas do Armazenamento do Azure no Azure Monitor

Com as métricas no armazenamento do Azure, você pode analisar tendências de uso, rastrear solicitações e diagnosticar problemas com sua conta de armazenamento.

Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre diferentes serviços do Azure. Para obter mais informações, consulte [Azure monitor](../../monitoring-and-diagnostics/monitoring-overview.md). O armazenamento do Azure integra Azure Monitor enviando dados de métrica para a plataforma Azure Monitor.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Você pode acessá-los do [portal do Azure](https://portal.azure.com), as APIs de Azure monitor (REST e .net) e as soluções de análise, como os hubs de eventos. Para obter mais informações, consulte [Azure monitor métricas](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As métricas são habilitadas por padrão e você pode acessar os últimos 93 dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../../azure-monitor/platform/platform-logs-overview.md) no Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Métricas de acesso no portal do Azure

Você pode monitorar as métricas ao longo do tempo no portal do Azure. O exemplo a seguir mostra como exibir **Transações** no nível da conta.

![captura de tela de acesso a métricas no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para métricas com suporte a dimensões, você pode filtrar a métrica com o valor de dimensão desejado. O exemplo a seguir mostra como exibir **Transações** no nível da conta em uma operação específica, selecionando valores para a dimensão **nome da API** .

![captura de tela de acesso a métricas com dimensão na portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Acessar métricas com a API REST

Azure Monitor fornece [APIs REST](/rest/api/monitor/) para ler a definição e os valores da métrica. Esta seção mostra como ler as métricas de armazenamento. A ID do recurso é usada em todas as APIS REST. Para obter mais informações, Leia Noções básicas sobre ID de recurso para serviços no armazenamento.

O exemplo a seguir mostra como usar [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comando para simplificar o teste com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Listar a definição de métrica de nível de conta com a API REST

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Se você quiser listar as definições de métrica para BLOB, tabela, arquivo ou fila, deverá especificar IDs de recurso diferentes para cada serviço com a API.

A resposta contém a definição de métrica no formato JSON:

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Ler valores de métrica no nível da conta com a API REST

O exemplo a seguir mostra como ler dados de métrica no nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

No exemplo acima, se você quiser ler valores de métrica para BLOB, tabela, arquivo ou fila, deverá especificar IDs de recurso diferentes para cada serviço com a API.

A resposta a seguir contém valores de métrica no formato JSON:

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

### <a name="access-metrics-with-the-net-sdk"></a>Acessar métricas com o SDK do .NET

Azure Monitor fornece o [SDK do .net](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler a definição e os valores da métrica. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou versão posterior para métricas de armazenamento. A ID do recurso é usada no SDK do .NET. Para obter mais informações, Leia Noções básicas sobre ID de recurso para serviços no armazenamento.

O exemplo a seguir mostra como usar Azure Monitor SDK do .NET para ler as métricas de armazenamento.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listar a definição de métrica de nível de conta com o SDK do .NET

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

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

Se você quiser listar as definições de métrica para BLOB, tabela, arquivo ou fila, deverá especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Ler valores de métrica com o SDK do .NET

O exemplo a seguir mostra como ler dados de `UsedCapacity` no nível de conta:

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

No exemplo acima, se você quiser ler valores de métrica para BLOB, tabela, arquivo ou fila, deverá especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Ler valores de métrica multidimensionais com o SDK do .NET

Para métricas multidimensionais, você precisa definir o filtro de metadados se quiser ler dados de métricas em um valor de dimensão específico.

O exemplo a seguir mostra como ler dados de métrica na métrica com suporte para várias dimensões:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Noções básicas sobre ID de recurso para serviços no armazenamento do Azure

A ID do recurso é um identificador exclusivo de um recurso no Azure. Quando você usa o Azure Monitor API REST para ler definições ou valores de métricas, deve usar a ID de recurso para o recurso no qual você pretende operar. O modelo de ID de recurso segue este formato:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

O armazenamento fornece métricas no nível da conta de armazenamento e no nível de serviço com Azure Monitor. Por exemplo, você pode recuperar métricas apenas para o armazenamento de BLOBs. Cada nível tem sua própria ID de recurso, que é usada para recuperar as métricas apenas para esse nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recurso para uma conta de armazenamento

O seguinte mostra o formato para especificar a ID de recurso para uma conta de armazenamento.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID de recurso para os serviços de armazenamento

O seguinte mostra o formato para especificar a ID de recurso para cada um dos serviços de armazenamento.

* ID do recurso do serviço blob
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID do recurso do serviço tabela
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID do recurso de serviço Fila
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID do recurso do serviço de arquivo
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID do recurso na API REST do Azure Monitor

O seguinte mostra o padrão usado ao chamar a API REST do Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Métricas de capacidade
Os valores de métricas de capacidade são enviados a Azure Monitor a cada hora. Os valores são atualizados diariamente. O detalhamento define o intervalo de tempo para o qual os valores de métricas são apresentados. O intervalo de tempo com suporte para todas as métricas de capacidade é de uma hora (PT1H).

O armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

### <a name="account-level"></a>Nível da conta

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento usada pela conta de armazenamento. Para as contas de armazenamento standard, é a soma da capacidade utilizada pelos blobs, tabelas, ficheiros e filas. Para as contas de armazenamento premium e contas de armazenamento de blobs, é o mesmo que BlobCapacity. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blobs

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 <br/> Dimensions: **BlobType**e **BlobTier** ([definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos de blob armazenados na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 <br/> Dimensions: **BlobType**e **BlobTier** ([definição](#metrics-dimensions)) |
| ContainerCount    | O número de contêineres na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento usada pelo ADLS Gen2 índice hierárquico <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Armazenamento de tabela

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de tabela usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades de tabela na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de fila usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número de mensagens de fila não expiradas na conta de armazenamento. <br/><br/>Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de ficheiros

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Capacidade de | A quantidade de armazenamento de arquivos usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de arquivos na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de compartilhamentos de arquivos na conta de armazenamento. <br/><br/> Unidade: contagem <br/> Tipo de agregação: média <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são emitidas em cada solicitação para uma conta de armazenamento do armazenamento do Azure para Azure Monitor. No caso de nenhuma atividade em sua conta de armazenamento, não haverá dados em métricas de transação no período. Todas as métricas de transação estão disponíveis no nível de conta e de serviço (armazenamento de BLOBs, armazenamento de tabelas, arquivos do Azure e armazenamento de filas). O detalhamento define o intervalo de tempo que os valores de métrica são apresentados. Os refinamentos de tempo com suporte para todas as métricas de transação são PT1H e PT1M.

O armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e autenticação ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Authentication ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Authentication ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: milissegundos <br/> Tipo de agregação: média <br/> Dimensões aplicáveis: GeoType, ApiName e Authentication ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. <br/><br/> Unidade: milissegundos <br/> Tipo de agregação: média <br/> Dimensões aplicáveis: GeoType, ApiName e Authentication ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada ao dividir o valor do total dos pedidos faturáveis pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada. <br/><br/> Unidade: porcentagem <br/> Tipo de agregação: média <br/> Dimensões aplicáveis: GeoType, ApiName e Authentication ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99,99 |

## <a name="metrics-dimensions"></a>Dimensões de métricas

O armazenamento do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor.

| Nome da dimensão | Descrição |
| ------------------- | ----------------- |
| **BlobType** | O tipo de BLOB para métricas de blob somente. Os valores com suporte são **BlockBlob**, **PageBlob**e **Azure data Lake Storage**. O blob de acréscimo está incluído no BlockBlob. |
| **BlobTier** | O armazenamento do Azure oferece diferentes níveis de acesso, que permitem armazenar dados de objeto de BLOB da maneira mais econômica. Veja mais na [camada de blob do armazenamento do Azure](../blobs/storage-blob-storage-tiers.md). Os valores com suporte incluem: <br/> <li>**Quente**: camada quente</li> <li>**Legal**: camada fria</li> <li>**Arquivo morto**: camada de arquivamento</li> <li>**Premium**: camada Premium para blob de blocos</li> <li>**P4/P6/P10/P15/P20/p30/P40/P50/P60**: tipos de camada para o blob de páginas Premium</li> <li>**Standard**: tipo de camada para o blob de páginas padrão</li> <li>Não em **camadas**: tipo de camada para conta de armazenamento v1 de uso geral</li> |
| **GeoType** | Transação do cluster primário ou secundário. Os valores disponíveis incluem **primário** e **secundário**. Ele se aplica ao armazenamento com redundância geográfica de acesso de leitura (RA-GRS) ao ler objetos do locatário secundário. |
| **ResponseType** | Tipo de resposta da transação. Os valores disponíveis incluem: <br/><br/> <li>**ServerOtherError**: todos os outros erros do lado do servidor, exceto os descritos </li> <li>**ServerBusyError**: solicitação autenticada que retornou um código de status HTTP 503. </li> <li>**ServerTimeoutError**: solicitação autenticada com tempo limite que retornou um código de status http 500. O tempo limite excedido ocorreu devido a um erro de servidor. </li> <li>**AuthorizationError**: solicitação autenticada que falhou devido a acesso não autorizado de dados ou uma falha de autorização. </li> <li>**NetworkError**: solicitação autenticada que falhou devido a erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes da expiração do tempo limite. </li> <li>**ClientThrottlingError**: erro de limitação do lado do cliente. </li> <li>**ClientTimeoutError**: solicitação autenticada com tempo limite que retornou um código de status http 500. Se o tempo limite da rede do cliente ou do pedido estiver definido como um valor inferior ao esperado pelo serviço de armazenamento, trata-se de um tempo limite esperado. Caso contrário, é reportado como um ServerTimeoutError. </li> <li>**ClientOtherError**: todos os outros erros do lado do cliente, exceto os descritos. </li> <li>**Êxito**: solicitação bem-sucedida</li> <li> **SuccessWithThrottling**: solicitação bem-sucedida quando um cliente SMB é limitado na primeira tentativa (s), mas é bem-sucedido após novas tentativas.</li> |
| **ApiName** | O nome da operação. Por exemplo: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Para todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Autenticação** | Tipo de autenticação usado em transações. Os valores disponíveis incluem: <br/> <li>**AccountKey**: a transação é autenticada com a chave da conta de armazenamento.</li> <li>**SAS**: a transação é autenticada com assinaturas de acesso compartilhado.</li> <li>**OAuth**: a transação é autenticada com tokens de acesso OAuth.</li> <li>**Anônimo**: a transação é solicitada anonimamente. Ele não inclui solicitações de simulação.</li> <li>**AnonymousPreflight**: a transação é uma solicitação de simulação.</li> |

Para as métricas com suporte a dimensões, você precisa especificar o valor da dimensão para ver os valores correspondentes de métricas. Por exemplo, se você examinar o valor de **Transações** para respostas bem-sucedidas, será necessário filtrar a dimensão **ResponseType** com **êxito**. Ou, se você examinar o valor de **BlobCount** para blob de blocos, precisará filtrar a dimensão **BlobType** com **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade de serviço de métricas herdadas

As métricas herdadas estão disponíveis em paralelo com Azure Monitor métricas gerenciadas. O suporte mantém o mesmo até que o armazenamento do Azure encerre o serviço em métricas herdadas.

## <a name="faq"></a>FAQ

**As novas métricas dão suporte à conta de armazenamento clássico?**

Não, novas métricas no Azure Monitor dão suporte apenas a contas de armazenamento Azure Resource Manager. Se você quiser usar métricas em contas de armazenamento, precisará migrar para Azure Resource Manager conta de armazenamento. Consulte [migrar para Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**O armazenamento do Azure oferece suporte a métricas para discos Managed Disks ou não gerenciados?**

Não, a computação do Azure dá suporte às métricas em discos. Consulte o [artigo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) para obter mais detalhes.

**Como mapear e migrar métricas clássicas com novas métricas?**

Você pode encontrar mapeamento detalhado entre métricas clássicas e novas métricas na [migração de métricas de armazenamento do Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Passos seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
