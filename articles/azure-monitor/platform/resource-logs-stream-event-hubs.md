---
title: Transmitir logs da plataforma Azure para um hub de eventos
description: Saiba como transmitir logs de recursos do Azure para um hub de eventos para enviar dados a sistemas externos, como SIEMs de terceiros e outras soluções do log Analytics.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 00dcc1c1a1d823ab0f2497e47641916d391ee37b
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750350"
---
# <a name="stream-azure-platform-logs-to-azure-event-hubs"></a>Transmitir logs da plataforma Azure para os hubs de eventos do Azure
[Os logs de plataforma](platform-logs-overview.md) no Azure, incluindo logs de recursos e log de atividades do Azure, fornecem informações detalhadas de diagnóstico e auditoria para recursos do Azure e a plataforma do Azure da qual dependem.  Este artigo descreve os logs da plataforma de streaming para os hubs de eventos para enviar dados a sistemas externos, como SIEMs de terceiros e outras soluções do log Analytics.


## <a name="what-you-can-do-with-platform-logs-sent-to-an-event-hub"></a>O que você pode fazer com os logs de plataforma enviados para um hub de eventos
Transmita logs da plataforma no Azure para os hubs de eventos para fornecer a seguinte funcionalidade:

* **Transmita logs para sistemas de registro em log e telemetria de terceiros** – transmita todos os seus logs de plataforma para um único Hub de eventos para canalizar dados de log para uma ferramenta Siem ou log Analytics de terceiros.
  
* **Crie uma plataforma de registro em log e telemetria personalizada** – a natureza altamente escalonável de publicação-assinatura dos hubs de eventos permite que você inscreva com flexibilidade os logs da plataforma em uma plataforma teletry personalizada. Consulte [projetando e dimensionando uma plataforma de telemetria de escala global nos hubs de eventos do Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.

* **Exibir a integridade do serviço transmitindo dados para Power bi** – use os hubs de eventos, Stream Analytics e Power bi para transformar os dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Consulte [Stream Analytics e Power bi: um painel de análise em tempo real para dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para obter detalhes sobre essa solução.

    O código SQL a seguir é um exemplo de Stream Analytics consulta que você pode usar para analisar todos os dados de log em uma tabela Power BI:
    
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
Você precisará [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se você já tiver uma configuração de diagnóstico usando esse namespace de hubs de eventos, esse Hub de eventos será reutilizado.

A política de acesso compartilhado para o namespace define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciar, enviar e escutar. Você pode criar ou modificar políticas de acesso compartilhado no portal do Azure na guia Configurar para seu namespace de hubs de eventos.

Para atualizar a configuração de diagnóstico para incluir streaming, você deve ter a permissão ListKey nessa regra de autorização de hubs de eventos. O namespace de hubs de eventos não precisa estar na mesma assinatura que a assinatura que está emitindo logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas e ambas as assinaturas estejam no mesmo locatário do AAD.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Envie os logs da plataforma para um hub de eventos e outros destinos criando uma configuração de diagnóstico para um recurso do Azure. Consulte [criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.

## <a name="collect-data-from-compute-resources"></a>Coletar dados de recursos de computação
As configurações de diagnóstico coletarão logs de recursos para recursos de computação do Azure como qualquer outro recurso, mas não seu sistema operacional convidado ou cargas de trabalho. Para coletar esses dados, instale o [agente de log Analytics](log-analytics-agent.md). 


## <a name="consuming-log-data-from-event-hubs"></a>Consumindo dados de log dos hubs de eventos
Os logs de plataforma dos hubs de eventos são consumidos no formato JSON com os elementos na tabela a seguir.

| Nome do Elemento | Descrição |
| --- | --- |
| grava |Uma matriz de todos os eventos de log nesta carga. |
| hora |Hora em que o evento ocorreu. |
| categoria |Categoria de log para este evento. |
| resourceId |ID de recurso do recurso que gerou este evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível de evento de log. |
| propriedades |Propriedades do evento. Elas irão variar para cada serviço do Azure, conforme [ ]()descrito em. |


Veja a seguir exemplos de dados de saída dos hubs de eventos para um log de recursos:

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

* [Leia mais sobre os logs de recursos](platform-logs-overview.md).
* [Crie a configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md).
* [Transmita logs de Azure Active Directory com Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Introdução aos hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

