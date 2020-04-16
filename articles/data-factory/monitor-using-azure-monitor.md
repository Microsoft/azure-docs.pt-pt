---
title: Monitorize fábricas de dados utilizando o Monitor Azure
description: Aprenda a utilizar o Monitor Azure para monitorizar os oleodutos /Azure Data Factory, permitindo registos de diagnóstico com informações da Data Factory.
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
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419464"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Alertar e monitorizar as fábricas de dados utilizando o Monitor Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As aplicações em nuvem são complexas e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que as suas aplicações permanecem a funcionar em estado saudável. Os monitores também ajudam a evitar potenciais problemas e a resolver problemas passados.

Pode utilizar dados de monitorização para obter informações profundas sobre as suas aplicações. Este conhecimento ajuda-o a melhorar o desempenho e a manutenção da aplicação. Também o ajuda a automatizar ações que de outra forma requerem intervenção manual.

O Azure Monitor fornece métricas e registos de infraestrutura de nível base para a maioria dos serviços Azure. Os registos de diagnóstico azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. E a Azure Data Factory escreve registos de diagnóstico no Monitor.

Para mais detalhes, consulte a [visão geral do Monitor Azure.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)

## <a name="keeping-azure-data-factory-data"></a>Mantendo os dados da Fábrica de Dados Azure

A Fábrica de Dados armazena dados de gasodutos por apenas 45 dias. Utilize o Monitor se pretender manter esses dados por mais tempo. Com o Monitor, pode encaminhar registos de diagnóstico para análise. Também pode mantê-los numa conta de armazenamento para que tenha informações de fábrica para a duração escolhida.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

* Guarde os seus registos de diagnóstico para uma conta de armazenamento para auditoria ou inspeção manual. Pode utilizar as definições de diagnóstico para especificar o tempo de retenção em dias.
* Transmita os registos para os Hubs de Eventos Azure. Os registos tornam-se entrada para um serviço parceiro ou para uma solução de análise personalizada como o Power BI.
* Analise os registos com log Analytics.

Pode utilizar uma conta de armazenamento ou um espaço de nome sem nome do site event-hub que não esteja na subscrição do recurso que emite registos. O utilizador que configura a definição deve ter acesso adequado ao controlo de acesso baseado em funções (RBAC) a ambas as subscrições.

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico

### <a name="diagnostic-settings"></a>Definições de diagnóstico

Utilize definições de diagnóstico para configurar registos de diagnóstico para recursos não computacionais. As definições para um controlo de recursos têm as seguintes funcionalidades:

* Especificam para onde são enviados registos de diagnóstico. Exemplos incluem uma conta de armazenamento Azure, um hub de eventos Azure, ou registos monitor.
* Especificam quais as categorias de registo enviadas.
* Especificam quanto tempo cada categoria de registo deve ser mantida numa conta de armazenamento.
* Uma retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas armazenar registos numa conta de armazenamento é desativada, as políticas de retenção não têm qualquer efeito. Por exemplo, esta condição pode acontecer quando apenas os Centros de Eventos ou as opções de registos do Monitor são selecionadas.
* As políticas de retenção são aplicadas por dia. A fronteira entre dias ocorre à meia-noite Hora Universal Coordenada (UTC). No final de um dia, os registos dos dias que estão para além da política de retenção são eliminados. Por exemplo, se tiver uma política de retenção de um dia, no início de hoje os registos de antes de ontem são apagados.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Ativar registos de diagnóstico através da API REST Do Monitor Azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Criar ou atualizar uma definição de diagnóstico na API do Monitor REST

