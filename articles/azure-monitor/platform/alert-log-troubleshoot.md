---
title: Solucionar problemas de alertas de log no Azure Monitor | Microsoft Docs
description: Problemas comuns, erros e resoluções para regras de alerta de log no Azure.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: aa2f3481b63c98ec23e1db8213939278684a4cd6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977646"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Solucionar problemas de alertas de log no Azure Monitor  

Este artigo mostra como resolver problemas comuns que podem acontecer quando você está Configurando alertas de log no Azure Monitor. Ele também fornece soluções para problemas comuns com a funcionalidade ou a configuração de alertas de log.

O termo *alertas de log* descreve as regras que são acionadas com base em uma consulta de log em um [espaço de trabalho log Analytics do Azure](../learn/tutorial-viewdata.md) ou no [aplicativo Azure insights](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidade, terminologia e tipos em [alertas de log em Azure monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera casos em que a portal do Azure mostra uma regra de alerta disparada e uma notificação não é executada por um grupo de ação associado. Para esses casos, consulte os detalhes em [criar e gerenciar grupos de ações no portal do Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>O alerta de log não foi acionado

Aqui estão alguns motivos comuns pelos quais o estado de uma [regra de alerta de log](../platform/alerts-log.md) configurada no Azure monitor não é exibido [como *acionado* quando esperado](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para logs

Um alerta de log executa periodicamente sua consulta com base em [log Analytics](../learn/tutorial-viewdata.md) ou [Application insights](../../azure-monitor/app/analytics.md). Como o Azure Monitor processa muitos terabytes de dados de milhares de clientes de fontes variadas em todo o mundo, o serviço é suscetível a diferentes atrasos de tempo. Para obter mais informações, consulte [tempo de ingestão de dados em logs de Azure monitor](../platform/data-ingestion-time.md).

Para atenuar os atrasos, o sistema aguarda e repete a consulta de alerta várias vezes se encontrar os dados necessários ainda não está ingerido. O sistema tem um tempo de espera cada vez maior definido. O alerta de log é disparado somente depois que os dados estão disponíveis, portanto, o atraso pode ser devido à ingestão lenta de dados de log.

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado

Conforme descrito no artigo sobre [terminologia para alertas de log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta retorna somente os registros que foram criados nesse intervalo.

O período de tempo restringe os dados buscados para uma consulta de log para evitar abusos e evita qualquer comando de tempo (como **atrás**) usado em uma consulta de log. Por exemplo, se o período de tempo for definido como 60 minutos e a consulta for executada às 1:15 PM, somente os registros criados entre 12:15 PM e 1:15 PM serão usados para a consulta de log. Se a consulta de log usar um comando de tempo como **atrás (1D)** , a consulta ainda usará apenas dados entre 12:15 pm e 1:15, pois o período de tempo é definido para esse intervalo.

Verifique se o período de tempo na configuração corresponde à sua consulta. Para o exemplo mostrado anteriormente, se a consulta de log usar **atrás (1D)** com o marcador verde, o período de tempo deve ser definido como 24 horas ou 1.440 minutos (indicado em vermelho). Essa configuração garante que a consulta seja executada conforme o esperado.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção suprimir alertas está definida

Conforme descrito na etapa 8 do artigo sobre como [criar uma regra de alerta de log na portal do Azure, os](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)alertas de log fornecem uma opção de **supressão de alertas** para suprimir ações de disparo e de notificação por um período de tempo configurado. Como resultado, você pode imaginar que um alerta não foi acionado. Na verdade, ele foi acionado, mas foi suprimido.  

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra de alerta de medição métrica está incorreta

Os *alertas de log de medição de métrica* são um subtipo de alertas de log que têm recursos especiais e uma sintaxe de consulta de alerta restrita. Uma regra para um alerta de log de medição de métrica exige que a saída da consulta seja uma série temporal. Ou seja, a saída é uma tabela com períodos de tempo distintos e uniformemente dimensionados, juntamente com valores agregados correspondentes.

Você pode optar por ter variáveis adicionais na tabela junto com **AggregatedValue**. Essas variáveis podem ser usadas para classificar a tabela.

Por exemplo, suponha que uma regra para um alerta de log de medição de métrica foi configurada como:

- Consulta de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Período de 6 horas
- Limite de 50
- Lógica de alerta de três violações consecutivas
- **Agregar após** escolhido como **$Table**

Porque o comando inclui **resumir... por** e fornece duas variáveis (**timestamp** e **$Table**), o sistema escolhe **$Table** para **agregação**. O sistema classifica a tabela de resultados pelo campo **$Table** , conforme mostrado na captura de tela a seguir. Em seguida, ele examina as várias instâncias de **AggregatedValue** para cada tipo de tabela (como **availabilityResults**) para ver se havia três ou mais violações consecutivas.

![Execução de consulta de medição métrica com vários valores](media/alert-log-troubleshoot/LogMMQuery.png)

Como **Aggregate** on é definido em **$Table**, os dados são classificados em uma coluna **$Table** (indicada em vermelho). Em seguida, agrupamos e procuramos tipos de **agregação no** campo.

Por exemplo, para **$Table**, os valores de **availabilityResults** serão considerados como um gráfico/uma entidade (indicado em laranja). Nesse valor de plotagem/entidade, o serviço de alerta verifica três violações consecutivas (indicadas em verde). As violações disparam um alerta para o valor de tabela **availabilityResults**.

Da mesma forma, se três violações consecutivas ocorrerem para qualquer outro valor de **$Table**, outra notificação de alerta será disparada para a mesma coisa. O serviço de alerta classifica automaticamente os valores em um gráfico/entidade (indicado em laranja) por tempo.

Agora suponha que a regra para o alerta do log de medição de métrica foi modificada e a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. O restante da configuração permaneceu o mesmo que antes, incluindo a lógica de alerta para três violações consecutivas. A opção **agregar sobre** , nesse caso, é **carimbo de data/hora** por padrão. Apenas um valor é fornecido na consulta para **resumir... por** (ou seja, **carimbo de data/hora**). Como no exemplo anterior, a saída no final da execução seria conforme ilustrado a seguir.

   ![Execução de consulta de medição métrica com valor singular](media/alert-log-troubleshoot/LogMMtimestamp.png)

Como **Aggregate** on é definido no **timestamp**, os dados são classificados na coluna **timestamp** (indicada em vermelho). Em seguida, agrupamos por **carimbo de data/hora**. Por exemplo, os valores para `2018-10-17T06:00:00Z` serão considerados como um gráfico/uma entidade (indicado em laranja). Nesse valor de plotagem/entidade, o serviço de alerta não encontrará violações consecutivas (porque cada valor de **carimbo de data/hora** tem apenas uma entrada). Portanto, o alerta nunca é disparado. Nesse caso, o usuário deve:

- Adicione uma variável fictícia ou uma variável existente (como **$Table**) para classificar corretamente usando o campo **agregar sobre** .
- Reconfigure a regra de alerta para usar a lógica de alerta com base na **violação total** .

## <a name="log-alert-fired-unnecessarily"></a>Alerta de log acionado desnecessariamente

Uma [regra de alerta de log](../platform/alerts-log.md) configurada no Azure monitor pode ser disparada inesperadamente quando você a exibe nos [alertas do Azure](../platform/alerts-managing-alert-states.md). As seções a seguir descrevem alguns motivos comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta disparado por dados parciais

Log Analytics e Application Insights estão sujeitos a atrasos de ingestão e processamento. Ao executar uma consulta de alerta de log, você pode achar que nenhum dado está disponível ou apenas alguns dados estão disponíveis. Para obter mais informações, veja [tempo de ingestão de dados de log em Azure monitor](../platform/data-ingestion-time.md).

Dependendo de como você configurou a regra de alerta, o erro de acionamento pode ocorrer se não houver dados ou dados parciais em logs no momento da execução do alerta. Nesses casos, aconselhamos que você altere a configuração ou a consulta de alerta.

Por exemplo, se você configurar a regra de alerta de log para ser disparada quando o número de resultados de uma consulta de análise for menor que 5, o alerta será disparado quando não houver dados (registro zero) ou resultados parciais (um registro). Mas, após o atraso de ingestão de dados, a mesma consulta com dados completos pode fornecer um resultado de 10 registros.

### <a name="alert-query-output-is-misunderstood"></a>A saída da consulta de alerta é mal compreendida

Você fornece a lógica para alertas de log em uma consulta do Analytics. A consulta de análise pode usar várias funções Big Data e matemáticas. O serviço de alerta executa sua consulta em intervalos especificados com dados por um período de tempo especificado. O serviço de alerta faz alterações sutis na consulta com base no tipo de alerta. Você pode exibir essa alteração na seção **consulta a ser executada** na tela **Configurar lógica de sinal** :

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

A caixa **consulta a ser executada** é o que o serviço de alerta de log executa. Se você quiser entender o que a saída de consulta de alerta pode ser antes de criar o alerta, você pode executar a consulta declarada e o período por meio do [portal de análise](../log-query/portals.md) ou da [API de análise](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>O alerta de log foi desabilitado

As seções a seguir listam alguns motivos pelos quais Azure Monitor pode desabilitar a [regra de alerta de log](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>O recurso no qual o alerta foi criado não existe mais

As regras de alerta de log criadas no Azure Monitor alvo de um recurso específico, como um espaço de trabalho do Azure Log Analytics, um aplicativo do insights Aplicativo Azure e um recurso do Azure. Em seguida, o serviço de alerta de log executará uma consulta de análise fornecida na regra para o destino especificado. Mas, após a criação da regra, os usuários costumam continuar a excluir do Azure – ou mover para dentro do Azure – o destino da regra de alerta de log. Como o destino da regra de alerta não é mais válido, a execução da regra falhará.

Nesses casos, Azure Monitor desabilita o alerta de log e garante que você não seja cobrado desnecessariamente quando a regra não puder ser executada continuamente por período dimensionável (como uma semana). Você pode descobrir o momento exato em que Azure Monitor desabilitou o alerta de log por meio do [log de atividades do Azure](../../azure-resource-manager/management/view-activity-logs.md). No log de atividades do Azure, um evento é adicionado quando Azure Monitor desabilita a regra de alerta de log.

O seguinte evento de exemplo no log de atividades do Azure é para uma regra de alerta que foi desabilitada devido a uma falha contínua.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>A consulta usada em um alerta de log não é válida

Cada regra de alerta de log criada em Azure Monitor como parte de sua configuração deve especificar uma consulta de análise que o serviço de alerta irá executar periodicamente. A consulta do Analytics pode ter a sintaxe correta no momento da criação ou atualização da regra. Mas, às vezes, ao longo de um período de tempo, a consulta fornecida na regra de alerta de log pode desenvolver problemas de sintaxe e fazer com que a execução da regra falhe. Alguns motivos comuns pelos quais uma consulta de análise fornecida em uma regra de alerta de log pode desenvolver erros são:

- A consulta é gravada para ser [executada em vários recursos](../log-query/cross-workspace-query.md). E um ou mais dos recursos especificados não existem mais.
- O [alerta de log do tipo de medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurado tem uma consulta de alerta que não está em conformidade com as normas de sintaxe
- Não houve fluxo de dados para a plataforma de análise. A [execução da consulta gera um erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não há dados para a consulta fornecida.
- As alterações na [linguagem de consulta](https://docs.microsoft.com/azure/kusto/query/) incluem um formato revisado para comandos e funções. Portanto, a consulta fornecida anteriormente em uma regra de alerta não é mais válida.

O [Azure Advisor](../../advisor/advisor-overview.md) avisa você sobre esse comportamento. Uma recomendação é adicionada para a regra de alerta de log específica no Azure Advisor, sob a categoria de alta disponibilidade com impacto médio e uma descrição de "reparar sua regra de alerta de log para garantir o monitoramento". Se uma consulta de alerta na regra de alerta de log não for corrigida depois que o Azure Advisor tiver fornecido uma recomendação por sete dias, Azure Monitor desabilitará o alerta de log e garantirá que você não seja cobrado desnecessariamente quando a regra não puder ser executada continuamente por um período dimensionável ( como uma semana).

Você pode encontrar a hora exata em que Azure Monitor desabilitou a regra de alerta de log procurando um evento no [log de atividades do Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [alertas de log no Azure](../platform/alerts-unified-log.md).
- Saiba mais sobre [Application insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [consultas de log](../log-query/log-query-overview.md).
