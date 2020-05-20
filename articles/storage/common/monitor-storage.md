---
title: Monitor Armazém Azure [ Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade do Armazenamento Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring
ms.openlocfilehash: c8125001b5960a0bf770e8e015ad757a277629ea
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684897"
---
# <a name="monitor-azure-storage"></a>Armazenamento Do Monitor Azure

Quando se tem aplicações críticas e processos de negócio que dependem dos recursos do Azure, pretende monitorizar esses recursos para a sua disponibilidade, desempenho e operação. Este artigo descreve os dados de monitorização gerados pelo Armazenamento Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar alertas sobre estes dados.

> [!NOTE]
> Os registos de armazenamento azure no Monitor Azure estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões públicas de nuvem. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos de bolhas (que inclui O Armazenamento de Lagos Azure Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

## <a name="monitor-overview"></a>Visão geral do monitor

A página **de visão geral** do portal Azure para cada recurso de armazenamento inclui uma breve visão do uso de recursos, tais como pedidos e faturação horária. Esta informação é útil, mas apenas uma pequena quantidade dos dados de monitorização está disponível. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso de armazenamento. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Storage cria dados de monitorização utilizando o [Azure Monitor,](../../azure-monitor/overview.md)que é um serviço de monitorização de pilhas completa seletiva em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos e recursos Azure em outras nuvens e no local. 

Para saber mais sobre o Monitor Azure, consulte [a Monitorização dos recursos do Azure com o Monitor Azure.](../../azure-monitor/insights/monitor-azure-resource.md) O artigo descreve os seguintes assuntos:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Configurar a recolha de dados
- Ferramentas padrão em Azure para analisar e alertar sobre dados de monitorização

As seguintes secções baseiam-se neste artigo descrevendo os dados específicos recolhidos do Armazenamento Azure. Exemplos mostram como configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitor-data-from-azure-storage"></a>Monitorize dados do Armazenamento Azure

O Azure Storage recolhe os mesmos tipos de dados de monitorização que outros recursos do Azure, que são descritos na [Monitorização de dados a partir de recursos Azure.](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) Para obter mais informações sobre os registos e métricas criados pelo Armazenamento Azure, consulte a referência de dados de monitorização do [Armazenamento Azure](monitor-storage-reference.md).

Métricas e registos no Suporte do Monitor Azure apenas contas de armazenamento do Gestor de Recursos Azure. O Azure Monitor não suporta contas de armazenamento clássicas. Se quiser utilizar métricas ou registos numa conta de armazenamento clássica, precisa de migrar para uma conta de armazenamento do Gestor de Recursos Azure. Consulte [migrate para Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

Pode continuar a usar métricas e registos clássicos, se quiser. Na verdade, as métricas e registos clássicos estão disponíveis em paralelo com métricas e registos no Monitor Azure. O suporte permanece em vigor até que o Armazenamento Azure termine o serviço em métricas e registos legados.

### <a name="logs-in-azure-monitor-preview"></a>Registos no Monitor Azure (pré-visualização)

As entradas de registo só são criadas se houver pedidos contra o ponto final do serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final de bolha, mas não na sua tabela ou pontos finais de fila, apenas são criados registos que dizem respeito ao serviço de blob. Os registos de armazenamento azure contêm informações detalhadas sobre pedidos bem sucedidos e falhados a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados com o melhor esforço.

#### <a name="log-authenticated-requests"></a>Registar pedidos autenticados

 São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizem uma assinatura de acesso partilhado (SAS) ou OAuth, incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise (dados clássicos de registo no contentor **$logs** e dados métricos de classe nas tabelas **$metric)**

Os pedidos feitos pelo próprio serviço de armazenamento, tais como criação de registo ou eliminação, não são registados. Para obter uma lista completa dos dados registados, consulte [as operações de armazenamento registadas e as mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) de estado e o [formato de registo de armazenamento](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Registar pedidos anónimos

 Registam-se os seguintes tipos de pedidos anónimos:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo para cliente e servidor
- Pedidos DE GET falhados com o código de erro 304 (não modificado)

Todos os outros pedidos anónimos falhados não estão registados. Para obter uma lista completa dos dados registados, consulte [as operações de armazenamento registadas e as mensagens](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) de estado e o [formato de registo de armazenamento](monitor-storage-reference.md).

## <a name="configuration"></a>Configuração

As métricas da plataforma e o registo de atividade são recolhidos automaticamente, mas é necessário criar uma definição de diagnóstico para recolher registos de recursos ou encaminhar para fora do Monitor Azure. Para que o processo crie uma definição de diagnóstico utilizando o portal Azure, o Azure CLI, ou PowerShell, consulte [Criar definição de diagnóstico para recolher registos e métricas de plataforma no Azure](../../azure-monitor/platform/diagnostic-settings.md).

Quando criar uma definição de diagnóstico, escolha o tipo de armazenamento para o qual pretende ativar registos, como uma bolha, fila, mesa ou ficheiro. Se criar a definição de diagnóstico no portal Azure, pode selecionar o recurso a partir de uma lista. Se utilizar o PowerShell ou o Azure CLI, tem de utilizar o ID de recurso do tipo de armazenamento. Pode encontrar o ID de recurso no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Também tem de especificar as categorias de operações para as quais pretende recolher registos. As categorias de Armazenamento Azure estão listadas nesta tabela.

| Categoria | Descrição |
|:---|:---|
| Leitura de armazenamento | Leia as operações em blobs. |
| StorageWrite | Escreva operações sobre bolhas. |
| ArmazenamentoDelete | Apagar operações em bolhas. |

## <a name="analyze-metric-data"></a>Analisar dados métricos

Pode analisar métricas para o Armazenamento Azure com métricas de outros serviços Azure utilizando o Metrics Explorer. Open Metrics Explorer escolhendo **Métricas** do menu **Azure Monitor.** Para mais detalhes sobre a utilização desta ferramenta, consulte [Iniciar-se com o Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Este exemplo mostra como ver **as Transações** ao nível da conta.

![Screenshot de métricas de acesso no portal Azure](./media/monitor-storage/access-metrics-portal.png)

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão desejado. Este exemplo mostra como visualizar **as Transações** ao nível da conta numa operação específica, selecionando valores para a dimensão **nome API.**

![Screenshot de aceder a métricas com dimensão no portal Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Para obter uma lista completa das dimensões que o Azure Storage suporta, consulte as dimensões das [Métricas.](monitor-storage-reference.md#metrics-dimensions)

Todas as métricas para o Armazenamento Azure estão localizadas nestes espaços de nome:

- Microsoft.Storage/storageAccounts
- Microsoft.Armazenamento/armazenamentoContas/blobServices
- Microsoft.Armazenamento/armazenamentoContas/ficheirosServiços
- Microsoft.Armazenamento/armazenamentoContas/filaServiços
- Microsoft.Armazenamento/armazenamentoContas/tableServices

Para obter uma lista de todas as métricas de suporte do Monitor Azure, que inclui o Armazenamento Azure, consulte [as métricas suportadas pelo Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)


### <a name="access-metrics"></a>Métricas de acesso

> [!TIP]
> Para ver os exemplos Azure CLI ou .NET, escolha os separadores correspondentes listados aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Enumerar a definição métrica

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, file, table ou queue. Utilize o cmdlet [Get-AzMetricDefinition.](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0)

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou o id de recursos de um serviço de armazenamento individual, como o serviço de blob, file, table ou fila. Pode encontrar estas IDs de recurso nas páginas **propriedades** da sua conta de armazenamento no portal Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Ler valores métricos

Pode ler valores métricos ao nível da conta da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, file, mesa ou fila. Utilize o cmdlet [Get-AzMetric.](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Enumerar a definição métrica do nível da conta

Pode listar a definição métrica da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, file, table ou queue. Utilize o comando de [definições de listas de métricas az monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions)
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou o id de recursos de um serviço de armazenamento individual, como o serviço de blob, file, table ou fila. Pode encontrar estas IDs de recurso nas páginas **propriedades** da sua conta de armazenamento no portal Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Ler valores métricos ao nível da conta

Pode ler os valores métricos da sua conta de armazenamento ou do serviço de armazenamento individual, como o serviço de blob, file, table ou queue. Use o comando da lista de [métricas az monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

O Azure Monitor fornece o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler a definição métrica e os valores. O [código da amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Precisa de usar ou uma versão posterior para métricas de `0.18.0-preview` armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pelo id de recursos de toda a conta de armazenamento ou o dispositivo de identificação de recursos de um serviço de armazenamento individual, como o serviço de blob, file, table ou queue. Pode encontrar estas IDs de recurso nas páginas **propriedades** da sua conta de armazenamento no portal Azure.

Substitua a `<subscription-ID>` variável pela identificação da sua subscrição. Para obter orientações sobre como obter valores `<tenant-ID>` `<application-ID>` para, `<AccessKey>` e, ver Utilizar o portal para criar uma aplicação e um diretor de serviço seletiva seletiva que possam aceder a [recursos.](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/) 

#### <a name="list-the-account-level-metric-definition"></a>Enumerar a definição métrica do nível da conta

O exemplo que se segue mostra como enumerar uma definição métrica ao nível da conta:

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

#### <a name="read-account-level-metric-values"></a>Ler valores métricos ao nível da conta

O exemplo que se segue mostra como ler `UsedCapacity` dados ao nível da conta:

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

#### <a name="read-multidimensional-metric-values"></a>Ler valores métricos multidimensionais

Para métricas multidimensionais, é necessário definir filtros de metadados se quiser ler dados métricos sobre valores de dimensão específicos.

O exemplo que se segue mostra como ler dados métricos sobre a multidimensionação de suporte métrico:

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

## <a name="analyze-log-data"></a>Analisar dados de registo

Pode aceder a registos de recursos quer como uma bolha numa conta de armazenamento, como dados de eventos, quer através de consultas de Log Analytic.

Para obter uma referência detalhada dos campos que aparecem nestes registos, consulte a referência de dados de monitorização do [Armazenamento Azure](monitor-storage-reference.md).

> [!NOTE]
> Os registos de armazenamento azure no Monitor Azure estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões públicas de nuvem. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos de bolhas (que inclui O Armazenamento de Lagos Azure Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

### <a name="access-logs-in-a-storage-account"></a>Acesso a registos numa conta de armazenamento

Os registos aparecem como bolhas armazenadas num contentor na conta de armazenamento do alvo. Os dados são recolhidos e armazenados dentro de uma única bolha como uma carga útil JSON delimitada por linha. O nome da bolha segue esta convenção de nomeação:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Eis um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Acesso a registos em um centro de eventos

Os registos enviados para um centro de eventos não são armazenados como um ficheiro, mas pode verificar se o centro do evento recebeu as informações de registo. No portal Azure, vá ao seu centro de eventos e verifique se a contagem de **mensagens de entrada** é maior que zero. 

![Registos de auditoria](media/monitor-storage/event-hub-log.png)

Pode aceder e ler dados de registo enviados para o seu centro de eventos utilizando informações de segurança e ferramentas de gestão e monitorização de eventos. Para mais informações, veja o que posso fazer com os dados de [monitorização enviados para o meu centro de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="access-logs-in-a-log-analytics-workspace"></a>Acesso a registos num espaço de trabalho de Log Analytics

Pode aceder aos registos enviados para um espaço de trabalho do Log Analytics utilizando consultas de registo do Monitor Azure.

Para mais informações, consulte [Iniciar com Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Os dados são armazenados nestas tabelas.

| Tabela | Descrição |
|:---|:---|
|StorageBlobLogs | Registos que descrevem a atividade no armazenamento de bolhas. |
|FileLogs de Armazenamento | Registos que descrevem a atividade em ações de ficheiros. |
|Logs de Fila de Armazenamento | Registos que descrevem a atividade nas filas.|
|StorageTableLogs| Registos que descrevem a atividade nas tabelas.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Consultas de log de armazenamento azure no Monitor Azure

Aqui estão algumas consultas que pode introduzir na barra de pesquisa de **Registo** para ajudá-lo a monitorizar as suas contas de Armazenamento Azure. Estas consultas funcionam com a [nova linguagem.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

Utilize estas consultas para o ajudar a monitorizar as suas contas de Armazenamento Azure:

* Para enumerar os 10 erros mais comuns nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Para enumerar as 10 operações que causaram mais erros nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Para listar as 10 operações com a maior latência final nos últimos três dias.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Para enumerar todas as operações que causaram erros de estrangulamento do lado do servidor nos últimos três dias.

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
* Para criar um gráfico de operações usadas nos últimos três dias.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>FAQ

**O Armazenamento Azure suporta métricas para discos geridos ou discos não geridos?**

Não. A Azure Compute suporta as métricas nos discos. Para obter mais informações, consulte [as métricas de disco per para discos geridos e não geridos](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Passos seguintes

- Para uma referência dos registos e métricas criados pelo Armazenamento Azure, consulte a referência de dados de monitorização do [Armazenamento Azure](monitor-storage-reference.md).
- Para mais informações sobre a monitorização dos recursos do Azure, consulte os recursos do Monitor Azure com o [Monitor Azure.](../../azure-monitor/insights/monitor-azure-resource.md)
- Para obter mais informações sobre a migração de métricas, consulte a migração de [métricas de Armazenamento Azure.](./storage-metrics-migration.md)

