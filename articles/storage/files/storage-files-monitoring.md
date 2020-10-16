---
title: Monitorar ficheiros Azure / Microsoft Docs
description: Saiba como monitorizar o desempenho e disponibilidade dos Ficheiros Azure. Monitorize os dados do Azure Files, aprenda sobre a configuração e analise dados métricos e de registo.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91772673"
---
# <a name="monitoring-azure-files"></a>Monitorar ficheiros Azure

Quando você tem aplicações críticas e processos de negócio que dependem de recursos Azure, você quer monitorizar esses recursos para sua disponibilidade, desempenho e funcionamento. Este artigo descreve os dados de monitorização gerados pelos Ficheiros Azure e como pode utilizar as funcionalidades do Azure Monitor para analisar alertas sobre estes dados.

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos para bolhas (que inclui Azure Data Lake Storage Gen2), ficheiros, filas e tabelas. Esta funcionalidade encontra-se disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Azure Resource Manager. Consulte [a visão geral da conta de armazenamento](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Visão geral do monitor

A página **'Visão Geral'** no portal Azure para cada recurso de Ficheiros Azure inclui uma breve visão da utilização do recurso, como pedidos e faturação horária. Esta informação é útil, mas apenas uma pequena quantidade dos dados de monitorização está disponível. Alguns destes dados são recolhidos automaticamente e estão disponíveis para análise assim que criar o recurso. Pode ativar tipos adicionais de recolha de dados com alguma configuração.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
O Azure Files cria dados de monitorização utilizando [o Azure Monitor,](../../azure-monitor/overview.md)que é um serviço de monitorização de pilhas completa em Azure. O Azure Monitor fornece um conjunto completo de funcionalidades para monitorizar os seus recursos e recursos Azure em outras nuvens e no local. 

Comece com o artigo [Monitorar os recursos do Azure com o Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)que descreve o seguinte:

- O que é o Azure Monitor?
- Custos associados à monitorização
- Dados de monitorização recolhidos em Azure
- Recolha de dados configurado
- Ferramentas padrão em Azure para analisar e alertar sobre os dados de monitorização

As secções seguintes baseiam-se neste artigo descrevendo os dados específicos recolhidos a partir de Ficheiros Azure. Exemplos mostram como configurar a recolha de dados e analisar estes dados com ferramentas Azure.

## <a name="monitoring-data"></a>Monitorizar dados

A Azure Files recolhe os mesmos tipos de dados de monitorização que outros recursos Azure, que são descritos na [monitorização de dados a partir de recursos Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Consulte [a referência de dados de monitorização do Ficheiro Azure](storage-files-monitoring-reference.md) para obter informações detalhadas sobre as métricas de métricas e registos criadas pelos Ficheiros Azure.

Métricas e registos no Azure Monitor suportam apenas contas de armazenamento do Gestor de Recursos Azure. O Azure Monitor não suporta contas clássicas de armazenamento. Se quiser utilizar métricas ou registos numa conta de armazenamento clássica, tem de migrar para uma conta de armazenamento do Azure Resource Manager. Consulte [a Migração para o Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Recolha e encaminhamento

As métricas da plataforma e o registo de atividade são recolhidos automaticamente, mas podem ser encaminhados para outros locais utilizando uma definição de diagnóstico. Tem de criar uma definição de diagnóstico para recolher registos de recursos. 

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos para blobs (que inclui Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

Para criar uma definição de diagnóstico utilizando o portal Azure, o Azure CLI ou PowerShell, consulte [criar definição de diagnóstico para recolher registos e métricas da plataforma em Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Para ver um modelo do Gestor de Recursos Azure que cria uma definição de diagnóstico, consulte [a definição de Diagnóstico para armazenamento Azure](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

Quando criar uma definição de diagnóstico, escolha o tipo de armazenamento para o qual pretende ativar os registos, tais como uma bolha, fila, mesa ou ficheiro. Para ficheiros Azure, escolha **Ficheiro**. 

Se criar a definição de diagnóstico no portal Azure, pode selecionar o recurso a partir de uma lista. Se utilizar o PowerShell ou o Azure CLI, tem de utilizar o ID de recursos do ponto final dos Ficheiros Azure. Pode encontrar o ID de recursos no portal Azure abrindo a página **Propriedades** da sua conta de armazenamento.

Também tem de especificar uma das seguintes categorias de operações para as quais pretende recolher registos. 

| Categoria | Descrição |
|:---|:---|
| ArmazenamentoRead | Leia as operações em objetos. |
| ArmazenamentoPorite | Escreva operações em objetos. |
| StorageDelete | Eliminar operações em objetos. |

Para obter a lista de operações SMB e REST que estão registadas, consulte [as operações de armazenamento registadas e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

## <a name="analyzing-metrics"></a>Análise de métricas

Pode analisar métricas para armazenamento Azure com métricas de outros serviços Azure utilizando o Metrics Explorer. Open Metrics Explorer escolhendo **métricas** do menu **Azure Monitor.** Para obter detalhes sobre a utilização desta ferramenta, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md). 

Para métricas que suportam dimensões, pode filtrar a métrica com o valor de dimensão pretendido.  Para obter uma lista completa das dimensões que o Azure Storage suporta, consulte [as dimensões das Métricas.](storage-files-monitoring-reference.md#metrics-dimensions) As métricas para ficheiros Azure estão nestes espaços de nome: 

- Microsoft.Storage/storageAcontas
- Microsoft.Storage/storageAcontas/fileServices

Para obter uma lista de todas as métricas de suporte do Azure Monitor, que inclui ficheiros Azure, consulte [as métricas suportadas pelo Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Métricas de acesso

> [!TIP]
> Para ver exemplos de Azure CLI ou .NET, escolha os separadores correspondentes listados aqui.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Listar a definição métrica

Pode listar a definição métrica da sua conta de armazenamento ou do serviço Azure Files. Utilize o [cmdlet Get-AzMetricDefinition.](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition)

Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço Azure Files.  Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Valores métricos de leitura

Pode ler os valores métricos ao nível da conta da sua conta de armazenamento ou o serviço Azure Files. Utilize o [cmdlet Get-AzMetric.](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric)

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Listar a definição métrica de nível de conta

Pode listar a definição métrica da sua conta de armazenamento ou do serviço Azure Files. Utilize o comando [de definições de listas de métricas az monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions)
 
Neste exemplo, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo ID de recursos do serviço Azure Files. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Leia os valores métricos ao nível da conta

Pode ler os valores métricos da sua conta de armazenamento ou do serviço Azure Files. Utilize o comando [da lista de métricas do monitor az.](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list)

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

O Azure Monitor fornece o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler definições e valores métricos. O [código de amostra](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como utilizar o SDK com diferentes parâmetros. Você precisa usar `0.18.0-preview` ou uma versão posterior para métricas de armazenamento.
 
Nestes exemplos, substitua o `<resource-ID>` espaço reservado pelo ID de recursos de toda a conta de armazenamento ou pelo serviço Azure Files. Pode encontrar estes IDs de recursos nas páginas **Propriedades** da sua conta de armazenamento no portal Azure.

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
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

Para obter a lista de operações SMB e REST que estão registadas, consulte [as operações de armazenamento registadas e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

> [!NOTE]
> Os registos de armazenamento Azure no Azure Monitor estão em pré-visualização pública e estão disponíveis para testes de pré-visualização em todas as regiões de nuvem pública. Para se inscrever na pré-visualização, consulte [esta página](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Esta pré-visualização permite registos para blobs (que inclui Azure Data Lake Storage Gen2), ficheiros, filas, tabelas, contas de armazenamento premium em v1 de uso geral e contas de armazenamento v2 de uso geral. As contas clássicas de armazenamento não são suportadas.

As entradas de registo só são criadas se houver pedidos feitos contra o ponto final de serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final de ficheiro, mas não nos seus pontos finais de tabela ou fila, apenas são criados registos relativos ao serviço de ficheiros. Os registos de armazenamento Azure contêm informações detalhadas sobre pedidos bem sucedidos e falhados para um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados numa base de melhor esforço.

### <a name="log-authenticated-requests"></a>Registar pedidos autenticados

 São registados os seguintes tipos de pedidos autenticados:

- Pedidos com êxito
- Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros
- Pedidos que utilizem uma assinatura de acesso partilhado (SAS) ou OAuth, incluindo pedidos falhados e bem sucedidos
- Pedidos de dados de análise (dados clássicos **de** registo no $logs dados métricos do recipiente e da classe nas tabelas **$metric)**

Os pedidos feitos pelo próprio serviço Azure Files, como criação de registos ou eliminação, não são registados. Para obter uma lista completa dos pedidos de SMB e REST que estão registados, consulte [as operações registadas de Armazenamento e mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Registar pedidos anónimos

 Os seguintes tipos de pedidos anónimos são registados:

- Pedidos com êxito
- Erros de servidor
- Erros de tempo limite para o cliente e o servidor
- Pedidos GET falhados com o código de erro 304 (Não Modificado)

Todos os outros pedidos anónimos falhados não estão registados. Para obter uma lista completa dos pedidos de SMB e REST que estão registados, consulte [as operações registadas de Armazenamento e mensagens de estado](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) e os [Ficheiros Azure que monitorizam a referência de dados](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Aceder a registos numa conta de armazenamento

Os registos aparecem como bolhas armazenadas num contentor na conta de armazenamento do alvo. Os dados são recolhidos e armazenados dentro de uma única bolha como uma carga de carga JSON delimitada pela linha. O nome da bolha segue esta convenção de nomeação:

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Eis um exemplo:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Aceder a troncos num centro de eventos

Os registos enviados para um centro de eventos não são armazenados como um ficheiro, mas pode verificar se o centro de eventos recebeu a informação de registo. No portal Azure, vá ao seu centro de eventos e verifique se a contagem **de mensagens recebidas** é superior a zero. 

![Registos de auditoria](media/storage-files-monitoring/event-hub-log.png)

Pode aceder e ler dados de registo que são enviados para o seu centro de eventos utilizando informações de segurança e ferramentas de gestão e monitorização de eventos. Para mais informações, veja [o que posso fazer com os dados de monitorização que estão a ser enviados para o meu centro de eventos?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Aceder a registos num espaço de trabalho do Log Analytics

Pode aceder aos registos enviados para um espaço de trabalho do Log Analytics utilizando consultas de registo do Azure Monitor. Os dados são armazenados na tabela **StorageFileLogs.** 

Para obter mais informações, consulte [Começar com o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Aqui ficam algumas consultas que pode introduzir na barra **de pesquisa log** para ajudá-lo a monitorizar os seus Ficheiros Azure. Estas consultas funcionam com a [nova linguagem.](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)

> [!IMPORTANT]
> Quando seleciona **Logs** do menu do grupo de recursos de conta de armazenamento, o Log Analytics é aberto com o âmbito de consulta definido para o grupo de recursos atual. Isto significa que as consultas de registo só incluirão dados desse grupo de recursos. Se pretender executar uma consulta que inclua dados de outros recursos ou dados de outros serviços Azure, selecione **Logs** do menu **Azure Monitor.** Consulte [o âmbito de consulta de registo e o intervalo de tempo no Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) para obter mais detalhes.

Utilize estas consultas para ajudá-lo a monitorizar as suas partilhas de ficheiros Azure:

- Ver erros de SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Criar um gráfico de tortas de operações SMB na última semana

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Ver erros do REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Crie um gráfico de tortas de operações REST na última semana

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Para ver a lista de nomes e descrições de colunas para Ficheiros Azure, consulte [StorageFileLogs](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Para obter mais informações sobre como escrever consultas, consulte [Tutorial: Começar com consultas de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam-no proativamente quando forem encontradas condições importantes nos seus dados de monitorização. Permitem identificar e resolver problemas no seu sistema antes que os seus clientes os percebam. Pode definir alertas em [métricas,](/azure/azure-monitor/platform/alerts-metric-overview) [registos](/azure/azure-monitor/platform/alerts-unified-log)e no registo de [atividades](/azure/azure-monitor/platform/activity-log-alerts). 

A tabela a seguir enumera alguns cenários de exemplo para monitorizar e a métrica adequada a utilizar para o alerta:

| Cenário | Métrica para usar para alerta |
|-|-|
| A partilha de ficheiros está acelerada. | Métrica: Transações<br>Nome de dimensão: Tipo de resposta <br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |
| O tamanho da partilha de ficheiros é de 80% da capacidade. | Métrica: Capacidade de Arquivo<br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |
| A saída de partilha de ficheiros ultrapassou os 500 GiB num dia. | Métrica: Egress<br>Nome de dimensão: FileShare (apenas partilha de ficheiros premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Como criar alertas para ficheiros Azure

1. Aceda à sua **conta de armazenamento** no portal **Azure.** 

2. Clique **em Alertas** e, em seguida, clique **em + Nova regra de alerta**.

3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** e, em seguida, clique em **Fazer**. 

4. Clique **em Selecionar a condição** e fornecer as seguintes informações para o alerta: 

    - **Métrica**
    - **Nome de dimensão**
    - **Lógica de alerta**

5. Clique **em Selecionar grupo de ação** e adicione um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.

6. Preencha os **detalhes do Alerta** como o nome da regra de **alerta,** **descrição**e **severidade**.

7. Clique **em Criar regra de alerta** para criar o alerta.

> [!NOTE]  
> Se criar um alerta e for muito barulhento, ajuste o valor do limiar e alerte a lógica.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Como criar um alerta se uma partilha de ficheiros for acelerada

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção **de Monitorização,** clique em **Alertas**e, em seguida, clique **em + Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento `contoso` for, selecione o `contoso/file` recurso.
4. Clique **em Selecionar Condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **de Transações.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione o tipo **de resposta**.
7. Clique nos valores de **dimensionamento** e selecione **SuccessWithThrottling** (para SMB) ou **ClientThrottlingError** (para REST).

   > [!NOTE]
   > Se o valor da dimensão SuccessWithThrottling ou ClientThrottlingError não estiver listado, isto significa que o recurso não foi estrangulado. Para adicionar o valor de dimensão, clique em **Adicionar valor personalizado** ao lado dos **valores** de Dimension para baixo, **digite SuccessWithThrottling** ou **ClientThrottlingError,** clique em **OK** e repita o passo #7.

8. Clique no **drop-down** do nome Dimension e selecione A partilha **de ficheiros**.
9. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, selecione **Todos os valores atuais e futuros**. Os valores de dimensão não listam as ações de ficheiros porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas de estrangulamento para as ações de ficheiros padrão serão desencadeados se alguma parte do ficheiro dentro da conta de armazenamento for acelerada e o alerta não identificar qual a partilha de ficheiros que foi acelerada. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

10. Defina os **parâmetros** de alerta (valor limiar, operador, granularidade de agregação e frequência de avaliação) e clique em **Fazer**.

    > [!TIP]
    > Se estiver a utilizar um limiar estático, o gráfico métrico pode ajudar a determinar um valor limiar razoável se a parte do ficheiro estiver atualmente a ser estrangulada. Se estiver a utilizar um limiar dinâmico, o gráfico métrico apresentará os limiares calculados com base em dados recentes.

11. Clique **em Selecionar grupo de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta**, **Descrição e **Severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Como criar um alerta se o tamanho da partilha do ficheiro Azure for 80% da capacidade

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção **'Monitorização',** clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento `contoso` for, selecione o `contoso/file` recurso.
4. Clique **em Selecionar Condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica capacidade de **ficheiro.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione File **Share**.
7. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, selecione **Todos os valores atuais e futuros**. Os valores de dimensão não listam as ações de ficheiros porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas para as ações de ficheiros padrão baseiam-se em todas as ações de ficheiros na conta de armazenamento. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

8. Introduza o **valor Threshold** em bytes. Por exemplo, se o tamanho da partilha de ficheiros for de 100 TiB e pretender receber um alerta quando o tamanho da ação do ficheiro for de 80% da capacidade, o valor-limiar nos bytes é de 87960930222080.
9. Defina o resto dos **parâmetros** de alerta (granularidade agregação e frequência de avaliação) e clique em **Fazer**.
10. Clique em Selecionar grupo de ação para adicionar um grupo de ação (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
11. Preencha os **detalhes do Alerta** como o nome da regra de **alerta**, **Descrição e **Severidade**.
12. Clique **em Criar regra de alerta** para criar o alerta.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Como criar um alerta se a saída de partilha de ficheiros Azure excedeu 500 GiB num dia

1. Aceda à sua **conta de armazenamento** no portal **Azure.**
2. Na secção 'Monitorização', clique em **Alertas** e clique em **+ Nova regra de alerta**.
3. Clique **em Editar o recurso,** selecione o **tipo de recurso De ficheiro** para a conta de armazenamento e, em seguida, clique em **Fazer**. Por exemplo, se o nome da conta de armazenamento for contoso, selecione o recurso contoso/ficheiro.
4. Clique **em Selecionar Condição** para adicionar uma condição.
5. Verá uma lista de sinais suportados para a conta de armazenamento, selecione a métrica **Egress.**
6. Na lâmina lógica de **sinal configurar,** clique no **nome Dimension** drop-down e selecione File **Share**.
7. Clique nos **valores** de Dimension drop-down e selecione as ações de ficheiros em que pretende alertar.

   > [!NOTE]
   > Se a partilha de ficheiros for uma partilha de ficheiros padrão, selecione **Todos os valores atuais e futuros**. Os valores de dimensão não listam as ações de ficheiros porque as métricas por ação não estão disponíveis para ações de ficheiros padrão. Os alertas para as ações de ficheiros padrão baseiam-se em todas as ações de ficheiros na conta de armazenamento. Uma vez que as métricas por ação não estão disponíveis para ações de ficheiros padrão, a recomendação é ter uma ação de ficheiro por conta de armazenamento.

8. Introduza **536870912000** bytes para o valor limiar. 
9. Clique na **granularidade** da agregação e selecione **24 horas**.
10. Selecione a **Frequência da avaliação** e **clique em Fazer**.
11. Clique **em Selecionar grupo de ação** para adicionar um grupo de **ação** (e-mail, SMS, etc.) ao alerta, selecionando um grupo de ação existente ou criando um novo grupo de ação.
12. Preencha os **detalhes do Alerta** como o nome da regra de **alerta**, **Descrição e **Severidade**.
13. Clique **em Criar regra de alerta** para criar o alerta.

## <a name="next-steps"></a>Passos seguintes

- [Azure Files monitorizando a referência de dados](storage-files-monitoring.md)
- [Monitorar recursos do Azure com o Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Migração de métricas de armazenamento Azure](../common/storage-metrics-migration.md)
- [Planear uma implementação de Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Como implementar os Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Resolução de problemas dos Ficheiros do Azure no Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Resolução de problemas dos Ficheiros do Azure no Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
