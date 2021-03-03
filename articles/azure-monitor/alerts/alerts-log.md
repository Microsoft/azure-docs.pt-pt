---
title: Criar, visualizar e gerir alertas de registo Usando o Monitor Azure | Microsoft Docs
description: Use o Azure Monitor para criar, visualizar e gerir as regras de alerta de registo
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: 2cff0773883f7de5788bca5e4e3f680818e2bd77
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734881"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Criar, visualizar e gerir alertas de registo usando o Azure Monitor

## <a name="overview"></a>Descrição Geral

Os alertas de registo permitem que os utilizadores utilizem uma consulta [log Analytics](../logs/log-analytics-tutorial.md) para avaliar os registos de recursos em cada frequência definida e disparem um alerta com base nos resultados. As regras podem desencadear uma ou mais ações utilizando [grupos de ação.](./action-groups.md) [Saiba mais sobre funcionalidade e terminologia de alertas de registo.](./alerts-unified-log.md)

Este artigo mostra-lhe como criar e gerir alertas de registo usando o Azure Monitor. As regras de alerta são definidas por três componentes:
- Alvo: Um recurso Azure específico para monitorizar.
- Critérios: Lógica para avaliar. Se formos atingidos, o alerta dispara.  
- Ação: Notificações ou automação - e-mail, SMS, webhook, e assim por diante.

Também pode criar regras de alerta de registo utilizando modelos do Gestor de Recursos Azure, que são descritos [num artigo separado](../alerts/alerts-log-create-templates.md).

