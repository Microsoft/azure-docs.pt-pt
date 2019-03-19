---
title: Métricas de armazenamento do Azure no Azure Monitor | Documentos da Microsoft
description: Saiba mais sobre a nova métrica fornecida a partir do Azure Monitor.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: a5ebd50b3a5fe3b611bae28db98979eee40f9490
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899031"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Métricas do Armazenamento do Azure no Azure Monitor

Com a métrica no armazenamento do Azure, pode analisar tendências de utilização, pedidos de rastreio e diagnosticar problemas com a sua conta de armazenamento.

O Azure Monitor proporciona interfaces do usuário unificada para monitoramento em diferentes serviços do Azure. Para obter mais informações, consulte [do Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). O armazenamento do Azure integra-se do Azure Monitor através do envio de dados de métrica para a plataforma do Azure Monitor.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor proporciona várias formas de métricas de acesso. Pode acessá-los da [portal do Azure](https://portal.azure.com), o Azure Monitor APIs (REST e .NET) e soluções de análise de como os Hubs de eventos. Para obter mais informações, consulte [monitorizar as métricas do Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Métricas estão ativadas por predefinição, e pode acessar 93 nos últimos dias de dados. Se precisar de reter dados durante um período mais longo do tempo, pode arquivar dados de métricas para uma conta de armazenamento do Azure. Este é configurado no [das definições de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) no Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Métricas de acesso no portal do Azure

Pode monitorizar as métricas ao longo do tempo no portal do Azure. O exemplo seguinte mostra como visualizar **transações** ao nível da conta.

![captura de ecrã do acesso a métricas no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para suportar as dimensões de métricas, pode filtrar métrica com o valor da dimensão desejada. O exemplo seguinte mostra como visualizar **transações** ao nível da conta numa operação específica ao selecionar os valores para **nome da API** dimensão.

![captura de ecrã do acesso a métricas com dimensões no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Métricas de acesso com a API REST

O Azure Monitor proporciona [REST APIs](/rest/api/monitor/) para ler a definição de métrica e valores. Esta secção mostra como ler as métricas de armazenamento. ID de recurso é utilizado em todas as APIS REST. Para obter mais informações, leia a compreender o ID de recurso para os serviços de armazenamento.

O exemplo seguinte mostra como usar [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comandos para simplificar o teste com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Listar definição métricas ao nível da conta com a API REST

O exemplo seguinte mostra como listar a definição de métrica ao nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Se pretende listar as definições de métricas para BLOBs, tabelas, ficheiros ou filas, tem de especificar as IDs de recurso diferente para cada serviço com a API.

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

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Ler valores de métrica de nível de conta com a API REST

O exemplo seguinte mostra como ler dados de métricos ao nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Acima de exemplo, se quiser ler valores de métrica para BLOBs, tabelas, ficheiros ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

A seguinte resposta contém valores de métrica no formato JSON:

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

### <a name="access-metrics-with-the-net-sdk"></a>Métricas de acesso com o SDK de .NET

O Azure Monitor proporciona [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler a definição de métrica e valores. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com parâmetros diferentes. Tem de utilizar `0.18.0-preview` ou versão posterior para as métricas de armazenamento. ID de recurso é utilizado no SDK do .NET. Para obter mais informações, leia a compreender o ID de recurso para os serviços de armazenamento.

O exemplo seguinte mostra como utilizar o SDK .NET do Azure Monitor para ler métricas de armazenamento.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listar definição métricas ao nível da conta com o SDK de .NET

O exemplo seguinte mostra como listar a definição de métrica ao nível da conta:

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

Se pretende listar as definições de métricas para BLOBs, tabelas, ficheiros ou filas, tem de especificar as IDs de recurso diferente para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Valores de métrica de leitura com o SDK de .NET

O exemplo seguinte mostra como ler `UsedCapacity` dados ao nível da conta:

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

Acima de exemplo, se quiser ler valores de métrica para BLOBs, tabelas, ficheiros ou fila, tem de especificar as IDs de recurso diferente para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Ler valores de métricos multidimensionais com o SDK de .NET

Para métricas multidimensionais, terá de definir o filtro de dados de metadados para ler dados de métrica no valor de dimensão específicos.

O exemplo seguinte mostra como ler dados de métrica a métrica multidimensional de suporte:

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Noções básicas sobre o ID de recurso para os serviços no armazenamento do Azure

ID de recurso é um identificador exclusivo de um recurso no Azure. Quando utiliza a API de REST do Azure Monitor para ler definições das métricas ou valores, tem de utilizar o ID de recurso para o recurso no qual pretende operar. O modelo de ID de recurso segue este formato:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

O armazenamento fornece as métricas ao nível da conta de armazenamento e o nível de serviço com o Azure Monitor. Por exemplo, pode obter métricas para o armazenamento de BLOBs apenas. Cada nível tem seu próprio ID de recurso, o que é utilizada para obter as métricas para apenas esse nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recurso para uma conta de armazenamento

O código a seguir mostra o formato para especificar o ID de recurso para uma conta de armazenamento.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID de recurso para os serviços de armazenamento

O código a seguir mostra o formato para especificar o ID de recurso para cada um dos serviços de armazenamento.

* ID de recurso do serviço de BLOBs `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID de recurso do serviço de tabela `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID de recurso do serviço de fila `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID de recurso de serviço do ficheiro `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de recurso no Azure Monitor REST API

O código a seguir mostra o padrão utilizado quando chamar a API de REST do Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Métricas de capacidade
Valores de métricas de capacidade são enviadas para o Azure Monitor, a cada hora. Os valores são atualizados diariamente. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação suportado para todas as métricas de capacidade é uma hora (PT1H).

Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

### <a name="account-level"></a>Nível de conta

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento utilizado pela conta de armazenamento. Para contas de armazenamento standard, é a soma da capacidade utilizada pelo blob, tabela, ficheiro e fila. Para contas de armazenamento premium e contas de armazenamento de BLOBs, é o mesmo que BlobCapacity. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blobs

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de BLOBs utilizado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos de BLOBs armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([definição](#metrics-dimensions)) |
| ContainerCount    | O número de contentores na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| IndexCapacity     | A quantidade de armazenamento utilizado pelo índice hierárquica do ADLS geração 2 <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Table Storage

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de tabela utilizada a conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades da tabela na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de filas utilizada a conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número de mensagens em fila existe na conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de ficheiros

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de ficheiros utilizado a conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de ficheiros na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de ficheiro de partilha na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

Métricas de transação são emitidas em cada solicitação para uma conta de armazenamento do armazenamento do Azure para o Azure Monitor. No caso de nenhuma atividade na sua conta de armazenamento, haverá sem dados nas métricas de transação no período. Todas as métricas de transação estão disponíveis no nível de conta e de serviço (armazenamento de BLOBs, armazenamento de tabelas, ficheiros do Azure e o armazenamento de filas). O intervalo de agregação define o intervalo de tempo que são apresentados valores de métrica. O detalhamento de agregação suportado para todas as métricas de transação é PT1H e PT1M.

O armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Nome da Métrica | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicável: ResponseType, GeoType, ApiName e autenticação ([definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicável: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicável: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicável: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicável: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada Considerando o valor total de pedidos faturáveis e dividindo-o pelo número de pedidos aplicáveis, incluindo os pedidos que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada. <br/><br/> Unidade: Percentagem <br/> Tipo de agregação: Média <br/> Dimensões aplicável: GeoType, ApiName e autenticação ([definição](#metrics-dimensions)) <br/> Exemplo de valor: 99.99 |

## <a name="metrics-dimensions"></a>Dimensões de métricas

O armazenamento do Azure suporta seguintes dimensões de métricas no Azure Monitor.

| Nome da Dimensão | Descrição |
| ------------------- | ----------------- |
| BlobType | O tipo de blob para apenas as métricas de Blob. Os valores suportados são **BlockBlob** e **PageBlob**. Acrescentar Blob está incluído no BlockBlob. |
| ResponseType | Tipo de resposta de transação. Os valores disponíveis incluem: <br/><br/> <li>ServerOtherError: todos os outros erros do lado do servidor, exceto os descritos </li> <li> ServerBusyError: pedido autenticado que devolveu um código de estado HTTP 503. </li> <li> ServerTimeoutError: pedido autenticado com tempo limite excedido que devolveu um código de estado HTTP 500. O tempo limite excedido ocorreu devido a um erro de servidor. </li> <li> AuthorizationError: pedido autenticado que falhou devido a acesso não autorizado a dados ou a uma falha de autorização. </li> <li> NetworkError: pedido autenticado que falhou devido a erros de rede. Ocorre normalmente quando um cliente fecha prematuramente uma ligação antes da expiração do tempo limite. </li> <li>    ClientThrottlingError: erro de limitação do lado do cliente. </li> <li> ClientTimeoutError: pedido autenticado com tempo limite excedido que devolveu um código de estado HTTP 500. Se o tempo limite da rede do cliente ou do pedido estiver definido como um valor inferior ao esperado pelo serviço de armazenamento, trata-se de um tempo limite esperado. Caso contrário, é reportado como um ServerTimeoutError. </li> <li> ClientOtherError: todos os outros erros do lado do cliente, exceto os descritos. </li> <li> Êxito: pedido com êxito|
| GeoType | Transação do cluster primária ou secundária. Os valores disponíveis incluem primária e secundária. Aplica-se para Storage(RA-GRS) redundantes de Georreplicação de acesso de leitura ao ler objetos do inquilino secundário. |
| ApiName | O nome da operação. Por exemplo: <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Para todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| Authentication | Tipo de autenticação utilizado em transações. Os valores disponíveis incluem: <br/> <li>AccountKey: A transação é autenticada com a chave de conta de armazenamento.</li> <li>SAS: A transação é autenticada com assinaturas de acesso partilhado.</li> <li>OAuth: A transação é autenticada com tokens de acesso de OAuth.</li> <li>Anónimo: A transação é solicitada anonimamente. Ele não inclui pedidos de simulação.</li> <li>AnonymousPreflight: A transação é a solicitação de simulação.</li> |

Para as dimensões de suporte de métricas, terá de especificar o valor de dimensão para ver os valores de métricas correspondentes. Por exemplo, se olhar **transações** valor para respostas com êxito, tem de filtrar a **ResponseType** dimensão com **êxito**. Ou se olhar **BlobCount** valor para o Blob de bloco, precisa filtrar os **BlobType** dimensão com **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade do serviço de métricas de legado

Métricas de legado estão disponíveis em paralelo com métricas do Azure Monitor geridos. O suporte mantém o mesmo até que o armazenamento do Azure termina o serviço em métricas de legado.

## <a name="faq"></a>FAQ

**Novas métricas suporta contas de armazenamento clássicas?**

Não, novas métricas nas contas de armazenamento para o Azure Monitor apenas suporte do Azure Resource Manager. Se pretender utilizar as métricas em contas de armazenamento, terá de migrar para a conta de armazenamento do Azure Resource Manager. Ver [migrar para o Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**O armazenamento do Azure suporta métricas para Managed Disks ou discos não geridos?**

Não, computação do Azure suporta as métricas em discos. Ver [artigo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/) para obter mais detalhes.

**Como mapear e migrar métricas clássicas com a nova métrica?**

Pode encontrar detalhado mapeamento entre métricas clássicas e métricas de novo no [migração de métricas de armazenamento do Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Passos Seguintes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
