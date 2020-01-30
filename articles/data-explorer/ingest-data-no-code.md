---
title: 'Tutorial: Ingerir dados de monitorização no Azure Data Explorer sem uma linha de código'
description: Neste tutorial, aprende-se a ingerir dados de monitorização ao Azure Data Explorer sem uma linha de código e consulta desses dados.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: c160f04ef7120a6c90991d8e6ecdf98b2f0d348e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836564"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Tutorial: Ingerir e consultar dados de monitorização no Azure Data Explorer 

Este tutorial irá ensiná-lo a ingerir dados desde registos de diagnóstico e atividade a um cluster Azure Data Explorer sem escrever código. Com este método simples de ingestão, pode começar rapidamente a consultar o Azure Data Explorer para análise de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão numa base de dados do Azure Data Explorer.
> * Forforme os dados ingeridos utilizando uma política de atualização.
> * Crie um centro de [eventos](/azure/event-hubs/event-hubs-about) e conecte-o ao Azure Data Explorer.
> * Transmita dados para um centro de eventos a partir de [métricas de diagnóstico](/azure/azure-monitor/platform/diagnostic-settings) do Monitor Azure e registos de [atividade.](/azure/azure-monitor/platform/activity-logs-overview)
> * Consulta dos dados ingeridos utilizando o Azure Data Explorer.

> [!NOTE]
> Crie todos os recursos na mesma localização ou região azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster de data Explorer do Azure e um banco de dados](create-cluster-database-portal.md). Neste tutorial, o nome da base de dados é *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Fornecedor de dados do Monitor Azure: métricas de diagnóstico e registos de atividades

Ver e compreender os dados fornecidos pelas métricas de diagnóstico do Monitor Azure e registos de atividade abaixo. Criará um oleoduto de ingestão baseado nestes esquemas de dados. Note que cada evento num registo tem uma série de registos. Este conjunto de registos será dividido mais tarde no tutorial.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Exemplos de métricas de diagnóstico e registos de atividade

As métricas de diagnóstico azure e os registos de atividade são emitidos por um serviço Azure e fornecem dados sobre o funcionamento desse serviço. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="example"></a>Exemplo

As métricas de diagnóstico são agregadas com um grão de tempo de 1 minuto. Segue-se um exemplo de um esquema de eventométrico do Azure Data Explorer sobre a duração da consulta:

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