> [!NOTE]
> Os dados de registo de um espaço de [trabalho log Analytics](../logs/log-analytics-tutorial.md) podem ser enviados para a loja de métricas do Azure Monitor. Os alertas de métricas têm [um comportamento diferente,](./alerts-metric-overview.md)o que pode ser mais desejável dependendo dos dados com que está a trabalhar. Para obter informações sobre o que e como pode encaminhar os registos para métricas, consulte [o Alerta Métrico de Registos](./alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta de registo com o portal Azure

Aqui os passos para começar a escrever consultas para alertas:

1. Vá ao recurso que gostaria de alertar. Considere criar regras de alerta sobre vários recursos selecionando sempre que possível um âmbito de grupo de subscrição ou de grupo de recursos. Alertar sobre vários recursos reduz os custos e a necessidade de gerir várias regras de alerta.
1. Em **Monitor**, selecione **Logs**.
1. Consulte os dados de registo que podem indicar o problema. Pode utilizar o tópico de [exemplos de consulta de alerta](../logs/example-queries.md) para entender o que pode descobrir ou começar a escrever a sua própria [consulta.](../logs/log-analytics-tutorial.md) Além disso, [saiba como criar consultas de alertas otimizadas](alerts-log-query.md).
1. Prima o botão '+ Nova Regra de Alerta' para iniciar o fluxo de criação de alerta.

    ![Log Analytics - Alerta de conjunto](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Recomenda-se que crie alertas à escala, quando utilizar o modo de acesso a recursos para registos, que funciona em vários recursos utilizando um grupo de recursos ou âmbito de subscrição. Alertar em escala reduz a gestão de regras. Para poder direcionar os recursos, por favor inclua a coluna de identificação de recursos nos resultados. [Saiba mais sobre a divisão de alertas por dimensões.](./alerts-unified-log.md#split-by-alert-dimensions)

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Alerta de registo para Log Analytics e Insights de Aplicações

1. Se a sintaxe de consulta estiver correta, então os dados históricos para a consulta aparecem como um gráfico com a opção de ajustar o período de gráfico das últimas seis horas até à semana passada.
 
    Se os resultados da sua consulta contiverem dados resumidos ou colunas específicas do [projeto](/azure/kusto/query/projectoperator) sem coluna de tempo, o gráfico mostra um único valor.

    ![Regra de alerta de configuração](media/alerts-log/AlertsPreviewAlertLog.png)

1. Escolha o intervalo de tempo sobre o qual para avaliar a condição especificada, utilizando a opção [**Period.**](./alerts-unified-log.md#query-time-range)

1. Os alertas de registo podem basear-se em dois tipos de [**medidas:**](./alerts-unified-log.md#measure)
    1. **Número de resultados** - Contagem de registos devolvidos pela consulta.
    1. **Medição**  -  métrica *Valor agregado* calculado com base em resumo agrupados por expressões escolhidas e seleção [de contentores.](/azure/kusto/query/binfunction) Por exemplo:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. Para a lógica de alerta de medições métricas, pode especificar opcionalmente como [dividir os alertas por dimensões](./alerts-unified-log.md#split-by-alert-dimensions) utilizando a opção **Agregada.** A expressão de agrupamento de linha deve ser única e ordenada.

    > [!NOTE]
    > Como [o caixote pode](/azure/kusto/query/binfunction) resultar em intervalos de tempo desiguais, o serviço de alerta converterá automaticamente a função do caixote do [lixo](/azure/kusto/query/binfunction) para [bin_at()](/azure/kusto/query/binatfunction) função com o tempo de execução adequado, para garantir resultados com um ponto fixo.

    > [!NOTE]
    > Dividido por dimensões de alerta só está disponível para a atual API de Regras Desíparos. Se utilizar o legado [Log Analytics Alert API,](./api-alerts.md)terá de mudar. [Saiba mais sobre a troca.](./alerts-log-api-switch.md) O alerta centrado em recursos em escala só é suportado na versão API `2020-05-01-preview` e acima.

    ![agregado na opção](media/alerts-log/aggregate-on.png)

1. Em seguida, com base nos dados de pré-visualização definidos [ **o Operador,** **Valor Limiar**](./alerts-unified-log.md#threshold-and-operator)e [**Frequência**](./alerts-unified-log.md#frequency).

1. Também pode configurar opcionalmente o [número de violações para desencadear um alerta](./alerts-unified-log.md#number-of-violations-to-trigger-alert) utilizando **Violações Totais ou Consecutivas.**

1. Selecione **Concluído**. 

1. Defina o **nome da regra de alerta**, **Descrição,** e selecione o alerta **Severidade**. Estes detalhes são usados em todas as ações de alerta. Além disso, pode optar por não ativar a regra de alerta sobre a criação selecionando **Enable rule após a criação**.

1. Escolha se pretende suprimir as ações de regras durante um tempo após o disparo de um alerta, utilize a opção [**Alertas de Supressão.**](./alerts-unified-log.md#state-and-resolving-alerts) A regra ainda vai funcionar e criar alertas, mas as ações não serão desencadeadas para evitar o ruído. O valor das ações de silenciamento deve ser maior do que a frequência de alerta para ser eficaz.

    ![Alertas de supressão para alertas de registo](media/alerts-log/AlertsPreviewSuppress.png)

1. Especifique se a regra de alerta deve desencadear um ou mais [**grupos de ação**](./action-groups.md#webhook) quando a condição de alerta for cumprida.

    > [!NOTE]
    > Consulte os limites do [serviço de subscrição Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) para limites às ações que podem ser executadas.  

1. Pode personalizar opcionalmente as ações nas regras de alerta de registo:

    - **Assunto de e-mail personalizado**: Substitui o tema do *e-mail* de ações de e-mail. Não é possível modificar o corpo do correio e este campo **não é para endereços de e-mail.**
    - **Inclua a carga útil personalizada Json**: Substitui o webhook JSON usado pelos Grupos de Ação assumindo que o grupo de ação contém uma ação webhook. Saiba mais sobre [a ação webhook para Alertas de Registos](./alerts-log-webhook.md).

    ![Ação sobrepõe-se a alertas de registo](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Se todos os campos estiverem corretamente definidos, o botão **de regra de alerta Criar** pode ser clicado e é criado um alerta.

    Em poucos minutos, o alerta está ativo e dispara como descrito anteriormente.

    ![Criação de Regras](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Criação de alerta de registo para Log Analytics e Application Insights da gestão de alertas

> [!NOTE]
> Criação a partir de alertas a gestão não é atualmente suportada para registos centrados em recursos

1. No [portal,](https://portal.azure.com/)selecione **Monitor** e escolha **Alertas**.

    ![Monitorização](media/alerts-log/AlertsPreviewMenu.png)

1. Selecione **Nova Regra de Alerta**. 

    ![Adicionar Alerta](media/alerts-log/AlertsPreviewOption.png)

1. Aparece o painel **de alerta** de criação. Tem quatro partes: 
    - O recurso a que o alerta se aplica.
    - A condição para verificar.
    - As ações a tomar se a condição for verdadeira.
    - Os detalhes para nomear e descrever o alerta. 

    ![Criar regra](media/alerts-log/AlertsPreviewAdd.png)

1. Prima no **botão 'Selecionar Recursos'.** Filtre por escolher a *Subscrição*, *Tipo de Recurso* e selecione um recurso. Certifique-se de que o recurso tem registos disponíveis.

   ![Selecione recurso](media/alerts-log/Alert-SelectResourceLog.png)

1. Em seguida, utilize o botão **'Adicionar Condição'** para visualizar a lista de opções de sinal disponíveis para o recurso. Selecione a opção **de pesquisa de registo personalizado.**

   ![Selecione um recurso - pesquisa de registo personalizado](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > O portal de alertas lista consultas guardadas de Log Analytics e Application Insights e podem ser usados como consultas de alerta de modelo.

1. Uma vez selecionado, escreva, cole ou edite a consulta de alerta no campo **'Consulta de Procura'.**

1. Continue até os próximos passos descritos na [última secção](#log-alert-for-log-analytics-and-application-insights).

### <a name="log-alert-for-all-other-resource-types"></a>Alerta de registo para todos os outros tipos de recursos

> [!NOTE]
> Atualmente, não existem taxas adicionais para a versão API `2020-05-01-preview` e alertas de registo centrados em recursos.  Os preços das funcionalidades que estão em pré-visualização serão anunciados no futuro e um aviso fornecido antes do início da faturação. Caso opte por continuar a utilizar novas versões API e alertas de registo centrados em recursos após o período de pré-aviso, será faturado à taxa aplicável.

1. Comece pelo **separador Condição:**

    1. Verifique se a [**medida**](./alerts-unified-log.md#measure), [**tipo de agregação**](./alerts-unified-log.md#aggregation-type)e [**granularidade de agregação e granularidade**](./alerts-unified-log.md#aggregation-granularity) estão corretas. 
        1. Por predefinição, a regra conta o número de resultados nos últimos 5 minutos.
        1. Se detetarmos resultados de consulta sumário, a regra será atualizada automaticamente dentro de alguns segundos para capturar isso.

    1. Escolha [a divisão de alerta por dimensões,](./alerts-unified-log.md#split-by-alert-dimensions)se necessário: 
       - **A coluna de ID de recursos** é selecionada automaticamente, se detetada, e altera o contexto do alerta disparado para o recurso do registo. 
       - **A coluna de ID de recursos** pode ser desessionada para alertas de incêndio em grupos de subscrição ou recursos. A desescodagem é útil quando os resultados da consulta são baseados em recursos cruzados. Por exemplo, uma consulta que verifica se 80% das máquinas virtuais do grupo de recursos estão a ter uma elevada utilização de CPU.
       - Podem também ser selecionadas até seis divisões para qualquer número ou tipos de colunas de texto utilizando a tabela de dimensões.
       - Os alertas são disparados separadamente de acordo com a divisão com base em combinações únicas e a carga útil de alerta inclui esta informação.
    
        ![Selecione parâmetros de agregação e divisão](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. O **gráfico de pré-visualização** mostra os resultados das avaliações de consulta ao longo do tempo. Pode alterar o período de gráfico ou selecionar séries de tempo diferentes que resultaram da divisão de alertas únicas por dimensões.

        ![Gráfico de pré-visualização](media/alerts-log/preview-chart.png)

    1. Em seguida, com base nos dados de pré-visualização, desateie a **lógica de Alerta;** [ **Operador,** **Valor Limiar**](./alerts-unified-log.md#threshold-and-operator)e [**Frequência.**](./alerts-unified-log.md#frequency)

        ![Gráfico de pré-visualização com lógica de limiar e alerta](media/alerts-log/chart-and-alert-logic.png)

    1. Pode configurar opcionalmente [**número de violações para desencadear o alerta**](./alerts-unified-log.md#number-of-violations-to-trigger-alert) na secção de **opções Avançadas.**
    
        ![Opções avançadas](media/alerts-log/advanced-options.png)

1. No separador **Ações,** selecione ou crie os [grupos de ação necessários.](./action-groups.md)

    ![Separador de ações](media/alerts-log/actions-tab.png)

1. No separador **Detalhes,** defina os detalhes da **regra alerta** e detalhes **do Projeto**. Pode configurar opcionalmente se não **deve começar** a funcionar agora , ou [**ações de silenciamento**](./alerts-unified-log.md#state-and-resolving-alerts) por um período após o incêndio da regra de alerta.

    > [!NOTE]
    > As regras de alerta de registo são atualmente apátridas e disparam uma ação sempre que um alerta é criado, a menos que o silenciamento seja definido.

    ![Separador de detalhes](media/alerts-log/details-tab.png)

1. No **separador Tags,** desaprote as etiquetas necessárias no recurso de regra de alerta.

    ![Separador tags](media/alerts-log/tags-tab.png)

1. No **separador 'Rever +' criar,** uma validação será executada e informará de quaisquer problemas. Reveja e aprove a definição de regra.
1. Se todos os campos estiverem corretos, selecione o botão **Criar** e complete a criação da regra de alerta. Todos os alertas podem ser vistos a partir da gestão de alertas.
 
    ![Rever e criar separador](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Ver & gerir alertas de registo no portal Azure

1. No [portal,](https://portal.azure.com/)selecione o recurso relevante ou o serviço **Monitor.** Em seguida, **selecione Alertas** na secção Monitor.

1. A gestão de alertas mostra todos os alertas que dispararam. [Saiba mais sobre a gestão de alertas.](alerts-managing-alert-instances.md)

    > [!NOTE]
    > As regras de alerta de registo são atualmente [apátridas e não resolvem](./alerts-unified-log.md#state-and-resolving-alerts).

1. Selecione Gerir o botão **de regras de alerta** na barra superior para editar as regras:

    ![ gerir regras de alerta](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Gerir alertas de registo usando o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell não é atualmente suportado na versão API `2020-05-01-preview`

Os cmdlets PowerShell listados abaixo estão disponíveis para gerir regras com as [Regras de Consulta Agendadas API](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell cmdlet para criar uma nova regra de alerta de registo.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell cmdlet para atualizar uma regra de alerta de registo existente.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell cmdlet para criar ou atualizar objetos que especificam parâmetros de origem para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell cmdlet para criar ou atualizar o objeto especificando os parâmetros do calendário para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell cmdlet para criar ou atualizar o objeto especificando parâmetros de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet.
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell cmdlet para criar ou atualizar objetos especificando parâmetros de grupos de ação para um alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet.
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell cmdlet para criar ou atualizar o objeto especificando os parâmetros da condição do gatilho para o alerta de registo. Usado como entrada por [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) cmdlet.
- [Novo-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell cmdlet para criar ou atualizar o objeto especificando parâmetros de condição de gatilho métrico para [o alerta de registo do tipo de medição métrica](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). Usado como entrada por [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet.
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell cmdlet para listar as regras de alerta de registo existentes ou uma regra específica de alerta de registo
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : PowerShell cmdlet para ativar ou desativar a regra de alerta de registo
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): PowerShell cmdlet para eliminar uma regra de alerta de registo existente

> [!NOTE]
> Os cmdlets Desagravamentos Descaídos Agendados só podem gerir as regras criadas na atual [API das Regras de Consulta Agendadas](/rest/api/monitor/scheduledqueryrules/). As regras de alerta de registo criadas utilizando o legado [Log Analytics Alert API](./api-alerts.md) só podem ser geridas utilizando o PowerShell depois de [mudar para API regras de consulta programadas](../alerts/alerts-log-api-switch.md).

Aqui estão os passos de exemplo para criar uma regra de alerta de registo utilizando o PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

Aqui estão passos de exemplo para criar uma regra de alerta de registo usando o PowerShell com consultas de recursos cruzados:

```powershell
$authorized = @ ("/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicewsCrossExample", "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceAppInsights")

$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews" -AuthorizedResource $authorized

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition
New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name" 
```

Também pode criar o alerta de registo utilizando um [modelo e ficheiros de parâmetros](./alerts-log-create-templates.md) utilizando o PowerShell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Gestão de alertas de registo usando CLI

> [!NOTE]
> O suporte Azure CLI só está disponível para a versão API agendada de Regras `2020-05-01-preview` API e acima. A versão API perviosa pode usar o CLI do Gestor de Recursos Azure com modelos como descrito abaixo. Se utilizar o legado [Log Analytics Alert API,](./api-alerts.md)terá de mudar para utilizar o CLI. [Saiba mais sobre a troca.](./alerts-log-api-switch.md)

As secções anteriores descreveram como criar, visualizar e gerir as regras de alerta de registo utilizando o portal Azure. Esta secção descreverá como fazer o mesmo utilizando o [Azure CLI](/cli/azure/get-started-with-azure-cli)da plataforma transversal . A forma mais rápida de começar a usar o Azure CLI é através da [Azure Cloud Shell](../../cloud-shell/overview.md). Para este artigo, usaremos a Cloud Shell.

1. Vá ao portal Azure, selecione **Cloud Shell**.

1. No momento, pode utilizar comandos com ``--help`` opção de saber mais sobre o comando e como usá-lo. Por exemplo, o seguinte comando mostra-lhe a lista de comandos disponíveis para criar, visualizar e gerir alertas de registo:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. Pode criar uma regra de alerta de registo que monitorize a contagem de erros de evento do sistema:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. Pode ver todos os alertas de registo num grupo de recursos utilizando o seguinte comando:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. Pode ver os detalhes de uma determinada regra de alerta de registo utilizando o nome ou o ID de recurso da regra:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. Pode desativar uma regra de alerta de registo utilizando o seguinte comando:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. Pode eliminar uma regra de alerta de registo utilizando o seguinte comando:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Também pode utilizar o CLI do Gestor de Recursos Azure com [ficheiros de modelos:](./alerts-log-create-templates.md)

```azurecli
az login

az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Sobre o sucesso da criação, 201 é devolvido. No sucesso para a atualização, 200 são devolvidos.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os alertas de registo.](./alerts-unified-log.md)
* Crie alertas de registo utilizando [modelos de gestor de recursos Azure](./alerts-log-create-templates.md).
* Compreenda [as ações do webhook para alertas de registo.](./alerts-log-webhook.md)
* Saiba mais sobre [consultas de registo.](../logs/log-query-overview.md)
