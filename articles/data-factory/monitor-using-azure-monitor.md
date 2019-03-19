---
title: Monitorizar fábricas de dados através do Azure Monitor | Documentos da Microsoft
description: Saiba como utilizar o Azure Monitor para monitorizar pipelines do Data Factory, permitindo que os registos de diagnóstico com informações do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: shlo
ms.openlocfilehash: e96e462709ab0c715c831bd10c628869d5c617fe
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013313"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Alertas e monitorizar fábricas de dados através do Azure Monitor
Aplicações na cloud são complexas com muitas partes móveis. A monitorização fornece dados para garantir que seu aplicativo mantém-se e em execução em bom estado. Também ajuda-o a protele potenciais problemas ou resolução de problemas anteriores são. Além disso, pode utilizar dados de monitorização para obter informações aprofundadas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual.

O Azure Monitor proporciona registos e métricas de infraestrutura de nível de base para a maioria dos serviços no Microsoft Azure. Para obter detalhes, consulte [descrição geral da monitorização](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Registos de diagnóstico do Azure são registos emitidos por um recurso que fornecem dados avançados e frequentes sobre o funcionamento desse recurso. Fábrica de dados devolve registos de diagnóstico no Azure Monitor.

## <a name="persist-data-factory-data"></a>Manter os dados de fábrica de dados
Fábrica de dados só armazena a execução do pipeline dados para 45 dias. Se pretender manter os dados por mais de 45 dias de execução do pipeline, utilizar o Azure Monitor, apenas não é possível encaminhar os registos de diagnóstico para análise, pode mantê-los para uma conta de armazenamento, para que tenha informações de fábrica durante o período de sua escolha.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Guarde-as para um **conta de armazenamento** para inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias), utilizando as definições de diagnóstico.
* Stream-lhes **os Hubs de eventos** para ingestão por um serviço de terceiros ou de uma solução de análise personalizada como o Power BI.
* Analisá-los com **do Log Analytics**

Pode usar um armazenamento conta ou event hub espaço de nomes que não esteja na mesma subscrição que o recurso que emite os registos. O utilizador que configura a definição tem de ter o acesso de controlo (RBAC) de acesso baseado em funções adequadas para ambas as subscrições.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

### <a name="diagnostic-settings"></a>Definições de diagnóstico
Registos de diagnóstico para recursos de computação-não são configurados com definições de diagnóstico. Definições de diagnóstico para um controlo de recursos:

* Onde os registos de diagnóstico são enviados (conta de armazenamento, os Hubs de eventos ou registos do Azure Monitor).
* As categorias de registo são enviadas.
* O tempo em que cada categoria de registo deve ser mantida numa conta de armazenamento.
* A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
* Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativado (por exemplo, apenas os Hubs de eventos do Azure Monitor ou registos opções estão selecionadas), as políticas de retenção não têm efeito.
* Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Ativar registos de diagnóstico através de REST APIs

Criar ou atualizar uma definição de diagnóstico na API de REST do Azure Monitor

**Pedido**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers** (Cabeçalhos)
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` com o ID de recurso do recurso para o qual quiser para editar as definições de diagnóstico. Para obter mais informações [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Definir o `Content-Type` cabeçalho para `application/json`.
* Defina o cabeçalho de autorização para um token web JSON que obtém do Azure Active Directory. Para obter mais informações, consulte [autenticar pedidos](../active-directory/develop/authentication-scenarios.md).

**Corpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| storageAccountId |String | O ID de recurso da conta do storage para o qual gostaria de enviar registos de diagnóstico |
| serviceBusRuleId |String | O service bus ID da regra do espaço de nomes de barramento de serviço no qual deseja que os Hubs de eventos criado para transmissão em fluxo registos de diagnóstico. A regra ID é o formato: "{ID de recurso de barramento de serviço} /authorizationrules/ {nome da chave}".|
| workspaceId | Tipo complexo | Matriz de Detalhamento de métrica de tempo e suas diretivas de retenção. Atualmente, esta propriedade está vazia. |
|métricas| Valores de parâmetros de execução para ser transmitido para pipeline invocado do pipeline| Um objeto JSON mapear nomes de parâmetros para valores de argumento |
| registos| Tipo complexo| Nome de uma categoria de registo de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registo de diagnóstico para um recurso, primeiro execute uma operação de obtenção de definições de diagnóstico. |
| categoria| String| Matriz de categorias de registo e suas diretivas de retenção |
| timeGrain | String | A granularidade de métricas que são capturadas no formato de duração ISO 8601. Tem de ser PT1M (um minuto)|
| enabled| Booleano | Especifica se a coleção dessa categoria de métrica ou registo está ativada para este recurso|
| retentionPolicy| Tipo complexo| Descreve a política de retenção para uma categoria de métrica ou registo. Utilizado para a opção de conta de armazenamento apenas.|
| dias| Int| Número de dias a manter a métricas ou registos. Um valor de 0 mantém os registos indefinidamente. Utilizado para a opção de conta de armazenamento apenas. |

**Resposta**

200 OK


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Obter informações sobre a definição de diagnóstico na API de REST do Azure Monitor

**Pedido**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers** (Cabeçalhos)
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` com o ID de recurso do recurso para o qual quiser para editar as definições de diagnóstico. Para obter mais informações utilizando grupos de recursos para gerir os recursos do Azure.
* Definir o `Content-Type` cabeçalho para `application/json`.
* Defina o cabeçalho de autorização para um JSON Web Token que obtém do Azure Active Directory. Para obter mais informações, consulte os pedidos de autenticação.

