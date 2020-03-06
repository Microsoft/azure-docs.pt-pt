---
title: Alertas de registo de problemas no Monitor Azure Microsoft Docs
description: Questões comuns, erros e resoluções para regras de alerta de log em Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395603"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertas de registo de resolução de problemas no Monitor Azure  

Este artigo mostra-lhe como resolver problemas comuns que podem acontecer quando estiver a criar alertas de registo no Monitor Azure. Também fornece soluções para problemas comuns com funcionalidade ou configuração de alertas de registo.

Os *alertas* de registo de termo descrevem regras que disparam com base numa consulta de log num espaço de [trabalho do Azure Log Analytics](../learn/tutorial-viewdata.md) ou em Insights de [Aplicação Azure](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidade, terminologia e tipos em alertas de [registo no Monitor Azure](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera casos em que o portal Azure mostre uma regra de alerta desencadeada e uma notificação não é realizada por um grupo de ação associado. Para estes casos, consulte os detalhes em Criar e gerir grupos de [ação no portal Azure.](../platform/action-groups.md)

## <a name="log-alert-didnt-fire"></a>Alerta de registo não disparou

Aqui estão algumas razões comuns pelas quais o estado para uma regra de alerta de registo configurado [no Monitor Azure](../platform/alerts-log.md) não aparece [como *despedido* quando esperado](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para registos

Um alerta de registo executa periodicamente a sua consulta com base em [Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Uma vez que o Azure Monitor processa muitos terabytes de dados de milhares de clientes de diversas fontes em todo o mundo, o serviço é suscetível a vários atrasos de tempo. Para mais informações, consulte o tempo de [ingestão de dados nos registos do Monitor Do Azure](../platform/data-ingestion-time.md).

Para mitigar os atrasos, o sistema aguarda e retenta a consulta de alerta várias vezes se descobrir que os dados necessários ainda não estão ingeridos. O sistema tem um conjunto de tempo de espera exponencialmente crescente. O alerta de registo só é acionado após a disponibilização dos dados, pelo que o atraso pode dever-se à lentidão da ingestão de dados de registo.

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado

Tal como descrito no artigo sobre [terminologia para alertas](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)de registo, o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta devolve apenas registos que foram criados dentro desta gama.

O período de tempo restringe os dados recolhidos para uma consulta de registo para evitar abusos, e contorna qualquer comando de tempo (como **há pouco**) usado numa consulta de registo. Por exemplo, Se o período de tempo for definido para 60 minutos, e a consulta for executada às 13:15, apenas são utilizados registos criados entre as 12:15 e as 13:15 horas para a consulta de registo. Se a consulta de registo utilizar um comando de tempo como **há algum tempo (1d),** a consulta ainda utiliza apenas dados entre as 12:15 e as 13:15 porque o período de tempo está definido para esse intervalo.

Verifique se o período de tempo na configuração corresponde à sua consulta. Para o exemplo anteriormente indicado, se a consulta de registo for de **atrás (1d)** com o marcador verde, o período de tempo deve ser definido para 24 horas ou 1.440 minutos (indicado a vermelho). Esta definição garante que a consulta funciona como pretendido.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção Supressão de Alertas está definida

Conforme descrito na fase 8 do artigo sobre a criação de uma regra de alerta de [registo no portal Azure,](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)os alertas de registo fornecem uma opção **Desativação de Alertas** para suprimir ações de desencadeamento e notificação por um período de tempo configurado. Como resultado, pode pensar que um alerta não disparou. Na verdade, disparou, mas foi suprimido.  

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra do alerta de medição métrica está incorreta

*Os alertas* de registo de medição métrica são um subtipo de alertas de registo que têm capacidades especiais e uma sintaxe restrita de consulta de alerta. Uma regra para um alerta de registo de medição métrica requer que a saída da consulta seja uma série de tempo métrica. Ou seja, a saída é uma tabela com períodos de tempo distintos e igualmente dimensionados, juntamente com os valores agregados correspondentes.

Pode optar por ter variáveis adicionais na tabela ao lado do **AggregatedValue**. Estas variáveis podem ser usadas para separar a tabela.

Por exemplo, suponha que uma regra para um alerta de registo de medição métrica foi configurada como:

- Consulta de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Período de tempo de 6 horas
- Limiar de 50
- Lógica de alerta de três infrações consecutivas
- **Agregado Após** escolhido como **$table**

Porque o comando inclui **resumo... e** fornece duas variáveis **(carimbo** de tempo e **$table),** o sistema escolhe **$table** para **agregado.** O sistema classifica a tabela de resultados pelo campo **$table,** como mostra a seguinte imagem. Em seguida, olha para as múltiplas instâncias **AgregadValue** para cada tipo de tabela (como **disponibilidadeResultados**) para ver se houve três ou mais violações consecutivas.

![Execução de consulta de medição métrica com múltiplos valores](media/alert-log-troubleshoot/LogMMQuery.png)

Uma vez que o **Agregado upon** é definido em **$table,** os dados são classificados numa coluna **de $table** (indicada a vermelho). Em seguida, agrupamo-nos e procuramos tipos de campo **Agregado Upon.**

Por exemplo, para **$table**, valores de **disponibilidadeOs resultados** serão considerados como um enredo/entidade (indicado a laranja). Nesta parcela/entidade de valor, o serviço de alerta verifica três infrações consecutivas (indicadas em verde). As infrações desencadeiam um alerta para a disponibilidade do valor da **tabelaResultados**.

Da mesma forma, se acontecerem três infrações consecutivas por qualquer outro valor de **$table,** outra notificação de alerta é desencadeada para a mesma coisa. O serviço de alerta classifica automaticamente os valores de um lote/entidade (indicado a laranja) por tempo.

Agora suponha que a regra do alerta de registo de medição métrica foi modificada e que a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. O resto da configuração permaneceu o mesmo de antes, incluindo a lógica de alerta para três violações consecutivas. A opção **Agregado Após,** neste caso, é **a marca** de tempo por defeito. Apenas um valor é fornecido na consulta para **resumir... por** (isto é, **carimbo de tempo).** Tal como o exemplo anterior, a produção no final da execução seria tão ilustrada como a seguinte.

   ![Execução de consulta de medição métrica com valor singular](media/alert-log-troubleshoot/LogMMtimestamp.png)

Como o **Agregado Sobre** é definido no **carimbo**temporal, os dados são classificados na coluna do carimbo de **tempo** (indicado a vermelho). Depois agrupamo-nos por **carimbo**temporal. Por exemplo, os valores para `2018-10-17T06:00:00Z` serão considerados como um enredo/entidade (indicado a laranja). Neste lote/entidade de valor, o serviço de alerta não encontrará infrações consecutivas (porque cada valor de **carimbo** tem apenas uma entrada). Então o alerta nunca é acionado. Nesse caso, o utilizador deve:

- Adicione uma variável de boneco ou uma variável existente (como **$table)** para classificar corretamente utilizando o campo **Agregado Sobre.**
- Reconfigure a regra de alerta para utilizar a lógica de alerta com base na **violação total.**

## <a name="log-alert-fired-unnecessarily"></a>Alerta de log disparado desnecessariamente

Uma regra de alerta de registo configurada [no Monitor Azure](../platform/alerts-log.md) pode ser desencadeada inesperadamente quando a visualiza r'se em [Alertas Azure](../platform/alerts-managing-alert-states.md). As seguintes secções descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta desencadeado por dados parciais

Log Analytics e Application Insights estão sujeitos a atrasos de ingestão e processamento. Quando executa uma consulta de alerta de registo, pode descobrir que não existem dados disponíveis ou apenas alguns dados estão disponíveis. Para mais informações, consulte o [tempo de ingestão](../platform/data-ingestion-time.md)de dados de registo no Monitor Azure .

Dependendo da forma como configuraa a regra do alerta, pode ocorrer um disparo incorreto se não houver dados ou dados parciais em registos no momento da execução do alerta. Nestes casos, aconselhamos que altere a consulta de alerta ou a configuração.

Por exemplo, se configurar a regra de alerta de registo a ser desencadeada quando o número de resultados de uma consulta de análise for inferior a 5, o alerta é desencadeado quando não há dados (registo zero) ou resultados parciais (um registo). Mas depois do atraso na ingestão de dados, a mesma consulta com dados completos pode fornecer um resultado de 10 registos.

### <a name="alert-query-output-is-misunderstood"></a>A saída de consulta de alerta é mal compreendida

Fornece a lógica para alertas de registo numa consulta de análise. A consulta de análise pode usar várias funções matemáticas e big data. O serviço de alerta executa a sua consulta em intervalos especificados com dados para um período de tempo especificado. O serviço de alerta faz alterações subtis na consulta com base no tipo de alerta. Pode visualizar esta alteração na secção **Consulta a ser executada** no ecrã lógico de sinal **configurar:**

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

A caixa de **consulta a ser executada** é o que o serviço de alerta de registo executa. Se quiser entender qual poderá ser a saída de alerta antes de criar o alerta, pode executar a consulta indicada e a hora através do [portal Analytics](../log-query/portals.md) ou da [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>O alerta de registo foi desativado

As seguintes secções listam algumas razões pelas quais o Monitor Azure pode desativar a regra de alerta de [registo](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Recurso onde o alerta foi criado já não existe

As regras de alerta de log criadas no Azure Monitor visam um recurso específico como um espaço de trabalho Azure Log Analytics, uma app Azure Application Insights e um recurso Azure. O serviço de alerta de registo irá então executar uma consulta de análise fornecida na regra para o alvo especificado. Mas após a criação de regras, os utilizadores muitas vezes vão apagar do Azure -- ou mover-se dentro do Azure -- o alvo da regra de alerta de registo. Como o alvo da regra de alerta já não é válido, a execução da regra falha.

Nestes casos, o Azure Monitor desativa o alerta de registo e garante que não é cobrado desnecessariamente quando a regra não pode ser executada continuamente durante um período considerável (como uma semana). Pode descobrir a hora exata em que o Monitor Azure desativou o alerta de registo através do Registo de [Atividade sinuoso](../../azure-resource-manager/management/view-activity-logs.md). No Registo de Atividade sinuoso, é adicionado um evento quando o Monitor Azure desativa a regra de alerta de registo.

O seguinte evento de amostra no Registo de Atividade seletiva azure é para uma regra de alerta que foi desativada devido a uma falha contínua.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Consulta usada em alerta de registo não é válida

Cada regra de alerta de registo criada no Monitor Azure como parte da sua configuração deve especificar uma consulta de análise que o serviço de alerta será executado periodicamente. A consulta de análise pode ter corretas sintaxe no momento da criação ou atualização de regras. Mas, por vezes, ao longo de um período de tempo, a consulta fornecida na regra de alerta de registo pode desenvolver problemas de sintaxe e fazer com que a execução da regra falhe. Algumas razões comuns para que uma consulta de análise fornecida numa regra de alerta de registo possa desenvolver erros são:

- A consulta está escrita para [passar por vários recursos.](../log-query/cross-workspace-query.md) E um ou mais dos recursos especificados já não existem.
- Alerta de registo de tipo de [medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurado tem uma consulta de alerta não está em conformidade com as normas de sintaxe
- Não houve fluxo de dados para a plataforma de análise. A [execução](https://dev.loganalytics.io/documentation/Using-the-API/Errors) da consulta dá um erro porque não há dados para a consulta fornecida.
- As alterações na [linguagem de consulta](https://docs.microsoft.com/azure/kusto/query/) incluem um formato revisto para comandos e funções. Assim, a consulta fornecida anteriormente numa regra de alerta já não é válida.

[O Azure Advisor](../../advisor/advisor-overview.md) avisa-o sobre este comportamento. É adicionada uma recomendação para a regra específica de alerta de registo no Azure Advisor, na categoria de Alta Disponibilidade com impacto médio e uma descrição de "Reparar a sua regra de alerta de registo para garantir a monitorização". Se uma consulta de alerta na regra de alerta de registo não for retificada depois de o Azure Advisor ter fornecido uma recomendação durante sete dias, o Azure Monitor irá desativar o alerta de registo e garantir que não é faturado desnecessariamente quando a regra não pode ser executada continuamente por um período considerável ( como uma semana).

Pode encontrar a hora exata em que o Monitor Azure desativou a regra de alerta de registo, procurando um evento no Registo de [Atividade sinuoso](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre alertas de [log em Azure.](../platform/alerts-unified-log.md)
- Saiba mais sobre [os Insights de Aplicação.](../../azure-monitor/app/analytics.md)
- Saiba mais sobre consultas de [registo.](../log-query/log-query-overview.md)
