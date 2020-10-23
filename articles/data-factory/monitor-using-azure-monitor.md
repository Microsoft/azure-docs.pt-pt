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
ms.date: 07/13/2020
ms.openlocfilehash: 6862fa6c9dfa3e8ba26d6f07dc1d9096cf16f092
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151909"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorizar e alertar a fábrica de dados utilizando o Monitor Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

As aplicações em nuvem são complexas e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que as suas aplicações permanecem em funcionamento em um estado saudável. Os monitores também ajudam a evitar potenciais problemas e a resolver problemas. Pode utilizar dados de monitorização para obter informações profundas sobre as suas aplicações. Este conhecimento ajuda-o a melhorar o desempenho da aplicação e a manutenção. Também ajuda a automatizar ações que de outra forma requerem intervenção manual.

O Azure Monitor fornece métricas e registos de infraestrutura de nível base para a maioria dos serviços Azure. Os registos de diagnóstico Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. A Azure Data Factory (ADF) pode escrever registos de diagnóstico no Azure Monitor. Para uma introdução e demonstração de sete minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Para mais informações, consulte [a visão geral do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Manter as métricas da Azure Data Factory e os dados de conduta

A Data Factory armazena dados de condutas por apenas 45 dias. Utilize o Azure Monitor se quiser manter esses dados por mais tempo. Com o Monitor, pode encaminhar registos de diagnóstico para análise para vários alvos diferentes.

* **Conta de Armazenamento**: Guarde os seus registos de diagnóstico numa conta de armazenamento para auditoria ou inspeção manual. Pode utilizar as definições de diagnóstico para especificar o tempo de retenção em dias.
* **Centro de Eventos**: Transmita os registos para Azure Event Hubs. Os registos tornam-se entradas para uma solução de análise de serviço de parceiro/personalizado como o Power BI.
* **Log Analytics**: Analise os registos com Log Analytics. A integração da Data Factory com o Azure Monitor é útil nos seguintes cenários:
  * Você quer escrever consultas complexas sobre um rico conjunto de métricas que são publicadas pela Data Factory para monitorizar. Pode criar alertas personalizados nestas consultas através do Monitor.
  * Quer monitorizar as fábricas de dados. Pode encaminhar dados de várias fábricas de dados para um único espaço de trabalho monitor.

Também pode utilizar uma conta de armazenamento ou espaço de nome de centro de eventos que não esteja na subscrição do recurso que emite registos. O utilizador que configurar a definição deve ter acesso adequado ao controlo de acesso baseado em funções (Azure RBAC) a ambas as subscrições.

## <a name="configure-diagnostic-settings-and-workspace"></a>Configurar configurações de diagnóstico e espaço de trabalho

Crie ou adicione definições de diagnóstico para a sua fábrica de dados.

1. No portal, vá ao Monitor. Selecione **Settings**  >  **definições de diagnóstico**.

1. Selecione a fábrica de dados para a qual pretende definir uma definição de diagnóstico.

1. Se não existirem definições na fábrica de dados selecionada, é solicitado que crie uma definição. **Selecione Ligue os diagnósticos**.

   ![Crie uma definição de diagnóstico se não existirem definições](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se houver definições existentes na fábrica de dados, vê uma lista de definições já configuradas na fábrica de dados. **Selecione Adicionar a definição de diagnóstico**.

   ![Adicione uma definição de diagnóstico se existirem definições](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Dê um nome à sua definição, selecione **Enviar para registar análises**e, em seguida, selecione um espaço de trabalho no **Log Analytics Workspace**.

    * No modo _Azure-Diagnostics,_ os registos de diagnóstico fluem para a tabela _AzureDiagnostics._

    * No modo _Específico de Recursos,_ os registos de diagnóstico da Azure Data Factory fluem para as seguintes tabelas:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMess_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Pode selecionar vários registos relevantes para as suas cargas de trabalho para enviar para as tabelas 'Registar Analytics'. Por exemplo, se não utilizar os Serviços de Integração do Servidor SQL (SSIS) de todo, não precisa de selecionar quaisquer registos SSIS. Se pretender registar operações de funcionação /stop/manutenção da SSIS Integration, pode selecionar registos de IR SSIS. Se invocar execuções de pacotes SSIS via T-SQL no SQL Server Management Studio (SSMS), sql Server Agent ou outras ferramentas designadas, pode selecionar registos de pacotes SSIS. Se invocar execuções de pacotes SSIS através de atividades do Pacote SSIS executadas em oleodutos ADF, pode selecionar todos os registos.

    * Se selecionar _AllMetrics,_ serão disponibilizadas várias métricas ADF para monitorizar ou elevar alertas, incluindo as métricas para a atividade da ADF, pipeline e trigger runs, bem como para operações SSIS IR e execuções de pacotes SSIS.

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

A instalação desta solução cria um conjunto predefinido de vistas dentro da secção de livros de trabalho do espaço de trabalho do Log Analytics escolhido. Como resultado, as seguintes métricas tornam-se ativadas:

* ADF Runs - 1) Pipeline Runs by Data Factory
* ADF Executa - 2) Atividades executados por fábrica de dados
* ADF Runs - 3) Trigger Runs by Data Factory
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