##### <a name="request"></a>Pedir

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua-a `{resource-id}` com a identificação do recurso para o qual pretende editar as definições de diagnóstico. Para mais informações, consulte [Utilizar grupos de Recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Coloque `Content-Type` o `application/json`cabeçalho para .
* Detete o cabeçalho de autorização para o token web da JSON que obteve do Azure Ative Directory (Azure AD). Para mais informações, consulte [pedidos de autenticação](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Corpo

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
| **armazenamentoAccountId** |String | A identificação de recursos da conta de armazenamento para a qual pretende enviar registos de diagnóstico. |
| **serviçoBusRuleId** |String | A regra do ônibus de serviço identifica o espaço de nome do ônibus de serviço no qual você quer ter Hubs de evento criados para streaming de registos de diagnóstico. O ID da `{service bus resource ID}/authorizationrules/{key name}`regra tem o formato .|
| **workspaceId** | Tipo complexo | Uma variedade de grãos métricos do tempo e suas políticas de retenção. O valor desta propriedade está vazio. |
|**métricas**| Valores parâmetros do gasoduto a serem passados para o gasoduto invocado| Um objeto JSON que mapeia nomes de parâmetros para valores de argumento. |
| **troncos**| Tipo complexo| O nome de uma categoria de registo de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registo de diagnóstico para um recurso, execute uma operação de definições de diagnóstico GET. |
| **categoria**| String| Uma série de categorias de registos e as suas políticas de retenção. |
| **timeGrain** | String | A granularidade das métricas, que são capturadas no formato de duração ISO 8601. O valor do `PT1M`imóvel deve ser, o que especifica um minuto. |
| **habilitado**| Booleano | Especifica se a recolha da métrica ou da categoria de registo está ativada para este recurso. |
| **retençãoPolítica**| Tipo complexo| Descreve a política de retenção para uma categoria métrica ou de registo. Esta propriedade é usada apenas para contas de armazenamento. |
|**dias**| int| O número de dias para manter as métricas ou registos. Se o valor da propriedade for 0, os registos são mantidos para sempre. Esta propriedade é usada apenas para contas de armazenamento. |

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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obtenha informações sobre as definições de diagnóstico na API do Monitor REST

##### <a name="request"></a>Pedir

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* Substitua-a `{resource-id}` com a identificação do recurso para o qual pretende editar as definições de diagnóstico. Para mais informações, consulte [Utilizar grupos de Recursos para gerir os seus recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Coloque `Content-Type` o `application/json`cabeçalho para .
* Detete o cabeçalho de autorização para um token web Da JSON que obteve do Azure AD. Para mais informações, consulte [pedidos de autenticação](../active-directory/develop/authentication-scenarios.md).

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
Para mais informações, consulte [Definições de Diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Esquema de registos e eventos

### <a name="monitor-schema"></a>Monitorizar esquema

#### <a name="activity-run-log-attributes"></a>Atributos de registo de execução de atividade

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividade, delineie o valor da propriedade para 4. | `4` |
| **coralaid** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**atividadeRunId**| String| A identificação da atividade. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| A identificação do oleoduto. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**recursosId**| String | A identificação associada ao recurso da fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desloque `ActivityRuns`o valor da propriedade para . | `ActivityRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desloque `Informational`o valor da propriedade para . | `Informational` |
|**operaçãoNome**| String | O nome da atividade com o seu estatuto. Se a atividade for o batimento `MyActivity -`cardíaco inicial, o valor da propriedade é . Se a atividade for o batimento `MyActivity - Succeeded`cardíaco final, o valor da propriedade é . | `MyActivity - Succeeded` |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**atividadeNome**| String | O nome da atividade. | `MyActivity` |
|**começar**| String | O tempo de início da atividade funciona no formato TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`|
|**fim**| String | O tempo final da atividade é executado no formato TIMEPAN UTC. Se o registo de diagnóstico mostrar que uma atividade começou mas ainda não terminou, o valor da propriedade é `1601-01-01T00:00:00Z`. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributos de registo de pipeline

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividade, delineie o valor da propriedade para 4. | `4` |
| **coralaid** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| A identificação do oleoduto. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**recursosId**| String | A identificação associada ao recurso da fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desloque `PipelineRuns`o valor da propriedade para . | `PipelineRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desloque `Informational`o valor da propriedade para . | `Informational` |
|**operaçãoNome**| String | O nome do oleoduto juntamente com o seu estado. Após a execução do gasoduto, `Pipeline - Succeeded`o valor da propriedade é . | `MyPipeline - Succeeded`. |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**começar**| String | O tempo de início da atividade funciona no formato TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`. |
|**fim**| String | O tempo final da atividade é executado no formato TIMEPAN UTC. Se o registo de diagnóstico mostrar que uma atividade `1601-01-01T00:00:00Z`começou mas ainda não terminou, o valor da propriedade é .  | `2017-06-26T20:55:29.5007959Z` |
|**estado**| String | O estado final da corrida do oleoduto. Os possíveis `Succeeded` `Failed`valores de propriedade são e. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Atributos de registo de execução de gatilho

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividade, delineie o valor da propriedade para 4. | `4` |
| **coralaid** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z`TIMEPAN UTC . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| A identificação do gatilho. | `08587023010602533858661257311` |
|**recursosId**| String | A identificação associada ao recurso da fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desloque `PipelineRuns`o valor da propriedade para . | `PipelineRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desloque `Informational`o valor da propriedade para . | `Informational` |
|**operaçãoNome**| String | O nome do gatilho com o seu estado final, o que indica se o gatilho disparou com sucesso. Se o batimento cardíaco foi `MyTrigger - Succeeded`bem sucedido, o valor da propriedade é. | `MyTrigger - Succeeded` |
|**nome do gatilho**| String | O nome do gatilho. | `MyTrigger` |
|**gatilhoType**| String | O tipo do gatilho. Os possíveis `Manual Trigger` `Schedule Trigger`valores de propriedade são e. | `ScheduleTrigger` |
|**triggerEvent**| String | O caso do gatilho. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**começar**| String | O tempo de início do disparo do gatilho no formato TIMEPAN UTC. | `2017-06-26T20:55:29.5007959Z`|
|**estado**| String | O estado final mostrando se o gatilho disparou com sucesso. Os possíveis `Succeeded` `Failed`valores de propriedade são e. | `Succeeded`|

### <a name="log-analytics-schema"></a>Esquema de Log Analytics

O Log Analytics herda o esquema do Monitor com as seguintes exceções:

* A primeira letra de cada nome da coluna é capitalizada. Por exemplo, o nome da coluna "correlationId" no Monitor é "CorrelationId" no Log Analytics.
* Não há coluna de "Nível".
* A coluna dinâmica "propriedades" é preservada como o seguinte tipo dinâmico de bolhas JSON.

    | Coluna Azure Monitor | Coluna Log Analytics | Tipo |
    | --- | --- | --- |
    | Propriedades de $.. Propriedades do Utilizador | Propriedades do Utilizador | Dinâmica |
    | Propriedades de $.. Anotações | Anotações | Dinâmica |
    | Propriedades de $.. Entrada | Input | Dinâmica |
    | Propriedades de $.. Saída | Saída | Dinâmica |
    | Propriedades de $.. Error.errorCode | CódigoDoErro | int |
    | Propriedades de $.. Erro.mensagem | Mensagem de erro | string |
    | Propriedades de $.. Erro | Erro | Dinâmica |
    | Propriedades de $.. Predecessores | Predecessores | Dinâmica |
    | Propriedades de $.. Parâmetros | Parâmetros | Dinâmica |
    | Propriedades de $.. Parâmetros systemparâmetros | Parâmetros systemparâmetros | Dinâmica |
    | Propriedades de $.. Etiquetas | Etiquetas | Dinâmica |
    
## <a name="metrics"></a>Métricas

Com o Monitor, pode ganhar visibilidade no desempenho e saúde das suas cargas de trabalho Azure. O tipo mais importante de dados do Monitor é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos do Azure. O Monitor fornece várias formas de configurar e consumir estas métricas para monitorização e resolução de problemas.

A versão 2 da Azure Data Factory emite as seguintes métricas.

| **Métrica**           | **Nome de exibição métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Gasoduto bem sucedido executa métricas | Contagem    | Total                | O número total de gasodutos que conseguiram dentro de um minuto de janela. |
| PipelineFailedRuns   | Gasoduto falhado executa métricas    | Contagem    | Total                | O número total de gasodutos que falharam dentro de um minuto de janela.    |
| ActivitySucceededRuns | Atividade bem sucedida executa métricas | Contagem    | Total                | O número total de atividades corre que conseguiu dentro de um minuto de janela.  |
| ActivityFailedRuns   | A atividade falhada executa métricas    | Contagem    | Total                | O número total de atividades funciona que falhou dentro de um minuto de janela.     |
| TriggerSucceedruns | O gatilho bem sucedido corre métricas  | Contagem    | Total                | O número total de disparos que conseguiram dentro de um minuto de janela.   |
| TriggerFailedRuns    | O gatilho falhado corre métricas     | Contagem    | Total                | O número total de disparos que falharam dentro de uma janela de minutos.      |

Para aceder às métricas, preencha as instruções na plataforma de [dados Do Monitor Do Monitor.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)

> [!NOTE]
> Apenas são emitidos eventos de atividade e gasodutos concluídos, desencadeados e gasodutos. Em curso, as corridas de caixas de areia/depuração **não** são emitidas. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorizar métricas da Fábrica de Dados com o Monitor Azure

Pode utilizar a integração da Data Factory com o Monitor para direcionar os dados para o Monitor. Esta integração é útil nos seguintes cenários:

* Você quer escrever consultas complexas sobre um conjunto rico de métricas que é publicado pela Data Factory para Monitorizar. Pode criar alertas personalizados sobre estas consultas via Monitor.

* Quer monitorizar através das fábricas de dados. Pode encaminhar dados de várias fábricas de dados para um único espaço de trabalho do Monitor.

Para uma introdução de sete minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configure as definições de diagnóstico e o espaço de trabalho

Crie ou adicione definições de diagnóstico para a sua fábrica de dados.

1. No portal, vá ao Monitor. Selecione **Definições** > **De diagnóstico**.

1. Selecione a fábrica de dados para a qual pretende definir uma definição de diagnóstico.

1. Se não existirem definições na fábrica de dados selecionada, é-lhe pedido que crie uma definição. Selecione **Ligar os diagnósticos**.

   ![Criar uma definição de diagnóstico se não existirem definições](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se existirem definições existentes na fábrica de dados, consulte uma lista de definições já configuradas na fábrica de dados. **Selecione Adicionar definição de diagnóstico**.

   ![Adicione uma definição de diagnóstico se existirem definições](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê um nome à sua definição, selecione **Enviar para Registar Analytics**e, em seguida, selecione um espaço de trabalho a partir do Log Analytics **Workspace**.

    ![Nomeie as suas definições e selecione um espaço de trabalho de análise de registo](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Selecione **Guardar**.

Após alguns momentos, a nova configuração aparece na sua lista de definições para esta fábrica de dados. Os registos de diagnóstico são transmitidos para esse espaço de trabalho assim que os novos dados do evento são gerados. Podem decorrer até 15 minutos entre quando um evento é emitido e quando aparece no Log Analytics.

* No modo _Específico de Recursos,_ os registos de diagnóstico da Azure Data Factory fluem para _tabelas ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun_
* No modo _Diagnóstico Azure_, os registos de diagnóstico fluem para as tabelas _AzureDiagnostics_

> [!NOTE]
> Como uma tabela de log Azure não pode ter mais de 500 colunas, recomendamos vivamente que selecione o modo Específico de Recursos. Para mais informações, consulte [Log Analytics Known Limitations](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Instale a Azure Data Factory Analytics do Azure Marketplace

![Vá ao "Azure Marketplace", introduza "Analytics filter", e selecione "Azure Data Factory Analytics (Pré-visualização")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Detalhes sobre "Azure Data Factory Analytics (Pré-visualização)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selecione **Criar** e, em seguida, selecione as definições de espaço de **trabalho OMS** e **OMS**.

![Criar uma nova solução](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorizar métricas da fábrica de dados

Instalar a Azure Data Factory Analytics cria um conjunto de visualizações padrão para que as seguintes métricas fiquem ativadas:

- ADF Runs - 1) Pipeline Runs by Data Factory
 
- ADF Runs- 2) Atividade saem por Fábrica de Dados

- ADF Runs - 3) Trigger Runs by Data Factory

- Erros ADF - 1) Top 10 Erros de Pipeline por Fábrica de Dados

- Erros ADF - 2) Top 10 De Atividade saem por Fábrica de Dados

- Erros ADF - 3) Top 10 Erros de gatilho por Fábrica de Dados

- Estatísticas ADF - 1) Atividade saem por tipo

- Estatísticas ADF - 2) Trigger Runs by Type

- Estatísticas ADF - 3) Duração do Gasoduto Max

