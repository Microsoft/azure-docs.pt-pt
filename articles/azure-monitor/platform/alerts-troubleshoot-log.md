---
title: Alertas de registo de resolução de problemas no Azure Monitor Microsoft Docs
description: Questões comuns, erros e resoluções para regras de alerta de registo em Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: 7be1c350af6c9bb84669b45a9bc8a1d9dd808133
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165639"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertas de registo de resolução de problemas no Azure Monitor  

Este artigo mostra-lhe como resolver problemas comuns com alertas de registo no Azure Monitor. Também fornece soluções para problemas comuns com a funcionalidade e configuração de alertas de log.

O termo alerta de registo descreve *regras* que disparam com base numa consulta de log num [espaço de trabalho Azure Log Analytics](../learn/tutorial-viewdata.md) ou em [Azure Application Insights](../../azure-monitor/app/analytics.md). Saiba mais sobre funcionalidades, terminologia e tipos em [alertas de Log no Azure Monitor.](../platform/alerts-unified-log.md)

> [!NOTE]
> Este artigo não considera casos em que o portal Azure apresente uma regra de alerta desencadeada e uma notificação não seja realizada por um grupo de ação associado. Para estes casos, consulte os detalhes na [Criar e gerir grupos de ação no portal Azure.](../platform/action-groups.md)

## <a name="log-alert-didnt-fire"></a>O alerta de registo não disparou.

Eis algumas razões comuns para que o estado para uma regra de alerta de registo configurado [no Azure Monitor](../platform/alerts-log.md) não apareça [como *despedido* quando esperado](../platform/alerts-managing-alert-states.md).

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para registos

