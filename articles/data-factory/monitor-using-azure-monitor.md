---
title: Monitorar fábricas de dados usando Azure Monitor | Microsoft Docs
description: Saiba como usar Azure Monitor para monitorar pipelines de Data Factory habilitando logs de diagnóstico com informações de Azure Data Factory.
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
ms.openlocfilehash: 6bad74d33f5d50bb7a35de69927bf97daad07798
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326844"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Alertar e monitorar fábricas de dados usando Azure Monitor
Os aplicativos de nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça em funcionamento em um estado íntegro. Ele também ajuda a afastarr problemas em potencial ou a solucionar problemas anteriores. Além disso, você pode usar dados de monitoramento para obter informações aprofundadas sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção do aplicativo, ou automatizar ações que, caso contrário, exigirão intervenção manual.

Azure Monitor fornece logs e métricas de infraestrutura de nível base para a maioria dos serviços no Microsoft Azure. Para obter detalhes, consulte [visão geral de monitoramento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). Os logs de diagnóstico do Azure são logs emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. Data Factory gera logs de diagnóstico no Azure Monitor.

## <a name="persist-data-factory-data"></a>Persistir dados de Data Factory
Data Factory só armazena dados de execução de pipeline por 45 dias. Se você quiser manter os dados de execução do pipeline por mais de 45 dias, usando Azure Monitor, não poderá apenas rotear os logs de diagnóstico para análise, você pode mantê-los em uma conta de armazenamento para que você tenha informações de fábrica pela duração da sua escolha.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Salve-os em uma **conta de armazenamento** para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as configurações de diagnóstico.
* Transmita-os para os **hubs de eventos** para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como Power bi.
* Analisá-los com **log Analytics**

Você pode usar uma conta de armazenamento ou um namespace de Hub de eventos que não esteja na mesma assinatura que o recurso que está emitindo logs. O usuário que define a configuração deve ter o acesso RBAC (controle de acesso baseado em função) apropriado para ambas as assinaturas.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

### <a name="diagnostic-settings"></a>Configurações de diagnóstico
Os logs de diagnóstico para recursos que não são de computação são configurados usando as configurações de diagnóstico. Configurações de diagnóstico para um controle de recurso:

* Onde os logs de diagnóstico são enviados (conta de armazenamento, hubs de eventos ou logs de Azure Monitor).
* Quais categorias de log são enviadas.
* Quanto tempo cada categoria de log deve ser mantida em uma conta de armazenamento.
* A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
* Se as políticas de retenção forem definidas, mas o armazenamento de logs em uma conta de armazenamento estiver desabilitado (por exemplo, somente as opções hubs de eventos ou logs de Azure Monitor são selecionadas), as políticas de retenção não terão nenhum efeito.
* Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Habilitar logs de diagnóstico por meio de APIs REST

Criar ou atualizar uma configuração de diagnóstico na API REST do Azure Monitor

