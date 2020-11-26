---
title: Alertas de registo de resolução de problemas no Azure Monitor Microsoft Docs
description: Questões comuns, erros e resoluções para regras de alerta de registo em Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: e9a1bef582053eccdbfef63c2159cf540ffd9bfb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186597"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Alertas de registo de resolução de problemas no Azure Monitor  

Este artigo mostra-lhe como resolver problemas comuns com alertas de registo no Azure Monitor. Também fornece soluções para problemas comuns com a funcionalidade e configuração de alertas de log.

Os alertas de registo permitem que os utilizadores utilizem uma consulta [log Analytics](../log-query/log-analytics-tutorial.md) para avaliar os registos de recursos em cada frequência definida e disparem um alerta com base nos resultados. As regras podem desencadear uma ou mais ações utilizando [grupos de ação.](./action-groups.md) [Saiba mais sobre funcionalidade e terminologia de alertas de registo.](alerts-unified-log.md)

> [!NOTE]
> Este artigo não considera casos em que o portal Azure apresente uma regra de alerta desencadeada e uma notificação não seja realizada por um grupo de ação associado. Para estes casos, consulte os detalhes sobre a resolução de problemas [aqui.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

## <a name="log-alert-didnt-fire"></a>O alerta de registo não disparou.

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para registos

O Azure Monitor processa terabytes dos registos dos clientes de todo o mundo, o que pode causar [latência de ingestão de registos.](./data-ingestion-time.md)

Os registos são dados semi-estruturados e inerentemente mais latentes do que as métricas. Se tiver mais de 4 minutos de atraso em alertas disparados, deve considerar a utilização [de alertas métricos](alerts-metric-overview.md). Pode enviar dados para a loja métrica a partir de registos utilizando [alertas métricos para registos](alerts-metric-logs.md).

O sistema retrição a avaliação de alerta várias vezes para mitigar a latência. Uma vez que os dados chegam, o alerta dispara, o que na maioria dos casos não é igual ao tempo recorde de registo.

### <a name="incorrect-query-time-range-configured"></a>Intervalo de tempo de consulta incorreto configurado

O intervalo de tempo de consulta é definido na definição da condição de regra. Este campo é chamado **period for** workspaces and Application Insights, e chamado **override intervalo de tempo** de consulta para todos os outros tipos de recursos. Tal como na análise de registos, o intervalo de tempo limita os dados de consulta ao período especificado. Mesmo que o comando **de há pouco** seja usado na consulta, o intervalo de tempo será aplicado. 

Por exemplo, uma consulta digitaliza 60 minutos, quando o intervalo de tempo é de 60 minutos, mesmo que o texto contenha **há pouco(1d)**. O intervalo de tempo e a filtragem do tempo de consulta precisam de coincidir. No caso do exemplo, alterar o intervalo de tempo de consulta **de**  /  **Override** period para um dia, funcionaria como esperado.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>As ações são silenciadas na regra de alerta

Os alertas de registo fornecem uma opção para silenciar as ações de alerta disparados durante um período de tempo definido. Este campo é chamado **alertas de Supressão** em espaços de trabalho e Insights de Aplicação. Em todos os outros tipos de recursos, chama-se **ações de silenciamento.** 

Uma questão comum é que acha que o alerta não despediu as ações por causa de um problema de serviço. Mesmo duro, foi silenciado pela configuração da regra.

![Alertas de supressão](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>Regra de alerta de medição métrica com divisão usando o legado Log Analytics API

[A medição métrica](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) é um tipo de alerta de log que se baseia nos resultados resumidos da série temporal. Estas regras permitem que o agrupamento por colunas [divida alertas](alerts-unified-log.md#split-by-alert-dimensions). Se estiver a usar o legado Log Analytics API, dividir não funcionará como esperado. Escolher o agrupamento no legado da API não é apoiado.

A atual API De Placas Agendadas permite-lhe definir regras de [medição](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) **agregadas,** que funcionarão como esperado. [Saiba mais sobre a mudança para a atual API de Placas DeQueryRules.](alerts-log-api-switch.md)

## <a name="log-alert-fired-unnecessarily"></a>Alerta de registo disparado desnecessariamente

Uma regra de alerta de registo configurada [no Monitor Azure](./alerts-log.md) pode ser ativada inesperadamente. As seguintes secções descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta desencadeado por dados parciais

O Azure Monitor processa terabytes dos registos dos clientes de todo o mundo, o que pode causar [latência de ingestão de registos.](./data-ingestion-time.md)

Os registos são dados semi-estruturados e inerentemente mais latentes do que as métricas. Se está a sentir muitas falhas em alertas disparados, deve considerar usar [alertas métricos.](alerts-metric-overview.md) Pode enviar dados para a loja métrica a partir de registos utilizando [alertas métricos para registos](alerts-metric-logs.md).

Os alertas de registo funcionam melhor quando tenta detetar dados nos registos. Funciona menos bem quando se tenta detetar a falta de dados nos registos. Por exemplo, alertando sobre o batimento cardíaco da máquina virtual. 

Embora existam capacidades construídas para prevenir falsos alertas, eles ainda podem ocorrer em dados muito latentes (mais de ~30 minutos) e dados com picos de latência.

### <a name="query-optimization-issues"></a>Questões de otimização de consultas

O serviço de alerta altera a sua consulta para otimizar para uma carga mais baixa e para a latência de alerta. O fluxo de alerta foi construído para transformar os resultados que indicam o problema para um alerta. Por exemplo, num caso de consulta como:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Se a intenção do utilizador for alertar, quando este tipo de evento acontece, a lógica de alerta `count` apia-se à consulta. A consulta que irá decorrer será:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Não há necessidade de adicionar lógica de alerta à consulta e fazer que pode até causar problemas. No exemplo acima, se incluir `count` na sua consulta, resultará sempre no valor 1, uma vez que o serviço de alerta fará `count` de `count` .

A consulta otimizada é o que o serviço de alerta de registo funciona. Pode executar a consulta modificada no [portal](../log-query/log-query-overview.md) Log Analytics ou [API](/rest/api/loganalytics/).

Para espaços de trabalho e Insights de Aplicação, chama-se **Consulta a ser executada** no painel de condições. Em todos os outros tipos de recursos, **selecione Consulte a consulta de alerta final** no separador condição.

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>O alerta de registo foi desativado

As secções seguintes listam algumas razões pelas quais o Azure Monitor pode desativar uma regra de alerta de registo. Também incluímos um [exemplo do registo de atividade que é enviado quando uma regra é desativada.](#activity-log-example-when-rule-is-disabled)

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>O âmbito de alerta já não existe ou foi movido

Quando os recursos de âmbito de uma regra de alerta já não são válidos, a execução da regra falha. Neste caso, a faturação também para.

O Azure Monitor desativará o alerta de registo após uma semana se falhar continuamente.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>Consulta usada num alerta de registo não é válida

Quando uma regra de alerta de registo é criada, a consulta é validada para sintaxe correta. Mas, às vezes, a consulta fornecida na regra de alerta de registo pode começar a falhar. Algumas razões comuns são:

- As regras foram criadas através da API e a validação foi ignorada pelo utilizador.
- A consulta [funciona com múltiplos recursos](../log-query/cross-workspace-query.md) e um ou mais dos recursos foram eliminados ou movidos.
- A [consulta falha](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque:
    - A solução de registo não foi [implantada no espaço de trabalho,](../insights/solutions.md#install-a-monitoring-solution)pelo que as tabelas não são criadas.
    - Os dados pararam de fluir para uma mesa na consulta por mais de 30 dias.
    - [As tabelas de registos personalizados](data-sources-custom-logs.md) ainda não foram criadas, uma vez que o fluxo de dados ainda não começou.
- As alterações no [idioma de consulta](/azure/kusto/query/) incluem um formato revisto para comandos e funções. Assim, a consulta fornecida anteriormente já não é válida.

[O Conselheiro Azure](../../advisor/advisor-overview.md) avisa-o sobre este comportamento. Acrescenta uma recomendação sobre a regra de alerta de registo afetada. A categoria utilizada é "Alta Disponibilidade" com impacto médio e uma descrição de "Repare a sua regra de alerta de registo para garantir a monitorização".

## <a name="alert-rule-quota-was-reached"></a>A quota de regra de alerta foi atingida

O número de regras de alerta de pesquisa de registos por subscrição e recurso está sujeito aos limites de quota descritos [aqui](../service-limits.md).

### <a name="recommended-steps"></a>Passos Recomendados
    
Se atingiu o limite de quota, os seguintes passos podem ajudar a resolver o problema.

1. Tente eliminar ou desativar as regras de alerta de pesquisa de registo que já não são usadas.
1. Tente utilizar [a divisão de alertas por dimensões](alerts-unified-log.md#split-by-alert-dimensions) para reduzir a contagem de regras. Estas regras podem monitorizar muitos recursos e casos de deteção.
1. Se necessitar do limite de quota para ser aumentado, continue a abrir um pedido de apoio e forneça as seguintes informações:

    - IDs de subscrição e IDs de recursos para os quais o limite de quota precisa de ser aumentado.
    - Razão para o aumento da quota.
    - Tipo de recurso para o aumento da quota: **Log Analytics,** **Application Insights,** e assim por diante.
    - Limite de quota solicitado.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>Para verificar o uso atual de novas regras de alerta de registo
    
#### <a name="from-the-azure-portal"></a>No portal do Azure

1. Abra o ecrã *alertas* e *selecione Gerir as regras de alerta*
2. Filtre pela subscrição relevante com o controlo pendente *Subscrição*
3. Certifique-se de não filtrar para um grupo de recursos específico, tipo de recurso ou recurso
4. No controlo pendente *Tipo de sinal*, selecione “Pesquisa de Registos”
5. Verifique se o controlo pendente *Estado* está definido como “Ativado”
6. O número total de regras de alerta de pesquisa de registos será apresentado acima da lista de regras

#### <a name="from-api"></a>Na API

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- API REST – [Listar por subscrição](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>Exemplo de registo de atividade quando a regra é desativada

Se a consulta falhar durante sete dias continuamente, o Azure Monitor desativará o alerta de registo e impedirá a faturação da regra. Pode descobrir a hora exata em que o Azure Monitor desativou o alerta de registo no Registo de [Atividades Azure](../../azure-resource-manager/management/view-activity-logs.md). Veja este exemplo:

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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os alertas de registo em Azure](./alerts-unified-log.md).
- Saiba mais sobre [configurar alertas de registo.](../log-query/log-query-overview.md)
- Saiba mais sobre [consultas de registo.](../log-query/log-query-overview.md)