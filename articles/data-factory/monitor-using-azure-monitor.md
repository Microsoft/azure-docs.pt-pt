---
title: Monitorize fábricas de dados usando o Azure Monitor
description: Aprenda a utilizar o Azure Monitor para monitorizar os oleodutos /Azure Data Factory, permitindo registos de diagnóstico com informações da Data Factory.
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
ms.openlocfilehash: 3f97db2e2722d16c3fa780dbe7205813c0e75420
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84655581"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorizar e alertar a fábrica de dados utilizando o Monitor Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As aplicações em nuvem são complexas e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que as suas aplicações permanecem em funcionamento em um estado saudável. Os monitores também ajudam a evitar potenciais problemas e a resolver problemas. Pode utilizar dados de monitorização para obter informações profundas sobre as suas aplicações. Este conhecimento ajuda-o a melhorar o desempenho da aplicação e a manutenção. Também ajuda a automatizar ações que de outra forma requerem intervenção manual.

O Azure Monitor fornece métricas e registos de infraestrutura de nível base para a maioria dos serviços Azure. Os registos de diagnóstico Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. A Azure Data Factory pode escrever registos de diagnóstico no Azure Monitor. Para uma introdução e demonstração de sete minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Para mais detalhes, consulte [a visão geral do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Manter as métricas da Azure Data Factory e os dados de conduta

A Data Factory armazena dados de condutas por apenas 45 dias. Utilize o Azure Monitor se quiser manter esses dados por mais tempo. Com o Monitor, pode encaminhar registos de diagnóstico para análise para vários alvos diferentes.

* **Conta de Armazenamento**: Guarde os seus registos de diagnóstico numa conta de armazenamento para auditoria ou inspeção manual. Pode utilizar as definições de diagnóstico para especificar o tempo de retenção em dias.
* **Centro de Eventos**: Transmita os registos para Azure Event Hubs. Os registos tornam-se entradas para um serviço de parceiros ou para uma solução de análise personalizada como o Power BI.
* **Log Analytics**: Analise os registos com Log Analytics. A integração da Data Factory com o Azure Monitor é útil nos seguintes cenários:
  * Você quer escrever consultas complexas sobre um rico conjunto de métricas que são publicadas pela Data Factory para monitorizar. Pode criar alertas personalizados nestas consultas através do Monitor.
  * Quer monitorizar as fábricas de dados. Pode encaminhar dados de várias fábricas de dados para um único espaço de trabalho monitor.

Também pode utilizar uma conta de armazenamento ou espaço de nome de centro de eventos que não esteja na subscrição do recurso que emite registos. O utilizador que configurar a definição deve ter acesso adequado ao controlo de acesso baseado em funções (RBAC) a ambas as subscrições.

## <a name="configure-diagnostic-settings-and-workspace"></a>Configurar configurações de diagnóstico e espaço de trabalho

Crie ou adicione definições de diagnóstico para a sua fábrica de dados.

1. No portal, vá ao Monitor. Selecione **Settings**  >  **definições de diagnóstico**.

1. Selecione a fábrica de dados para a qual pretende definir uma definição de diagnóstico.

1. Se não existirem definições na fábrica de dados selecionada, é solicitado que crie uma definição. **Selecione Ligue os diagnósticos**.

   ![Crie uma definição de diagnóstico se não existirem definições](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver definições existentes na fábrica de dados, vê uma lista de definições já configuradas na fábrica de dados. **Selecione Adicionar a definição de diagnóstico**.

   ![Adicione uma definição de diagnóstico se existirem definições](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê um nome à sua definição, selecione **Enviar para registar análises**e, em seguida, selecione um espaço de trabalho no **Log Analytics Workspace**.

    * No modo _Específico de Recursos,_ os registos de diagnóstico da Azure Data Factory fluem para as tabelas _ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun._
    * No modo _Azure-Diagnostics,_ os registos de diagnóstico fluem para a tabela _AzureDiagnostics._

   ![Nomeie as suas definições e selecione um espaço de trabalho de análise de log-analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Como uma tabela de registoS Azure não pode ter mais de 500 colunas, **recomendamos vivamente** que selecione _o modo Específico de Recursos_. Para obter mais informações, consulte [Log Analytics As limitações conhecidas.](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)

1. Selecione **Guardar**.

Após alguns momentos, a nova definição aparece na sua lista de definições para esta fábrica de dados. Os registos de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de eventos são gerados. Podem decorrer até 15 minutos entre quando um evento é emitido e quando aparece no Log Analytics.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Instalar solução Azure Data Factory Analytics a partir do Azure Marketplace

Esta solução fornece-lhe um resumo da saúde geral da sua Data Factory, com opções para perfurar detalhes e para resolver padrões de comportamento inesperados. Com vistas ricas e fora da caixa, você pode obter insights sobre o processamento chave, incluindo:

* Num resumo resumido do oleoduto de fábrica de dados, a atividade e o gatilho correm
* A capacidade de perfurar a atividade da fábrica de dados funciona por tipo
* Resumo do pipeline topo da fábrica de dados, erros de atividade

1. Vá ao **Azure Marketplace,** escolha o filtro **Analytics** e procure **a Azure Data Factory Analytics (Preview)**

   ![Vá ao "Azure Marketplace", introduza "Filtro analytics", e selecione "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Detalhes sobre **Azure Data Factory Analytics (Preview)**

   ![Detalhes sobre "Azure Data Factory Analytics (Preview)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Selecione **Criar** e, em seguida, criar ou selecionar o **espaço de trabalho do Log Analytics**.

   ![Criar uma nova solução](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorizar métricas da Fábrica de Dados

A instalação do Azure Data Factory Analytics cria um conjunto predefinido de vistas dentro da secção de livros de trabalho do espaço de trabalho do Log Analytics escolhido. Isto resulta em que as seguintes métricas ficam ativadas:

* ADF Runs - 1) Pipeline Runs by Data Factory
* ADF Executa - 2) Atividade é executado por fator de dados
* ADF Runs - 3) Trigger Runs by Data Factor
* Erros ADF - 1) Principais 10 erros de gasoduto por fábrica de dados
* Erros ADF - 2) Top 10 Atividades executados por fábrica de dados
* Erros ADF - 3) Principais 10 erros de disparo por fábrica de dados
* Estatísticas ADF - 1) Atividade é executado por tipo
* Estatísticas ADF - 2) Trigger Runs by Type
* Estatísticas ADF - 3) Duração do gasoduto Max

