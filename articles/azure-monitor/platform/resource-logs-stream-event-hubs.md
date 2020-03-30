---
title: Stream Azure logs plataforma para um hub de eventos
description: Saiba como transmitir registos de recursos do Azure para um centro de eventos para enviar dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 72341b6da0068ba4b7e3f53b08e6015cafb70f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658919"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Stream Azure logs plataforma para Azure Event Hubs
[Os registos da plataforma](platform-logs-overview.md) no Azure, incluindo registos de registos de atividades do Azure e de recursos, fornecem informações detalhadas de diagnóstico e auditoria para os recursos do Azure e da plataforma Azure de que dependem.  Este artigo descreve registos de plataformas de streaming em centros de eventos para enviar dados para sistemas externos, tais como SIEMs de terceiros e outras soluções de análise de registo.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>O que pode fazer com os registos da plataforma enviados para um centro de eventos
Stream platform logs in Azure to event hubs to providenciando a seguinte funcionalidade:

* **Transmita registos para sistemas de registo e telemetria** de terceiros – Transmita todos os registos da sua plataforma para um único centro de eventos para canalizar dados de registo para uma ferramenta de análise de registo siEM ou registo de terceiros.
  
* **Construa uma plataforma personalizada de telemetria e registo –** A natureza altamente escalável de centros de eventos permite-lhe ingerir flexionavelmente os registos de plataformas numa plataforma de telemetria personalizada. Consulte [design e dimensionamento de uma plataforma de telemetria](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) de escala global em Hubs de eventos Azure para mais detalhes.

* Consulte a **saúde do serviço através do streaming de dados para Power BI** – Use Event Hubs, Stream Analytics e Power BI para transformar os seus dados de diagnóstico em informações quase em tempo real sobre os seus serviços Azure. Consulte [stream analytics e Power BI: Um dashboard de análise em tempo real para transmitir dados](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre esta solução.

    O seguinte código SQL é uma consulta de stream analytics de amostra que pode usar para analisar todos os dados de registo numa tabela Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Pré-requisitos
Precisa criar um centro de [eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se já tiver uma definição de diagnóstico utilizando este espaço de nome sinuoso do Event Hubs, então esse centro de eventos será reutilizado.

A política de acesso partilhado para o espaço de nome define as permissões que o mecanismo de streaming tem. O streaming para centros de eventos requer permissões de Gestão, Envio e Escuta. Pode criar ou modificar políticas de acesso partilhado no portal Azure sob o separador Configure para o seu espaço de nome Sem Evento.

Para atualizar a definição de diagnóstico para incluir o streaming, deve ter a permissão ListKey nessa regra de autorização do Event Hubs. O espaço de nome sem nome do Event Hubs não tem de estar na mesma subscrição que a subscrição que está a emitir registos, desde que o utilizador que configura a definição tenha acesso rBAC adequado a ambas as subscrições e ambas as subscrições estejam no mesmo inquilino AAD.

## <a name="create-a-diagnostic-setting"></a>Criar uma definição de diagnóstico
Envie registos de plataformas para um hub de eventos e outros destinos, criando uma definição de diagnóstico para um recurso Azure. Consulte [a definição de diagnóstico Para recolher registos e métricas em Azure](diagnostic-settings.md) para obter mais detalhes.

## <a name="collect-data-from-compute-resources"></a>Recolher dados de recursos computacionais
As definições de diagnóstico recolherão registos de recursos para os recursos computacionais do Azure, como qualquer outro recurso, mas não o seu sistema operativo ou cargas de trabalho. Para recolher estes dados, instale o [agente Log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Consumir dados de registo dos centros de eventos
Os registos da plataforma dos centros de eventos são consumidos em formato JSON com os elementos na tabela seguinte.

| Nome do Elemento | Descrição |
| --- | --- |
| registos |Uma série de todos os eventos de registo nesta carga. |
| hora |Hora em que ocorreu o evento. |
| categoria |Categoria de log para este evento. |
| resourceId |Identificação de recursos do recurso que gerou este evento. |
| operationName |Nome da operação. |
| nível |Opcional. Indica o nível do evento de registo. |
| propriedades |Propriedades do evento. Estes variarão para cada serviço [ ]()Azure, conforme descrito em . |


Seguem-se os dados de saída da amostra dos Centros de Eventos para um registo de recursos:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Passos seguintes

* [Leia mais sobre registos](platform-logs-overview.md)de recursos .
* [Crie uma definição de diagnóstico para recolher registos e métricas em Azure](diagnostic-settings.md).
* [Stream Azure Ative Directory logy com Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Começar com Centros de Eventos.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

