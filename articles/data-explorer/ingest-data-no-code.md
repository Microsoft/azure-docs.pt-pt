---
title: 'Tutorial: Atividade de diagnóstico e dados de registo no Explorador de dados do Azure sem uma linha de código de ingestão'
description: Neste tutorial, irá aprender ingerir dados para o Explorador de dados do Azure sem uma linha de código e consultar dados.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: a678722666146fdf22e88680ab414b09d2a7ffaa
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749941"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Tutorial: Ingestão de dados no Explorador de dados do Azure sem uma linha de código

Este tutorial irá ensiná-lo ingerir dados de diagnóstico e registos de atividade para um cluster do Explorador de dados do Azure sem escrever código. Com este método de ingestão simples, pode começar rapidamente a consultar o Explorador de dados do Azure para análise de dados.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão num banco de dados do Explorador de dados do Azure.
> * Formate os dados ingeridos com uma política de atualização.
> * Criar uma [hub de eventos](/azure/event-hubs/event-hubs-about) e ligá-lo ao Explorador de dados do Azure.
> * Stream dados para um hub de eventos a partir [registos de diagnóstico do Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) e [registos de atividades do Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Consulte os dados ingeridos com o Explorador de dados do Azure.

> [!NOTE]
> Crie todos os recursos na mesma localização do Azure ou região. Este é um requisito para os registos de diagnóstico do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md). Neste tutorial, é o nome de base de dados *AzureMonitoring*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Fornecedor de dados do Azure Monitor: registos de atividade e diagnóstico

Ver e compreender os dados fornecidos pelos registos de atividade e diagnóstico do Azure Monitor. Vamos criar um pipeline de ingestão com base nesses esquemas de dados.

### <a name="diagnostic-logs-example"></a>Exemplo de registos de diagnóstico

Registos de diagnóstico do Azure são métricas emitidas por um serviço do Azure que fornece dados sobre a operação desse serviço. Os dados são agregados com um grão de tempo de 1 minuto. Cada evento no registo de diagnóstico contém um registo. Eis um exemplo de um esquema de eventos de métrica do Explorador de dados do Azure na duração de consulta:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Exemplo de registos de atividade

Registos de atividades do Azure estão os registos de nível de assinatura que contêm uma coleção de registos. Os registos de fornecem informações sobre as operações executadas nos recursos da sua subscrição. Ao contrário dos registos de diagnóstico, cada evento no registo de atividades com uma matriz de registos. Vamos precisar dividir essa matriz de registos mais tarde no tutorial. Eis um exemplo de um evento de registo de atividades para a verificação de acesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configure um pipeline de ingestão no Explorador de dados do Azure

