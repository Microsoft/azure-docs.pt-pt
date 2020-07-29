---
title: Criar, ver e gerir alertas de registo Usando o Azure Monitor Microsoft Docs
description: Utilize o Monitor Azure para autor, ver e gerir as regras de alerta de registo em Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 25604bde3afbbef0d541bc21996b59e98b3090f4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327502"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Criar, visualizar e gerir alertas de registo usando o Azure Monitor

## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como criar e gerir alertas de registo usando a interface de alertas dentro do portal Azure. As regras de alerta são definidas por três componentes:
- Alvo: Um recurso Azure específico para monitorizar
- Critérios: Condição ou lógica para avaliar a verdade. Se for verdade, o alerta dispara.  
- Ação: Chamada específica enviada a um recetor de uma notificação - e-mail, SMS, webhook etc.

O termo **Alerta de Registo** descreve alertas onde é avaliada uma consulta de registo no espaço de trabalho do Log [Analytics](../log-query/get-started-portal.md) ou no [Application Insights](../log-query/log-query-overview.md) e um alerta disparado se o resultado for verdadeiro. Saiba mais sobre funcionalidades, terminologia e tipos de alertas de [Log - Visão geral](alerts-unified-log.md).

> [!NOTE]
> Os dados de registo de um espaço de [trabalho log Analytics](../log-query/get-started-portal.md) também podem ser encaminhados para a base de dados de métricas do Azure Monitor. Os alertas de métricas têm [um comportamento diferente,](alerts-metric-overview.md)o que pode ser mais desejável dependendo dos dados com que está a trabalhar.   Para obter informações sobre o que e como pode encaminhar os registos para métricas, consulte [o Alerta Métrico de Registos](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta de registo com o portal Azure

1. No [portal,](https://portal.azure.com/)selecione **Monitor**. Nessa secção, escolha **Alertas.**

    ![Monitorização](media/alerts-log/AlertsPreviewMenu.png)

1. Clique em **Nova Regra de Alerta**. 

    ![Adicionar Alerta](media/alerts-log/AlertsPreviewOption.png)

1. Aparece o painel **de alerta** de criação. Tem quatro partes: 
    - O recurso a que o alerta se aplica
    - A condição para verificar
    - A ação a tomar se a condição for verdadeira
    - Os detalhes para nomear e descrever o alerta. 

    ![Criar regra](media/alerts-log/AlertsPreviewAdd.png)

1. Defina a condição de alerta utilizando o link **'Selecionar Recursos'** e especificando o alvo selecionando um recurso. Filtrar escolhendo a *Subscrição,* *Tipo de Recurso*e *Recurso*necessário. 

   ![Selecione recurso](media/alerts-log/Alert-SelectResourceLog.png)

1. Certifique-se de que **o Tipo de Recurso** é uma fonte analítica como Log *Analytics* ou *Application Insights* e tipo de sinal como *Log*. Clique em **Concluído**. Em seguida, utilize o botão **de critérios Adicionar** para visualizar a lista de opções de sinal disponíveis para o recurso. Encontre e escolha a opção **de pesquisa de registo personalizado** para log *Analytics* ou *Application Insights,* dependendo de onde residem os dados dos seus alertas de registo.

   ![Selecione um recurso - pesquisa de registo personalizado](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > As listas de alertas podem importar consulta de análise como tipo de sinal - **Log (Consulta Guardada)**, como visto na ilustração acima. Para que os utilizadores possam aperfeiçoar a sua consulta em Analytics e, em seguida, guardá-los para uso futuro em alertas. Para obter mais detalhes sobre a utilização de consultas guardadas, consulte [a consulta de registo no Azure Monitor](../log-query/log-query-overview.md) e consulta partilhada na análise de Insights de [Aplicação](../app/app-insights-overview.md).

1. Uma vez selecionado, crie a consulta de alerta no campo **'Consulta de Pesquisa'.** Se a sintaxe de consulta estiver incorreta, o campo aparece e erro a vermelho. 

1. Se a sintaxe de consulta estiver correta, os dados históricos para a consulta aparecem como um gráfico com a opção de ajustar o tempo das últimas seis horas até à semana passada.

    ![Regra de alerta de configuração](media/alerts-log/AlertsPreviewAlertLog.png)

   A visualização de dados históricos só é mostrada se os resultados da consulta tiverem detalhes de tempo. Se a sua consulta resultar em dados resumidos ou valores específicos da coluna, o visor mostra um único enredo.
  
   Para medições métricas utilizando Insights de Aplicação ou [a API do Log Analytics,](/rest/api/monitor/scheduledqueryrules)pode especificar qual a variável específica para agrupar os dados utilizando o **Agregado na** opção; como mostrado aqui: 
  
   ![agregado na opção](media/alerts-log/aggregate-on.png)



1. Em seguida, escolha a condição **de Lógica de Alerta,** agregação e limiar. 

1. Escolha o período de tempo para avaliar a condição especificada, utilizando a opção **Period.** 

1. Escolha a frequência com que o alerta é executado na **Frequência.** 

    **Os alertas de** registo podem basear-se em:
    - [Número de Registos](./alerts-unified-log.md#number-of-results-alert-rules): É criado um alerta se a contagem de registos devolvidos pela consulta for superior ou inferior ao valor fornecido.
    - [Medição métrica](./alerts-unified-log.md#metric-measurement-alert-rules): É criado um alerta se cada *valor agregado* dos resultados exceder o valor limiar fornecido e for *agrupado pelo* valor escolhido. O número de infrações a um alerta é o número de vezes que o limiar é ultrapassado no período de tempo escolhido. Pode especificar as infrações totais para qualquer combinação de infrações através dos resultados definidos ou violações consecutivas para exigir que as infrações ocorram em amostras consecutivas.


1. Clique em **Concluído**. 

1. Defina um nome para o seu alerta no campo de **nome da regra de alerta,** juntamente com uma **Descrição** detalhando as especificidades para o valor de alerta e **gravidade** das opções fornecidas. Estes detalhes são reutilizados em todos os e-mails de alerta, notificações ou pushs feitos pelo Azure Monitor. Além disso, pode optar por ativar imediatamente a regra de alerta sobre a criação clicando em Ativar a **regra após a criação**.

1. Escolha se deseja **suprimir alertas** por um período de tempo.  Quando liga a supressão para a regra de alerta, as ações para a regra são desativadas por um período de tempo definido após a criação de um novo alerta. A regra ainda corre e cria registos de alerta desde que os critérios sejam cumpridos. Esta definição permite-lhe tempo para corrigir o problema sem executar ações duplicadas.

   ![Alertas de supressão para alertas de registo](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > Especifique um valor de alerta de supressão superior à frequência de alerta para garantir que as notificações são paradas sem sobreposição

1. Como terceiro e último passo, especifique se a regra de alerta deve desencadear um ou mais **Grupo de Ação** quando a condição de alerta for cumprida. Pode escolher qualquer Grupo de Ação existente ou criar um novo. Com grupos de ação, pode enviar uma série de ações como enviar e-mails(s), enviar SMS(s), ligar para Webhook(s), remediar usando Azure Runbooks, empurrar para a sua ferramenta ITSM, e muito mais. Saiba mais sobre [grupos de ação.](action-groups.md)

    > [!NOTE]
    > Consulte os limites do [serviço de subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites às ações que podem ser executadas.  

    Algumas funcionalidades adicionais estão disponíveis para anular as ações predefinidos:

    - **Notificação por e-mail**: Substitui o *e-mail no* e-mail enviado através do Grupo de Ação. Não é possível modificar o corpo do correio e este campo **não** é para endereço de e-mail.
    - **Inclua a carga útil personalizada Json**: Substitui o webhook JSON utilizado pelos Grupos de Ação assumindo que o grupo de ação contém um tipo webhook. Para obter mais informações sobre formatos webhook, consulte [a ação webhook para Alertas de Registo](./alerts-log-webhook.md). A opção Ver Webhook é fornecida para verificar o formato utilizando dados JSON da amostra.

        ![Ação sobrepõe-se a alertas de registo](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se todos os campos forem válidos e com o sinal verde o botão de regra de alerta de **criação** pode ser clicado e um alerta é criado no Azure Monitor - Alertas. Todos os alertas podem ser vistos a partir do painel de alertas.

     ![Criação de Regras](media/alerts-log/AlertsPreviewCreate.png)

     Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

Os utilizadores também podem finalizar a sua consulta de análise em [análise de registo](../log-query/log-query-overview.md) e, em seguida, pressioná-lo para criar um alerta através do botão 'set Alert' - seguindo depois as instruções do Passo 6 no tutorial acima.

 ![Log Analytics - Alerta de conjunto](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Ver & gerir alertas de registo no portal Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e na secção MONITOR - escolha **Alertas**.

1. O **Painel de Alertas** é apresentado - em que todos os alertas de Azure (incluindo alertas de registo) são apresentados numa placa singular; incluindo todos os casos de quando a sua regra de alerta de registo disparou. Para saber mais, consulte [Gestão de Alerta.](https://aka.ms/managealertinstances)
    > [!NOTE]
    > As regras de alerta de registo compreendem a lógica personalizada fornecida pelos utilizadores e, portanto, sem um estado resolvido. Devido ao qual sempre que as condições especificadas na regra de alerta de registo são cumpridas, é disparado.

1. Selecione o botão **'Gerir regras'** na barra superior, para navegar para a secção de gestão de regras - onde estão listadas todas as regras de alerta criadas; incluindo alertas que foram desativadas.
    ![gerir regras de alerta](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gestão de alertas de registo usando o modelo de recursos Azure

Os alertas de registo no Azure Monitor estão associados ao tipo de recurso `Microsoft.Insights/scheduledQueryRules/` . Para obter mais informações sobre este tipo de recurso, consulte [o Azure Monitor - Referência API das Regras de Consulta Agendadas](/rest/api/monitor/scheduledqueryrules/). Os alertas de registo para Insights de Aplicações ou Análise de Registo, podem ser criados usando [API de Regras de Consulta Agendadas.](/rest/api/monitor/scheduledqueryrules/)

> [!NOTE]
> Os alertas de registo para o Log Analytics também podem ser geridos usando o legado [Log Analytics Alert API](api-alerts.md) e modelos legados de [Log Analytics também guardados pesquisas e alertas.](../insights/solutions.md) Para obter mais informações sobre a utilização da nova API de Placas Agendadas detalhadas aqui por padrão, consulte [a Switch para a nova API para Alertas de Análise de Registos](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Criação de alerta de registo de amostra usando modelo de recurso Azure

Segue-se a estrutura do modelo [de criação de regras de consulta programadas](/rest/api/monitor/scheduledqueryrules/createorupdate) utilizando a consulta padrão de pesquisa de registo de [registo do número de resultados,](alerts-unified-log.md#number-of-results-alert-rules)com o conjunto de dados da amostra como variáveis.

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

A amostra json acima pode ser guardada como (digamos) sampleScheduledQueryRule.jspara o propósito desta caminhada e pode ser implantada usando [O Gestor de Recursos Azure no portal Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Alerta de registo com consulta de recursos cruzados usando modelo de recursos Azure

Segue-se a estrutura do modelo [de criação de regras](/rest/api/monitor/scheduledqueryrules/createorupdate) de consulta programadas utilizando [consulta de pesquisa de registo de registo de recursos cruzados](../log-query/cross-workspace-query.md) do alerta de [registo do tipo de medição métrica,](./alerts-unified-log.md#metric-measurement-alert-rules)com o conjunto de dados da amostra como variáveis.

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
> Ao utilizar consulta de recursos cruzados em alerta de registo, o uso de [recursos autorizados](/rest/api/monitor/scheduledqueryrules/createorupdate#source) é obrigatório e o utilizador deve ter acesso à lista de recursos indicados

A amostra json acima pode ser guardada como (digamos) sampleScheduledQueryRule.jspara o propósito desta caminhada e pode ser implantada usando [O Gestor de Recursos Azure no portal Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Gerir alertas de registo usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor - [Regras de Consulta ProgramadaS A API](/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com AZure Resource Manager REST API. E os cmdlets PowerShell listados abaixo estão disponíveis para alavancar as [Regras de Consulta Agendadas API](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell cmdlet para criar uma nova regra de alerta de registo.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell cmdlet para atualizar uma regra de alerta de registo existente.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell cmdlet para criar ou atualizar objetos que especificam parâmetros de origem para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell cmdlet para criar ou atualizar o objeto especificando os parâmetros do calendário para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell cmdlet para criar ou atualizar o objeto especificando parâmetros de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell cmdlet para criar ou atualizar objetos especificando parâmetros de grupos de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell cmdlet para criar ou atualizar o objeto especificando os parâmetros da condição do gatilho para o alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet.
- [Novo-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell cmdlet para criar ou atualizar o objeto especificando parâmetros de condição de gatilho métrico para [o alerta de registo do tipo de medição métrica](./alerts-unified-log.md#metric-measurement-alert-rules). Usado como entrada por [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell cmdlet para listar as regras de alerta de registo existentes ou uma regra específica de alerta de registo
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell cmdlet para ativar ou desativar a regra de alerta de registo
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell cmdlet para eliminar uma regra de alerta de registo existente

> [!NOTE]
> Os cmdlets De Configurações De Configurações Agendadas só podem gerir as regras criadas pelo próprio CMDLET ou utilizando o Azure Monitor - [Regras de Consulta Programadas API](/rest/api/monitor/scheduledqueryrules/). As regras de alerta de registo criadas utilizando o legado [Log Analytics Alert API](api-alerts.md) e modelos legados de [Registo Analytics guardam pesquisas e alertas](../insights/solutions.md) podem ser geridos usando os cmdlets PowerShell agendados do Dinheiro Só depois de o utilizador [mudar a preferência da API para alertas de analítica de registos.](alerts-log-api-switch.md)

Ilustrados em seguida estão os passos para a criação de uma regra de alerta de registo de amostra usando os cmdlets PowerShell programados.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Gerir alertas de registos utilizando CLI ou API

Azure Monitor - [Regras de Consulta ProgramadaS A API](/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com AZure Resource Manager REST API. Assim, pode ser usado através de Powershell usando comandos de Gestor de Recursos para Azure CLI.


> [!NOTE]
> Os alertas de registo para o Log Analytics também podem ser geridos usando o legado [Log Analytics Alert API](api-alerts.md) e modelos legados de [Log Analytics também guardados pesquisas e alertas.](../insights/solutions.md) Para obter mais informações sobre a utilização da nova API de Placas Agendadas detalhadas aqui por padrão, consulte [a Switch para a nova API para Alertas de Análise de Registos](alerts-log-api-switch.md).

Os alertas de registo atualmente não têm comandos CLI dedicados atualmente; mas como ilustrado abaixo pode ser usado através do comando CLI do Gestor de Recursos Azure para o modelo de recursos da amostra mostrado anteriormente (sampleScheduledQueryRule.js) na secção modelo de recursos:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Em funcionamento bem sucedido, 201 será devolvido ao estado de nova criação de regras de alerta ou 200 serão devolvidos se uma regra de alerta existente for alterada.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais [sobre alertas de registo em alertas de Azure](./alerts-unified-log.md)
* Compreenda [as ações do Webhook para alertas de registo](./alerts-log-webhook.md)
* Saiba mais sobre [a Aplicação Insights](../log-query/log-query-overview.md)
* Saiba mais sobre [consultas de registo.](../log-query/log-query-overview.md)

