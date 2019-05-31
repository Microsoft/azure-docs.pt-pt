---
title: Resolver problemas relacionados com alertas de registo no Azure Monitor | Documentos da Microsoft
description: Problemas comuns, erros e resoluções para regras de alerta de registo no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 8b1a9b3dee999a35950559a049230f7fdbbc47b6
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399195"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Resolver problemas relacionados com alertas de registo no Azure Monitor  

Este artigo mostra-lhe como resolver problemas comuns que poderão ocorrer se estiver configurando a alertas de registo no Azure Monitor. Ele também fornece soluções para problemas comuns com a funcionalidade ou configuração de alertas de registo. 

O termo *alertas de registo* descreve os alertas que fire com base numa consulta de log num [área de trabalho do Log Analytics do Azure](../learn/tutorial-viewdata.md) ou no [Azure Application Insights](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidades, terminologia e tipos no [alertas de registo no Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera os casos em que o portal do Azure mostra uma regra de alerta acionada e uma notificação não é efetuada por um grupo de ação associada. Para tais casos, consulte os detalhes na [criar e gerir grupos de ação no portal do Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alerta de registo não são acionados

Aqui estão algumas razões comuns de por que o estado de um configurado [regra de alerta de registo no Azure Monitor](../platform/alerts-log.md) não mostra [como *disparado* quando esperado](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para os registos

Um alerta de registo executado periodicamente sua consulta com base na [do Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Porque o Azure Monitor processa muitos terabytes de dados a partir de milhares de clientes de origens variadas em todo o mundo, o serviço está suscetível a atrasos de tempo diferentes. Para obter mais informações, consulte [tempo de ingestão de dados nos registos do Azure Monitor](../platform/data-ingestion-time.md).

Para mitigar atrasos, o sistema deve aguardar e repete a consulta de alerta várias vezes se ele encontrar que os dados necessários não são ingeridos ainda. O sistema tem um tempo de espera aumentar exponencialmente definido. O alerta de registo é acionado apenas depois dos dados estão disponíveis, portanto, o atraso pode ser devido a lenta ingestão de dados de registo. 

### <a name="incorrect-time-period-configured"></a>Período de hora incorreto configurado

Conforme descrito no artigo [terminologia para alertas de registo](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo. 

O período de tempo restringe os dados obtidos de uma consulta de registo evitar abusos e ele evita que qualquer comando de tempo (como **há**) utilizado numa consulta de registo. Por exemplo, se o período de tempo é definido como 60 minutos, e a consulta é executada em 1 às 15:15, apenas os registos criados entre 12:15 e 1 às 15:15 são utilizados para a consulta de registo. Se a consulta de registo utiliza um comando de tempo como **há (1 dia)** , a consulta utiliza apenas dados entre 12:15 e 1 às 15:15 porque o período de tempo é definido como esse intervalo.

Verifique que o período de tempo na configuração corresponde à sua consulta. Para o exemplo mostrado anteriormente, se a consulta de registo utiliza **há (1 dia)** com marcador verde, o período de tempo deve ser definido para 24 horas ou minutos 1.440 (indicados em vermelho). Esta definição garante que a consulta é executada conforme pretendido.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Suprimir alertas a opção estiver definida

Conforme descrito no passo 8 deste artigo [criar uma regra de alerta de registo no portal do Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), os alertas de registo fornecem um **suprimir alertas** opção para suprimir as ações de acionamento e de notificação para um período configurado de tempo. Como resultado, pode pensar que um alerta não são acionados. Na verdade, ele acionado, mas foi suprimido.  

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regra de alerta de medida da métrica está incorreta

*Alertas de registo de medida da métrica* são um subtipo de alertas de registo que têm capacidades especiais e uma sintaxe de consulta de alerta restrito. Uma regra para um alerta de registo de medida da métrica requer a consulta de saída para ser uma série de métrica de tempo. Ou seja, o resultado é uma tabela com períodos de tempo de tamanho igual, distintos, juntamente com os valores agregados correspondentes. 

Pode optar por ter variáveis adicionais da tabela juntamente com **AggregatedValue**. Estas variáveis podem ser utilizadas para ordenar a tabela. 

Por exemplo, suponha que uma regra para um alerta de registo de medida da métrica foi configurada como:

- Consulta de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- período de tempo de seis horas
- limiar de 50
- lógica de alerta de falhas consecutivas três
- **Agregação após** escolhido como **$table**

Uma vez que o comando inclui **resumir... por** e fornece duas variáveis (**timestamp** e **$table**), o sistema escolhe **$table** para **agregado após** . O sistema ordena a tabela de resultado, o **$table** campo, conforme mostrado na captura de ecrã seguinte. Em seguida, ele analisa o múltiplo **AggregatedValue** instâncias para cada tipo de tabela (como **availabilityResults**) para ver se houve três ou mais falhas consecutivas.

![Execução da consulta de medida da métrica com vários valores](media/alert-log-troubleshoot/LogMMQuery.png)

Porque **agregado após** está definida nas **$table**, os dados são ordenados num **$table** coluna (indicada em vermelho). Em seguida, temos de grupo e procurar por tipos do **agregado após** campo. 

Por exemplo, para **$table**, os valores de **availabilityResults** será considerado como um desenho/entidade (indicada na cor de laranja). Nesta plotagem de valor/entidade, o serviço de alerta verifica a existência de falhas consecutivas três (indicadas em verde). As violações acionam um alerta para o valor de tabela **availabilityResults**. 

Da mesma forma, se três falhas consecutivas acontecem para qualquer outro valor de **$table**, outra notificação de alerta é acionada para a mesma coisa. O serviço de alertas automaticamente ordena os valores num desenho/entidade (indicado na cor de laranja) por vez.

Agora suponha que a regra do alerta de registo de medida da métrica foi modificada e a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. O resto da configuração permaneceu o mesmo que antes, inclusive a lógica de alerta de falhas consecutivas três. O **agregado após** opção nesse caso, é **timestamp** por predefinição. Apenas um valor é fornecido na consulta para **resumir... por** (ou seja, **timestamp**). Semelhante ao exemplo anterior, a saída exibida na parte final da execução seria conforme ilustrado da seguinte forma.

   ![Execução da consulta de medida da métrica com o valor único](media/alert-log-troubleshoot/LogMMtimestamp.png)

Porque **agregado após** está definida nas **timestamp**, os dados são ordenados no **timestamp** coluna (indicada em vermelho). Em seguida, podemos agrupar por **timestamp**. Por exemplo, os valores de `2018-10-17T06:00:00Z` será considerado como um desenho/entidade (indicada na cor de laranja). Este valor plotagem/entidade, o serviço de alertas encontrará sem falhas consecutivas (uma vez que cada **timestamp** valor tem apenas uma entrada). Portanto, nunca é acionado o alerta. Nesse caso, o utilizador tem de ser:

- Adicionar uma variável fictícia ou uma variável existente (como **$table**) para ordenar corretamente utilizando o **agregado após** campo.
- Reconfigurar a regra de alerta para utilizar a lógica de alerta com base na **total de violação** em vez disso.

## <a name="log-alert-fired-unnecessarily"></a>Alerta de registo acionado desnecessariamente

Um configurado [regra de alerta de registo no Azure Monitor](../platform/alerts-log.md) possam ser acionadas inesperadamente quando vê-la na [alertas do Azure](../platform/alerts-managing-alert-states.md). As secções seguintes descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta acionada por dados parciais

O log Analytics e Application Insights estão sujeitos aos atrasos de ingestão e processamento. Quando executar uma consulta de alerta de registo, poderá achar que não estão disponíveis dados ou apenas de alguns dados estão disponíveis. Para obter mais informações, consulte [tempo de ingestão de dados de registo no Azure Monitor](../platform/data-ingestion-time.md).

Dependendo de como configurou a regra de alerta, misfiring pode acontecer se não há dados ou dados parciais nos registos no momento da execução de alerta. Nesses casos, Aconselhamo-lo para alterar a configuração ou a consulta de alerta. 

Por exemplo, se configurar a regra de alerta de registo para ser acionado quando o número de resultados de uma consulta do analytics é inferior a 5, o alerta é acionado quando não existe (zero registo) de dados ou resultados parciais (um registo). Mas, após o atraso de ingestão de dados, a mesma consulta com dados completa pode fornecer um resultado de 10 registos.

### <a name="alert-query-output-is-misunderstood"></a>Resultado da consulta de alerta é mal compreendido

Fornecer a lógica para alertas de registo numa consulta do analytics. A consulta do analytics pode utilizar várias grandes quantidades de dados e funções matemáticas. O serviço de alertas é executada a consulta em intervalos especificados com os dados durante um período de tempo especificado. O serviço de alertas faz alterações sutis para a consulta com base no tipo de alerta. Pode ver esta alteração no **consulta a ser executado** secção sobre o **lógica de sinal de configurar** ecrã:

![Consulta a ser executado](media/alert-log-troubleshoot/LogAlertPreview.png)

O **consulta a ser executado** caixa está o que é executado o serviço de alerta de registo. Se quiser entender o que a consulta de alerta de saída pode ser antes de criar o alerta, pode executar a consulta declarada e o período de tempo através do [portal do Analytics](../log-query/portals.md) ou o [API de análise de](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alerta de registo foi desativado

As secções seguintes listam alguns motivos por que o Azure Monitor pode desabilitar o [regra de alerta de registo](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Recursos em que o alerta foi criado já não existe

Um recurso específico como uma área de trabalho do Log Analytics do Azure, uma aplicação do Azure Application Insights e um recurso do Azure de destino de regras de alerta de registo criadas no Azure Monitor. O serviço de alerta de registo, em seguida, executará uma consulta do analytics fornecida na regra para o destino especificado. Mas, após a criação de regra, os usuários muitas vezes, avance para eliminar a partir do Azure – ou mover dentro do Azure, o destino da regra de alerta de registo. Como o destino da regra de alerta já não é válido, a execução da regra de falha.

Nesses casos, o Azure Monitor desativa o alerta de registo e garante que não cobrado desnecessariamente quando a regra não é possível executar continuamente durante um período considerável (como uma semana). Pode encontrar o tempo exato quando o Azure Monitor desativado o alerta de registo via [registo de atividades do Azure](../../azure-resource-manager/resource-group-audit.md). No registo de atividades do Azure, é adicionado um evento quando o Azure Monitor desativa a regra de alerta de registo.

O evento de exemplo seguintes no registo de atividades do Azure destina-se uma regra de alerta que foi desativada devido a uma falha contínua.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Consulta utilizada num alerta de registo não é válida

Cada regra de alerta de registo criada no Azure Monitor, como parte de sua configuração tem de especificar uma consulta do analytics que o serviço de alertas é executada periodicamente. A consulta do analytics pode ter a sintaxe correta no momento da criação de regras ou atualização. Mas, às vezes, durante um período de tempo, a consulta fornecida na regra de alerta de registo pode desenvolver problemas de sintaxe e fazer com que a execução da regra efetuar a ativação. Algumas razões comuns, por que uma consulta do analytics fornecida numa regra de alerta de registo pode desenvolver os erros são:

- A consulta é escrita [são executadas em vários recursos](../log-query/cross-workspace-query.md). E já não existem um ou mais dos recursos especificados.
- [alerta de registo do tipo de medida da métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurado tenha um alerta consulta não está em conformidade com as normas de sintaxe
- Não houve nenhum fluxo de dados para a plataforma de análise. O [execução da consulta dá um erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não existem dados para a consulta fornecida não existe.
- Alterações na [linguagem de consulta](https://docs.microsoft.com/azure/kusto/query/) incluem um formato revisado para comandos e funções. Portanto, a consulta fornecida anteriormente numa regra de alerta já não é válida.

[O Assistente do Azure](../../advisor/advisor-overview.md) avisa-o sobre esse comportamento. Uma recomendação é adicionada para a regra de alerta de registo específicos no Assistente do Azure, sob a categoria de elevada disponibilidade com impacto intermédio e uma descrição de "Reparação sua regra de alerta de registo para garantir a monitorização." Se uma consulta de alerta na regra de alerta de registo não é retificada depois do Assistente do Azure forneceu uma recomendação durante sete dias, o Azure Monitor irá desativar o alerta de registo e certifique-se de que não cobrado desnecessariamente quando a regra não é possível executar continuamente para um grande (período como uma semana).

Pode encontrar o tempo exato quando o Azure Monitor desativado a regra de alerta de registo, procurando um evento no [registo de atividades do Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [alertas de registo no Azure](../platform/alerts-unified-log.md).
- Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md).