**Pedido**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers** (Cabeçalhos)
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID de recurso do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações sobre [como usar grupos de recursos para gerenciar os recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Defina o `Content-Type` cabeçalho como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON obtido de Azure Active Directory. Para obter mais informações, consulte [Autenticando solicitações](../active-directory/develop/authentication-scenarios.md).

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
| storageAccountId |String | A ID de recurso da conta de armazenamento para a qual você deseja enviar os logs de diagnóstico |
| serviceBusRuleId |String | A ID da regra de barramento de serviço do namespace do barramento de serviço no qual você gostaria que os hubs de eventos fossem criados para transmitir os logs de diagnóstico. A ID da regra é do formato: "{ID do recurso do barramento de serviço}/authorizationrules/{nome da sessão}".|
| workspaceId | Tipo complexo | Matriz de detalhamentos de tempo de métrica e suas políticas de retenção. Atualmente, essa propriedade está vazia. |
|metrics| Valores de parâmetro da execução do pipeline a serem passados para o pipeline invocado| Um nome de parâmetro de mapeamento de objeto JSON para valores de argumento |
| logs| Tipo complexo| Nome de uma categoria de log de diagnóstico para um tipo de recurso. Para obter a lista de categorias de log de diagnóstico para um recurso, primeiro execute uma operação obter configurações de diagnóstico. |
| category| String| Matriz de categorias de log e suas políticas de retenção |
| timeGrain | String | A granularidade das métricas que são capturadas no formato de duração ISO 8601. Deve ser PT1M (um minuto)|
| enabled| Booleano | Especifica se a coleção dessa métrica ou categoria de log está habilitada para este recurso|
| retentionPolicy| Tipo complexo| Descreve a política de retenção para uma métrica ou categoria de log. Usado somente para a opção de conta de armazenamento.|
| days| Int| Número de dias para manter as métricas ou os logs. Um valor de 0 retém os logs indefinidamente. Usado somente para a opção de conta de armazenamento. |

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

Obter informações sobre a configuração de diagnóstico na API REST do Azure Monitor

**Pedido**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Headers** (Cabeçalhos)
* Substitua `{api-version}` por `2016-09-01`.
* Substitua `{resource-id}` pela ID de recurso do recurso para o qual você deseja editar as configurações de diagnóstico. Para obter mais informações sobre como usar grupos de recursos para gerenciar os recursos do Azure.
* Defina o `Content-Type` cabeçalho como `application/json`.
* Defina o cabeçalho de autorização para um token Web JSON obtido de Azure Active Directory. Para obter mais informações, consulte Autenticando solicitações.

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
[Mais informações aqui](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Esquema de logs & eventos

### <a name="azure-monitor-schema"></a>Esquema de Azure Monitor

#### <a name="activity-run-logs-attributes"></a>Atributos de logs de execução de atividade

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
| Nível |Cadeia | Nível dos logs de diagnóstico. O nível 4 sempre é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia | ID exclusiva para acompanhar uma solicitação específica de ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| Cadeia| ID da execução da atividade | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| Cadeia| ID da execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID de recurso associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia | Categoria de logs de diagnóstico. Defina essa propriedade como "ActivityRuns" | `ActivityRuns` |
|level| Cadeia | Nível dos logs de diagnóstico. Defina essa propriedade como "informativa" | `Informational` |
|operationName| Cadeia |Nome da atividade com status. Se o status for a pulsação inicial, será `MyActivity -`. Se o status for a pulsação final, ele `MyActivity - Succeeded` será com o status final | `MyActivity - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|activityName| Cadeia | Nome da atividade | `MyActivity` |
|start| Cadeia | Início da execução da atividade em TimeSpan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia | Extremidades da execução da atividade em TimeSpan, formato UTC. Se a atividade ainda não tiver sido encerrada (log de diagnóstico para uma atividade iniciando), um `1601-01-01T00:00:00Z` valor padrão de será definido.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Atributos de logs de execução de pipeline

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
| Nível |Cadeia | Nível dos logs de diagnóstico. O nível 4 é o caso para logs de execução de atividade. | `4`  |
| correlationId |Cadeia | ID exclusiva para acompanhar uma solicitação específica de ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| Cadeia| ID da execução do pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| Cadeia | ID de recurso associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia | Categoria de logs de diagnóstico. Defina essa propriedade como "PipelineRuns" | `PipelineRuns` |
|level| Cadeia | Nível dos logs de diagnóstico. Defina essa propriedade como "informativa" | `Informational` |
|operationName| Cadeia |Nome do pipeline com status. "Pipeline-bem-sucedido" com status final quando a execução do pipeline é concluída| `MyPipeline - Succeeded` |
|pipelineName| Cadeia | Nome do pipeline | `MyPipeline` |
|start| Cadeia | Início da execução da atividade em TimeSpan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| Cadeia | Fim das execuções de atividade em TimeSpan, formato UTC. Se a atividade ainda não tiver sido encerrada (log de diagnóstico para uma atividade iniciando), um `1601-01-01T00:00:00Z` valor padrão de será definido.  | `2017-06-26T20:55:29.5007959Z` |
|status| Cadeia | Status final da execução do pipeline (com êxito ou falha) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Atributos de logs de execução de gatilho

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
| Nível |Cadeia | Nível dos logs de diagnóstico. Defina como nível 4 para logs de execução de atividade. | `4`  |
| correlationId |Cadeia | ID exclusiva para acompanhar uma solicitação específica de ponta a ponta | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | Cadeia | Hora do evento em TimeSpan, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| Cadeia| ID da execução do gatilho | `08587023010602533858661257311` |
|resourceId| Cadeia | ID de recurso associado para o recurso de data factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| Cadeia | Categoria de logs de diagnóstico. Defina essa propriedade como "PipelineRuns" | `PipelineRuns` |
|level| Cadeia | Nível dos logs de diagnóstico. Defina essa propriedade como "informativa" | `Informational` |
|operationName| Cadeia |Nome do gatilho com status final se ele foi acionado com êxito. "Mytrigger-Succeeded" se a pulsação tiver sido bem-sucedida| `MyTrigger - Succeeded` |
|triggerName| Cadeia | Nome do gatilho | `MyTrigger` |
|triggerType| Cadeia | Tipo do gatilho (gatilho manual ou gatilho de agendamento) | `ScheduleTrigger` |
|triggerEvent| Cadeia | Evento do gatilho | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| Cadeia | Início do acionamento do gatilho em TimeSpan, formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| Cadeia | Status final de se o gatilho foi disparado com êxito (com êxito ou falha) | `Succeeded`|

### <a name="log-analytics-schema"></a>Esquema de Log Analytics

Log Analytics herda o esquema do Azure Monitor com as seguintes exceções:

* A primeira letra em cada nome de coluna será capitalizada, por exemplo  , CorrelationId em Azure monitor será CorrelationId em log Analytics.
* O *nível* de coluna será Descartado.
* As *Propriedades* de coluna dinâmicas serão preservadas como o tipo de blob JSON dinâmico abaixo:

    | Azure Monitor coluna | Log Analytics coluna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dinâmica |
    | $. Properties. Anotações | Anotações | Dinâmica |
    | $. Properties. Entrada | Input | Dinâmica |
    | $. Properties. Der | Output | Dinâmica |
    | $. Properties. Erro. errorCode | Código de erro | int |
    | $. Properties. Erro. mensagem | ErrorMessage | Cadeia de caracteres |
    | $. Properties. Ao | Erro | Dinâmica |
    | $. Properties. Predecessores | Predecessores | Dinâmica |
    | $. Properties. Parâmetro | Parâmetros | Dinâmica |
    | $. Properties. Sistemaparameters | Sistemaparameters | Dinâmica |
    | $. Properties. Sinalizadores | Tags | Dinâmica |
    
## <a name="metrics"></a>Métricas

Azure Monitor permite que você consuma a telemetria para obter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir essas métricas para monitoramento e solução de problemas.

ADFV2 emite as seguintes métricas:

| **Métricas**           | **Nome de exibição da métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Métricas de execuções de pipeline com êxito | Count    | Total                | O total de pipelines é executado com êxito em uma janela de minuto |
| PipelineFailedRuns   | Métricas de execuções de pipeline com falha    | Count    | Total                | Total de execuções de pipelines com falha em uma janela de minuto    |
| ActivitySucceededRuns | Métricas de execuções de atividades bem-sucedidas | Count    | Total                | Total de execuções de atividade com êxito em uma janela de minuto  |
| ActivityFailedRuns   | Métricas de execuções de atividade com falha    | Count    | Total                | Total de execuções de atividade com falha em uma janela de minuto     |
| TriggerSucceededRuns | Métricas de execuções de gatilho com êxito  | Count    | Total                | O total de execuções do gatilho foi bem-sucedido em uma janela de minuto   |
| TriggerFailedRuns    | Métricas de execuções de gatilho com falha     | Count    | Total                | Falha na execução total do gatilho dentro de uma janela de minuto      |

Para acessar as métricas, conclua as instruções em [Azure monitor plataforma de dados](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorar Data Factory métricas com Azure Monitor

Você pode usar a integração de Azure Data Factory com Azure Monitor para rotear dados para Azure Monitor. Essa integração é útil nos seguintes cenários:

1.  Você deseja escrever consultas complexas em um conjunto avançado de métricas que é publicado pelo Data Factory para Azure Monitor. Você também pode criar alertas personalizados nessas consultas por meio de Azure Monitor.

2.  Você deseja monitorar entre fábricas de dados. Você pode rotear dados de várias fábricas de dados para um único espaço de trabalho de Azure Monitor.

Para uma introdução e uma demonstração desse recurso de sete minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Definir configurações de diagnóstico e espaço de trabalho

Habilite as configurações de diagnóstico para seu data factory.

1. No portal, navegue até Azure Monitor e clique em **configurações de diagnóstico** no menu **configurações** .

2. Selecione o data factory para o qual você gostaria de definir uma configuração de diagnóstico.
    
3. Se não houver configurações no data factory que você selecionou, será solicitado que você crie uma configuração. Clique em "Ativar diagnósticos."

   ![Adicionar definição de diagnóstico - sem definições existentes](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver configurações existentes no data factory, você verá uma lista de configurações já configuradas neste data factory. Clique em "Adicionar definição de diagnóstico".

   ![Adicionar definição de diagnóstico - existente definições](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Dê um nome à sua configuração e marque a caixa **Enviar para log Analytics**e, em seguida, selecione um espaço de trabalho log Analytics.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Clique em **Guardar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações dessa data factory e os logs de diagnóstico serão transmitidos para esse espaço de trabalho assim que novos dados de evento forem gerados. Pode haver até 15 minutos entre o momento em que um evento é emitido e quando ele aparece no Log Analytics.

> [!NOTE]
> Devido a um limite explícito de uma determinada tabela de log do Azure não ter mais de 500 colunas, **é altamente recomendável usar o modo específico do recurso**. Para obter mais informações, consulte [log Analytics limitações conhecidas](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instalar a análise de Azure Data Factory do Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Clique em **criar** e selecione as configurações de espaço de trabalho e espaço de trabalho.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorar Data Factory métricas

A instalação do **Azure data Factory Analytics** cria um conjunto padrão de exibições que habilita as seguintes métricas:

- Execuções do ADF-1) execuções de pipeline por Data Factory

- Execuções do ADF-2) execuções de atividade por Data Factory

- Execuções do ADF-3) execuções de gatilho por Data Factory

- Erros do ADF-1) 10 principais erros de pipeline por Data Factory

- Erros do ADF-2) primeiras 10 execuções de atividade por Data Factory

- Erros do ADF-3) 10 principais erros de gatilho por Data Factory

- Estatísticas do ADF-1) execuções de atividade por tipo

- Estatísticas do ADF-2) execuções de gatilho por tipo

- Estatísticas do ADF-3) duração máxima de execuções de pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Você pode visualizar as métricas acima, examinar as consultas por trás dessas métricas, editar as consultas, criar alertas e assim por diante.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Alertas

Entre no portal do Azure e clique em **monitorar** > **alertas** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar alertas

1.  Clique em **+ nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Defina a **condição de alerta**.

    > [!NOTE]
    > Certifique-se de selecionar **tudo** no **tipo de recurso filtrar por**.

    ![Condição de alerta, tela 1 de 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Condição de alerta, tela 2 de 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Condição de alerta, tela 3 de 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Defina os **detalhes do alerta**.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Defina o **grupo de ações**.

    ![Grupo de ações, tela 1 de 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Grupo de ações, tela 2 de 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Grupo de ações, tela 3 de 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Grupo de ações, tela 4 de 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passos Seguintes

Confira o artigo [monitorar e gerenciar pipelines](monitor-programmatically.md) programaticamente para saber mais sobre como monitorar e gerenciar pipelines com código.
