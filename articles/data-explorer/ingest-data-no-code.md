---
title: 'Tutorial: ingestão de dados de monitoramento no Azure Data Explorer sem uma linha de código'
description: Neste tutorial, você aprenderá a ingerir dados de monitoramento para o Azure Data Explorer sem uma linha de código e consultar esses dados.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 11/17/2019
ms.openlocfilehash: 97faa445a286574aa5fc05d084d21c0740bc8a8b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173858"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Tutorial: ingestão e consulta de dados de monitoramento no Azure Data Explorer 

Este tutorial ensinará a você como ingerir dados de logs de diagnóstico e de atividades para um cluster de Data Explorer do Azure sem escrever código. Com esse simples método de ingestão, você pode começar rapidamente a consultar a Data Explorer do Azure para análise de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão em um banco de dados Data Explorer do Azure.
> * Formate os dados ingeridos usando uma política de atualização.
> * Crie um [Hub de eventos](/azure/event-hubs/event-hubs-about) e conecte-o ao Azure data Explorer.
> * Transmita dados para um hub de eventos de Azure Monitor [logs e métricas de diagnóstico](/azure/azure-monitor/platform/diagnostic-settings) e [logs de atividades](/azure/azure-monitor/platform/activity-logs-overview).
> * Consultar os dados ingeridos usando o Azure Data Explorer.

> [!NOTE]
> Crie todos os recursos no mesmo local ou região do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md). Neste tutorial, o nome do banco de dados é *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Provedor de dados de Azure Monitor: logs e métricas de diagnóstico e logs de atividade

Exiba e entenda os dados fornecidos pelo Azure Monitor logs e métricas de diagnóstico e logs de atividades abaixo. Você criará um pipeline de ingestão com base nesses esquemas de dados. Observe que cada evento em um log tem uma matriz de registros. Esta matriz de registros será dividida posteriormente no tutorial.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Exemplos de métricas de diagnóstico e logs e logs de atividade

As métricas de diagnóstico do Azure e os logs de atividade são emitidos por um serviço do Azure e fornecem dados sobre a operação desse serviço. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="example"></a>Exemplo

As métricas de diagnóstico são agregadas com um intervalo de tempo de 1 minuto. Veja a seguir um exemplo de um esquema de evento de métrica de Data Explorer do Azure na duração da consulta:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
#### <a name="example"></a>Exemplo