![Janela com "Livros de Trabalho (Pré-visualização)" e "AzureDataFactoryAnalytics" em destaque](media/data-factory-monitor-oms/monitor-oms-image6.png)

Pode visualizar as métricas anteriores, olhar para as consultas por trás destas métricas, editar as consultas, criar alertas e tomar outras ações.

![Representação gráfica do gasoduto é executado por fábrica de dados"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> O Azure Data Factory Analytics (Pré-visualização) envia registos de diagnóstico para tabelas de destino _específicas do Recurso._ Pode escrever consultas contra as seguintes tabelas: _ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun_.

## <a name="alerts"></a>Alertas

Inscreva-se no portal Azure e selecione **Monitor** > **Alerts** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar Alertas

1. Selecione **+ Nova regra de Alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

1. Defina a condição de alerta.

    > [!NOTE]
    > Certifique-se de selecionar **Tudo** no Filtro por lista de desistente do **tipo de recurso.**

    !["Definir condição de alerta" > "Selecione o alvo", que abre o painel "Selecione um recurso" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definir condição de alerta" >" Adicione critérios", que abre o painel "Configure signal logic"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Painel "Configurar tipo de sinal"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Defina os detalhes do alerta.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

1. Defina o grupo de ação.

    ![Criar uma regra, com "New Action group" em destaque](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Criar um novo grupo de ação](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configure e-mail, SMS, push e voz](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Defina um grupo de ação](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passos seguintes
[Monitorizar e gerir os gasodutos programáticamente](monitor-programmatically.md)
