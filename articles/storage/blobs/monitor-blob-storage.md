---
title: Monitorização do armazenamento do Blob Azure / Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade do armazenamento Azure Blob. Monitorize os dados de armazenamento do Azure Blob, aprenda sobre a configuração e analise dados métricos e de registo.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: b5eb37ff7063662341cf3b92176c26839df4b21d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711576"
---
# <a name="monitoring-azure-blob-storage"></a>Monitorização do armazenamento do Azure Blob

Quando você tem aplicações críticas e processos de negócio que dependem de recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelo armazenamento Azure Blob e como pode utilizar as funcionalidades do Azure Monitor para analisar alertas sobre estes dados.

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Esta funcionalidade encontra-se disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Azure Resource Manager. Consulte [a visão geral da conta de armazenamento](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Visão geral do monitor

A página **geral** no portal Azure para cada recurso de armazenamento Blob inclui uma breve visão da utilização do recurso, tais como pedidos e faturação horária. Esta informação é útil, mas apenas uma pequena quantidade dos dados de monitorização está disponível. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O armazenamento Azure Blob cria dados de monitorização utilizando [o Azure Monitor,](../../azure-monitor/overview.md)que é um serviço de monitorização de pilhas completa em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos e recursos Azure em outras nuvens e no local. 

Comece com o artigo [Monitorar os recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As seguintes secções baseiam-se neste artigo descrevendo os dados específicos recolhidos a partir do Azure Storage. Exemplos mostram como configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitoring-data"></a>Monitorizar dados

O armazenamento Azure Blob recolhe os mesmos tipos de dados de monitorização que outros recursos Azure, que são descritos em [dados de monitorização a partir de recursos Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Consulte [a referência de dados de monitorização de armazenamento Azure Blob](monitor-blob-storage-reference.md) para obter informações detalhadas sobre as métricas de métricas e registos criadas pelo armazenamento Azure Blob.

Métricas e registos no Azure Monitor suportam apenas contas de armazenamento do Gestor de Recursos Azure. O Azure Monitor não suporta contas clássicas de armazenamento. Se quiser utilizar métricas ou registos numa conta de armazenamento clássica, tem de migrar para uma conta de armazenamento do Azure Resource Manager. Consulte [a Migração para o Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Pode continuar a usar métricas e registos clássicos, se quiser. Na verdade, métricas e troncos clássicos estão disponíveis em paralelo com métricas e troncos no Azure Monitor. O suporte permanece em vigor até que o Azure Storage termine o serviço em métricas e registos antigos.

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de Atividade são recolhidos automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico. Tem de criar uma definição de diagnóstico para recolher registos de recursos. 

Para criar uma definição de diagnóstico utilizando o portal Azure, o Azure CLI ou PowerShell, consulte [criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Para ver um modelo do Gestor de Recursos Azure que cria uma definição de diagnóstico, consulte [a definição de Diagnóstico para armazenamento Azure](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Quando criar uma definição de diagnóstico, escolha o tipo de armazenamento para o qual pretende ativar os registos, tais como uma bolha, fila, mesa ou ficheiro. Para o armazenamento blob, escolha **blob**. Data Lake Storage Gen2 não aparece como um tipo de armazenamento. Isto porque o Data Lake Storage Gen2 é um conjunto de capacidades disponíveis para o armazenamento blob. 

Se criar a definição de diagnóstico no portal Azure, pode selecionar o recurso a partir de uma lista. Se utilizar o PowerShell ou o Azure CLI, tem de utilizar o ID de recursos do ponto final de armazenamento Blob. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Também tem de especificar uma das seguintes categorias de operações para as quais pretende recolher registos. 

| Categoria | Descrição |
|:---|:---|
| ArmazenamentoRead | Leia as operações em objetos. |
| ArmazenamentoPorite | Escreva operações em objetos. |
| StorageDelete | Eliminar operações em objetos. |

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para armazenamento Azure com métricas de outros serviços Azure utilizando o Metrics Explorer. Open Metrics Explorer escolhendo **métricas** do menu **Azure Monitor.** Para obter detalhes sobre a utilização desta ferramenta, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Este exemplo mostra como ver **transações** ao nível da conta.

![Screenshot de acesso às métricas no portal Azure](./media/monitor-blob-storage/access-metrics-portal.png)

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão pretendido. Este exemplo mostra como visualizar **as Transações** ao nível da conta numa operação específica, selecionando valores para a dimensão **nome da API.**

![Screenshot de acesso a métricas com dimensão no portal Azure](./media/monitor-blob-storage/access-metrics-portal-with-dimension.png)

Para obter uma lista completa das dimensões que o Azure Storage suporta, consulte [as dimensões das Métricas.](monitor-blob-storage-reference.md#metrics-dimensions)

As métricas para o armazenamento do Azure Blob estão nestes espaços de nome: 

- Microsoft.Storage/storageAcontas
- Microsoft.Storage/storageAcontas/blobServices

Para obter uma lista de todas as métricas de suporte do Azure Monitor, que inclui o armazenamento do Azure Blob, consulte [as métricas suportadas pelo Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)


### <a name="accessing-metrics"></a>Métricas de acesso

> [!TIP]
> Para ver exemplos de Azure CLI ou .NET, escolha os separadores correspondentes listados aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição métrica

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento Blob. Utilize o [cmdlet Get-AzMetricDefinition.](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition)

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço de armazenamento Blob.  Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Valores métricos de leitura

Pode ler os valores métricos ao nível da conta da sua conta de armazenamento ou o serviço de armazenamento Blob. Utilize o [cmdlet Get-AzMetric.](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento Blob. Utilize o comando [de definições de listas de métricas az monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions)
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço de armazenamento Blob. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Leia os valores métricos ao nível da conta

Pode ler os valores métricos da sua conta de armazenamento ou do serviço de armazenamento Blob. Utilize o comando [da lista de métricas do monitor az.](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

O Azure Monitor fornece o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler definições e valores métricos. O [código de amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou do serviço de armazenamento Blob. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

Substitua a `<subscription-ID>` variável pelo ID da sua subscrição. Para obter orientações sobre como obter valores `<tenant-ID>` `<application-ID>` para, `<AccessKey>` e, ver [Usar o portal para criar uma aplicação AD AZure e um principal de serviço que possa aceder a recursos.](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/) 

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

O exemplo a seguir mostra como listar uma definição métrica ao nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="reading-account-level-metric-values"></a>Leitura de valores métricos ao nível da conta

O exemplo a seguir mostra como ler `UsedCapacity` dados ao nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="reading-multidimensional-metric-values"></a>Leitura de valores métricos multidimensionais

Para métricas multidimensionais, é necessário definir filtros de metadados se quiser ler dados métricos sobre valores de dimensão específicas.

O exemplo a seguir mostra como ler dados métricos sobre a multidimensional de suporte métrico:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyzing-logs"></a>Análise de registos

Pode aceder a registos de recursos como uma bolha numa conta de armazenamento, como dados de eventos, ou através de consultas de Log Analytic.

Para obter uma referência detalhada dos campos que aparecem nestes registos, consulte [a referência de dados de monitorização do armazenamento Azure Blob](monitor-blob-storage-reference.md).

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos para blobs (que inclui Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

As entradas de registo só são criadas se houver pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final blob, mas não nos seus pontos finais de mesa ou fila, apenas são criados registos relativos ao serviço blob. Os registos de armazenamento Azure contêm informações detalhadas sobre pedidos bem sucedidos e falhados para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registar pedidos autenticados

 São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizem uma assinatura de acesso partilhado (SAS) ou OAuth, incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise (dados clássicos **de** registo no $logs dados métricos do recipiente e da classe nas tabelas **$metric)**

Os pedidos feitos pelo próprio serviço de armazenamento Blob, como criação de registos ou eliminação, não são registados. Para obter uma lista completa dos dados registados, consulte [as operações registadas do Armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e o formato de registo de [armazenamento.](monitor-blob-storage-reference.md)

### <a name="log-anonymous-requests"></a>Registar pedidos anónimos

 Os seguintes tipos de pedidos anónimos são registados:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo para o cliente e para o servidor
- Pedidos GET falhados com o código de erro 304 (Não Modificado)

Todos os outros pedidos anónimos falhados não estão registados. Para obter uma lista completa dos dados registados, consulte [as operações registadas do Armazenamento e as mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e o formato de registo de [armazenamento.](monitor-blob-storage-reference.md)

### <a name="accessing-logs-in-a-storage-account"></a>Aceder a registos numa conta de armazenamento

Os registos aparecem como bolhas armazenadas num contentor na conta de armazenamento do alvo. Os dados são recolhidos e armazenados dentro de uma única bolha como uma carga de carga JSON delimitada pela linha. O nome da bolha segue esta convenção de nomeação:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Eis um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Aceder a troncos num centro de eventos

Os registos enviados para um centro de eventos não são armazenados como um ficheiro, mas pode verificar se o centro de eventos recebeu a informação de registo. No portal Azure, vá ao seu centro de eventos e verifique se a contagem **de mensagens recebidas** é superior a zero. 

![Registos de auditoria](media/monitor-blob-storage/event-hub-log.png)

Pode aceder e ler dados de registo que são enviados para o seu centro de eventos utilizando informações de segurança e ferramentas de gestão e monitorização de eventos. Para mais informações, veja [o que posso fazer com os dados de monitorização que estão a ser enviados para o meu centro de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Aceder a registos num espaço de trabalho do Log Analytics

Pode aceder aos registos enviados para um espaço de trabalho do Log Analytics utilizando consultas de registo do Azure Monitor.

Para obter mais informações, consulte [Começar com o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Os dados são armazenados na tabela **StorageBlobLog.** Os registos para data lake storage Gen2 não aparecem numa tabela dedicada. Isso é porque o Data Lake Storage Gen2 não é serviço. É um conjunto de capacidades que pode ativar na sua conta de armazenamento. Se tiver ativado essas capacidades, os registos continuarão a aparecer na tabela StorageBlobLogs. 

#### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Aqui ficam algumas consultas que pode introduzir na barra **de pesquisa log** para ajudá-lo a monitorizar o seu armazenamento Blob. Estas consultas funcionam com a [nova linguagem.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

> [!IMPORTANT]
> Quando seleciona **Logs** do menu do grupo de recursos de conta de armazenamento, o Log Analytics é aberto com o âmbito de consulta definido para o grupo de recursos atual. Isto significa que as consultas de registo só incluirão dados desse grupo de recursos. Se pretender executar uma consulta que inclua dados de outros recursos ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter mais detalhes.

Utilize estas consultas para ajudá-lo a monitorizar as suas contas de Armazenamento Azure:

* Para enumerar os 10 erros mais comuns nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Para listar as 10 operações que causaram mais erros nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Para listar as 10 melhores operações com a maior latência de ponta a ponta nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Para listar todas as operações que causaram erros de estrangulamento do lado do servidor nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Para listar todos os pedidos com acesso anónimo nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Para criar um gráfico de operações usado nos últimos três dias.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>FAQ

**O Azure Storage suporta métricas para Discos Geridos ou Discos Não Geridos?**

N.º O Azure Compute suporta as métricas nos discos. Para obter mais informações, consulte [as métricas de discos por discos geridos e não geridos.](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma referência dos registos e métricas criados pelo armazenamento Azure Blob, consulte [a referência de dados de monitorização do armazenamento Azure Blob](monitor-blob-storage-reference.md).
- Para obter informações sobre a monitorização dos recursos do Azure, consulte [os recursos do Monitor Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Para obter mais informações sobre a migração de métricas, consulte [a migração das métricas de Armazenamento Azure.](../common/storage-metrics-migration.md)
