---
title: Criar, visualizar e gerir alertas de registo Utilizando o Monitor Azure , Microsoft Docs
description: Utilize o Monitor Azure para autor, visualizar e gerir as regras de alerta de registo em Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249428"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Criar, visualizar e gerir alertas de registo usando o Monitor Azure

## <a name="overview"></a>Descrição geral
Este artigo mostra-lhe como configurar alertas de registo utilizando a interface de alertas dentro do portal Azure. A definição de regra de alerta é em três partes:
- Alvo: Recurso Azure específico, que deve ser monitorizado
- Critérios: Condição específica ou lógica que, quando vista no Signal, deve desencadear ação
- Ação: Chamada específica enviada a um recetor de uma notificação - e-mail, SMS, webhook etc.

O termo **Alertas** de Registo para descrever alertas onde o sinal é consulta de log em um espaço de [trabalho Log Analytics](../learn/tutorial-viewdata.md) ou Insights de [aplicação](../app/analytics.md). Saiba mais sobre funcionalidade, terminologia e tipos de [alertas](alerts-unified-log.md)de Registo - Visão geral .

> [!NOTE]
> Dados de registo populares de um espaço de [trabalho log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) também estão disponíveis na plataforma métrica no Monitor Azure. Para mais detalhes, [Alerta Métrico para Registos](alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gestão de alertas de registo do portal Azure

Em seguida, o guia detalhado é o guia passo a passo para utilizar alertas de registo utilizando a interface do portal Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Crie uma regra de alerta de log com o portal Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e sob a secção MONITOR - escolha **Alertas**.

    ![Monitorização](media/alerts-log/AlertsPreviewMenu.png)

1. Selecione o botão **New Alert Rule** para criar um novo alerta em Azure.

    ![Adicionar Alerta](media/alerts-log/AlertsPreviewOption.png)

1. A secção Criar Alerta é mostrada com as três partes compostas por: Definir condição de *alerta,* *Definir detalhes*de alerta e *definir grupo*de ação .

    ![Criar regra](media/alerts-log/AlertsPreviewAdd.png)

1. Defina a condição de alerta utilizando a ligação **Select Resource** e especificando o alvo selecionando um recurso. Filtrar escolhendo a _Subscrição,_ _Tipo de Recurso,_ e _recurso_necessário.

   > [!NOTE]
   > Para criar um alerta de registo - verifique se o sinal de **registo** está disponível para o recurso selecionado antes de proceder.
   >  ![Selecione](media/alerts-log/Alert-SelectResourceLog.png) de recursos

1. *Alertas de registo*: Certifique-se de que o **Tipo de Recursos** é uma fonte de análise como Log *Analytics* ou *Application Insights* e tipo de sinal como **Log**, e ntão, uma vez escolhido **o recurso** apropriado, clique em *Done*. Em seguida, utilize o botão **adicionar critérios** para visualizar a lista de opções de sinal disponíveis para o recurso e a partir da lista de sinais Opção de **pesquisa de registo personalizado** para o serviço de monitor de registo escolhido, como Log *Analytics* ou *Application Insights*.

   ![Selecione um recurso - pesquisa de registo personalizado](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > As listas de alertas podem importar consulta de análise como tipo de sinal - **Log (Consulta Guardada)** , como visto na ilustração acima. Para que os utilizadores possam aperfeiçoar a sua consulta no Analytics e, em seguida, guardá-las para uso futuro em alertas - mais detalhes sobre a utilização de consultas de poupança disponíveis na utilização de consulta de [log no Monitor Azure](../log-query/log-query-overview.md) ou consulta partilhada na análise de insights de [aplicações](../app/app-insights-overview.md).

1. *Alertas de log*: Uma vez selecionado, a consulta para alerta pode ser indicada no campo **Search Query;** se a sintaxe de consulta estiver incorreta, o campo apresenta erro em VERMELHO. Se a sintaxe de consulta estiver correta - Para os dados históricos de referência da consulta declarada é mostrado como um gráfico com opção de ajustar a janela de tempo das últimas seis horas para a semana passada.

    ![Configurar regra de alerta](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > A visualização histórica de dados só pode ser mostrada se os resultados da consulta tiverem detalhes do tempo. Se a sua consulta resultar em dados resumidos ou valores específicos da coluna - o mesmo é mostrado como um enredo singular.
   > Para o tipo de alertas de registo de medição métrica utilizando insights de aplicação ou [comutados para novos API,](alerts-log-api-switch.md)pode especificar qual a variável específica para agrupar os dados utilizando o **Agregado na** opção; como ilustrado abaixo:
   > 
   > ![agregado em opção](media/alerts-log/aggregate-on.png)

1. *Alertas*de log : Com a visualização no lugar, a Lógica de **Alerta** pode ser selecionada a partir de opções mostradas de Condição, Agregação e, finalmente, Limiar. Finalmente especifique na lógica, o tempo para avaliar a condição especificada, utilizando a opção **Period.** Juntamente com a frequência com que o Alerta deve ser executado selecionando **frequência**. **Os alertas** de log podem basear-se em:
    - [Número de Registos](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): É criado um alerta se a contagem de registos devolvidos pela consulta for superior ou inferior ao valor fornecido.
    - [Medição Métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): É criado um alerta se cada *valor agregado* nos resultados exceder o valor limiar fornecido e for *agrupado pelo* valor escolhido. O número de infrações para um alerta é o número de vezes que o limiar é ultrapassado no período de tempo escolhido. Pode especificar as violações totais de qualquer combinação de infrações através do conjunto de resultados ou violações consecutivas para exigir que as infrações ocorram em amostras consecutivas.


1. Como segundo passo, defina um nome para o seu alerta no campo de **nome** sintetizador, juntamente com uma **Descrição** detalhando especificações para o valor de alerta e **gravidade** das opções fornecidas. Estes detalhes são reutilizados em todos os e-mails, notificações ou push sintetizantes do Azure Monitor. Além disso, o utilizador pode optar por ativar imediatamente a regra de alerta sobre a criação, toggling adequadamente enable regra sobre a opção **de criação.**

    Apenas para alertas de **registo,** algumas funcionalidades adicionais estão disponíveis em detalhes do Alerta:

    - **Alertas de supressão**: Quando liga a supressão para a regra do alerta, as ações para a regra são desativadas durante um período de tempo definido após a criação de um novo alerta. A regra ainda está em execução e cria registos de alerta desde que os critérios sejam cumpridos. Dar-lhe tempo para corrigir o problema sem executar ações duplicadas.

        ![Suprimir alertas para alertas de registo](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Especifique um valor de alerta de supressão superior à frequência do alerta para garantir que as notificações são interrompidas sem sobreposição

1. Como terceiro e último passo, especifique se algum Grupo de **Ação** precisa de ser acionado para a regra de alerta quando a condição de alerta for satisfeita. Pode escolher qualquer Grupo de Ação existente com alerta ou criar um novo Grupo de Ação. De acordo com o Grupo de Ação selecionado, quando o alerta é disparado, o Azure irá: enviar e-mails ou s, enviar SMS(s), ligar para Webhook(s), remediar usando os Livros De Execução Azure, empurrar para a sua ferramenta ITSM, etc. Saiba mais sobre [grupos de ação.](action-groups.md)

    > [!NOTE]
    > Consulte os limites do serviço de [subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites de cargas do Livro de Execução desencadeados para alertas de registo através de grupos de ação Azure

    Para **alertas** de registo alguma funcionalidade adicional está disponível para anular as Ações padrão:

    - **Notificação por e-mail**: Substitui o assunto do *e-mail* no e-mail, enviado via Action Group; se existirem uma ou mais ações de e-mail no referido Grupo de Ação. Não é possível modificar o corpo do correio e este campo **não** é para endereço de e-mail.
    - **Incluir carga útil personalizada json**: Sobrepõe-se ao webhook JSON utilizado por Grupos de Ação; se existirem uma ou mais ações de webhook no referido Grupo de Ação. O utilizador pode especificar o formato da JSON a ser utilizado para todos os webhooks configurados no Grupo de Ação associado; para obter mais informações sobre os formatos webhook, consulte [a ação webhook para alertas](../../azure-monitor/platform/alerts-log-webhook.md)de registo . A opção De visualização webhook é fornecida para verificar o formato utilizando dados JSON da amostra.

        ![Sobreposições de ação para alertas de log](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se todos os campos forem válidos e com carraça verde, o botão de regra de **alerta de criação** pode ser clicado e um alerta é criado no Monitor Azure - Alertas. Todos os alertas podem ser vistos a partir dos alertas Dashboard.

     ![Criação de Regras](media/alerts-log/AlertsPreviewCreate.png)

     Dentro de poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

Os utilizadores também podem finalizar a sua consulta de análise em análise de [log](../log-query/portals.md) e, em seguida, pressioná-la para criar um alerta através do botão 'set Alert' - em seguida, seguindo instruções do Passo 6 no tutorial acima.

 ![Log Analytics - Definir alerta](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Ver e gerir alertas de registo no portal Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e sob a secção MONITOR - escolha **Alertas**.

1. O Painel de **Alertas** é apresentado - onde todos os alertas Azure (incluindo alertas de registo) são apresentados numa placa singular; incluindo todos os casos de quando a sua regra de alerta de registo disparou. Para saber mais, consulte [A Gestão de Alerta.](https://aka.ms/managealertinstances)
    > [!NOTE]
    > As regras de alerta de log são constituídas por lógica personalizada baseada em consultas fornecidas pelos utilizadores e, portanto, sem um estado resolvido. Devido ao qual cada vez que as condições especificadas na regra de alerta de registo são cumpridas, é disparada.

1. Selecione o botão **de regras de Gestão** na barra superior, para navegar para a secção de gestão de regras - onde todas as regras de alerta criadas estão listadas; incluindo alertas que foram desativados.
    ![ gerir as regras de alerta](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gerir alertas de registo usando o modelo de recurso Azure

Os alertas de registo no Monitor Azure estão associados ao tipo de recursos `Microsoft.Insights/scheduledQueryRules/`. Para obter mais informações sobre este tipo de recurso, consulte [O Monitor Azure -](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Referência a API regras de consulta programada . Os alertas de registo para Insights de Aplicação ou Log Analytics podem ser criados usando [a API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)Regras de Consulta Agendada .

> [!NOTE]
> Os alertas de log para Log Analytics também podem ser geridos usando a [API](api-alerts.md) de Alerta de Log Analytics e modelos legados de [Log Analytics também guardavam pesquisas e alertas.](../insights/solutions-resources-searches-alerts.md) Para obter mais informações sobre a utilização da nova API ScheduledQueryRules detalhado aqui por padrão, consulte [a Switch para nova API para Alertas de Análise](alerts-log-api-switch.md)de Registo .


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Criação de alerta de registo de amostra usando o modelo de recurso Azure

Segue-se a estrutura do modelo de recursos baseado [supérreo](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) de regras de consulta programada utilizando consulta padrão de pesquisa de registo de [número de resultados tipo alerta,](alerts-unified-log.md#number-of-results-alert-rules)com conjunto de dados de amostra como variáveis.

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

A amostra json acima pode ser guardada como (por exemplo) amostraScheduledQueryRule.json para efeitos desta caminhada e pode ser implantada usando o Gestor de [Recursos Azure no portal Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Alerta de log com consulta de recursos cruzados usando o modelo de recurso Azure

Segue-se a estrutura do modelo de recurso baseado em regras de [consulta programada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) utilizando [consulta de pesquisa de registo de](../../azure-monitor/log-query/cross-workspace-query.md) registo de dados de [métricas,](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)com conjunto de dados de amostra como variáveis.

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
> Ao utilizar a consulta de recursos cruzados em alerta de registo, a utilização de [Recursos Autorizados](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) é obrigatória e o utilizador deve ter acesso à lista de recursos indicados

A amostra json acima pode ser guardada como (por exemplo) amostraScheduledQueryRule.json para efeitos desta caminhada e pode ser implantada usando o Gestor de [Recursos Azure no portal Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Gerir alertas de registo usando powerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor - [Regras de Consulta AgendadaS API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com a API REST API do Gestor de Recursos Azure. E os cmdlets PowerShell listados abaixo estão disponíveis para alavancar as Regras de [Consulta AgendadaA API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

1. [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) : Powershell cmdlet para criar uma nova regra de alerta de registo.
1. [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) : Powershell cmdlet para atualizar uma regra de alerta de registo existente.
1. [New-AzScheduledQueryRuleSource](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) : Powershell cmdlet para criar ou atualizar os parâmetros de origem especificando os parâmetros de origem para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): Powershell cmdlet para criar ou atualizar os parâmetros de programação do objetivo para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) : Powershell cmdlet para criar ou atualizar os parâmetros de ação especificando parâmetros de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
1. [New-AzScheduledQueryRuleAznsActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : Powershell cmdlet para criar ou atualizar os parâmetros dos grupos de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet.
1. [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : Powershell cmdlet para criar ou atualizar os parâmetros da condição do gatilho para alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet.
1. [New-AzScheduledQueryRuleLogMetricTrigger](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : Powershell cmdlet para criar ou atualizar o objetais especificando parâmetros métricos da condição do gatilho para o alerta de registo do tipo de [medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Utilizado como entrada por [New-AzScheduledQueryRuleTriggerCondition](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet.
1. [Get-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) : Powershell cmdlet para listar as regras de alerta de registo existentes ou uma regra específica de alerta de registo
1. [Update-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) : Powershell cmdlet para ativar ou desativar regra de alerta de registo
1. [Remover-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule): Powershell cmdlet para eliminar uma regra de alerta de registo existente

> [!NOTE]
> AgendadoSRegras PowerShell cmdlets só podem gerir as regras criadas por si mesmos ou usando o Monitor Azure - [Regras de Consulta AgendadaA API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). As regras de alerta de log criadas usando o legado [Log Analytics Alert API](api-alerts.md) e modelos legados de [Log Analytics salvaram pesquisas e alertas](../insights/solutions-resources-searches-alerts.md) podem ser geridos usando os cmdlets De PowerShell scheduledQueryRules apenas após o utilizador mudar a preferência da [API para alertas de Log Analytics](alerts-log-api-switch.md).

Ilustrados em seguida são os passos para a criação de uma regra de alerta de registo de amostras utilizando os cmdlets agendados QueryRules PowerShell.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Gestão de alertas de registo utilizando CLI ou API

Azure Monitor - [Regras de Consulta AgendadaS API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) é uma API REST e totalmente compatível com a API REST API do Gestor de Recursos Azure. Assim, pode ser usado através da Powershell utilizando comandos do Gestor de Recursos para o Azure CLI.


> [!NOTE]
> Os alertas de log para Log Analytics também podem ser geridos usando a [API](api-alerts.md) de Alerta de Log Analytics e modelos legados de [Log Analytics também guardavam pesquisas e alertas.](../insights/solutions-resources-searches-alerts.md) Para obter mais informações sobre a utilização da nova API ScheduledQueryRules detalhado aqui por padrão, consulte [a Switch para nova API para Alertas de Análise](alerts-log-api-switch.md)de Registo .

Os alertas de registo não têm atualmente comandos CLI dedicados; mas, como ilustrado abaixo pode ser usado através do comando CLI do Gestor de Recursos Azure para o modelo de recurso da amostra mostrado anteriormente (sampleScheduledQueryRule.json) na secção Modelo de Recurso:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Em funcionamento bem sucedido, 201 será devolvido para declarar nova regra de alerta criação ou 200 serão devolvidos se uma regra de alerta existente for alterada.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre alertas de [log em alertas Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Compreender [as ações do Webhook para alertas](../../azure-monitor/platform/alerts-log-webhook.md) de registo
* Saiba mais sobre [insights de aplicação](../../azure-monitor/app/analytics.md)
* Saiba mais sobre consultas de [registo.](../log-query/log-query-overview.md)
