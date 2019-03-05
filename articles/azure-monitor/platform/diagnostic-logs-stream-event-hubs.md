---
title: Registos de diagnóstico do Azure Stream para um hub de eventos
description: Aprenda a transmitir em fluxo registos de diagnóstico do Azure para um hub de eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: b5299af375646e7759d0770139df2cd6d7ce105c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314083"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Registos de diagnóstico do Azure Stream para um hub de eventos
**[Registos de diagnóstico do Azure](diagnostic-logs-overview.md)**  podem ser transmitidos em fluxo em tempo real para qualquer aplicação que utilize a opção "Exportar para os Hubs de eventos" incorporada no Portal ou ao ativar o ID da regra de autorização de Hub de eventos numa definição de diagnóstico do Azure CLI do Azure ou Cmdlets do PowerShell.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que pode fazer com os registos de diagnóstico e os Hubs de eventos
Seguem-se apenas algumas maneiras, pode utilizar a capacidade de transmissão em fluxo registos de diagnóstico:

* **Stream registos para sistemas 3º de Registro em log e telemetria de terceiros** – pode transmitir todos os seus registos de diagnóstico para um hub de eventos único para dados de registo de pipe para uma ferramenta de análise de terceiros SIEM ou do registo.
* **Ver o estado de funcionamento do serviço através da transmissão em fluxo de dados de "hot path" para o Power BI** – utilizar os Hubs de eventos, o Stream Analytics e o Power BI, pode transformar facilmente seus dados de diagnóstico para uma quase informações em tempo real nos seus serviços do Azure. [Este artigo de documentação fornece uma excelente descrição geral de como configurar os Hubs de eventos, processar dados com o Stream Analytics e utilizar o Power BI como uma saída](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Aqui estão algumas dicas para obter configurado com os registos de diagnóstico:

  * Um hub de eventos para uma categoria de registos de diagnóstico é criado automaticamente quando marcar a opção no portal ou ativá-la através do PowerShell, pelo que deve selecionar o hub de eventos no espaço de nomes com o nome que começa com **insights -**.
  * O seguinte código SQL é uma consulta do Stream Analytics de exemplo que pode utilizar para analisar todos os dados de registo na uma tabela do Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Criar uma plataforma de Registro em log e telemetria personalizada** – se já tiver uma plataforma de telemetria personalizados internos ou esteja pensando em criar um, altamente dimensionável apenas a natureza de publicação-subscrição de Hubs de eventos permite ingerir forma flexível os registos de diagnóstico. [Consulte o guia de Dan Rosanova para utilizar os Hubs de eventos numa plataforma de telemetria de escala global aqui](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Ativar a transmissão em fluxo de registos de diagnóstico

Pode ativar a transmissão em fluxo de registos de diagnóstico através de programação, através do portal, ou utilizando o [APIs de REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, criar uma definição de diagnóstico em que especificar um espaço de nomes de Hubs de eventos e as categorias de registo e métricas que deseja enviar para o espaço de nomes. Um hub de eventos é criado no espaço de nomes para cada categoria de registo que ativa. Um diagnóstico **categoria de registo** é um tipo de registo que pode recolher um recurso.

> [!WARNING]
> Ativar e transmissão em fluxo registos de diagnóstico a partir dos recursos de computação (por exemplo, VMs ou recursos de infraestrutura do serviço) [requer um conjunto diferente de passos](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

O espaço de nomes não tem de estar na mesma subscrição que o recurso emite os registos, desde que o utilizador que configura a definição possui acesso RBAC adequado para as subscrições e ambas as subscrições do Event Hubs são parte do mesmo inquilino do AAD.

> [!NOTE]
> Atualmente, o envio de métricas multidimensionais através das definições de diagnóstico não é suportado. As métricas com dimensões são exportadas como métricas dimensionais simples e agregadas em valores de dimensões.
>
> *Por exemplo*: A métrica "Mensagens recebidas" num Hub de eventos pode ser explorada e representada um por nível de fila. No entanto, se for exportada através das definições de diagnóstico, a métrica será representada como todas as mensagens recebidas em todas as filas do hub do Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Registos de diagnóstico do Stream com o portal

1. No portal, navegue para o Azure Monitor e clique em **definições de diagnóstico**

    ![Secção de monitorização do Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Opcionalmente, filtrar a lista por grupo de recursos ou tipo de recurso, em seguida, clique no recurso para o qual pretende configurar uma definição de diagnóstico.

3. Se não existem definições existem no recurso que selecionou, lhe for pedido para criar uma definição. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   Se existirem definições existentes no recurso, verá uma lista das definições já configurada neste recurso. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Dê a sua definição de um nome e marque a caixa **Stream para um hub de eventos**, em seguida, selecione um espaço de nomes de Hubs de eventos.

   ![Adicionar definição de diagnóstico - existente definições](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   O espaço de nomes selecionado será em que o hub de eventos é criado (se este é os primeiro tempo transmissão em fluxo registos de diagnóstico) ou transmitidas em fluxo para (se já existem recursos que são de transmissão em fluxo dessa categoria de registo para este espaço de nomes), e a política define as permissões que o mecanismo de transmissão em fluxo tem. Hoje em dia, a transmissão em fluxo para um hub de eventos exige permissões de gerir, envio e escutar. Pode criar ou modificar as políticas de acesso partilhado do espaço de nomes dos Hubs de eventos no portal no separador Configurar para o espaço de nomes. Para atualizar uma destas definições de diagnóstico, o cliente tem de ter a permissão de ListKey na regra de autorização de Hubs de eventos. Opcionalmente, também pode especificar um nome de hub de eventos. Se especificar um nome de hub de eventos, os registos são encaminhados para esse hub de eventos, em vez de um hub de eventos criado recentemente por categoria de registo.

4. Clique em **Guardar**.

Após alguns instantes, a nova definição é apresentada na sua lista de definições para este recurso, e os registos de diagnóstico são transmitidas em fluxo para esse hub de eventos quando novos dados de evento são gerados.

### <a name="via-powershell-cmdlets"></a>Cmdlets do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para ativar a transmissão em fluxo através do [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), pode utilizar o `Set-AzDiagnosticSetting` cmdlet com estes parâmetros:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

O ID de regra de autorização de Hub de eventos é uma cadeia de caracteres com este formato: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, por exemplo, `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Atualmente não é possível selecionar um nome de hub de eventos específico com o PowerShell.

### <a name="via-azure-cli"></a>Através da CLI do Azure

Para ativar a transmissão em fluxo através do [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), pode utilizar o [az monitor diagnostic-settings criar](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) comando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Pode adicionar categorias adicionais no registo de diagnóstico através da adição de dicionários para a matriz JSON passada como o `--logs` parâmetro.

O `--event-hub-rule` argumento utiliza o mesmo formato como o ID de regra de autorização de Hub de eventos, conforme explicado para o Cmdlet do PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como posso consomem os dados de registo dos Hubs de eventos?

Segue-se dados de saída de exemplo dos Hubs de eventos:

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

| Nome do elemento | Descrição |
| --- | --- |
| registos |Uma matriz de todos os eventos de registo neste payload. |
| hora |Tempo em que ocorreu o evento. |
| categoria |Categoria de registo para este evento. |
| resourceId |ID de recurso do recurso que gerou este evento. |
| operationName |Nome da operação. |
| nível |Opcional. Indica o nível de evento do registo. |
| propriedades |Propriedades do evento. |

Pode exibir uma lista de todos os fornecedores de recursos que suportam a transmissão em fluxo aos Hubs de eventos [aqui](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Dados de Stream de recursos de computação

Também pode transmitir em fluxo registos de diagnóstico a partir dos recursos de computação com o agente do Windows Azure Diagnostics. [Veja este artigo](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md) para saber como configurar essa opção.

## <a name="next-steps"></a>Passos Seguintes

* [Registos do Azure Active Directory Stream com o Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Leia mais sobre os Registos de Diagnóstico do Azure](diagnostic-logs-overview.md)
* [Introdução ao Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