Segue-se um exemplo de um registo de [ingestão](using-diagnostic-logs.md#diagnostic-logs-schema)de diagnóstico do Azure Data Explorer:

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
# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
#### <a name="example"></a>Exemplo

Os registos de atividade do Azure são registos de nível de subscrição que fornecem informações sobre as operações realizadas sobre os recursos na sua subscrição. Segue-se um exemplo de um evento de registo de atividades para verificar o acesso:

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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Criar um oleoduto de ingestão no Azure Data Explorer

A criação de um oleoduto Azure Data Explorer envolve vários passos, tais como criação de [mesa e ingestão](/azure/data-explorer/ingest-sample-data#ingest-data)de dados. Também pode manipular, mapear e atualizar os dados.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Ligue-se à Web UI do Web Explorer de Dados Azure

Na sua base de dados azure Data Explorer *TestDatabase,* selecione **Query** para abrir o Azure Data Explorer Web UI.

![Página de consulta](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Criar as tabelas-alvo

A estrutura dos troncos do Monitor Azure não é tabular. Manipulará os dados e expandirá cada evento para um ou mais registos. Os dados brutos serão ingeridos para uma tabela intermédia chamada *ActivityLogsRawRecords* para registos de atividade e *DiagnosticRawRecords* para métricas de diagnóstico e registos. Nessa altura, os dados serão manipulados e expandidos. Utilizando uma política de atualização, os dados expandidos serão então ingeridos na tabela *ActivityLogs* para registos de atividade, *DiagnosticMetrics* para métricas de diagnóstico e Diagnóstico de *Registos* para registos de diagnóstico. Isto significa que terá de criar duas tabelas separadas para ingerir registos de atividade e três tabelas separadas para ingerir métricas e registos de diagnóstico.

Utilize a UI Web do Azure Data Explorer para criar as tabelas-alvo na base de dados do Azure Data Explorer.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Criar tabelas para as métricas de diagnóstico

1. Na base de dados *TestDatabase,* crie uma tabela chamada *DiagnosticMetrics* para armazenar os registos de métricas de diagnóstico. Utilize o seguinte comando de controlo `.create table`:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **Executar** para criar a tabela.

    ![Executar consulta](media/ingest-data-no-code/run-query.png)

1. Crie a tabela de dados intermédia denominada *DiagnosticRawRecords* na base de *dados testDatabase* para manipulação de dados utilizando a seguinte consulta. Selecione **Executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Definir a política de [retenção](/azure/kusto/management/retention-policy) zero para a tabela intermédia:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Criar tabelas para os registos de diagnóstico 

1. Na base de dados *TestDatabase,* crie uma tabela chamada *DiagnosticLogs* para armazenar os registos de registos de diagnóstico. Utilize o seguinte comando de controlo `.create table`:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selecione **Executar** para criar a tabela.

1. Crie a tabela de dados intermédia denominada *DiagnosticRawRecords* na base de *dados testDatabase* para manipulação de dados utilizando a seguinte consulta. Selecione **Executar** para criar a tabela.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Definir a política de [retenção](/azure/kusto/management/retention-policy) zero para a tabela intermédia:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Criar tabelas para os registos de atividade 

1. Crie uma tabela chamada *ActivityLogs* na base de dados *testDatabase* para receber registos de registos de atividade. Para criar a tabela, execute a seguinte consulta do Explorador de Dados Azure:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Crie a tabela de dados intermédia denominada *ActivityLogsRawRecords* na base de dados *testDatabase* para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Definir a política de [retenção](/azure/kusto/management/retention-policy) zero para a tabela intermédia:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Criar mapeamentos de mesa

 Como o formato de dados é `json`, é necessário mapeamento de dados. O `json` mapeamento mapeia cada caminho json para um nome de coluna de mesa.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico / Registos de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Mapeie métricas de diagnóstico e troncos para a mesa

Para mapear a métrica de diagnóstico e registar dados para a tabela, utilize a seguinte consulta:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Registos de atividade do mapa para a mesa

Para mapear os dados de registo de atividade para a tabela, utilize a seguinte consulta:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Criar a política de atualização para dados métricos e de registo

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Criar política de atualização de dados para métricas de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expanda a recolha de registos métricos de diagnóstico para que cada valor da coleção receba uma linha separada. Utilize o operador [`mv-expand`:](/azure/kusto/query/mvexpandoperator)
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Adicione a política de [atualização](/azure/kusto/concepts/updatepolicy) à tabela-alvo. Esta política executará automaticamente a consulta sobre quaisquer dados recentemente ingeridos na tabela de dados intermédios *DiagnosticRawRecords* e ingerirá os seus resultados na tabela *DiagnosticMetrics:*

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Registos de diagnóstico](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Criar política de atualização de dados para registos de diagnósticos

1. Crie uma [função](/azure/kusto/management/functions) que expanda a recolha de registos de diagnóstico para que cada valor da coleção receba uma linha separada. Você vai ativar registos de ingestão em um cluster Azure Data Explorer, e usar [logs de ingestão schema](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Criará uma tabela para o sucesso e para a ingestão falhada, enquanto alguns dos campos estarão vazios para a ingestão bem sucedida (ErrorCode, por exemplo). Utilize o operador [`mv-expand`:](/azure/kusto/query/mvexpandoperator)

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
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

2. Adicione a política de [atualização](/azure/kusto/concepts/updatepolicy) à tabela-alvo. Esta política executará automaticamente a consulta sobre quaisquer dados recentemente ingeridos na tabela de dados intermédios *DiagnosticRawRecords* e ingerirá os seus resultados na tabela *DiagnosticLogs:*

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Criar política de atualização de dados para registos de atividades

1. Crie uma [função](/azure/kusto/management/functions) que expanda a recolha de registos de registos de atividade para que cada valor da coleção receba uma linha separada. Utilize o operador [`mv-expand`:](/azure/kusto/query/mvexpandoperator)

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
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

2. Adicione a política de [atualização](/azure/kusto/concepts/updatepolicy) à tabela-alvo. Esta política executará automaticamente a consulta sobre quaisquer dados recentemente ingeridos na tabela de dados intermédios *ActivityLogsRawRecords* e ingerirá os seus resultados na tabela *ActivityLogs:*

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um espaço de nome azure Event Hubs

As definições de diagnóstico azure permitem a exportação de métricas e registos para uma conta de armazenamento ou para um centro de eventos. Neste tutorial, vamos encaminhar as métricas e os registos através de um centro de eventos. Você vai criar um espaço de nome de Event Hubs e um centro de eventos para as métricas de diagnóstico e logs nos seguintes passos. O Azure Monitor criará os *registos operacionais do* hub do evento para os registos de atividade.

1. Crie um hub de evento utilizando um modelo de Gestor de Recursos Azure no portal Azure. Para seguir o resto dos passos deste artigo, clique no botão **Deploy para Azure** e, em seguida, selecione **Open em nova janela**. O botão **Deploy to Azure** leva-o ao portal Azure.

    [![Desdobrar para o botão Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um espaço de nome sinuoso do Event Hubs e um centro de eventos para os registos de diagnóstico.

    ![Criação do hub do evento](media/ingest-data-no-code/event-hub.png)

1. Preencha o formulário com as seguintes informações. Para quaisquer definições não listadas na tabela a seguir, utilize os valores predefinidos.

    **Definição** | **Valor sugerido** | **Descrição**
    |---|---|---|
    | **Subscrição** | *A sua subscrição* | Selecione a subscrição do Azure que quer utilizar para o hub de eventos.|
    | **Grupo de recursos** | *test-resource-group* | Crie um novo grupo de recursos. |
    | **Localização** | Selecione a região que melhor satisfaz as suas necessidades. | Crie o espaço de nome sinuoso do Event Hubs no mesmo local que outros recursos.
    | **Nome do espaço de nome** | *AzureMonitoringData* | Escolha um nome exclusivo que identifique o seu espaço de nomes.
    | **Nome do centro do evento** | *DiagnosticData* | O hub de eventos encontra-se no espaço de nomes, que fornece um contentor de âmbito exclusivo. |
    | **Nome do grupo de consumidores** | *adxpipeline* | Crie um nome de grupo de consumidores. Os grupos de consumidores permitem que cada aplicação de consumo tenha uma vista separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Conecte as métricas e registos do Monitor Azure ao seu centro de eventos

Agora precisa ligar as suas métricas e registos de diagnóstico e os seus registos de atividade ao centro do evento.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico / Registos de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Ligue métricas e registos de diagnóstico ao seu centro de eventos

Selecione um recurso a partir do qual as métricas de exportação. Vários tipos de recursos suportam a exportação de dados de diagnóstico, incluindo o espaço de nome de Event Hubs, Cofre chave Azure, Hub Azure IoT e clusters Azure Data Explorer. Neste tutorial, usaremos um cluster Azure Data Explorer como nosso recurso, vamos rever métricas de desempenho de consulta e registos de resultados de ingestão.

1. Selecione o seu cluster Kusto no portal Azure.
1. Selecione **definições de diagnóstico**e, em seguida, selecione o link **de diagnóstico Ligar.** 

    ![Definições de diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. O painel de **definições de diagnóstico** abre. Tome os seguintes passos:
   1. Forneça aos seus dados de registo de diagnóstico o nome *ADXExportedData*.
   1. Em **LOG,** selecione ambas as caixas de verificação de verificação de **ingestão bem sucedidas** e **falhadas.**
   1. Em **MÉTRICA,** selecione a caixa de verificação de desempenho da **Consulta.**
   1. Selecione o Stream para uma caixa de verificação do centro de **eventos.**
   1. **Selecione Configurar**.

      ![Painel de configurações de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. No painel de **hub** select, configure como exportar dados de registos de diagnóstico para o centro de eventos que criou:
    1. Na lista de nomes do centro de **eventos Select,** selecione *AzureMonitoringData*.
    1. Na lista de nomes do centro de **eventos Select,** selecione *DiagnosticData*.
    1. Na lista de nomes de política do centro de **eventos Select,** selecione **RootManagerSharedAccessKey**.
    1. Selecione **OK**.

1. Selecione **Guardar**.

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Ligue os registos de atividade ao seu centro de eventos

1. No menu esquerdo do portal Azure, selecione **registo de atividade**.
1. A janela de **registo de atividade** abre. Selecione **Export to Event Hub**.

    ![Janela de log de atividade](media/ingest-data-no-code/activity-log.png)

1. A janela de **registo da atividade de exportação** abre:
 
    ![Janela de registo de atividade de exportação](media/ingest-data-no-code/export-activity-log.png)

1. Na janela de registo da **atividade de exportação,** tome as seguintes etapas:
      1. Selecione a sua subscrição.
      1. Na lista **De Regiões,** escolha **Selecione todos**.
      1. Selecione a **Exportpara uma** caixa de verificação do hub de eventos.
      1. Escolha **Selecione um espaço** de nome de ônibus de serviço para abrir o painel do centro de **eventos Select.**
      1. No painel de **hub** select, selecione a sua subscrição.
      1. Na lista de nomes do centro de **eventos Select,** selecione *AzureMonitoringData*.
      1. Na lista de nomes de política do centro de **eventos Select,** selecione o nome de política padrão do hub do evento.
      1. Selecione **OK**.
      1. No canto superior esquerdo da janela, selecione **Guardar**.
   Será criado um centro de eventos com o nome *insights-registos operacionais.*
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Consulte os dados que fluem para os seus centros de eventos

1. Aguarde alguns minutos até que a ligação esteja definida e a exportação de registo de atividade para o centro do evento esteja terminada. Vá ao espaço de nome do Seu Event Hubs para ver os centros de eventos que criou.

    ![Centros de eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Consulte os dados que fluem para o seu centro de eventos:

    ![Dados do hub do evento](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Ligue um hub de eventos ao Azure Data Explorer

Agora precisa de criar as ligações de dados para as suas métricas de diagnóstico e registos e registos de atividade.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Criar a ligação de dados para métricas de diagnóstico e registos de atividade

1. No seu cluster Azure Data Explorer chamado *kustodocs,* selecione Bases de **Dados** no menu esquerdo.
1. Na janela Bases de **Dados,** selecione a base de dados *TestDatabase.*
1. No menu esquerdo, selecione **A ingestão**de Dados .
1. Na janela de **ingestão** de Dados, clique **+ Adicionar Ligação de Dados**.
1. Na janela de **ligação dados,** introduza as seguintes informações:

    ![Conexão de dados do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Métricas de diagnóstico / Registos de diagnóstico](#tab/diagnostic-metrics+diagnostic-logs) 

1. Utilize as seguintes definições na janela de ligação de **dados:**

    Fonte de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da ligação de dados** | *DiagnosticsLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Espaço de nome do centro de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Centro de eventos** | *DiagnosticData* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *adxpipeline* | O grupo de consumidores definido no hub de eventos que criou. |
    | | |

    Tabela-alvo:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Para este tutorial, utilizará o encaminhamento estático (o padrão), onde especifica o nome da tabela, o formato de dados e o mapeamento. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *DiagnosticRawRecords* | A tabela que criou na base de *dados testDatabase.* |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de colunas** | *DiagnosticRawRecordsMapping* | O mapeamento que criou na base de dados *testDatabase,* que mapeia os dados da JSON para os nomes das colunas e tipos de dados da tabela *DiagnosticRawRecords.*|
    | | |

1. Selecione **Criar**.  

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)

1. Utilize as seguintes definições na janela de ligação de **dados:**

    Fonte de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da ligação de dados** | *ActivityLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Espaço de nome do centro de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Centro de eventos** | *insights-operational-logs* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *$Default* | O grupo de consumidores padrão. Se necessário, pode criar um grupo de consumidores diferente. |
    | | |

    Tabela-alvo:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Para este tutorial, utilizará o encaminhamento estático (o padrão), onde especifica o nome da tabela, o formato de dados e o mapeamento. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *ActivityLogsRawRecords* | A tabela que criou na base de *dados testDatabase.* |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de colunas** | *ActivityLogsRawRecordsMapping* | O mapeamento que criou na base de dados *testDatabase,* que mapeia os dados jSON que chegam aos nomes das colunas e tipos de dados da tabela *ActivityLogsRawRecords.*|
    | | |

1. Selecione **Criar**.  
---

## <a name="query-the-new-tables"></a>Consulta das novas mesas

Agora tem um oleoduto com dados a fluir. A ingestão através do cluster demora 5 minutos por padrão, por isso deixe os dados fluir durante alguns minutos antes de começar a consultar.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Métricas de diagnóstico](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Consulta da tabela de métricas de diagnóstico

A seguinte consulta analisa os dados de duração da consulta a partir de registos métricos de diagnóstico no Azure Data Explorer:

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
### <a name="query-the-diagnostic-logs-table"></a>Consulta da tabela de registos de diagnóstico

Este oleoduto produz ingestão através de um centro de eventos. Vai rever os resultados destas ingestão.
A seguinte consulta analisa quantas ingestão acumuladas num minuto, incluindo uma amostra de `Database`, `Table` e `IngestionSourcePath` para cada intervalo:

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

# <a name="activity-logstabactivity-logs"></a>[Registos de atividade](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Consulta da tabela de registos de atividade

A seguinte consulta analisa dados de registos de registos de atividade no Azure Data Explorer:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg (DuraçãoMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>Passos seguintes

* Aprenda a escrever muitas mais consultas sobre os dados extraídos do Azure Data Explorer utilizando [consultas de Escrita para o Azure Data Explorer](write-queries.md).
* [Monitorar operações de ingestão de Data Explorer do Azure usando logs de diagnóstico](using-diagnostic-logs.md)
* [Usar métricas para monitorar a integridade do cluster](using-metrics.md)
