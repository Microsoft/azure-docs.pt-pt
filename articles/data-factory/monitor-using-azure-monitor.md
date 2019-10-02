---
title: Monitorar fábricas de dados usando Azure Monitor | Microsoft Docs
description: Saiba como usar Azure Monitor para monitorar pipelines de Data Factory do/Azure habilitando logs de diagnóstico com informações de Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 2a707eda6a7e32a95666dd70e196c8da3c3b7834
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815947"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Alertar e monitorar fábricas de dados usando Azure Monitor

Os aplicativos de nuvem são complexos e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que seus aplicativos permaneçam em funcionamento em um estado íntegro. Os monitores também ajudam a evitar possíveis problemas e a solucionar problemas anteriores.

Você pode usar dados de monitoramento para obter informações detalhadas sobre seus aplicativos. Esse conhecimento ajuda a melhorar o desempenho e a manutenção do aplicativo. Ele também ajuda a automatizar ações que, caso contrário, exigem intervenção manual.

O Azure Monitor fornece logs e métricas de infraestrutura de nível básico para a maioria dos serviços do Azure. Os logs de diagnóstico do Azure são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação do recurso. E Azure Data Factory grava logs de diagnóstico no monitor.

Para obter detalhes, consulte [Azure monitor visão geral](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Mantendo Azure Data Factory dados

Data Factory armazena dados de execução de pipeline por apenas 45 dias. Use o monitor se você quiser manter esses dados por mais tempo. Com o monitor, você pode rotear os logs de diagnóstico para análise. Você também pode mantê-los em uma conta de armazenamento para que você tenha informações de fábrica para a duração escolhida.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Salve os logs de diagnóstico em uma conta de armazenamento para auditoria ou inspeção manual. Você pode usar as configurações de diagnóstico para especificar o tempo de retenção em dias.
* Transmita os logs para os hubs de eventos do Azure. Os logs se tornam entrada para um serviço de parceiro ou para uma solução de análise personalizada, como Power BI.
* Analise os logs com Log Analytics.

Você pode usar uma conta de armazenamento ou um namespace de Hub de eventos que não esteja na assinatura do recurso que emite logs. O usuário que define a configuração deve ter acesso de RBAC (controle de acesso baseado em função) apropriado para ambas as assinaturas.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

### <a name="diagnostic-settings"></a>Definições de diagnóstico

Use as configurações de diagnóstico para configurar os logs de diagnóstico para recursos que não são de computação. As configurações de um controle de recurso têm os seguintes recursos:

* Eles especificam onde os logs de diagnóstico são enviados. Os exemplos incluem uma conta de armazenamento do Azure, um hub de eventos do Azure ou logs de monitor.
* Eles especificam quais categorias de log são enviadas.
* Eles especificam quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado, as políticas de retenção não terão nenhum efeito. Por exemplo, essa condição pode ocorrer quando apenas as opções hubs de eventos ou logs de monitor são selecionadas.
* As políticas de retenção são aplicadas por dia. O limite entre os dias ocorre no UTC (tempo universal coordenado da meia-noite). No final de um dia, os logs de dias que estão além da política de retenção são excluídos. Por exemplo, se você tiver uma política de retenção de um dia, no início de hoje, os logs de antes de ontem serão excluídos.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Habilitar logs de diagnóstico por meio da API REST do Azure Monitor

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Criar ou atualizar uma configuração de diagnóstico na API REST do monitor

##### <a name="request"></a>Pedir

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Defina o `Content-Type` cabeçalho como `application/json`.
* Defina o cabeçalho de autorização para o token Web JSON obtido do Azure Active Directory (Azure AD). Para obter mais informações, consulte [Autenticando solicitações](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Body

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
| **storageAccountId** |Cadeia | A ID de recurso da conta de armazenamento para a qual você deseja enviar os logs de diagnóstico. |
| **serviceBusRuleId** |Cadeia | A ID da regra de barramento de serviço do namespace do barramento de serviço no qual você deseja que os hubs de eventos sejam criados para os logs de diagnóstico de streaming. A ID da regra tem o `{service bus resource ID}/authorizationrules/{key name}`formato.|
| **workspaceId** | Tipo complexo | Uma matriz de detalhamentos de tempo de métrica e suas políticas de retenção. O valor desta propriedade está vazio. |
|**métricas**| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um objeto JSON que mapeia nomes de parâmetro para valores de argumento. |
| **logs**| Tipo complexo| O nome de uma categoria de log de diagnóstico para um tipo de recurso. Para obter a lista de categorias de log de diagnóstico para um recurso, execute uma operação obter diagnóstico-configurações. |
| **category**| Cadeia| Uma matriz de categorias de log e suas políticas de retenção. |
| **timeGrain** | Cadeia | A granularidade das métricas, que são capturadas no formato de duração ISO 8601. O valor da propriedade deve `PT1M`ser, que especifica um minuto. |
| **habilitado**| Booleano | Especifica se a coleta da categoria de métrica ou de log está habilitada para este recurso. |
| **retentionPolicy**| Tipo complexo| Descreve a política de retenção para uma métrica ou categoria de log. Esta propriedade é usada somente para contas de armazenamento. |
|**dias**| Int| O número de dias para manter as métricas ou os logs. Se o valor da propriedade for 0, os logs serão mantidos para sempre. Esta propriedade é usada somente para contas de armazenamento. |

##### <a name="response"></a>Resposta

200 OK.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obter informações sobre as configurações de diagnóstico na API REST do monitor

##### <a name="request"></a>Pedir

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Defina o `Content-Type` cabeçalho como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON que você obteve do Azure AD. Para obter mais informações, consulte [Autenticando solicitações](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Resposta

200 OK.

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
Para obter mais informações, consulte [configurações de diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Esquema de logs e eventos

### <a name="monitor-schema"></a>Monitorar esquema

#### <a name="activity-run-log-attributes"></a>Atributos de log de execução de atividade

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
| **Nível** |Cadeia | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |Cadeia | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | Cadeia | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC de TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Cadeia| A ID da execução da atividade. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Cadeia| A ID da execução do pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Cadeia | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Cadeia | A categoria dos logs de diagnóstico. Defina o valor da propriedade `ActivityRuns`como. | `ActivityRuns` |
|**geral**| Cadeia | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`como. | `Informational` |
|**operationName**| Cadeia | O nome da atividade com seu status. Se a atividade for a pulsação inicial, o valor da `MyActivity -`propriedade será. Se a atividade for a pulsação final, o valor da `MyActivity - Succeeded`propriedade será. | `MyActivity - Succeeded` |
|**pipelineName**| Cadeia | O nome do pipeline. | `MyPipeline` |
|**activityName**| Cadeia | O nome da atividade. | `MyActivity` |
|**start**| Cadeia | A hora de início da atividade é executada no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**completo**| Cadeia | A hora de término da atividade é executada no formato UTC de TimeSpan. Se o log de diagnóstico mostrar que uma atividade foi iniciada, mas ainda não terminou, o `1601-01-01T00:00:00Z`valor da propriedade será. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributos de log de execução de pipeline

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
| **Nível** |Cadeia | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |Cadeia | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | Cadeia | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC de TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Cadeia| A ID da execução do pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Cadeia | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Cadeia | A categoria dos logs de diagnóstico. Defina o valor da propriedade `PipelineRuns`como. | `PipelineRuns` |
|**geral**| Cadeia | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`como. | `Informational` |
|**operationName**| Cadeia | O nome do pipeline junto com seu status. Depois que a execução do pipeline for concluída, o valor `Pipeline - Succeeded`da propriedade será. | `MyPipeline - Succeeded`. |
|**pipelineName**| Cadeia | O nome do pipeline. | `MyPipeline` |
|**start**| Cadeia | A hora de início da atividade é executada no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`. |
|**completo**| Cadeia | A hora de término da atividade é executada no formato UTC de TimeSpan. Se o log de diagnóstico mostrar uma atividade iniciada mas ainda não tiver sido encerrada, `1601-01-01T00:00:00Z`o valor da propriedade será.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| Cadeia | O status final da execução do pipeline. Os valores de propriedade `Succeeded` possíveis `Failed`são e. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Gatilho-executar atributos de log

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
| **Nível** |Cadeia | O nível dos logs de diagnóstico. Para logs de execução de atividade, defina o valor da propriedade como 4. | `4` |
| **correlationId** |Cadeia | A ID exclusiva para acompanhar uma solicitação específica. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | Cadeia | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC de TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Cadeia| A ID da execução do gatilho. | `08587023010602533858661257311` |
|**resourceId**| Cadeia | A ID associada ao recurso de data Factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Cadeia | A categoria dos logs de diagnóstico. Defina o valor da propriedade `PipelineRuns`como. | `PipelineRuns` |
|**geral**| Cadeia | O nível dos logs de diagnóstico. Defina o valor da propriedade `Informational`como. | `Informational` |
|**operationName**| Cadeia | O nome do gatilho com seu status final, que indica se o gatilho foi acionado com êxito. Se a pulsação tiver sido bem-sucedida, o valor `MyTrigger - Succeeded`da propriedade será. | `MyTrigger - Succeeded` |
|**triggerName**| Cadeia | O nome do gatilho. | `MyTrigger` |
|**triggerType**| Cadeia | O tipo do gatilho. Os valores de propriedade `Manual Trigger` possíveis `Schedule Trigger`são e. | `ScheduleTrigger` |
|**triggerEvent**| Cadeia | O evento do gatilho. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Cadeia | A hora de início do acionamento do gatilho no formato UTC de TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**status**| Cadeia | O status final que mostra se o gatilho foi acionado com êxito. Os valores de propriedade `Succeeded` possíveis `Failed`são e. | `Succeeded`|

### <a name="log-analytics-schema"></a>Esquema de Log Analytics

Log Analytics herda o esquema do monitor com as seguintes exceções:

* A primeira letra de cada nome de coluna é capitalizada. Por exemplo, o nome da coluna "CorrelationId" no monitor é "CorrelationId" em Log Analytics.
* Não há nenhuma coluna de "nível".
* A coluna dinâmica "Propriedades" é preservada como o tipo de blob JSON dinâmico a seguir.

    | Azure Monitor coluna | Log Analytics coluna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinâmico |
    | $. Properties. Anotações | Anotações | Dinâmico |
    | $. Properties. Entrada | Input | Dinâmico |
    | $. Properties. Der | Output | Dinâmico |
    | $. Properties. Erro. errorCode | Código de erro | int |
    | $. Properties. Erro. mensagem | ErrorMessage | string |
    | $. Properties. Ao | Erro | Dinâmico |
    | $. Properties. Predecessores | Predecessores | Dinâmico |
    | $. Properties. Parâmetro | Parâmetros | Dinâmico |
    | $. Properties. Sistemaparameters | Sistemaparameters | Dinâmico |
    | $. Properties. Sinalizadores | Tags | Dinâmico |
    
## <a name="metrics"></a>Métricas

Com o monitor, você pode obter visibilidade do desempenho e da integridade de suas cargas de trabalho do Azure. O tipo de dados de monitor mais importante é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos do Azure. O monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

Azure Data Factory versão 2 emite as métricas a seguir.

| **Métricas**           | **Nome de exibição da métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Métricas de execuções de pipeline com êxito | Count    | Total                | O número total de execuções de pipeline que tiveram êxito em uma janela de minuto. |
| PipelineFailedRuns   | Métricas de execuções de pipeline com falha    | Count    | Total                | O número total de execuções de pipeline que falharam em uma janela de minuto.    |
| ActivitySucceededRuns | Métricas de execuções de atividades bem-sucedidas | Count    | Total                | O número total de execuções de atividade que tiveram êxito em uma janela de minuto.  |
| ActivityFailedRuns   | Métricas de execuções de atividade com falha    | Count    | Total                | O número total de execuções de atividade que falharam em uma janela de minuto.     |
| TriggerSucceededRuns | Métricas de execuções de gatilho com êxito  | Count    | Total                | O número total de execuções de gatilho que tiveram êxito em uma janela de minuto.   |
| TriggerFailedRuns    | Métricas de execuções de gatilho com falha     | Count    | Total                | O número total de execuções de gatilho que falharam em uma janela de minuto.      |

Para acessar as métricas, conclua as instruções em [Azure monitor plataforma de dados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorar Data Factory métricas com Azure Monitor

Você pode usar a integração do Data Factory com o monitor para rotear dados a serem monitorados. Essa integração é útil nos seguintes cenários:

* Você deseja escrever consultas complexas em um conjunto avançado de métricas que é publicado pelo Data Factory para monitorar. Você pode criar alertas personalizados nessas consultas via monitor.

* Você deseja monitorar entre fábricas de dados. Você pode rotear dados de várias fábricas de dados para um único espaço de trabalho de monitoramento.

Para uma introdução e uma demonstração desse recurso de sete minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Definir configurações de diagnóstico e espaço de trabalho

Crie ou adicione configurações de diagnóstico para seu data factory.

1. No portal, vá para monitor. Selecione **configurações** > configurações de**diagnóstico**.

1. Selecione o data factory para o qual você deseja definir uma configuração de diagnóstico.

1. Se não existir nenhuma configuração no data factory selecionado, você será solicitado a criar uma configuração. Selecione **ativar os diagnósticos**.

   ![Criar uma configuração de diagnóstico se não existir nenhuma configuração](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver configurações existentes no data factory, você verá uma lista de configurações já configuradas no data factory. Selecione **Adicionar configuração de diagnóstico**.

   ![Adicionar uma configuração de diagnóstico se houver configurações](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê um nome à sua configuração, selecione **Enviar para log Analytics**e, em seguida, selecione um espaço de trabalho no **espaço de trabalho log Analytics**.

    ![Nomeie suas configurações e selecione um espaço de trabalho do log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Selecione **Guardar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações dessa data factory. Os logs de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de evento são gerados. Até 15 minutos podem decorrer entre o momento em que um evento é emitido e quando ele aparece no Log Analytics.

* No modo _específico do recurso_ , os logs de diagnóstico de Azure data Factory fluxo em tabelas _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_
* No modo de _diagnóstico do Azure_ , os logs de diagnóstico fluem para a tabela _AzureDiagnostics_

> [!NOTE]
> Como uma tabela de log do Azure não pode ter mais de 500 colunas, é altamente recomendável selecionar o modo específico do recurso. Para obter mais informações, consulte [log Analytics limitações conhecidas](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalar a análise de Azure Data Factory do Azure Marketplace

![Vá para "Azure Marketplace", insira "filtro de análise" e selecione "análise de Azure Data Factory (versão prévia")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Detalhes sobre "análise de Azure Data Factory (versão prévia)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selecione **criar** e selecione **espaço** de trabalho do OMS e **configurações do espaço de trabalho do OMS**.

![Criando uma nova solução](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorar Data Factory métricas

A instalação do Azure Data Factory Analytics cria um conjunto padrão de exibições para que as seguintes métricas sejam habilitadas:

- Execuções do ADF-1) execuções de pipeline por Data Factory
 
- Execuções do ADF-2) execuções de atividade por Data Factory

- Execuções do ADF-3) execuções de gatilho por Data Factory

- Erros do ADF-1) 10 principais erros de pipeline por Data Factory

- Erros do ADF-2) primeiras 10 execuções de atividade por Data Factory

- Erros do ADF-3) 10 principais erros de gatilho por Data Factory

- Estatísticas do ADF-1) execuções de atividade por tipo

- Estatísticas do ADF-2) execuções de gatilho por tipo

- Estatísticas do ADF-3) duração máxima de execuções de pipeline

![Janela com "pastas de trabalho (visualização)" e "AzureDataFactoryAnalytics" realçadas](media/data-factory-monitor-oms/monitor-oms-image6.png)

Você pode visualizar as métricas anteriores, examinar as consultas por trás dessas métricas, editar as consultas, criar alertas e executar outras ações.

![Representação gráfica das execuções de pipeline por data factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A análise de Azure Data Factory (versão prévia) envia logs de diagnóstico para tabelas _de destino específicas do recurso_ . Você pode escrever consultas nas seguintes tabelas: _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_.

## <a name="alerts"></a>Alertas

Entre no portal do Azure e selecione **monitorar** > **alertas** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar Alertas

1. Selecione **+ nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

1. Defina a condição de alerta.

    > [!NOTE]
    > Certifique-se de selecionar **tudo** na lista suspensa **Filtrar por tipo de recurso** .

    !["Definir a condição de alerta" > "Selecionar destino", que abre o painel "selecionar um recurso" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definir a condição de alerta" > "adicionar critérios", que abre o painel "configurar lógica de sinal"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Painel "configurar tipo de sinal"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Defina os detalhes do alerta.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

1. Defina o grupo de ações.

    ![Criar uma regra, com "novo grupo de ações" realçado](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Criar um novo grupo de ação](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurar email, SMS, push e voz](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definir um grupo de ação](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passos seguintes
[Monitorar e gerenciar pipelines programaticamente](monitor-programmatically.md)