Estas são algumas das métricas emitidas pela versão 2 da Azure Data Factory:

| **Métrica**                           | **Nome de exibição métrica**                  | **Unidade** | **Tipo de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| AtividadeCancelledRuns                 | Atividade cancelada executa métricas           | de palavras    | Total                | O número total de operações que foram canceladas dentro de um minuto. |
| AtividadeFailedRuns                   | Atividade falhada executa métricas             | de palavras    | Total                | O número total de atividades que falharam dentro de um minuto. |
| AtividadeSSucceedEdRuns                | A atividade bem sucedida executa métricas          | de palavras    | Total                | O número total de operações é bem sucedido dentro de uma janela de um minuto. |
| PipelineCancelledRuns                 | Gasoduto cancelado executa métricas           | de palavras    | Total                | O número total de gasodutos que foram cancelados dentro de um minuto. |
| PipelineFailedRuns                   | Gasoduto falhado executa métricas             | de palavras    | Total                | O número total de gasodutos que falhou dentro de um minuto. |
| PipelineSucceededRuns                | Pipeline bem sucedido executa métricas          | de palavras    | Total                | O número total de gasodutos que foi bem sucedido dentro de uma janela de um minuto. |
| TriggerCancelledRuns                  | Gatilho cancelado executa métricas            | de palavras    | Total                | O número total de disparos que foram cancelados dentro de um minuto. |
| TriggerFailedRuns                    | O gatilho falhado executa métricas              | de palavras    | Total                | O número total de disparos que falharam dentro de um minuto. |
| TriggerSucceeddRuns                 | O gatilho bem sucedido executa métricas           | de palavras    | Total                | O número total de disparos que foi bem sucedido dentro de uma janela de um minuto. |
| SSISIntegrationRuntimeStartCancelled  | Métricas de início de tempo de integração da SSIS canceladas           | de palavras    | Total                | O número total de tempo de execução de integração SSIS que foram cancelados dentro de um minuto. |
| SSISIntegrationRuntimeStartFailed    | Métricas falhadas de início de tempo de integração do SSIS             | de palavras    | Total                | O número total de tempo de integração SSIS começa que falhou dentro de um minuto. |
| SSISIntegrationRuntimeStartSucceed | Métricas de início de tempo de integração SSIS bem sucedidas          | de palavras    | Total                | O número total de tempo de integração SSIS começa que foi bem sucedido dentro de um minuto. |
| SSISIntegrationRuntimestopStuck      | Métricas de paragem de tempo de integração SSIS stuck               | de palavras    | Total                | O número total de paragens de tempo de integração SSIS que ficaram presas dentro de uma janela de um minuto. |
| SSISIntegrationRuntimestopsucceed  | Métricas de paragem de tempo de integração SSIS bem sucedidas           | de palavras    | Total                | O número total de paragens de integração SSIS que foram bem sucedidas dentro de um minuto. |
| SSISPackageExecutionCancelled         | Métricas de execução de pacotes SSIS canceladas  | de palavras    | Total                | O número total de execuções de pacotes SSIS que foram canceladas dentro de um minuto. |
| SSISPackageExecutionFailed           | Métricas de execução de pacote ssis falhados    | de palavras    | Total                | O número total de execuções de pacotes SSIS que falharam dentro de um minuto. |
| SSISPackageExecutionSucceed        | Métricas de execução de pacote ssis bem sucedidas | de palavras    | Total                | O número total de execuções de pacotes SSIS que foram bem sucedidas dentro de um minuto. |

