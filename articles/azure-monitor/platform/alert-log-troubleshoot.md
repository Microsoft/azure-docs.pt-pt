---
title: Resolução de problemas de alertas de registo no Azure Monitor | Documentos da Microsoft
description: Comuns problemas, erros e resolução de regras de alerta de registo no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578718"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Resolução de problemas de alertas de registo no Azure Monitor  

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como resolver problemas comuns detetados quando a configuração de alertas de registo no Azure Monitor. Ele também fornece soluções para perguntas freqüentes sobre a funcionalidade ou configuração de alertas de registo. 

O termo **alertas de registo** descreve os alertas que fire com base numa consulta de log num [área de trabalho do Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidades, terminologia e tipos no [alertas - descrição geral de registo](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considere casos em que o portal do Azure mostra e alerta acionado de regra e uma notificação realizadas por um grupo de ação associada (s). Para tais casos, consulte detalhes no artigo sobre [grupos de ação](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alerta de registo não são acionados

Eis algumas razões comuns um configurado [regra de alerta de registo no Azure Monitor](../platform/alerts-log.md) Estado não mostra [como *disparado* quando esperado](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para os registos

Alerta de registo executado periodicamente sua consulta com base na [do Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Porque o Azure Monitor processa muitos terabytes de dados a partir de milhares de clientes de origens variadas em todo o mundo, o serviço é suscetível a um atraso de tempo diferentes. Para obter mais informações, consulte [tempo de ingestão de dados nos registos do Azure Monitor](../platform/data-ingestion-time.md).

Para atenuar o atraso de ingestão de dados, o sistema deve aguardar e repete a consulta de alerta várias vezes se ele encontrar que os dados necessários não são ingeridos ainda. O sistema tem um tempo de espera aumentar exponencialmente definido. Os registo alerta apenas acionadores depois dos dados estão disponíveis para que eles atraso podem ser devido a ingestão de dados de registo lento. 

### <a name="incorrect-time-period-configured"></a>Período de hora incorreto configurado

Conforme descrito no artigo [terminologia para alertas de registo](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), o tempo de configuração no declarado períoda Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo de tempo. Período de tempo restringe os dados obtidos para a consulta de registo evitar abusos e evita qualquer comando de tempo (como *há*) utilizado numa consulta de registo. Por exemplo, se o período de tempo é definido como 60 minutos, e a consulta é executada em 1 às 15:15, apenas os registos criados entre 12:15 e 1 às 15:15 são utilizados para a consulta de registo. Se a consulta de registo utiliza um comando de tempo como *há (1 dia)*, a consulta utiliza apenas dados entre 12:15 e 1 às 15:15 porque o período de tempo é definido como esse interval.*

Por conseguinte, a verificação esse período de tempo na configuração corresponde à sua consulta. Por exemplo, mencionado anteriormente, se a consulta de registo utiliza *há (1 dia)* como é mostrado com marcador verde, em seguida, o período de tempo deve ser definido como 24 horas ou 1440 minutos (como indicado em vermelho), para garantir que a consulta for executada conforme pretendido.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>Suprimir alertas a opção estiver definida

Conforme descrito no passo 8 deste artigo [criar uma regra de alerta de registo no portal do Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), os alertas de registo fornecem uma **suprimir alertas** opção para suprimir as ações de acionamento e de notificação para um período de configurado tempo. Como resultado, pode considerar que um alerta não são disparados enquanto na realidade ele fez, mas foi suprimido.  

![Suprimir Alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>Regra de alerta de medida da métrica está incorreta

**Alertas de registo de medida da métrica** são um subtipo de alertas de registo, que têm capacidades especiais e uma sintaxe de consulta de alerta restrito. A consulta de saída para ser uma série de métrica de tempo; necessita de uma regra de alerta de registo de medida da métrica ou seja, uma tabela com distintos igualmente tamanho períodos de tempo, juntamente com os valores agregados correspondentes. Além disso, os usuários podem escolher têm variáveis adicionais da tabela juntamente com AggregatedValue. Estas variáveis podem ser utilizadas para ordenar a tabela. 

Por exemplo, suponha que uma regra de alerta de registo de medida da métrica foi configurada como:

- consulta era: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- período de tempo de seis horas
- limiar de 50
- lógica de alerta de falhas consecutivas três
- Agregação após escolhido como $table

Uma vez que o comando inclui *resumir... por* e fornecidas duas variáveis (timestamp & $table), o sistema escolhe $table para *agregado após*. Ordena a tabela de resultados pelo campo *$table* conforme mostrado abaixo e, em seguida, analisa a vários AggregatedValue para cada tipo de tabela (como availabilityResults) para ver se haveria falhas consecutivas de 3 ou mais.

![Execução da consulta medida métrica com vários valores](media/alert-log-troubleshoot/LogMMQuery.png)

Como *agregado após* está definida nas *$table*, os dados são ordenados na coluna de $table (como em vermelho); em seguida, temos de grupo e procurar por tipos de *agregado após* campo (ou seja) $table para exemplo: os valores do availabilityResults será considerado como um desenho/entidade (como destacado na cor de laranja). Este valor plotagem/entidade Serviço de alertas verifica a existência de falhas consecutivas três ocorrendo (como mostra a verde) para a alerta irá obter acionada para o valor de tabela "availabilityResults". Da mesma forma, se em qualquer outro valor de $table se são visualizadas três falhas consecutivas - outra notificação de alertas será acionada para a mesma coisa; com o serviço de alertas classificar automaticamente os valores num desenho/entidade (como cor de laranja) por vez.

Agora suponha que, medida da métrica regra de alerta de registo foi modificada e a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` com o restante da configuração restante mesmo que antes de incluir a lógica de alerta de falhas consecutivas três. Opção "Aggregate após" nesse caso será por predefinição: timestamp. Uma vez que apenas um valor é fornecido na consulta para *resumir... por* (ou seja) *timestamp*; semelhante ao exemplo anterior, no final da execução, a saída seria conforme ilustrado abaixo.

   ![Métrica execução de consultas de medição com o valor único](media/alert-log-troubleshoot/LogMMtimestamp.png)

Como *agregado após* é definida no período de tempo, os dados são ordenados na *timestamp* coluna (como em vermelho), em seguida, podemos agrupar por timestamp por exemplo: os valores de `2018-10-17T06:00:00Z` será considerado como um desenho/entidade (como realçado na cor de laranja). Este valor plotagem/entidade Serviço de alertas encontrará sem falhas consecutivas que (como a cada valor de timestamp tem apenas uma entrada) e, por conseguinte, alertas nunca irão obter ativadas. Por conseguinte, nesse caso, utilizador tem de ser:

- Adicionar uma variável fictícia ou uma variável existente (como $table) a corretamente classificação concluído com o campo "Aggregate após" configurado
- (Ou) reconfigurar a regra de alerta para utilizar a lógica de alerta com base na *total de violação* em vez disso adequadamente

## <a name="log-alert-fired-unnecessarily"></a>Alerta de registo acionado desnecessariamente

Próxima detalhada são algumas razões comuns, por que um configurado [regra de alerta de registo no Azure Monitor](../platform/alerts-log.md) podem ser acionadas quando visualizado no [alertas do Azure](../platform/alerts-managing-alert-states.md), quando não espera que sejam acionados.

### <a name="alert-triggered-by-partial-data"></a>Alerta acionada por dados parciais

Análise que alimenta o Log Analytics e Application Insights está sujeitos aos atrasos de ingestão e de processamento; devido a que, ao tempo quando é executada a consulta de alerta de registo fornecida - pode haver um caso de dados não está disponível ou apenas alguns dados estejam disponíveis. Para obter mais informações, consulte [tempo de ingestão de dados de registo no Azure Monitor](../platform/data-ingestion-time.md).

Dependendo de como a regra de alerta é configurada, pode haver enganássemos firing se não existir nenhuma ou apenas dados parciais nos registos no momento da execução de alerta. Nesses casos, Aconselhamo-lo para alterar a configuração ou de consulta de alerta. 

Por exemplo, se a regra de alerta de registo está configurada para acionar quando o número de resultados de uma consulta do analytics é inferior a 5, em seguida, o alerta for acionado quando não existe (zero registo) de dados ou resultados parciais (um registo). No entanto, após o atraso de ingestão de dados, a mesma consulta com dados completa pode fornecer um resultado de 10 registos.

### <a name="alert-query-output-misunderstood"></a>Saída de consulta de alerta mal compreendida

Fornecer a lógica para alertas de registo numa consulta do analytics. A consulta do analytics pode utilizar várias grandes quantidades de dados e funções matemáticas.  O serviço de alerta executa a consulta em intervalos especificados com os dados para o período de tempo especificado. O serviço de alerta faz alterações sutis para consulta fornecida com base no tipo de alerta escolhido. Esta alteração pode ser visualizada na secção "Consultar a ser executado" na *lógica de sinal de configurar* ecrã, conforme mostrado abaixo: ![Consulta a ser executado](media/alert-log-troubleshoot/LogAlertPreview.png)

O que é mostrado na **consulta a ser executado** caixa está o que é executado o serviço de alerta de registo. Pode executar a consulta declarada como timespan através de [portal do Analytics](../log-query/portals.md) ou o [API de análise de](https://docs.microsoft.com/rest/api/loganalytics/) se quiser entender o que a consulta de alerta de saída pode ser antes de criar, na verdade, o alerta.

## <a name="log-alert-was-disabled"></a>Alerta de registo foi desativado

Abaixo encontram-se alguns motivos devido à qual [regra de alerta de registo no Azure Monitor](../platform/alerts-log.md) podem ser desativadas pelo Azure Monitor.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>Recursos em que foi criado alerta já não existe

Um recurso específico, como uma área de trabalho do Log Analytics do Azure, a aplicação de Azure Application Insights e o recurso do Azure de destino de regras de alerta de registo criadas no Azure Monitor. E o serviço de alerta de registo, em seguida, irá executar a consulta de análise fornecida na regra para o destino especificado. Mas, após a criação de regra, utilizadores, muitas vezes, avance para o eliminar do Azure ou mover dentro do Azure - o destino da regra de alerta. Como o destino da regra de alerta de registo já não é válido, a execução da regra de falha.

Nesses casos, o Azure Monitor irá desativar o alerta de registo e certifique-se de que os clientes não são faturados desnecessariamente, quando a regra em si não é capaz de executar continuamente durante o período especial, como uma semana. Os utilizadores podem encontrar no momento exato em que a regra de alerta de registo foi desativada pelo Azure Monitor através de [registo de atividades do Azure](../../azure-resource-manager/resource-group-audit.md). No registo de atividades do Azure quando a regra de alerta de registo está desativada pelo Azure, é adicionado um evento no registo de atividades do Azure.

Um evento de exemplo no registo de atividades do Azure para desativar a regra de alerta devido à sua falha contínua; é mostrado abaixo.

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

### <a name="query-used-in-log-alert-is-not-valid"></a>Consulta utilizada no alerta de registo não é válida

Cada regra de alerta de registo criada no Azure Monitor, como parte de sua configuração tem de especificar uma consulta do analytics para ser executada periodicamente pelo serviço de alerta. Enquanto a consulta do analytics pode ter a sintaxe correta no momento da criação de regras ou atualização. Algumas vezes num período de tempo, a consulta fornecem no registo de regra de alerta pode desenvolver problemas de sintaxe e fazer com que a execução da regra para começam a falhar. Algumas razões comuns, por que a consulta do analytics fornecida numa regra de alerta de registo pode desenvolver erros são:

- Consulta é escrita [são executadas em vários recursos](../log-query/cross-workspace-query.md) e um ou mais dos recursos especificados, agora existe.
- Não houve nenhum fluxo de dados para a plataforma de análise, devido a que o [oferece a execução da consulta erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não existem dados para a consulta fornecida não existe.
- Alterações na [linguagem de consulta](https://docs.microsoft.com/azure/kusto/query/) ocorreram nas quais comandos e as funções têm um formato revisado. Por conseguinte, a consulta fornecida anteriormente na regra de alerta já não é válida.

O usuário deverá ser avisado desse comportamento pela primeira vez [do Assistente do Azure](../../advisor/advisor-overview.md). Uma recomendação seria adicionada para a regra de alerta de registo específicos no Assistente do Azure, na categoria de elevada disponibilidade com impacto intermédio e a descrição, como "Reparar a sua regra de alerta de registo para garantir a monitorização". Se depois de sete dias da recomendação fornecendo no Assistente do Azure a consulta de alerta na regra de alerta de registo especificado não é retificada. Em seguida, o Azure Monitor desativar o alerta de registo e certifique-se de que os clientes não são faturados desnecessariamente, quando a regra em si não é capaz de executar continuamente durante o período especial, como uma semana.

Os utilizadores podem encontrar no momento exato em que a regra de alerta de registo foi desativada pelo Azure Monitor através de [registo de atividades do Azure](../../azure-resource-manager/resource-group-audit.md). No registo de atividades do Azure, quando a regra de alerta de registo está desativada pelo Azure - é adicionado um evento no registo de atividades do Azure.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [alertas de registo nos alertas do Azure](../platform/alerts-unified-log.md)
- Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md)
- Saiba mais sobre [consultas de registo](../log-query/log-query-overview.md)