Um alerta de registo executa periodicamente a sua consulta com base em [Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Uma vez que o Azure Monitor processa muitos terabytes de dados de milhares de clientes de fontes variadas em todo o mundo, o serviço é suscetível a diferentes atrasos de tempo. Para obter mais informações, consulte [o tempo de ingestão de dados nos registos do Azure Monitor](../platform/data-ingestion-time.md).

Para mitigar os atrasos, o sistema aguarda e retricha a consulta de alerta várias vezes se encontrar os dados necessários ainda não ingeridos. O sistema tem um tempo de espera exponencialmente crescente. O alerta de registo só é desencadeado após a disponibilização dos dados, pelo que o atraso pode dever-se à ingestão lenta de dados de registo.

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado

Conforme descrito no artigo sobre [terminologia para alertas de registo,](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta devolve apenas registos que foram criados dentro deste intervalo.

O período de tempo restringe os dados recolhidos para uma consulta de registo para evitar abusos, e contorna qualquer comando de tempo (como **há pouco**) usado numa consulta de log. Por exemplo, Se o período de tempo estiver definido para 60 minutos, e a consulta for executada às 13:15, apenas são utilizados registos criados entre as 12:15 e as 13:15 horas. Se a consulta de registo utilizar um comando de tempo como **ago (1d)**, a consulta ainda utiliza apenas dados entre as 12:15 e as 13:15, porque o período de tempo está definido para esse intervalo.

Verifique se o período de tempo na configuração coincide com a sua consulta. Para o exemplo mostrado anteriormente, se a consulta de registo utiliza **(1d)** com o marcador verde, o período de tempo deve ser definido para 24 horas ou 1.440 minutos (indicado a vermelho). Esta definição garante que a consulta funciona como pretendido.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção alertas de supressão está definida

Como descrito no passo 8 do artigo sobre a criação de uma regra de [alerta de registo no portal Azure, os](../platform/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal)alertas de registo fornecem uma opção de Alerta **suprimido** para suprimir ações de desencadeamento e notificação por um período de tempo configurado. Como resultado, pode pensar que um alerta não disparou. Na verdade, disparou, mas foi suprimido.  

![Alertas de supressão](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra do alerta de medição métrica está incorreta

*Os alertas de registo de medição métrica* são um subtipo de alertas de log que têm capacidades especiais e uma sintaxe de consulta de alerta restrita. Uma regra para um alerta de registo de medição métrica requer que a saída de consulta seja uma série de tempo métrica. Ou seja, a saída é uma tabela com períodos de tempo distintos e igualmente dimensionados, juntamente com os valores agregados correspondentes.

Pode optar por ter variáveis adicionais na tabela ao lado do **AgregadodValue.** Estas variáveis podem ser usadas para separar a tabela.

Por exemplo, suponha que uma regra para um alerta de registo de medição métrica foi configurada como:

- Consulta de`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- Período de tempo de 6 horas
- Limiar de 50
- Lógica de alerta de três infrações consecutivas
- **Agregado Após** escolhido como **$table**

Porque o comando inclui **resumir... por** e fornece duas variáveis (**timetamp** e **$table),** o sistema escolhe **$table** para **Aggregate Upon**. O sistema classifica a tabela de resultados pelo campo **$table,** como mostra a imagem seguinte. Em seguida, olha para os múltiplos casos **agregados de Valor** para cada tipo de tabela (como **disponibilidadeResults**) para ver se houve três ou mais violações consecutivas.

![Execução de consulta de medição métrica com vários valores](media/alert-log-troubleshoot/LogMMQuery.png)

Como **o Agregado Upon** é definido em **$table,** os dados são classificados numa coluna **$table** (indicada a vermelho). Em seguida, agrupar-se e procurar tipos de **campo Agregado Sobre.**

Por exemplo, para **$table,** os valores para **disponibilidade Os resultados** serão considerados como um lote/entidade (indicado a laranja). Neste lote/entidade de valor, o serviço de alerta verifica três infrações consecutivas (indicadas em verde). As infrações desencadeiam um alerta para a disponibilidade de valor da **tabelaResults**.

Da mesma forma, se ocorrerem três infrações consecutivas para qualquer outro valor de **$table**, outra notificação de alerta é desencadeada para a mesma coisa. O serviço de alerta classifica automaticamente os valores de um lote/entidade (indicado a laranja) pelo tempo.

Agora suponha que a regra para o alerta de registo de medição métrica foi modificada e a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` . O resto da configuração manteve-se como antes, incluindo a lógica de alerta para três infrações consecutivas. A opção **Agregado Upon** neste caso é a hora **de tempo** por padrão. Apenas um valor é fornecido na consulta para **resumir ... por** (isto é, **timetamp**). Tal como o exemplo anterior, a saída no final da execução seria tão ilustrada como se segue.

   ![Execução de consulta de medição métrica com valor singular](media/alert-log-troubleshoot/LogMMtimestamp.png)

Uma **vez que o Agregado Upon** é definido na **timetamp,** os dados são classificados na coluna **de tempotamp** (indicada a vermelho). Depois, agrutamo-nos por **tempos.** Por exemplo, os valores para `2018-10-17T06:00:00Z` serão considerados como um enredo/entidade (indicado em laranja). Neste lote/entidade de valor, o serviço de alerta não encontrará infrações consecutivas (porque cada valor **de pontuação** tem apenas uma entrada). Então o alerta nunca é acionado. Neste caso, o utilizador deve:

- Adicione uma variável falsa ou uma variável existente (como **$table)** para ordenar corretamente utilizando o campo **Agregado Upon.**
- Reconfigure a regra de alerta para utilizar a lógica de alerta com base na **violação total.**

## <a name="log-alert-fired-unnecessarily"></a>Alerta de registo disparado desnecessariamente

Uma regra de alerta de registo configurada [no Azure Monitor](../platform/alerts-log.md) pode ser ativada inesperadamente quando a vê em [Alertas Azure](../platform/alerts-managing-alert-states.md). As seguintes secções descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta desencadeado por dados parciais

Log Analytics e Application Insights estão sujeitos a atrasos de ingestão e processamento. Quando executar uma consulta de alerta de registo, poderá descobrir que não existem dados disponíveis ou apenas alguns dados estão disponíveis. Para obter mais informações, consulte [o tempo de ingestão de dados do Registo no Azure Monitor](../platform/data-ingestion-time.md).

Dependendo da forma como configuraste a regra de alerta, pode ocorrer uma falha de identificação se não houver dados ou dados parciais nos registos no momento da execução do alerta. Nestes casos, aconselhamos que altere a consulta de alerta ou configuração.

Por exemplo, se configurar a regra de alerta de registo a ser ativada quando o número de resultados de uma consulta analítica for inferior a 5, o alerta é acionado quando não há dados (registo zero) ou resultados parciais (um registo). Mas depois do atraso na ingestão de dados, a mesma consulta com dados completos pode fornecer um resultado de 10 registos.

### <a name="alert-query-output-is-misunderstood"></a>A saída de consulta de alerta é mal compreendida

Fornece a lógica dos alertas de registo numa consulta analítica. A consulta analítica pode usar vários grandes dados e funções matemáticas. O serviço de alerta executa a sua consulta em intervalos especificados com dados para um período de tempo especificado. O serviço de alerta es faz alterações subtis na consulta com base no tipo de alerta. Pode ver esta alteração na secção **'Consulta' a ser executada** no ecrã lógico de **sinal de configuração:**

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

A **caixa de consulta a executar** é o que o serviço de alerta de registo funciona. Se quiser entender qual pode ser a saída de consulta de alerta antes de criar o alerta, pode executar a consulta indicada e o intervalo de tempo através do [portal Analytics](../log-query/portals.md) ou da [API de Analytics](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>O alerta de registo foi desativado

As secções seguintes listam algumas razões pelas quais o Azure Monitor pode desativar a regra de [alerta de registo](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Recurso onde o alerta foi criado já não existe

As regras de alerta de registo criadas no Azure Monitor visam um recurso específico como um espaço de trabalho Azure Log Analytics, uma aplicação Azure Application Insights e um recurso Azure. O serviço de alerta de registo irá então executar uma consulta de análise fornecida na regra para o alvo especificado. Mas após a criação de regras, os utilizadores muitas vezes passam a apagar do Azure ou movem-se dentro do Azure- o alvo da regra de alerta de registo. Como o alvo da regra de alerta já não é válido, a execução da regra falha.

Nesses casos, o Azure Monitor desativa o alerta de registo e garante que não é faturado desnecessariamente quando a regra não pode ser executada continuamente por um período considerável (como uma semana). Pode descobrir a hora exata em que o Azure Monitor desativou o alerta de registo através do [Registo de Atividades Azure](../../azure-resource-manager/management/view-activity-logs.md). No Registo de Atividades Azure, um evento é adicionado quando o Azure Monitor desativa a regra de alerta de registo.

O seguinte evento de amostra no Registo de Atividades Azure é para uma regra de alerta que foi desativada devido a uma falha contínua.

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

Cada regra de alerta de registo criada no Azure Monitor como parte da sua configuração deve especificar uma consulta de análise que o serviço de alerta será executado periodicamente. A consulta analítica pode ter sintaxe correta no momento da criação ou atualização de regras. Mas, por vezes, ao longo de um período de tempo, a consulta fornecida na regra de alerta de registo pode desenvolver problemas de sintaxe e fazer com que a execução da regra falhe. Algumas razões comuns pelas quais uma consulta analítica fornecida numa regra de alerta de registo pode desenvolver erros são:

- A consulta é escrita para [correr através de vários recursos](../log-query/cross-workspace-query.md). E um ou mais dos recursos especificados já não existem.
- [O alerta de registo do tipo de medição métrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) configurado tem uma consulta de alerta que não está em conformidade com as normas de sintaxe
- Não houve fluxo de dados para a plataforma de análise. A [execução de consultas dá um erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não há dados para a consulta fornecida.
- As alterações no [idioma de consulta](https://docs.microsoft.com/azure/kusto/query/) incluem um formato revisto para comandos e funções. Assim, a consulta fornecida anteriormente numa regra de alerta já não é válida.

[O Conselheiro Azure](../../advisor/advisor-overview.md) avisa-o sobre este comportamento. É adicionada uma recomendação para a regra específica de alerta de registo no Azure Advisor, na categoria de Alta Disponibilidade com impacto médio e uma descrição de "Repare a sua regra de alerta de registo para garantir a monitorização".

> [!NOTE]
> Se uma consulta de alerta na regra de alerta de registo não for retificada depois de o Azure Advisor ter fornecido uma recomendação durante sete dias, o Azure Monitor desativará o alerta de registo e garantirá que não é faturado desnecessariamente quando a regra não pode ser executada continuamente por um período considerável (7 dias). Pode encontrar a hora exata em que o Azure Monitor desativou a regra do alerta de registo procurando um evento no [Registo de Atividades Azure](../../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os alertas de registo em Azure](../platform/alerts-unified-log.md).
- Saiba mais sobre [a Aplicação Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [consultas de registo.](../log-query/log-query-overview.md)