Configurar um pipeline do Explorador de dados do Azure envolve várias etapas, como [ingestão de dados e criação de tabela](/azure/data-explorer/ingest-sample-data#ingest-data). Também pode manipular, mapear e atualizar os dados.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Ligue-se para a Web do Explorador de dados do Azure da interface do Usuário

No seu Explorador de dados do Azure *AzureMonitoring* base de dados, selecione **consulta** para abrir a IU da Web do Azure Data Explorer.

![Página de consulta](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Criar as tabelas de destino

Utilize a IU da Web do Azure Data Explorer para criar as tabelas de destino na base de dados do Explorador de dados do Azure.

#### <a name="the-diagnostic-logs-table"></a>A tabela de registos de diagnóstico

1. Na *AzureMonitoring* bases de dados, criar uma tabela chamada *DiagnosticLogsRecords* para armazenar os registos de registo de diagnóstico. Utilize o seguinte `.create table` controlar o comando:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **executar** para criar a tabela.

    ![Executar consulta](media/ingest-data-no-code/run-query.png)

#### <a name="the-activity-logs-tables"></a>Os registos de atividades tabelas

Uma vez que a estrutura dos registos de Atividades não estiver em tabela, precisará manipular os dados e expanda cada evento para um ou mais registos. Os dados não processados irão ser ingeridos a uma tabela intermediária chamada *ActivityLogsRawRecords*. Nessa altura, os dados serão manipulados e expandidos. Os dados expandidos, em seguida, irão ser ingeridos para o *ActivityLogsRecords* tabela utilizando uma política de atualização. Isso significa que terá de criar duas tabelas separadas para a ingestão de registos de atividades.

1. Criar uma tabela chamada *ActivityLogsRecords* no *AzureMonitoring* para receber os registros de log de atividade da base de dados. Para criar a tabela, execute a seguinte consulta do Explorador de dados do Azure:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Criar a tabela de dados intermediários, com o nome *ActivityLogsRawRecords* no *AzureMonitoring* base de dados para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Criar mapeamentos de tabela

 Porque o formato de dados é `json`, é necessário o mapeamento de dados. O `json` mapeamento mapeia cada caminho json para um nome de coluna de tabela.

#### <a name="table-mapping-for-diagnostic-logs"></a>Mapeamento de tabela para os registos de diagnóstico

Para mapear dados dos registos de diagnóstico para a tabela, utilize a seguinte consulta:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Mapeamento de tabela para registos de atividades

Para mapear dados de registos de atividade para a tabela, utilize a seguinte consulta:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-activity-logs-data"></a>Crie a política de atualização para os dados dos registos de atividade

1. Criar uma [função](/azure/kusto/management/functions) que expande a recolha de registos, para que cada valor na coleção recebe uma linha separada. Utilize o [ `mvexpand` ](/azure/kusto/query/mvexpandoperator) operador:

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Adicionar a [atualizar política](/azure/kusto/concepts/updatepolicy) para a tabela de destino. Esta política irá automaticamente executar a consulta em quaisquer dados ingeridos recentemente na *ActivityLogsRawRecords* tabela de dados intermediários e ingerir os resultados para o *ActivityLogsRecords* tabela:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um espaço de nomes de Hubs de eventos do Azure

Registos de diagnóstico do Azure permitem exportar métricas para uma conta de armazenamento ou para um hub de eventos. Neste tutorial, iremos irá encaminhar as métricas através de um hub de eventos. Irá criar um espaço de nomes de Hubs de eventos e um hub de eventos para os registos de diagnóstico nos passos seguintes. Monitor do Azure irá criar o hub de eventos *insights-operational-logs* para os registos de atividades.

1. Crie um hub de eventos com um modelo Azure Resource Manager no portal do Azure. Para seguir o resto dos passos neste artigo, clique com botão direito a **implementar no Azure** e, em seguida, selecione **abrir numa janela nova**. O **implementar no Azure** botão leva-o para o portal do Azure.

    [![Implementar no botão do Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um espaço de nomes de Hubs de eventos e um hub de eventos para os registos de diagnóstico.

    ![Criação do hub de eventos](media/ingest-data-no-code/event-hub.png)

1. Preencha o formulário com as seguintes informações. Para as definições não listadas na tabela a seguir, utilize os valores predefinidos.

    **Definição** | **Valor sugerido** | **Descrição**
    |---|---|---|
    | **Subscrição** | *A sua subscrição* | Selecione a subscrição do Azure que quer utilizar para o hub de eventos.|
    | **Grupo de recursos** | *test-resource-group* | Crie um novo grupo de recursos. |
    | **Localização** | Selecione a região que melhor atenda às suas necessidades. | Crie o espaço de nomes de Hubs de eventos na mesma localização que outros recursos.
    | **Namespace name** | *AzureMonitoringData* | Escolha um nome exclusivo que identifique o seu espaço de nomes.
    | **Nome do hub de eventos** | *DiagnosticLogsData* | O hub de eventos encontra-se no espaço de nomes, que fornece um contentor de âmbito exclusivo. |
    | **Nome do grupo de consumidores** | *adxpipeline* | Crie um nome de grupo de consumidor. Os grupos de consumidores permitem que cada aplicação de consumo tenha uma vista separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Ligar os registos do Azure Monitor ao seu hub de eventos

Agora precisa de ligar os seus registos de diagnóstico e os registos de atividade para o hub de eventos.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Ligar os registos de diagnóstico ao seu hub de eventos

Selecione um recurso do qual pretende exportar as métricas. A exportação de registos de diagnóstico, incluindo o espaço de nomes de Hubs de eventos, Azure Key Vault, IoT Hub do Azure e clusters do Explorador de dados do Azure suportam de vários tipos de recursos. Neste tutorial, vamos utilizar um cluster do Explorador de dados do Azure como nosso recurso.

1. Selecione o cluster de Kusto no portal do Azure.
1. Selecione **das definições de diagnóstico**e, em seguida, selecione a **ativar diagnósticos** ligação. 

    ![Definições de diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. O **as definições de diagnóstico** painel abre-se. Siga os passos seguintes:
    1. Dê sua diagnóstico o nome de dados de registo *ADXExportedData*.
    1. Sob **MÉTRICA**, selecione a **AllMetrics** caixa de verificação (opcional).
    1. Selecione o **Stream para um hub de eventos** caixa de verificação.
    1. Selecione **configurar**.

    ![Painel de definições de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Na **hub de eventos selecione** painel, configurar como exportar dados de registos de diagnóstico para o hub de eventos que criou:
    1. Na **selecione o espaço de nomes de hub de eventos** , selecione *AzureMonitoringData*.
    1. Na **nome do hub de eventos Select** , selecione *diagnosticlogsdata*.
    1. Na **nome de política do hub de eventos Select** , selecione **RootManagerSharedAccessKey**.
    1. Selecione **OK**.

1. Selecione **Guardar**. O nome de espaço de nomes, nome e a política da hub de eventos será apresentada na janela.

    ![Guardar as definições de diagnóstico](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="connect-activity-logs-to-your-event-hub"></a>Ligar os registos de atividades ao seu hub de eventos

1. No menu à esquerda do portal do Azure, selecione **registo de atividades**.
1. O **registo de atividades** é aberta a janela. Selecione **exportar para o Hub de eventos**.

    ![Janela de registo de atividade](media/ingest-data-no-code/activity-log.png)

1. O **exportar registo de atividades** é aberta a janela:
 
    ![Janela de registo de atividade de exportação](media/ingest-data-no-code/export-activity-log.png)

1. Na **exportar registo de atividades** janela, siga os passos seguintes:
      1. Selecione a sua subscrição.
      1. Na **regiões** lista, escolha **Selecionar tudo**.
      1. Selecione o **exportar para um hub de eventos** caixa de verificação.
      1. Escolher **selecione um espaço de nomes do service bus** para abrir o **hub de eventos selecione** painel.
      1. Na **hub de eventos selecione** painel, selecione a sua subscrição.
      1. Na **selecione o espaço de nomes de hub de eventos** , selecione *AzureMonitoringData*.
      1. Na **nome de política do hub de eventos selecione** , selecione o nome de política do hub de eventos padrão.
      1. Selecione **OK**.
      1. No canto superior esquerdo da janela, selecione **guardar**.
   Um hub de eventos com o nome *insights-operational-logs* será criado.

### <a name="see-data-flowing-to-your-event-hubs"></a>Ver o fluxo de dados para os hubs de eventos

1. Aguarde alguns minutos até que a ligação é definida e a exportação de registo de atividades para o hub de eventos estiver concluída. Aceda ao seu espaço de nomes de Hubs de eventos para ver os hubs de eventos que criou.

    ![Hubs de eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Ver o fluxo de dados para o hub de eventos:

    ![Dados do hub de eventos](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Ligar um hub de eventos ao Explorador de dados do Azure

Agora precisa de criar as ligações de dados para os seus registos de diagnóstico e registos de atividades.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Criar a ligação de dados para os registos de diagnóstico

1. No seu cluster do Explorador de dados do Azure com o nome *kustodocs*, selecione **bases de dados** no menu à esquerda.
1. Na **bases de dados** janela, selecione seu *AzureMonitoring* base de dados.
1. No menu da esquerda, selecione **ingestão de dados**.
1. Na **ingestão de dados** janela, clique em **+ adicionar ligação de dados**.
1. Na **ligação de dados** janela, introduza as seguintes informações:

    ![Ligação de dados do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

    Origem de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da ligação de dados** | *DiagnosticsLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Espaço de nomes de hub de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Hub de eventos** | *diagnosticlogsdata* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *adxpipeline* | O grupo de consumidores definido no hub de eventos que criou. |
    | | |

    Tabela de destino:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Neste tutorial, vai utilizar encaminhamento (predefinição), onde especifica o nome da tabela, o formato de dados e o mapeamento estático. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *DiagnosticLogsRecords* | A tabela que criou o *AzureMonitoring* base de dados. |
    | **Formato de dados** | *JSON* | O formato utilizado na tabela. |
    | **Mapeamento de colunas** | *DiagnosticLogsRecordsMapping* | O mapeamento que criou o *AzureMonitoring* base de dados, que mapeia os dados recebidos de JSON para os tipos de dados e os nomes de coluna da *DiagnosticLogsRecords* tabela.|
    | | |

1. Selecione **Criar**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Criar a ligação de dados para os registos de atividade

Repita os passos a [criar a ligação de dados para os registos de diagnóstico](#diagnostic-logs-data-connection) secção para criar a ligação de dados para os seus registos de atividade.

1. Utilize as seguintes definições no **ligação de dados** janela:

    Origem de dados:

    **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da ligação de dados** | *ActivityLogsConnection* | O nome da ligação que quer criar no Azure Data Explorer.|
    | **Espaço de nomes de hub de eventos** | *AzureMonitoringData* | O nome que escolheu anteriormente que identifica o seu espaço de nomes. |
    | **Hub de eventos** | *insights-operational-logs* | O hub de eventos que criou. |
    | **Grupo de consumidores** | *$Default* | Grupo de consumidores predefinido. Se for necessário, pode criar um grupo de consumidores diferentes. |
    | | |

    Tabela de destino:

    Existem duas opções para o encaminhamento: *estático* e *dinâmico*. Neste tutorial, vai utilizar encaminhamento (predefinição), onde especifica o nome da tabela, o formato de dados e o mapeamento estático. Deixe o campo **Os meus dados incluem informações de encaminhamento** não selecionado.

     **Definição** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *ActivityLogsRawRecords* | A tabela que criou o *AzureMonitoring* base de dados. |
    | **Formato de dados** | *JSON* | O formato utilizado na tabela. |
    | **Mapeamento de colunas** | *ActivityLogsRawRecordsMapping* | O mapeamento que criou o *AzureMonitoring* base de dados, que mapeia os dados recebidos de JSON para os tipos de dados e os nomes de coluna da *ActivityLogsRawRecords* tabela.|
    | | |

1. Selecione **Criar**.  

## <a name="query-the-new-tables"></a>Consultar as novas tabelas

Agora que tem um pipeline com o fluxo de dados. Ingestão por meio do cluster demora 5 minutos por predefinição, por isso, permitem que os dados a fluir para alguns minutos antes de começar a consultar.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Um exemplo de consultar a tabela de registos de diagnóstico

A seguinte consulta analisa dados de duração de consulta de registos de registo de diagnóstico no Explorador de dados do Azure:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Resultados de consulta:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>Um exemplo de consultar a tabela de registos de atividade

A seguinte consulta analisa dados de registos de registo de atividade no Explorador de dados do Azure:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Resultados de consulta:
|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Passos Seguintes

Aprenda a escrever muitas consultas mais sobre os dados extraídos a partir do Explorador de dados do Azure utilizando o seguinte artigo:

> [!div class="nextstepaction"]
> [Escrever consultas do Azure Data Explorer](write-queries.md)