Veja a seguir um exemplo de um [log de ingestão do diagnóstico](using-diagnostic-logs.md#diagnostic-logs-schema)de data Explorer do Azure:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="example"></a>Exemplo

Os logs de atividades do Azure são logs de nível de assinatura que fornecem informações sobre as operações executadas nos recursos em sua assinatura. Veja a seguir um exemplo de um evento de log de atividade para verificar o acesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurar um pipeline de ingestão no Azure Data Explorer

Configurar um pipeline de Data Explorer do Azure envolve várias etapas, como [criação de tabela e ingestão de dados](/azure/data-explorer/ingest-sample-data#ingest-data). Você também pode manipular, mapear e atualizar os dados.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Conectar-se à interface do usuário da Web do Azure Data Explorer

No banco de dados do Azure Data Explorer *TestDatabase* , selecione **consulta** para abrir a interface do usuário da Web do Azure data Explorer.

![Página de consulta](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Criar as tabelas de destino

A estrutura dos logs de Azure Monitor não é tabular. Você manipulará os dados e expandirá cada evento para um ou mais registros. Os dados brutos serão ingeridos em uma tabela intermediária denominada *ActivityLogsRawRecords* para logs de atividade e *DiagnosticRawRecords* para métricas e logs de diagnóstico. Nesse momento, os dados serão manipulados e expandidos. Usando uma política de atualização, os dados expandidos serão então ingeridos na tabela *ActivityLogs* para logs de atividade, *DiagnosticMetrics* para métricas de diagnóstico e *DiagnosticLogs* para logs de diagnóstico. Isso significa que você precisará criar duas tabelas separadas para ingerir logs de atividades e três tabelas separadas para ingerir logs e métricas de diagnóstico.

Use a interface do usuário da Web do Azure Data Explorer para criar as tabelas de destino no banco de dados de Data Explorer do Azure.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Criar tabelas para as métricas de diagnóstico

1. No banco de dados *TestDatabase* , crie uma tabela chamada *DiagnosticMetrics* para armazenar os registros de métricas de diagnóstico. Use o seguinte comando de controle de `.create table`:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **executar** para criar a tabela.

    ![Executar consulta](media/ingest-data-no-code/run-query.png)

1. Crie a tabela de dados intermediárias chamada *DiagnosticRawRecords* no banco de dados *TestDatabase* para a manipulação de dados usando a consulta a seguir. Selecione **executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Definir a [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Criar tabelas para os logs de diagnóstico 

1. No banco de dados *TestDatabase* , crie uma tabela chamada *DiagnosticLogs* para armazenar os registros de log de diagnóstico. Use o seguinte comando de controle de `.create table`:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selecione **executar** para criar a tabela.

1. Crie a tabela de dados intermediárias chamada *DiagnosticRawRecords* no banco de dados *TestDatabase* para a manipulação de dados usando a consulta a seguir. Selecione **executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Definir a [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Criar tabelas para os logs de atividade 

1. Crie uma tabela chamada *ActivityLogs* no banco de dados *TestDatabase* para receber registros do log de atividades. Para criar a tabela, execute a seguinte consulta de Data Explorer do Azure:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Crie a tabela de dados intermediárias denominada *ActivityLogsRawRecords* no banco de dados *TestDatabase* para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Definir a [política de retenção](/azure/kusto/management/retention-policy) zero para a tabela intermediária:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Criar mapeamentos de tabela

 Como o formato de dados é `json`, o mapeamento de dados é necessário. O mapeamento de `json` mapeia cada caminho JSON para um nome de coluna de tabela.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapear logs e métricas de diagnóstico para a tabela

Para mapear a métrica de diagnóstico e os dados de log para a tabela, use a seguinte consulta:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Mapear logs de atividade para a tabela

Para mapear os dados do log de atividades para a tabela, use a seguinte consulta:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Criar a política de atualização para dados de métrica e de log

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Criar política de atualização de dados para métricas de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expanda a coleção de registros de métrica de diagnóstico para que cada valor na coleção receba uma linha separada. Use o operador de [`mv-expand`](/azure/kusto/query/mvexpandoperator) :
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events.count),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela de dados intermediários *DiagnosticRawRecords* e ingerirá seus resultados na tabela *DiagnosticMetrics* :

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Criar política de atualização de dados para logs de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expanda a coleção de registros de logs de diagnóstico para que cada valor na coleção receba uma linha separada. Você habilitará os logs de ingestão em um cluster de Data Explorer do Azure e usará o [esquema de logs de ingestão](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Você criará uma tabela para êxito e para ingestão com falha, enquanto alguns dos campos estarão vazios para a ingestão com êxito (ErrorCode, por exemplo). Use o operador de [`mv-expand`](/azure/kusto/query/mvexpandoperator) :

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela de dados intermediários *DiagnosticRawRecords* e ingerirá seus resultados na tabela *DiagnosticLogs* :

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Criar política de atualização de dados para logs de atividade

1. Crie uma [função](/azure/kusto/management/functions) que expanda a coleção de registros de log de atividades para que cada valor na coleção receba uma linha separada. Use o operador de [`mv-expand`](/azure/kusto/query/mvexpandoperator) :

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará automaticamente a consulta em todos os dados recentemente incluídos na tabela de dados intermediários *ActivityLogsRawRecords* e ingerirá seus resultados na tabela *ActivityLogs* :

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um namespace de hubs de eventos do Azure

As configurações de diagnóstico do Azure permitem exportar métricas e logs para uma conta de armazenamento ou para um hub de eventos. Neste tutorial, rotearemos as métricas e os logs por meio de um hub de eventos. Você criará um namespace de hubs de eventos e um hub de eventos para as métricas de diagnóstico e os logs nas etapas a seguir. Azure Monitor criará os logs do hub de eventos *-operações-operacionais* para os logs de atividade.

1. Crie um hub de eventos usando um modelo de Azure Resource Manager no portal do Azure. Para seguir o restante das etapas neste artigo, clique com o botão direito do mouse no botão **implantar no Azure** e, em seguida, selecione **abrir em nova janela**. O botão **implantar no Azure** leva você para o portal do Azure.

    [botão ![implantar no Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um namespace de hubs de eventos e um hub de eventos para os logs de diagnóstico.

    ![Criação do hub de eventos](media/ingest-data-no-code/event-hub.png)

1. Preencha o formulário com as seguintes informações. Para quaisquer configurações não listadas na tabela a seguir, use os valores padrão.

    **Definição** | **Valor sugerido** | **Descrição**
    |---|---|---|
    | **Subscrição** | *A sua subscrição* | Selecione a subscrição do Azure que quer utilizar para o hub de eventos.|
    | **Grupo de recursos** | *test-resource-group* | Crie um novo grupo de recursos. |
    | **Localização** | Selecione a região que melhor atende às suas necessidades. | Crie o namespace de hubs de eventos no mesmo local que outros recursos.
    | **Nome do namespace** | *AzureMonitoringData* | Escolha um nome exclusivo que identifique o seu espaço de nomes.
    | **Nome do hub de eventos** | *DiagnosticData* | O hub de eventos encontra-se no espaço de nomes, que fornece um contentor de âmbito exclusivo. |
    | **Nome do grupo de consumidores** | *adxpipeline* | Crie um nome de grupo de consumidores. Os grupos de consumidores permitem que cada aplicação de consumo tenha uma vista separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Conectar Azure Monitor métricas e logs ao seu hub de eventos

Agora você precisa conectar suas métricas de diagnóstico e logs e seus logs de atividade ao Hub de eventos.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Conectar logs e métricas de diagnóstico ao seu hub de eventos

Selecione um recurso do qual exportar métricas. Vários tipos de recursos dão suporte à exportação de dados de diagnóstico, incluindo namespace de hubs de eventos, Azure Key Vault, Hub IoT do Azure e clusters de Data Explorer do Azure. Neste tutorial, usaremos um cluster Data Explorer do Azure como nosso recurso, examinaremos as métricas de desempenho de consulta e os logs de resultados de ingestão.

1. Selecione o cluster Kusto no portal do Azure.
1. Selecione **configurações de diagnóstico**e, em seguida, selecione o link **Ativar diagnóstico** . 

    ![Definições de diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. O painel **configurações de diagnóstico** é aberto. Execute as seguintes etapas:
   1. Dê aos seus dados de log de diagnóstico o nome *ADXExportedData*.
   1. Em **log**, marque as caixas de seleção **SucceededIngestion** e **FailedIngestion** .
   1. Em **métrica**, marque a caixa de seleção **desempenho da consulta** .
   1. Marque a caixa de seleção **transmitir para um hub de eventos** .
   1. Selecione **Configurar**.

      ![Painel de configurações de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. No painel **selecionar Hub de eventos** , configure como exportar dados de logs de diagnóstico para o Hub de eventos que você criou:
    1. Na lista **selecionar namespace do hub de eventos** , selecione *AzureMonitoringData*.
    1. Na lista **selecionar nome do hub de eventos** , selecione *DiagnosticData*.
    1. Na lista **selecionar nome da política do hub de eventos** , selecione **RootManagerSharedAccessKey**.
    1. Selecione **OK**.

1. Selecione **Guardar**.

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Conectar logs de atividade ao seu hub de eventos

1. No menu à esquerda da portal do Azure, selecione **log de atividades**.
1. A janela **log de atividades** é aberta. Selecione **exportar para o Hub de eventos**.

    ![Janela log de atividades](media/ingest-data-no-code/activity-log.png)

1. A janela **Exportar log de atividades** é aberta:
 
    ![Janela exportar log de atividades](media/ingest-data-no-code/export-activity-log.png)

1. Na janela **Exportar log de atividades** , execute as seguintes etapas:
      1. Selecione a sua subscrição.
      1. Na lista **regiões** , escolha **selecionar tudo**.
      1. Marque a caixa de seleção **exportar para um hub de eventos** .
      1. Escolha **selecionar um namespace do barramento de serviço** para abrir o painel **selecionar Hub de eventos** .
      1. No painel **selecionar Hub de eventos** , selecione sua assinatura.
      1. Na lista **selecionar namespace do hub de eventos** , selecione *AzureMonitoringData*.
      1. Na lista **selecionar nome da política do hub de eventos** , selecione o nome da política do hub de eventos padrão.
      1. Selecione **OK**.
      1. No canto superior esquerdo da janela, selecione **salvar**.
   Um hub de eventos com o nome *insights-Operational-logs* será criado.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Confira dados fluindo para seus hubs de eventos

1. Aguarde alguns minutos até que a conexão seja definida e a exportação de log de atividades para o Hub de eventos seja concluída. Vá para o namespace de hubs de eventos para ver os hubs de eventos que você criou.

    ![Hubs de eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Confira dados fluindo para o Hub de eventos:

    ![Dados do hub de eventos](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Conectar um hub de eventos ao Azure Data Explorer

Agora você precisa criar as conexões de dados para suas métricas de diagnóstico e logs e logs de atividades.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Criar a conexão de dados para métricas de diagnóstico e logs e logs de atividade

1. No cluster Data Explorer do Azure chamado *kustodocs*, selecione **bancos de dados** no menu à esquerda.
1. Na janela **bancos** de dados, selecione seu banco de *TestDatabase* .
1. No menu à esquerda, selecione **ingestão de dados**.
1. Na janela de **ingestão de dados** , clique em **+ Adicionar conexão de dados**.
1. Na janela **conexão de dados** , insira as seguintes informações:

    ![Conexão de dados do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico/logs de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 

1. Use as seguintes configurações na janela **conexão de dados** :

    Fonte de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *DiagnosticsLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Namespace do hub de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Hub de eventos** | *DiagnosticData* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *adxpipeline* | O grupo de consumidores definido no hub de eventos que criou. |
    | | |

    Tabela de destino:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), em que você especifica o nome da tabela, o formato de dados e o mapeamento. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *DiagnosticRawRecords* | A tabela que você criou no banco de dados *TestDatabase* . |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de colunas** | *DiagnosticRawRecordsMapping* | O mapeamento que você criou no banco de dados *TestDatabase* , que MAPEIA dados JSON de entrada para os nomes de coluna e tipos de dado da tabela *DiagnosticRawRecords* .|
    | | |

1. Selecione **Criar**.  

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)

1. Use as seguintes configurações na janela **conexão de dados** :

    Fonte de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *ActivityLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Namespace do hub de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Hub de eventos** | *insights-operational-logs* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *$Default* | O grupo de consumidores padrão. Se necessário, você pode criar um grupo de consumidores diferente. |
    | | |

    Tabela de destino:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), em que você especifica o nome da tabela, o formato de dados e o mapeamento. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *ActivityLogsRawRecords* | A tabela que você criou no banco de dados *TestDatabase* . |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de colunas** | *ActivityLogsRawRecordsMapping* | O mapeamento que você criou no banco de dados *TestDatabase* , que MAPEIA dados JSON de entrada para os nomes de coluna e tipos de dado da tabela *ActivityLogsRawRecords* .|
    | | |

1. Selecione **Criar**.  
---

## <a name="query-the-new-tables"></a>Consultar as novas tabelas

Agora você tem um pipeline com fluxo de dados. A ingestão por meio do cluster leva 5 minutos por padrão, portanto, permita que os dados fluam por alguns minutos antes de começar a consultar.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Consultar a tabela de métricas de diagnóstico

A consulta a seguir analisa os dados de duração da consulta dos registros de métrica de diagnóstico no Azure Data Explorer:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Consultar a tabela de logs de diagnóstico

Esse pipeline produz ingestãos por meio de um hub de eventos. Você examinará os resultados dessas ingestãos.
A consulta a seguir analisa quantas ingestãos são acumuladas em um minuto, incluindo uma amostra de `Database`, `Table` e `IngestionSourcePath` para cada intervalo:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Logs de atividade](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Consultar a tabela de logs de atividade

A consulta a seguir analisa os dados dos registros de log de atividades no Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  AVG (DurationMs) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>Passos seguintes

* Saiba como escrever muitas outras consultas nos dados extraídos do Azure Data Explorer usando [consultas de gravação para o data Explorer do Azure](write-queries.md).
* [Monitorar operações de ingestão de Data Explorer do Azure usando logs de diagnóstico](using-diagnostic-logs.md)
* [Usar métricas para monitorar a integridade do cluster](using-metrics.md)