**Resposta**

200 OK

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
[Obter mais informações aqui](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Esquema de eventos e registos

### <a name="activity-run-logs-attributes"></a>Atributos de registos de execução de atividade

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |String | Nível dos registos de diagnóstico. Nível 4 é sempre o caso dos registos de execução da atividade. | `4`  |
| correlationId |String | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | String | Hora do evento no período de tempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| ID de execução de atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| ID de execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| String | Categoria de registos de diagnóstico. Definir esta propriedade para "ActivityRuns" | `ActivityRuns` |
|nível| String | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| String |Nome da atividade com o estado. Se o estado é a pulsação de início, é `MyActivity -`. Se o estado é o heartbeat do fim, é `MyActivity - Succeeded` com o estado final | `MyActivity - Succeeded` |
|pipelineName| String | Nome do pipeline | `MyPipeline` |
|activityName| String | Nome da atividade | `MyActivity` |
|start| String | Início da atividade execute no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Extremidades da atividade executam no período de tempo, formato UTC. Se a atividade não terminou, mas (registo de diagnóstico para uma atividade iniciar), um valor padrão de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |

### <a name="pipeline-run-logs-attributes"></a>Atributos de registos de execução de pipeline

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |String | Nível dos registos de diagnóstico. Nível 4 é o caso dos registos de execução da atividade. | `4`  |
| correlationId |String | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | String | Hora do evento no período de tempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| ID de execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| String | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| String | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| String |Nome do pipeline com o estado. "Pipeline - foi concluída com êxito" com o estado final aquando da conclusão da execução de pipeline| `MyPipeline - Succeeded` |
|pipelineName| String | Nome do pipeline | `MyPipeline` |
|start| String | Início da atividade execute no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| String | Fim da atividade é executada no período de tempo, formato UTC. Se a atividade não terminou, mas (registo de diagnóstico para uma atividade iniciar), um valor padrão de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| String | Estado final de execução (com êxito ou falha) do pipeline | `Succeeded`|

### <a name="trigger-run-logs-attributes"></a>Atributos de registos de execução do acionador

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| Propriedade | Tipo | Descrição | Exemplo |
| --- | --- | --- | --- |
| Nível |String | Nível dos registos de diagnóstico. Definida como nível 4 para registos de execução da atividade. | `4`  |
| correlationId |String | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | String | Hora do evento no período de tempo, formato UTC `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| ID da execução do acionador | `08587023010602533858661257311` |
|resourceId| String | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| String | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| String | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| String |Nome do acionador com o estado final se com êxito disparado. "MyTrigger - foi concluída com êxito" se o heartbeat foi concluída com êxito| `MyTrigger - Succeeded` |
|triggerName| String | Nome do acionador | `MyTrigger` |
|triggerType| String | Tipo de Acionador (acionador Manual ou acionador de agenda) | `ScheduleTrigger` |
|triggerEvent| String | Eventos do acionador | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| String | Início do acionador dispare no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| String | Estado final de se disparador acionado com êxito (com êxito ou falha) | `Succeeded`|

## <a name="metrics"></a>Métricas

O Azure Monitor permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure é as métricas (também chamadas de contadores de desempenho) emitidas pelos recursos mais do Azure. O Azure Monitor proporciona várias formas de configurar e consumir estas métricas de monitorização e resolução de problemas.

ADFV2 emite as métricas seguintes

| **Métricas**           | **Nome a apresentar de métrica**         | **Unit** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Foi efetuada com êxito as métricas de execuções de pipeline | Contagem    | Total                | Total pipelines é executado com êxito dentro de uma janela de minutos |
| PipelineFailedRuns   | Falha de métricas de execuções de pipeline    | Contagem    | Total                | Total de pipelines execuções falhadas dentro de uma janela de minutos    |
| ActivitySucceededRuns | Foi efetuada com êxito as métricas de execuções de atividade | Contagem    | Total                | Atividade total é executado com êxito dentro de uma janela de minutos  |
| ActivityFailedRuns   | Falha de métricas de execuções de atividade    | Contagem    | Total                | Atividade total é executado com falhas dentro de uma janela de minutos     |
| TriggerSucceededRuns | Foi efetuada com êxito as métricas de execuções do acionador  | Contagem    | Total                | Total de execuções do acionador com êxito dentro de uma janela de minutos   |
| TriggerFailedRuns    | Falha de métricas de execuções do acionador     | Contagem    | Total                | Total de execuções do acionador com falhas dentro de uma janela de minutos      |

Para acessar as métricas, siga as instruções no artigo- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorizar métricas de fábrica de dados com o Azure Monitor

Pode utilizar a integração do Azure Data Factory com o Azure Monitor para encaminhar os dados para o Azure Monitor. Esta integração é útil nos seguintes cenários:

1.  Quer escrever consultas complexas num conjunto avançado de métricas, que são publicadas pela fábrica de dados para o Azure Monitor. Também pode criar alertas personalizados nestas consultas através do Azure Monitor.

2.  Pretende monitorizar em fábricas de dados. Pode encaminhar os dados de diferentes fábricas de dados para um único espaço de trabalho do Azure Monitor.

Para obter uma introdução de sete minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configurar definições de diagnóstico e de área de trabalho

Ative as definições de diagnóstico para a fábrica de dados.

1.  Selecione **do Azure Monitor** -> **as definições de diagnóstico** -> selecionar a fábrica de dados -> ativar diagnósticos.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Fornece definições de diagnóstico, incluindo a configuração da área de trabalho.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalar o Analytics de fábrica de dados do Azure a partir do Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Clique em **criar** e selecione a área de trabalho e definições.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorizar as métricas de fábrica de dados

A instalar **do Azure Data Factory Analytics** cria um conjunto de padrão de modos de exibição que permite que as métricas seguintes:

- Execuções de Pipeline do ADF execuções-1) pelo Data Factory

- Execuções de atividades do ADF execuções-2) pelo Data Factory

- Execuções do acionador de execuções do ADF a 3) pelo Data Factory

- Erros de Pipeline do ADF erros-1) os 10 principais pela fábrica de dados

- Execuções de atividades do ADF erros-2) os 10 principais pela fábrica de dados

- Erros de Acionador do ADF erros a 3) os 10 principais pela fábrica de dados

- Execuções de atividades do ADF estatísticas-1) por tipo

- Execuções de Acionador do ADF estatísticas-2) por tipo

- Duração de execuções do Pipeline de máx. de estatísticas do ADF a 3)

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Pode visualizar as métricas acima, examinar as consultas por trás estas métricas, editar as consultas, criar alertas e assim por diante.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alertas

Inicie sessão no portal do Azure e clique em **Monitor -&gt; alertas** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar Alertas

1.  Clique em **+ nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definir o **condição do alerta**.

    > [!NOTE]
    > Verifique se seleciona **todos os** no **filtrar por tipo de recurso**.

    ![Condição de alerta, 1 de 3 de ecrã](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Condição de alerta, 2 de 3 de ecrã](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Condição de alerta, 3 de 3 de ecrã](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definir o **detalhes do alerta**.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definir o **grupo de ação**.

    ![Grupo de ação, o ecrã 1 de 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupo de ação, o ecrã 2 de 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupo de ação, o ecrã 3 de 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupo de ação, o ecrã 4 de 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passos Seguintes

Ver [monitorizar e gerir pipelines de forma programática](monitor-programmatically.md) artigo para saber como monitorizar e gerir pipelines com o código.