![Janela com "Workbooks (Preview)" e "AzureDataFactoryAnalytics" em destaque](media/data-factory-monitor-oms/monitor-oms-image6.png)

Pode visualizar as métricas anteriores, olhar para as consultas por trás destas métricas, editar as consultas, criar alertas e tomar outras ações.

![Representação gráfica do gasoduto é executado por fábrica de dados"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A Azure Data Factory Analytics (Preview) envia registos de diagnóstico para tabelas de destino _específicas de recursos._ Pode escrever consultas com as seguintes tabelas: _ADFPipelineRun,_ _ADFTriggerRun_e _ADFActivityRun_.


## <a name="data-factory-metrics"></a>Métricas da Fábrica de Dados

Com o Monitor, pode ganhar visibilidade no desempenho e saúde das suas cargas de trabalho Azure. O tipo mais importante de dados do Monitor é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos Azure. O Monitor fornece várias formas de configurar e consumir estas métricas para monitorização e resolução de problemas.

A versão 2 da Azure Data Factory emite as seguintes métricas.

| **Métrica**           | **Nome de exibição métrica**         | **Unidade** | **Tipo de agregação** | **Descrição**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Pipeline bem sucedido executa métricas | Contagem    | Total                | O número total de gasodutos que foi bem sucedido dentro de uma janela de um minuto. |
| PipelineFailedRuns   | Gasoduto falhado executa métricas    | Contagem    | Total                | O número total de gasodutos que falhou dentro de um minuto.    |
| AtividadeSSucceedEdRuns | A atividade bem sucedida executa métricas | Contagem    | Total                | O número total de operações é bem sucedido dentro de uma janela de um minuto.  |
| AtividadeFailedRuns   | Atividade falhada executa métricas    | Contagem    | Total                | O número total de atividades que falharam dentro de um minuto.     |
| TriggerSucceeddRuns | O gatilho bem sucedido executa métricas  | Contagem    | Total                | O número total de disparos que foi bem sucedido dentro de uma janela de um minuto.   |
| TriggerFailedRuns    | O gatilho falhado executa métricas     | Contagem    | Total                | O número total de disparos que falharam dentro de um minuto.      |

Para aceder às métricas, preencha as instruções na [plataforma de dados Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Apenas são emitidas atividades completas, desencadeadas e eventos de gasodutos. Em curso e as corridas de caixa de areia/depurg **não** são emitidas. 

## <a name="data-factory-alerts"></a>Alertas de fábrica de dados

Inscreva-se no portal Azure e selecione **Monitor**  >  **Alertas de Monitorização** para criar alertas.

![Alertas no menu do portal](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Criar alertas

1. Selecione **+ nova regra de alerta** para criar um novo alerta.

    ![Nova regra de alerta](media/monitor-using-azure-monitor/alerts_image4.png)

1. Defina a condição de alerta.

    > [!NOTE]
    > Certifique-se de que seleciona **All** in the Filter pela lista de down-down do tipo de **recurso.**

    !["Definir condição de alerta" > "Selecione target", que abre o painel "Selecione um recurso" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definir condição de alerta" >" Adicionar critérios", que abre o painel "Configurar lógica de sinal"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Painel "Tipo de sinal de configuração"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Defina os detalhes do alerta.

    ![Detalhes do alerta](media/monitor-using-azure-monitor/alerts_image8.png)

1. Defina o grupo de ação.

    ![Criar uma regra, com "New Action group" em destaque](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Criar um novo grupo de ação](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configure e-mail, SMS, push e voz](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definir um grupo de ação](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configurar registos de diagnóstico através da API do Monitor Azure

### <a name="diagnostic-settings"></a>Definições de diagnóstico

Utilize definições de diagnóstico para configurar registos de diagnóstico para recursos não conformes. As definições para um controlo de recursos têm as seguintes características:

* Especificam para onde são enviados registos de diagnóstico. Exemplos incluem uma conta de armazenamento Azure, um centro de eventos Azure ou registos monitor.
* Especificam quais as categorias de registos enviadas.
* Especificam quanto tempo cada categoria de registo deve ser mantida numa conta de armazenamento.
* Uma retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser de qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas armazenar registos numa conta de armazenamento é desativado, as políticas de retenção não têm efeito. Por exemplo, esta condição pode acontecer quando apenas são selecionadas as opções de Centros de Eventos ou de Registos do Monitor.
* As políticas de retenção são aplicadas por dia. A fronteira entre os dias ocorre à meia-noite Tempo Universal Coordenado (UTC). Ao fim de um dia, os registos de dias que estão para além da política de retenção são eliminados. Por exemplo, se tiver uma política de retenção de um dia, no início de hoje os registos de antes de ontem são apagados.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Ativar registos de diagnóstico através da API do Monitor Azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Criar ou atualizar uma definição de diagnóstico na API monitor REST

##### <a name="request"></a>Pedir

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* `{resource-id}`Substitua-o pelo ID do recurso para o qual pretende editar as definições de diagnóstico. Para obter mais informações, consulte [a Utilização de Grupos de Recursos para gerir os seus recursos Azure.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Desa estale o `Content-Type` cabeçalho para `application/json` .
* Desconfiem do cabeçalho de autorização para o token web JSON que obteve do Azure Ative Directory (Azure AD). Para mais informações, consulte [pedidos de autenticação.](../active-directory/develop/authentication-scenarios.md)

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
| **armazenamentoSacountId** |String | O ID de recursos da conta de armazenamento para a qual pretende enviar registos de diagnóstico. |
| **serviçoBusRuleId** |String | A regra de serviço-autocarro ID do espaço de nomes de autocarro de serviço no qual pretende ter Os Centros de Eventos criados para streaming de registos de diagnóstico. A regra ID tem o formato `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Tipo Complexo | Uma série de grãos de tempo métricos e as suas políticas de retenção. O valor desta propriedade está vazio. |
|**métricas**| Os valores parâmetros do gasoduto a passar para o gasoduto invocado| Um objeto JSON que mapeia nomes de parâmetros para valores de argumento. |
| **troncos**| Tipo Complexo| O nome de uma categoria de registo de diagnóstico para um tipo de recurso. Para obter a lista de categorias de registo de diagnóstico para um recurso, efetue uma operação de definições de diagnóstico GET. |
| **categoria**| String| Uma série de categorias de registos e as suas políticas de retenção. |
| **timeGrain** | String | A granularidade das métricas, que são capturadas no formato de duração ISO 8601. O valor da propriedade deve `PT1M` ser, que especifica um minuto. |
| **habilitado**| Booleano | Especifica se a recolha da categoria métrica ou de registo está ativada para este recurso. |
| **retençãoPolícia**| Tipo Complexo| Descreve a política de retenção para uma categoria métrica ou de registo. Esta propriedade é usada apenas para contas de armazenamento. |
|**dias**| int| O número de dias para manter as métricas ou registos. Se o valor da propriedade for 0, os registos são mantidos para sempre. Esta propriedade é usada apenas para contas de armazenamento. |

##### <a name="response"></a>Resposta

200 ok.


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Obtenha informações sobre as definições de diagnóstico na API monitor REST

##### <a name="request"></a>Pedir

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Cabeçalhos

* Substitua `{api-version}` por `2016-09-01`.
* `{resource-id}`Substitua-o pelo ID do recurso para o qual pretende editar as definições de diagnóstico. Para obter mais informações, consulte [a Utilização de Grupos de Recursos para gerir os seus recursos Azure.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Desa estale o `Content-Type` cabeçalho para `application/json` .
* Desemconfie o cabeçalho de autorização para um token web JSON que obteve do Azure AD. Para mais informações, consulte [pedidos de autenticação.](../active-directory/develop/authentication-scenarios.md)

##### <a name="response"></a>Resposta

200 ok.

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
Para obter mais informações, consulte [Definições de Diagnóstico](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Esquema de registos e eventos

### <a name="monitor-schema"></a>Esquema de monitorização

#### <a name="activity-run-log-attributes"></a>Atributos de registo executados por atividade

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividades, desa um valor de propriedade para 4. | `4` |
| **correlationId** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| A identificação da atividade funciona. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| A identificação do oleoduto. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**recursosId**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desa estava o valor da propriedade para `ActivityRuns` . | `ActivityRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desa estava o valor da propriedade para `Informational` . | `Informational` |
|**operaçãoName**| String | O nome da atividade com o seu estado. Se a atividade for o batimento cardíaco inicial, o valor da propriedade é `MyActivity -` . Se a atividade for o batimento cardíaco final, o valor da propriedade é `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**nome de atividades**| String | O nome da atividade. | `MyActivity` |
|**começar**| String | A hora de início da atividade é executado no formato UTC timepan. | `2017-06-26T20:55:29.5007959Z`|
|**fim**| String | O tempo final da atividade é executado em formato UTC timepan. Se o registo de diagnóstico mostrar que uma atividade começou mas ainda não terminou, o valor da propriedade é `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Atributos de registo executados por pipeline

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividades, desa um valor de propriedade para 4. | `4` |
| **correlationId** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| A identificação do oleoduto. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**recursosId**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desa estava o valor da propriedade para `PipelineRuns` . | `PipelineRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desa estava o valor da propriedade para `Informational` . | `Informational` |
|**operaçãoName**| String | O nome do oleoduto juntamente com o seu estado. Após o curso do gasoduto estar concluído, o valor da propriedade é `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| String | O nome do oleoduto. | `MyPipeline` |
|**começar**| String | A hora de início da atividade é executado no formato UTC timepan. | `2017-06-26T20:55:29.5007959Z`. |
|**fim**| String | O tempo final da atividade é executado em formato UTC timepan. Se o registo de diagnóstico mostrar que uma atividade começou mas ainda não terminou, o valor da propriedade é `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**estado**| String | O estado final do oleoduto. Possíveis valores de propriedade são `Succeeded` `Failed` e. | `Succeeded`|

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
| **Nível** |String | O nível dos registos de diagnóstico. Para registos de execução de atividades, desa um valor de propriedade para 4. | `4` |
| **correlationId** |String | A identificação única para rastrear um pedido particular. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tempo** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| A identificação do gatilho funciona. | `08587023010602533858661257311` |
|**recursosId**| String | O ID associado ao recurso de fábrica de dados. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**categoria**| String | A categoria dos registos de diagnóstico. Desa estava o valor da propriedade para `PipelineRuns` . | `PipelineRuns` |
|**nível**| String | O nível dos registos de diagnóstico. Desa estava o valor da propriedade para `Informational` . | `Informational` |
|**operaçãoName**| String | O nome do gatilho com o seu estado final, o que indica se o gatilho disparou com sucesso. Se o batimento cardíaco foi bem sucedido, o valor da propriedade é `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**gatilhoName**| String | O nome do gatilho. | `MyTrigger` |
|**triggerType**| String | O tipo do gatilho. Possíveis valores de propriedade são `Manual Trigger` `Schedule Trigger` e. | `ScheduleTrigger` |
|**triggerEvent**| String | O caso do gatilho. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**começar**| String | A hora de início do disparo do gatilho no formato UTC timepan. | `2017-06-26T20:55:29.5007959Z`|
|**estado**| String | O estado final mostrando se o gatilho disparou com sucesso. Possíveis valores de propriedade são `Succeeded` `Failed` e. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics herda o esquema do Monitor com as seguintes exceções:

* A primeira letra em cada nome de coluna é capitalizada. Por exemplo, o nome da coluna "correlationId" no Monitor é "CorrelationId" em Log Analytics.
* Não há nenhuma coluna "Level".
* A coluna dinâmica "propriedades" é preservada como o seguinte tipo de bolha JSON dinâmica.

    | Coluna Azure Monitor | Coluna Log Analytics | Tipo |
    | --- | --- | --- |
    | Propriedades. UserProperties | UserProperties | Dinâmica |
    | Propriedades. Anotações | Anotações | Dinâmica |
    | Propriedades. Entrada | Entrada | Dinâmica |
    | Propriedades. Saída | Saída | Dinâmica |
    | Propriedades. Erro.erroSDesco | CódigoDoErro | int |
    | Propriedades. Error.message | ErroS | string |
    | Propriedades. Erro | Erro | Dinâmica |
    | Propriedades. Antecessores | Antecessores | Dinâmica |
    | Propriedades. Parâmetros | Parâmetros | Dinâmica |
    | $.properties.SystemParameters | Sistemaparmetros | Dinâmica |
    | Propriedades. Etiquetas | Etiquetas | Dinâmica |


## <a name="next-steps"></a>Próximos passos
[Monitorize e gere os oleodutos programáticamente](monitor-programmatically.md)
