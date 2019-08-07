---
title: Criar, exibir e gerenciar alertas de log usando Azure Monitor | Microsoft Docs
description: Use o Azure Monitor para criar, exibir e gerenciar regras de alerta de log no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: f4d3a4d9a5785ae350874c400384477da1a6c22e
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774721"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de log usando Azure Monitor

## <a name="overview"></a>Descrição geral
Este artigo mostra como configurar alertas de log usando a interface alertas dentro de portal do Azure. A definição de uma regra de alerta está em três partes:
- Alvo: Recurso específico do Azure, que deve ser monitorado
- Critérios: Condição específica ou lógica que, quando visto em sinal, deve disparar ação
- Ação: Chamada específica enviada a um destinatário de uma notificação-email, SMS, webhook etc.

O termo **alertas de log** para descrever alertas em que o sinal é consulta de log em um [espaço de trabalho log Analytics](../learn/tutorial-viewdata.md) ou [Application insights](../app/analytics.md). Saiba mais sobre funcionalidade, terminologia e tipos de alertas de [log-visão geral](alerts-unified-log.md).

> [!NOTE]
> Os dados de log populares de [um espaço de trabalho log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) agora estão disponíveis na plataforma de métrica em Azure monitor. Para exibição de detalhes, [alerta de métrica para logs](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gerenciando alertas de log do portal do Azure

A seguir está o guia passo a passo para o uso de alertas de log usando a interface portal do Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta de log com o portal do Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e, na seção monitor, escolha **alertas**.

    ![Monitorização](media/alerts-log/AlertsPreviewMenu.png)

1. Selecione o botão **nova regra de alerta** para criar um novo alerta no Azure.

    ![Adicionar Alerta](media/alerts-log/AlertsPreviewOption.png)

1. A seção criar alerta é mostrada com as três partes que consistem em: *Defina a condição de alerta*, *defina os detalhes do alerta*e defina o grupo de *ações*.

    ![Criar regra](media/alerts-log/AlertsPreviewAdd.png)

1. Defina a condição de alerta usando o link **selecionar recurso** e especificando o destino selecionando um recurso. Filtre escolhendo a _assinatura_, o _tipo de recurso_e o _recurso_necessário.

   > [!NOTE]
   > Para criar um alerta de log-Verifique se o sinal de **log** está disponível para o recurso selecionado antes de continuar.
   >  ![Selecionar recurso](media/alerts-log/Alert-SelectResourceLog.png)

1. *Alertas de log*: Verifique **se tipo de recurso** é uma fonte de análise como *log Analytics* ou *Application insights* e tipo de sinal como **log**, depois que o **recurso** apropriado for escolhido, clique em *concluído*. Em seguida, use o botão **Adicionar critérios** para exibir a lista de opções de sinal disponíveis para o recurso e da opção de **pesquisa de logs personalizada** da lista de sinais para o serviço de monitoramento de log escolhido, como *log Analytics* ou *Application insights*.

   ![Selecionar um recurso-pesquisa de logs personalizada](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Listas de alertas podem importar a consulta de análise como tipo de sinal- **log (consulta salva)** , conforme mostrado na ilustração acima. Portanto, os usuários podem aperfeiçoar sua consulta no Analytics e, em seguida, salvá-las para uso futuro em alertas-mais detalhes sobre como usar a consulta de salvamento disponível em [usando a consulta de log em Azure monitor](../log-query/log-query-overview.md) ou [consulta compartilhada na análise do Application](../app/app-insights-overview.md)insights.

1. *Alertas de log*: Depois de selecionado, a consulta de alertas pode ser declarada no campo de **consulta de pesquisa** ; se a sintaxe da consulta estiver incorreta, o campo exibirá o erro em vermelho. Se a sintaxe da consulta estiver correta – para referência, os dados históricos da consulta declarada são mostrados como um grafo com opção para ajustar a janela de tempo das últimas seis horas até a última semana.

    ![Configurar regra de alerta](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > A visualização de dados históricos só poderá ser mostrada se os resultados da consulta tiverem detalhes de tempo. Se a consulta resultar em dados resumidos ou em valores de coluna específicos, o mesmo será mostrado como um gráfico singular.
   > Para o tipo de medição métrica de alertas de log usando Application Insights ou [alternado para nova API](alerts-log-api-switch.md), você pode especificar qual variável específica para agrupar os dados usando a opção **agregar em** ; conforme ilustrado abaixo:
   > 
   > ![agregar na opção](media/alerts-log/aggregate-on.png)

1. *Alertas de log*: Com a visualização em vigor, a **lógica de alerta** pode ser selecionada nas opções mostradas de condição, agregação e, por fim, limite. Por fim, especifique na lógica, o tempo para avaliar a condição especificada, usando a opção **período** . Juntamente com a frequência com que o alerta deve ser executado, selecionando **frequência**. Os **alertas de log** podem ser baseados em:
    - [Número de registros](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): Um alerta será criado se a contagem de registros retornados pela consulta for maior ou menor que o valor fornecido.
    - [Medida métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): Um alerta será criado se cada *valor agregado* nos resultados exceder o valor de limite fornecido e for *agrupado por* valor escolhido. O número de violações de um alerta é o número de vezes que o limite é excedido no período de tempo escolhido. Você pode especificar violações totais para qualquer combinação de violações no conjunto de resultados ou violações consecutivas para exigir que as violações devam ocorrer em amostras consecutivas.


1. Como a segunda etapa, defina um nome para o alerta no campo **nome da regra de alerta** , juntamente com uma **Descrição** detalhando as especificações do alerta e do valor de **severidade** das opções fornecidas. Esses detalhes são reutilizados em todos os emails de alerta, notificações ou envio por Push por Azure Monitor. Além disso, o usuário pode optar por ativar imediatamente a regra de alerta na criação, alternando adequadamente **habilitar regra na** opção de criação.

    Somente para **alertas de log** , algumas funcionalidades adicionais estão disponíveis nos detalhes do alerta:

    - **Suprimir alertas**: Quando você ativa a supressão da regra de alerta, as ações da regra são desabilitadas por um período de tempo definido após a criação de um novo alerta. A regra ainda está em execução e cria registros de alerta, desde que os critérios sejam atendidos. Permitindo o tempo para corrigir o problema sem executar ações duplicadas.

        ![Suprimir alertas para alertas de log](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Especifique um valor de alerta de supressão maior que a frequência de alerta para garantir que as notificações sejam interrompidas sem sobreposição

1. Como a terceira e última etapa, especifique se algum **grupo de ação** precisa ser disparado para a regra de alerta quando a condição de alerta é atendida. Você pode escolher qualquer grupo de ações existente com alerta ou criar um novo grupo de ação. De acordo com o grupo de ações selecionado, quando o alerta for disparado, o Azure irá: enviar email (s), enviar SMS (s), chamar webhook (s), corrigir usando Runbooks do Azure, enviá-lo por push para sua ferramenta de ITSM, etc. Saiba mais sobre [grupos de ações](action-groups.md).

    > [!NOTE]
    > Consulte os [limites do serviço de assinatura do Azure](../../azure-subscription-service-limits.md) para limites em cargas de runbook disparadas para alertas de log por meio de grupos de ação do Azure

    Para **alertas de log** , algumas funcionalidades adicionais estão disponíveis para substituir as ações padrão:

    - **Notificação por email**: Substitui o *assunto do email* no email, enviado por meio do grupo de ação; se uma ou mais ações de email existirem no grupo de ações dito. Você não pode modificar o corpo do email e esse campo **não** é para o endereço de email.
    - **Incluir conteúdo JSON personalizado**: Substitui o JSON de webhook usado por grupos de ação; se uma ou mais ações de webhook existirem no grupo de ações dito. O usuário pode especificar o formato do JSON a ser usado para todos os WebHooks configurados no grupo de ações associado; para obter mais informações sobre formatos de webhook, consulte [ação de webhook para alertas de log](../../azure-monitor/platform/alerts-log-webhook.md). A opção Exibir webhook é fornecida para verificar o formato usando dados JSON de exemplo.

        ![Substituições de ação para alertas de log](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se todos os campos forem válidos e com tique verde, o botão **criar regra de alerta** poderá ser clicado e um alerta será criado em alertas de Azure monitor. Todos os alertas podem ser exibidos no painel alertas.

     ![Criação de regra](media/alerts-log/AlertsPreviewCreate.png)

     Em alguns minutos, o alerta está ativo e é disparado conforme descrito anteriormente.

Os usuários também podem finalizar sua consulta de análise no [log Analytics](../log-query/portals.md) e, em seguida, enviá-la por push para criar um alerta por meio do botão ' Set Alert ' (definir alerta), seguindo as instruções da etapa 6 em diante no tutorial acima.

 ![Log Analytics-definir alerta](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Exibir & Gerenciar alertas de log no portal do Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e, na seção monitor, escolha **alertas**.

1. O **painel de alertas** é exibido-onde todos os alertas do Azure (incluindo alertas de log) são exibidos em uma placa singular; incluindo todas as instâncias do quando a regra de alerta de log foi acionada. Para saber mais, consulte [Gerenciamento de alertas](https://aka.ms/managealertinstances).
    > [!NOTE]
    > As regras de alerta de log compõem a lógica personalizada baseada em consulta fornecida pelos usuários e, portanto, sem um estado resolvido. Devido a cada vez que as condições especificadas na regra de alerta de log são atendidas, elas são disparadas.

1. Selecione o botão **gerenciar regras** na barra superior para navegar até a seção Gerenciamento de regras, em que todas as regras de alerta criadas são listadas; incluindo alertas que foram desabilitados.
    ![gerenciar regras de alerta](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gerenciando alertas de log usando o modelo de recurso do Azure

Os alertas de log no Azure Monitor estão associados ao `Microsoft.Insights/scheduledQueryRules/`tipo de recurso. Para obter mais informações sobre esse tipo de recurso, consulte [Azure monitor-referência de API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Os alertas de log para Application Insights ou Log Analytics podem ser criados usando a [API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Os alertas de log para Log Analytics também podem ser gerenciados usando a [API de alerta de log Analytics](api-alerts.md) herdado e modelos herdados de [log Analytics pesquisas e alertas salvos](../insights/solutions-resources-searches-alerts.md) também. Para obter mais informações sobre como usar a nova API ScheduledQueryRules detalhada aqui por padrão, consulte [mudar para nova API para alertas de log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Criação de alerta de log de exemplo usando o modelo de recurso do Azure

A seguir está a estrutura para o modelo de recurso baseado na [criação de regras de consulta agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) usando a consulta de pesquisa de logs padrão do [tipo de resultados alerta de log](alerts-unified-log.md#number-of-results-alert-rules), com o conjunto de dados de exemplo como variáveis.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

O JSON de exemplo acima pode ser salvo como (digamos) sampleScheduledQueryRule. JSON com a finalidade desse passo e pode ser implantado usando [Azure Resource Manager em portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Alerta de log com consulta entre recursos usando o modelo de recurso do Azure

A seguir está a estrutura para o modelo de recurso baseado na [criação de regras de consulta agendadas](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) usando [a consulta de pesquisa de log de recurso cruzado](../../azure-monitor/log-query/cross-workspace-query.md) do alerta de log do tipo de [medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules), com o conjunto de dados de exemplo como variáveis

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Ao usar a consulta entre recursos no alerta de log, o uso de [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) é obrigatório e o usuário deve ter acesso à lista de recursos declarados

O JSON de exemplo acima pode ser salvo como (digamos) sampleScheduledQueryRule. JSON com a finalidade desse passo e pode ser implantado usando [Azure Resource Manager em portal do Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Gerenciando alertas de log usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor-a [API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com Azure Resource Manager API REST. E os cmdlets do PowerShell listados abaixo estão disponíveis para aproveitar a [API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Cmdlet do PowerShell para criar uma nova regra de alerta de log.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Cmdlet do PowerShell para atualizar uma regra de alerta de log existente.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Cmdlet do PowerShell para criar ou atualizar um objeto especificando parâmetros de origem para um alerta de log. Usado como entrada pelo cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Cmdlet do PowerShell para criar ou atualizar objeto especificando parâmetros de agendamento para um alerta de log. Usado como entrada pelo cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Cmdlet do PowerShell para criar ou atualizar um objeto especificando parâmetros de ação para um alerta de log. Usado como entrada pelo cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) .
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Cmdlet do PowerShell para criar ou atualizar objetos que especificam parâmetros de grupos de ações para um alerta de log. Usado como entrada pelo cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Cmdlet do PowerShell para criar ou atualizar objeto especificando parâmetros de condição de gatilho para alerta de log. Usado como entrada pelo cmdlet [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) .
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Cmdlet do PowerShell para criar ou atualizar objeto especificando parâmetros de condição de gatilho de métrica para o [alerta de log do tipo de medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Usado como entrada pelo cmdlet [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Cmdlet do PowerShell para listar regras de alerta de log existentes ou uma regra de alerta de log específica
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Cmdlet do PowerShell para habilitar ou desabilitar a regra de alerta de log
1. [Remove-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Cmdlet do PowerShell para excluir uma regra de alerta de log existente

> [!NOTE]
> Os cmdlets do PowerShell ScheduledQueryRules só podem gerenciar regras criadas por meio do próprio cmdlet ou usando Azure Monitor [API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Regras de alerta de log criadas usando a [API de alerta log Analytics](api-alerts.md) herdada e modelos herdados de [log Analytics pesquisas e alertas salvos](../insights/solutions-resources-searches-alerts.md) podem ser gerenciados usando cmdlets do PowerShell do ScheduledQueryRules somente depois que o usuário [alternar a preferência de API para o log Alertas de análise](alerts-log-api-switch.md).

Em seguida, ilustramos as etapas para a criação de uma regra de alerta de log de exemplo usando os cmdlets do PowerShell scheduledQueryRules.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ \"alert\":\"#alertrulename\", \"IncludeSearchResults\":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Gerenciando alertas de log usando a CLI ou a API

Azure Monitor-a [API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com Azure Resource Manager API REST. Portanto, ele pode ser usado por meio do PowerShell usando comandos do Resource Manager para CLI do Azure.


> [!NOTE]
> Os alertas de log para Log Analytics também podem ser gerenciados usando a [API de alerta de log Analytics](api-alerts.md) herdado e modelos herdados de [log Analytics pesquisas e alertas salvos](../insights/solutions-resources-searches-alerts.md) também. Para obter mais informações sobre como usar a nova API ScheduledQueryRules detalhada aqui por padrão, consulte [mudar para nova API para alertas de log Analytics](alerts-log-api-switch.md).

Os alertas de log atualmente não têm comandos de CLI dedicados atualmente; Mas, conforme ilustrado abaixo, pode ser usado por meio do comando Azure Resource Manager CLI para o modelo de recurso de exemplo mostrado anteriormente (sampleScheduledQueryRule. JSON) na seção modelo de recurso:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Na operação bem-sucedida, 201 será retornado para o estado nova criação de regra de alerta ou 200 será retornado se uma regra de alerta existente tiver sido modificada.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre os [alertas de log nos alertas do Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Entender as [ações de webhook para alertas de log](../../azure-monitor/platform/alerts-log-webhook.md)
* Saiba mais sobre o [Application insights](../../azure-monitor/app/analytics.md)
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).
