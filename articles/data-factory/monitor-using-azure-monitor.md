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
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 25bb455ea46fdc96e32e34d434dd844779b0b650
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495303"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Alertas e monitorizar fábricas de dados através do Azure Monitor
Aplicações na cloud são complexas com muitas partes móveis. A monitorização fornece dados para garantir que seu aplicativo mantém-se e em execução em bom estado. Também ajuda-o a protele potenciais problemas ou resolução de problemas anteriores são. Além disso, pode utilizar dados de monitorização para obter informações aprofundadas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual.

O Azure Monitor proporciona registos e métricas de infraestrutura de nível de base para a maioria dos serviços no Microsoft Azure. Para obter detalhes, consulte [descrição geral da monitorização](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Registos de diagnóstico do Azure são registos emitidos por um recurso que fornecem dados avançados e frequentes sobre o funcionamento desse recurso. Fábrica de dados devolve registos de diagnóstico no Azure Monitor.

## <a name="persist-data-factory-data"></a>Manter os dados de fábrica de dados
Fábrica de dados só armazena a execução do pipeline dados para 45 dias. Se pretender manter os dados por mais de 45 dias, de execução do pipeline através do Azure Monitor, pode não apenas encaminhar os registos de diagnóstico para análise, pode mantê-los para uma conta de armazenamento, para que tenha informações de fábrica durante o período de sua chossing.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Guarde-as para um **conta de armazenamento** para inspeção de auditoria ou manual. Pode especificar o período de retenção (em dias), utilizando as definições de diagnóstico.
* Stream-lhes **os Hubs de eventos** para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.
* Analisá-los com **do Log Analytics**

Pode usar um armazenamento conta ou event hub espaço de nomes que não esteja na mesma subscrição que o recurso que emite os registos. O utilizador que configura a definição tem de ter o acesso de controlo (RBAC) de acesso baseado em funções adequadas para ambas as subscrições.

## <a name="set-up-diagnostic-logs"></a>Configurar registos de diagnóstico

### <a name="diagnostic-settings"></a>Definições de diagnóstico
Registos de diagnóstico para recursos de computação-não são configurados com definições de diagnóstico. Definições de diagnóstico para um controlo de recursos:

* Em que os registos de diagnóstico são enviados (conta de armazenamento, os Hubs de eventos, e/ou do Log Analytics).
* As categorias de registo são enviadas.
* O tempo em que cada categoria de registo deve ser mantida numa conta de armazenamento
* A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
* Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativada (por exemplo, apenas os Hubs de eventos ou o Log Analytics opções estão selecionadas), as políticas de retenção não têm qualquer efeito.
* Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Ativar registos de diagnóstico através de REST APIs

Criar ou atualizar uma definição de diagnóstico na API de REST do Azure Monitor

**Pedido**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers** (Cabeçalhos)
* Substitua `{api-version}` com `2016-09-01`.
* Substitua `{resource-id}` com o ID de recurso do recurso para o qual quiser para editar as definições de diagnóstico. Para obter mais informações [utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-portal.md).
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
| storageAccountId |Cadeia | O ID de recurso da conta do storage para o qual gostaria de enviar registos de diagnóstico |
| serviceBusRuleId |Cadeia | O service bus ID da regra do espaço de nomes de barramento de serviço no qual deseja que os Hubs de eventos criado para transmissão em fluxo registos de diagnóstico. A regra ID é o formato: "{ID de recurso de barramento de serviço} /authorizationrules/ {nome da chave}".|
| workspaceId | Tipo complexo | Matriz de Detalhamento de métrica de tempo e suas diretivas de retenção. Atualmente, esta propriedade está vazia. |
|métricas| Valores de parâmetros de execução para ser transmitido para pipeline invocado do pipeline| Um objeto JSON mapear nomes de parâmetros para valores de argumento |
| registos| Tipo complexo| Nome de uma categoria de registo de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registo de diagnóstico para um recurso, primeiro execute uma operação de obtenção de definições de diagnóstico. |
| categoria| Cadeia| Matriz de categorias de registo e suas diretivas de retenção |
| intervalo de agregação | Cadeia | A granularidade de métricas que são capturadas no formato de duração ISO 8601. Tem de ser PT1M (um minuto)|
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
* Substitua `{api-version}` com `2016-09-01`.
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
[Obter mais informações aqui](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings)

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
   "properties:"
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
| Nível |Cadeia | Nível dos registos de diagnóstico. Nível 4 é sempre o caso dos registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Cadeia| ID de execução de atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Cadeia| ID de execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "ActivityRuns" | `ActivityRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| Cadeia |Nome da atividade com o estado. Se o estado é a pulsação de início, é `MyActivity -`. Se o estado é o heartbeat do fim, é `MyActivity - Succeeded` com o estado final | `MyActivity - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|activityName| Cadeia | Nome da atividade | `MyActivity` |
|start| Cadeia | Início da atividade execute no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia | Extremidades da atividade executam no período de tempo, formato UTC. Se a atividade não terminou, mas (registo de diagnóstico para uma atividade iniciar), um valor padrão de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |


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
| Nível |Cadeia | Nível dos registos de diagnóstico. Nível 4 é o caso dos registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Cadeia| ID de execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| Cadeia |Nome do pipeline com o estado. "Pipeline - foi concluída com êxito" com o estado final aquando da conclusão da execução de pipeline| `MyPipeline - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|start| Cadeia | Início da atividade execute no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia | Fim da atividade é executada no período de tempo, formato UTC. Se a atividade não terminou, mas (registo de diagnóstico para uma atividade iniciar), um valor padrão de `1601-01-01T00:00:00Z` está definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| Cadeia | Estado final de execução (com êxito ou falha) do pipeline | `Succeeded`|


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
| Nível |Cadeia | Nível dos registos de diagnóstico. Definida como nível 4 para registos de execução da atividade. | `4`  |
| correlationId |Cadeia | ID exclusivo para controlar um pedido específico ponto-a-ponto | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| hora | Cadeia | Hora do evento no período de tempo, formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Cadeia| ID da execução do acionador | `08587023010602533858661257311` |
|resourceId| Cadeia | ID do recurso associado para o recurso de fábrica de dados | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|categoria| Cadeia | Categoria de registos de diagnóstico. Definir esta propriedade para "PipelineRuns" | `PipelineRuns` |
|nível| Cadeia | Nível dos registos de diagnóstico. Definir esta propriedade para "Informativo" | `Informational` |
|operationName| Cadeia |Nome do acionador com o estado final se com êxito disparado. "MyTrigger - foi concluída com êxito" se o heartbeat foi concluída com êxito| `MyTrigger - Succeeded` |
|triggerName| Cadeia | Nome do acionador | `MyTrigger` |
|triggerType| Cadeia | Tipo de Acionador (acionador Manual ou acionador de agenda) | `ScheduleTrigger` |
|triggerEvent| Cadeia | Eventos do acionador | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Cadeia | Início do acionador dispare no período de tempo, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Cadeia | Estado final de se disparador acionado com êxito (com êxito ou falha) | `Succeeded`|

## <a name="metrics"></a>Métricas

O Azure Monitor permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure é as métricas (também chamadas de contadores de desempenho) emitidas pelos recursos mais do Azure. O Azure Monitor proporciona várias formas de configurar e consumir estas métricas de monitorização e resolução de problemas.

ADFV2 emite as métricas seguintes

| **Métricas**           | **Nome a apresentar de métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Foi efetuada com êxito as métricas de execuções de pipeline | Contagem    | Total                | Total pipelines é executado com êxito dentro de uma janela de minutos |
| PipelineFailedRuns   | Falha de métricas de execuções de pipeline    | Contagem    | Total                | Total de pipelines execuções falhadas dentro de uma janela de minutos    |
| ActiviySucceededRuns | Foi efetuada com êxito as métricas de execuções de atividade | Contagem    | Total                | Atividade total é executado com êxito dentro de uma janela de minutos  |
| ActivityFailedRuns   | Falha de métricas de execuções de atividade    | Contagem    | Total                | Atividade total é executado com falhas dentro de uma janela de minutos     |
| TriggerSucceededRuns | Foi efetuada com êxito as métricas de execuções do acionador  | Contagem    | Total                | Total de execuções do acionador com êxito dentro de uma janela de minutos   |
| TriggerFailedRuns    | Falha de métricas de execuções do acionador     | Contagem    | Total                | Total de execuções do acionador com falhas dentro de uma janela de minutos      |

Para acessar as métricas, siga as instruções no artigo- https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics

## <a name="alerts"></a>Alertas

Pode emitir alertas em métricas suportadas no Data Factory. Clique nas **alertas** botão na fábrica de dados **Monitor** página.

![Opção de alertas](media/monitor-using-azure-monitor/alerts_image1.png)

Isto leva-o para o **alertas** página.

![Página de alertas](media/monitor-using-azure-monitor/alerts_image2.png)

Também pode iniciar sessão no portal do Azure e clique em **Monitor -&gt; alertas** para chegar a **alertas** página diretamente.

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
Ver [monitorizar e gerir pipelines de forma programática](monitor-programmatically.md) artigo para saber como monitorizar e gerir pipelines através da execução.