Para aceder às métricas, preencha as instruções na [plataforma de dados Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Apenas são emitidos eventos de atividades completas, desencadeadas e gasodutos. Em curso e as corridas de depurg **não** são emitidas. Por outro lado, são emitidos eventos de **todas as** execuções de pacotes SSIS, incluindo as que estão concluídas e em curso, independentemente dos seus métodos de invocação. Por exemplo, pode invocar execuções de pacotes em Ferramentas de Dados do Servidor SQL (SSDT) ativadas pelo Azure, via T-SQL em SSMS, SqL Server Agent ou outras ferramentas designadas, e como executados ou depurados das atividades do Pacote SSIS executar em oleodutos ADF.

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

    ![Definir um grupo de ações](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configurar registos de diagnóstico através da API do Monitor Azure

### <a name="diagnostic-settings"></a>Definições de diagnóstico

Utilize definições de diagnóstico para configurar registos de diagnóstico para recursos não computatados. As definições para um controlo de recursos têm as seguintes características:

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
|**Dias**| int| O número de dias para manter as métricas ou registos. Se o valor da propriedade for 0, os registos são mantidos para sempre. Esta propriedade é usada apenas para contas de armazenamento. |

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
| **hora** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
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
| **hora** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
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
| **hora** | String | A hora do evento no formato `YYYY-MM-DDTHH:MM:SS.00000Z` TEMPOMUC . | `2017-06-28T21:00:27.3534352Z` |
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

#### <a name="ssis-integration-runtime-log-attributes"></a>Atributos de registo de runtime de integração SSIS

Aqui estão os atributos de registo das operações de arranque/paragem/manutenção do SSIS IR.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Tipo   | Descrição                                                   | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **hora**                   | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**          | String | O nome da sua operação SSIS IR                            | `Start/Stop/Maintenance` |
| **categoria**               | String | A categoria de registos de diagnóstico                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | String | O ID único para rastrear uma determinada operação             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | String | O nome da sua ADF                                          | `MyADFv2` |
| **integraçãoRuntimeName** | String | O nome do seu SSIS IR                                      | `MySSISIR` |
| **nível**                  | String | O nível de registos de diagnóstico                                  | `Informational` |
| **resultadoType**             | String | O resultado da sua operação SSIS IR                          | `Started/InProgress/Succeeded/Failed` |
| **Mensagem**                | String | A mensagem de saída da sua operação SSIS IR                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **recursosId**             | String | O ID único do seu recurso ADF                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Atributos de registo de registo de mensagem de evento SSIS

Aqui estão os atributos de registo de condições relacionadas com mensagens de evento que são geradas por execuções de pacotes SSIS no seu SSIS IR. Transmitem informações semelhantes à tabela de contexto de mensagem de evento do [catálogo SSIS (SSISDB) ou à visão](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) que mostra valores de tempo de execução de muitas propriedades de pacotes SSIS. São geradas quando seleciona `Basic/Verbose` o nível de registo e é útil para depurar/verificar conformidade.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Tipo   | Descrição                                                          | Exemplo                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **hora**                   | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**          | String | Isto está definido para `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **categoria**               | String | A categoria de registos de diagnóstico                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | String | O ID único para rastrear uma determinada operação                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome da sua ADF                                                 | `MyADFv2` |
| **integraçãoRuntimeName** | String | O nome do seu SSIS IR                                             | `MySSISIR` |
| **nível**                  | String | O nível de registos de diagnóstico                                         | `Informational` |
| **operationId**            | String | O ID único para rastrear uma determinada operação no SSISDB          | `1` (1 significa operações relacionadas com embalagens **não** armazenadas em SSISDB/invocadas via T-SQL) |
| **contextDepth**           | String | A profundidade do contexto da sua mensagem de evento                              | `0` (0 significa o contexto antes do início da execução do pacote, 1 significa o contexto quando ocorre um erro, e aumenta à medida que o contexto está mais longe do erro) |
| **packagePath**            | String | O caminho do objeto do pacote como fonte de contexto de mensagem de evento      | `\Package` |
| **type de contexto**            | String | O tipo de objeto pacote como fonte de contexto de mensagem de evento      | `60`(ver [mais tipos de contexto)](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks) |
| **nome de contextoSourceName**      | String | O nome do objeto do pacote como fonte de contexto de mensagem de evento      | `MyPackage` |
| **contextSourceId**        | String | O ID único do objeto do pacote como fonte de contexto de mensagem de evento | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **nome de propriedade**           | String | O nome da propriedade do pacote para a fonte de contexto de mensagem do seu evento   | `DelayValidation` |
| **propriedadeValue**          | String | O valor da propriedade do pacote para a fonte de contexto de mensagem do seu evento  | `False` |
| **recursosId**             | String | O ID único do seu recurso ADF                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Atributos de registo de mensagens de evento SSIS

Aqui estão os atributos de registo de mensagens de evento que são geradas por execuções de pacotes SSIS no seu SSIS IR. Transmitem informações semelhantes à [tabela de mensagens de evento SSISDB ou visualização](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) que mostra o texto/metadados detalhados das mensagens de evento. São gerados em qualquer nível de registo, `None` exceto.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Propriedade                   | Tipo   | Descrição                                                        | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **hora**                   | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**          | String | Isto está definido para `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **categoria**               | String | A categoria de registos de diagnóstico                                    | `SSISPackageEventMessages` |
| **correlationId**          | String | O ID único para rastrear uma determinada operação                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome da sua ADF                                               | `MyADFv2` |
| **integraçãoRuntimeName** | String | O nome do seu SSIS IR                                           | `MySSISIR` |
| **nível**                  | String | O nível de registos de diagnóstico                                       | `Informational` |
| **operationId**            | String | O ID único para rastrear uma determinada operação no SSISDB        | `1` (1 significa operações relacionadas com embalagens **não** armazenadas em SSISDB/invocadas via T-SQL) |
| **hora da mensagem**            | String | O momento em que a sua mensagem de evento é criada em formato UTC          | `2017-06-28T21:00:27.3534352Z` |
| **mensagemType**            | String | O tipo da sua mensagem de evento                                     | `70`(ver [mais tipos de mensagens)](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks) |
| **mensagemSourceType**      | String | O tipo de origem da sua mensagem de evento                              | `20`(ver [mais tipos de origem de mensagens)](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks) |
| **Mensagem**                | String | O texto da sua mensagem de evento                                     | `MyPackage:Validation has started.` |
| **nome de pacote**            | String | O nome do seu ficheiro de pacote executado                             | `MyPackage.dtsx` |
| **nome de evento**              | String | O nome do evento relacionado em tempo de execução                                 | `OnPreValidate` |
| **nome de mensagemSourceName**      | String | O nome do componente do pacote como fonte de mensagem do evento         | `Data Flow Task` |
| **mensagemSourceId**        | String | O ID único do componente do pacote como fonte de mensagem do evento    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponenteName**       | String | O nome do componente de fluxo de dados como fonte de mensagem de evento       | `SSIS.Pipeline` |
| **packagePath**            | String | O caminho do objeto pacote como fonte de mensagem de evento            | `\Package\Data Flow Task` |
| **execuçãoAata**          | String | O caminho completo do pacote dos pais para o componente executado            | `\Transformation\Data Flow Task` (Este caminho também captura iterações de componentes) |
| **threadId**               | String | O ID único de fio executado quando a sua mensagem de evento é registada | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Atributos de registo de registo de estatísticas executáveis SSIS

Aqui estão os atributos de registo de estatísticas executáveis que são geradas por execuções de pacotes SSIS no seu SSIS IR, onde os executáveis são contentores ou tarefas no fluxo de controlo de pacotes. Transmitem informações semelhantes à [tabela de estatísticas executáveis do SSISDB ou à visão](https://docs.microsoft.com/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) que mostra uma linha para cada execução executável, incluindo as suas iterações. São gerados em qualquer nível de registo, exceto `None` e úteis para identificar estrangulamentos/falhas de nível de tarefa.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Tipo   | Descrição                                                      | Exemplo                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **hora**                   | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**          | String | Isto está definido para `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **categoria**               | String | A categoria de registos de diagnóstico                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | String | O ID único para rastrear uma determinada operação                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome da sua ADF                                             | `MyADFv2` |
| **integraçãoRuntimeName** | String | O nome do seu SSIS IR                                         | `MySSISIR` |
| **nível**                  | String | O nível de registos de diagnóstico                                     | `Informational` |
| **execuçãoId**            | String | O ID único para rastrear uma execução particular no SSISDB      | `1` (1 significa execuções relacionadas com embalagens **não** armazenadas no SSISDB/invocadas via T-SQL) |
| **execuçãoAata**          | String | O caminho completo do pacote dos pais para o componente executado          | `\Transformation\Data Flow Task` (Este caminho também captura iterações de componentes) |
| **horário de início**              | String | O tempo em que o executável entra na fase de pré-execução no formato UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | O tempo em que o executável entra na fase pós-execução no formato UTC | `2017-06-28T21:00:27.3534352Z` |
| **execuçãoDuration**      | String | O tempo de execução do executável em milissegundos                   | `1,125` |
| **resultadosResult**        | String | O resultado de correr executável                                 | `0` (0 significa sucesso, 1 significa falha, 2 significa conclusão e 3 significa cancelamento) |
| **execuçãoValue**         | String | O valor definido pelo utilizador devolvido por execução executável            | `1` |
| **recursosId**             | String | O ID único do seu recurso ADF                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Atributos de registo de fases de execução SSIS

Aqui estão os atributos de registo de estatísticas de tempo de execução para componentes de fluxo de dados que são gerados pelas execuções de pacotes SSIS no seu SSIS IR. Transmitem informações semelhantes à [tabela de fases de execução SSISDB ou vista](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) que mostra o tempo gasto pelos componentes do fluxo de dados em todas as suas fases de execução. São geradas quando seleciona `Performance/Verbose` o nível de registo e é útil para capturar estatísticas de execução de fluxo de dados.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriedade                   | Tipo   | Descrição                                                         | Exemplo                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **hora**                   | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**          | String | Isto está definido para `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **categoria**               | String | A categoria de registos de diagnóstico                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | String | O ID único para rastrear uma determinada operação                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | String | O nome da sua ADF                                                | `MyADFv2` |
| **integraçãoRuntimeName** | String | O nome do seu SSIS IR                                            | `MySSISIR` |
| **nível**                  | String | O nível de registos de diagnóstico                                        | `Informational` |
| **execuçãoId**            | String | O ID único para rastrear uma execução particular no SSISDB         | `1` (1 significa execuções relacionadas com embalagens **não** armazenadas no SSISDB/invocadas via T-SQL) |
| **nome de pacote**            | String | O nome do seu ficheiro de pacote executado                              | `MyPackage.dtsx` |
| **nome de tarefaName**               | String | O nome da tarefa de fluxo de dados executado                                 | `Data Flow Task` |
| **subcomponenteName**       | String | O nome da componente de fluxo de dados                                     | `Derived Column` |
| **fase**                  | String | O nome da fase de execução                                         | `AcquireConnections` |
| **horário de início**              | String | O momento em que a fase de execução começa no formato UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | String | O momento em que a fase de execução termina em formato UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **execuçãoAata**          | String | O caminho da execução para a tarefa de fluxo de dados                            | `\Transformation\Data Flow Task` |
| **recursosId**             | String | O ID único do seu recurso ADF                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Atributos de registo de estatísticas de execução SSIS

Aqui estão os atributos de registo de movimentos de dados através de cada parte dos oleodutos de fluxo de dados, desde a montante até componentes a jusante, que são gerados pelas execuções de pacotes SSIS no seu SSIS IR. Transmitem informações semelhantes à tabela de estatísticas de [resultados de execução do SSISDB ou à visão](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) que mostra a contagem de dados movidos através de tarefas de fluxo de dados. São geradas quando seleciona `Verbose` o nível de registo e é útil para calcular o fluxo de dados.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Propriedade                     | Tipo   | Descrição                                                        | Exemplo                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **hora**                     | String | A hora do evento em formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operaçãoName**            | String | Isto está definido para `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **categoria**                 | String | A categoria de registos de diagnóstico                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | String | O ID único para rastrear uma determinada operação                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | String | O nome da sua ADF                                               | `MyADFv2` |
| **integraçãoRuntimeName**   | String | O nome do seu SSIS IR                                           | `MySSISIR` |
| **nível**                    | String | O nível de registos de diagnóstico                                       | `Informational` |
| **execuçãoId**              | String | O ID único para rastrear uma execução particular no SSISDB        | `1` (1 significa execuções relacionadas com embalagens **não** armazenadas no SSISDB/invocadas via T-SQL) |
| **nome de pacote**              | String | O nome do seu ficheiro de pacote executado                             | `MyPackage.dtsx` |
| **nome de tarefaName**                 | String | O nome da tarefa de fluxo de dados executado                                | `Data Flow Task` |
| **dataflowPathIdString**     | String | O ID único para rastrear o caminho do fluxo de dados                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowMarme**         | String | O nome do caminho do fluxo de dados                                         | `ADO NET Source Output` |
| **fonteComponentName**      | String | O nome do componente de fluxo de dados que envia dados                    | `SQLDB Table3` |
| **destinoComponentName** | String | O nome da componente de fluxo de dados que recebe dados                 | `Derived Column` |
| **linhasSent**                 | String | O número de linhas enviadas por componente de origem                        | `500` |
| **createdTime**              | String | O tempo em que os valores da linha são obtidos em formato UTC                | `2017-06-28T21:00:27.3534352Z` |
| **execuçãoAata**            | String | O caminho da execução para a tarefa de fluxo de dados                           | `\Transformation\Data Flow Task` |
| **recursosId**               | String | O ID único do seu recurso ADF                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

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

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Monitorizar as operações do SSIS com o Azure Monitor

Para levantar & deslocar as suas cargas de trabalho SSIS, pode [providenciar SSIS IR em ADF](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) que suporte:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados pelo servidor de base de dados Azure SQL/Gestão de Instâncias (Modelo de Implementação de Projetos)
- Executando pacotes implantados no sistema de ficheiros, ficheiros Azure ou base de dados do SQL Server (MSDB) hospedada por Azure SQL Managed Instance (Modelo de Implementação de Pacotes)

Uma vez previsto, pode [verificar o estado operacional do SSIS **Monitor** IR utilizando o Azure PowerShell ou no centro monitor do portal ADF](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime). Com o Modelo de Implementação do Projeto, os registos de execução de pacotes SSIS são armazenados em tabelas ou vistas internas SSISDB, para que possa consultar, analisar e apresentar visualmente usando ferramentas designadas como SSMS. Com o Modelo de Implementação de Pacotes, os registos de execução de pacotes SSIS podem ser armazenados no sistema de ficheiros ou ficheiros Azure como ficheiros CSV que ainda precisa de analisar e processar utilizando outras ferramentas designadas antes de poder consultar, analisar e apresentar visualmente.

Agora, com a integração [do Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) pode consultar, analisar e apresentar visualmente todas as métricas e registos gerados a partir de operações do SSIS IR e execuções de pacotes SSIS no portal Azure. Além disso, também pode levantar alertas sobre eles.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Configurar configurações de diagnóstico e espaço de trabalho para operações SSIS

Para enviar todas as métricas e registos gerados a partir de operações SSIS IR e execuções de pacotes SSIS para o Azure Monitor, é necessário [configurar as definições de diagnóstico e espaço de trabalho para o seu ADF](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Métricas operacionais do SSIS

As [métricas](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics) operacionais do SSIS são contadores de desempenho ou valores numéricos que descrevem o estado das operações de arranque e paragem do SSIS IR, bem como as execuções de pacotes SSIS num determinado momento. Fazem parte das [métricas da ADF no Azure Monitor.](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)

Ao configurar as definições de diagnóstico e espaço de trabalho para o seu ADF no Azure Monitor, selecionar a caixa de verificação _AllMetrics_ tornará métricas operacionais SSIS disponíveis para [análise interativa utilizando o Azure Metrics Explorer,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) [apresentação no painel Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards), e [alertas em tempo quase real](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric).

![Nomeie as suas definições e selecione um espaço de trabalho de análise de log-analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Alertas operacionais do SSIS

Para elevar os alertas sobre as métricas operacionais do SSIS a partir do portal ADF, [selecione a página de **métricas alerts &** do hub do **Monitor** ADF e siga as instruções passo a passo fornecidas](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts).

![Elevação dos alertas operacionais do SSIS do portal ADF](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Para elevar os alertas sobre as métricas operacionais do SSIS a partir do portal Azure, [selecione a página **alertas** do hub do **Monitor** Azure e siga as instruções passo a passo fornecidas](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-alerts).

![Elevação dos alertas operacionais do SSIS a partir do portal Azure](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Registos operacionais do SSIS

Os registos operacionais SSIS são [eventos gerados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) pelas operações do SSIS IR e execuções de pacotes SSIS que fornecem contexto suficiente em quaisquer questões identificadas e são úteis para a análise de causas de raiz. 

Quando configurar as definições de diagnóstico e o espaço de trabalho para o seu ADF no Azure Monitor, pode selecionar os registos operacionais relevantes do SSIS e enviá-los para o Log Analytics que é baseado no Azure Data Explorer. Lá, serão disponibilizados para análise utilizando [uma linguagem de consulta rica,](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)apresentação no painel de [instrumentos Azure,](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)e [alertas em tempo real.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

![Nomeie as suas definições e selecione um espaço de trabalho de análise de log-analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

Os esquemas e o conteúdo dos registos de execução de pacotes SSIS no Azure Monitor e no Log Analytics são semelhantes aos esquemas de tabelas ou vistas internas SSISDB.

| Categorias de registos do Monitor Azure          | Registar tabelas de analíticas                     | Tabelas/vistas internas SSISDB              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Para obter mais informações sobre atributos/propriedades de registo operacional SSIS, consulte [esquemas de Azure Monitor e Log Analytics para ADF](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events).

Os registos de execução de pacotes SSIS selecionados são sempre enviados para o Log Analytics, independentemente dos seus métodos de invocação. Por exemplo, pode invocar execuções de pacotes em SSDT ativado pelo Azure, via T-SQL em SSMS, SqL Server Agent ou outras ferramentas designadas, e como desencadeado ou depurado corre as atividades do Pacote SSIS executado em oleodutos ADF.

Ao consultar os registos de operação SSIS IR no Logs Analytics, pode utilizar as propriedades **OperationName** e **ResultType** que estão definidas `Start/Stop/Maintenance` para `Started/InProgress/Succeeded/Failed` e, respectivamente. 

![Consulta de registos de operação SSIS IR no Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Ao consultar os registos de execução do pacote SSIS no Logs Analytics, pode juntar-se a eles utilizando propriedades **OperationId** / **ExecutionId** / **CorrelationId.** **OperationId** / **Os ExecutionId** são sempre `1` definidos para todas as operações/execuções relacionadas com pacotes **não** armazenados em SSISDB/invocados via T-SQL.

![Consulta de registos de execução de pacote sSIS no Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Passos seguintes
[Monitorize e gere os oleodutos programáticamente](monitor-programmatically.md)
