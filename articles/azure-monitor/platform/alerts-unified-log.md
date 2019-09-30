---
title: Alertas de log no Azure Monitor
description: Dispare emails, notificações, chame URLs de sites (WebHooks) ou automação quando as condições de consulta analítica especificadas forem atendidas para os alertas do Azure.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: f78f7c37fafd7f0b29f76220206b9adfb62f52c9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677740"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de log no Azure Monitor

Este artigo fornece detalhes de alertas de log que são um dos tipos de alertas com suporte nos [alertas do Azure](../../azure-monitor/platform/alerts-overview.md) e permitem que os usuários usem a plataforma de análise do Azure como base para alertas.

O alerta de log consiste em regras de pesquisa de log criadas para [logs de Azure monitor](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Para saber mais sobre seu uso, consulte [criando alertas de log no Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Os dados de log populares de [logs de Azure monitor](../../azure-monitor/learn/tutorial-viewdata.md) agora também estão disponíveis na plataforma de métrica em Azure monitor. Para exibição de detalhes, [alerta de métrica para logs](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Regra de alerta de pesquisa de logs – definição e tipos

São criadas regras de pesquisa de registos pelos Alertas do Azure para executar automaticamente consultas de registos especificados a intervalos regulares.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, é criado um registo de alerta. A regra pode executar automaticamente uma ou mais ações através dos [Grupos de Ações](../../azure-monitor/platform/action-groups.md). A função [colaborador de monitoramento do Azure](../../azure-monitor/platform/roles-permissions-security.md) para criar, modificar e atualizar alertas de log pode ser necessária; junto com o acesso & direitos de execução de consulta para os destinos de análise na regra de alerta ou consulta de alerta. Caso o usuário que está criando não tenha acesso a todos os destinos de análise na regra de alerta ou consulta de alerta-a criação da regra pode falhar ou a regra de alerta de log será executada com resultados parciais.

As regras de pesquisa de log são definidas pelos seguintes detalhes:

- **Consulta de log**.  A consulta que é executada toda vez que a regra de alerta é acionada.  Os registros retornados por essa consulta são usados para determinar se um alerta deve ser disparado. A consulta de análise pode ser para um espaço de trabalho Log Analytics específico ou Application Insights aplicativo e até mesmo abranger [vários log Analytics e Application insights recursos](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) fornecidos o usuário tem acesso, bem como direitos de consulta a todos os recursos. 
    > [!IMPORTANT]
    > suporte [a consultas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) em alertas de log para Application insights e alertas de log para [log Analytics configurados usando apenas a API scheduledQueryRules](../../azure-monitor/platform/alerts-log-api-switch.md) .

    Alguns comandos e combinações analíticas são incompatíveis com o uso em alertas de log; para obter mais detalhes, consulte [log de consultas de alerta em Azure monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo da hora atual. O período de tempo restringe os dados buscados para a consulta de log para evitar abuso e contorne qualquer comando de tempo (como atrás) usado na consulta de log. <br>*For exemplo, se o período de tempo for definido como 60 minutos e a consulta for executada às 1:15 PM, somente os registros criados entre 12:15 PM e 1:15 PM serão retornados para executar a consulta de log. Agora, se a consulta de log usar o comando de tempo como atrás (7D), a consulta de log será executada somente para dados entre 12:15 e 1:15 PM-como se os dados existirem apenas nos últimos 60 minutos. E não por sete dias de dados, conforme especificado na consulta de log.*

- **Frequência**.  Especifica com que frequência a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou menor que o período de tempo.  Se o valor for maior que o período de tempo, os registros de risco serão perdidos.<br>*For exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada às 1:00, ela retornará registros entre 12:30 e 1:00 PM.  A próxima vez que a consulta seria executada é 2:00 quando ela retornaria registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados.*

- **Limite**.  Os resultados da pesquisa de log são avaliados para determinar se um alerta deve ser criado.  O limite é diferente para os diferentes tipos de regras de alerta de pesquisa de logs.

As regras de pesquisa de log são para [Azure monitor logs](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), podem ser de dois tipos. Cada um desses tipos é descrito em detalhes nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)** . Um único alerta criado quando o número de registros retornados pela pesquisa de log excede um número especificado.
- **[Medida métrica](#metric-measurement-alert-rules)** .  Alerta criado para cada objeto nos resultados da pesquisa de log com valores que excedem o limite especificado.

As diferenças entre os tipos de regra de alerta são as seguintes.

- O *número de resultados* que as regras de alerta sempre criam um único alerta, enquanto a regra de alerta de *medição de métrica* cria um alerta para cada objeto que excede o limite.
- Regras *de alerta de número de resultados* criam um alerta quando o limite é excedido uma única vez. As regras de alerta de *medição de métrica* podem criar um alerta quando o limite é excedido um determinado número de vezes em um intervalo de tempo específico.

### <a name="number-of-results-alert-rules"></a>Regras de alerta do número de resultados

Regras de alerta de **número de resultados** criam um único alerta quando o número de registros retornados pela consulta de pesquisa excede o limite especificado. Esse tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, syslog, resposta WebApp e logs personalizados.  Talvez você queira criar um alerta quando um evento de erro específico for criado ou quando vários eventos de erro forem criados em um período de tempo específico.

**Limite**: O limite para um número de resultados de regras de alerta é maior ou menor que um valor específico.  Se o número de registros retornados pela pesquisa de log corresponder a esses critérios, um alerta será criado.

Para alertar sobre um único evento, defina o número de resultados para maior que 0 e verifique a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada. Alguns aplicativos podem registrar um erro ocasional que não deve necessariamente gerar um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e, em seguida, ter sucesso na próxima vez.  Nesse caso, talvez você não queira criar o alerta, a menos que vários eventos sejam criados em um período de tempo específico.  

Em alguns casos, talvez você queira criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que está funcionando corretamente.  Se não registrar um desses eventos em um período de tempo específico, um alerta deverá ser criado.  Nesse caso, você definiria o limite para **menos de 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemplo de número de registros-alerta de log de tipo

Considere um cenário em que você deseja saber quando seu aplicativo baseado na Web fornece uma resposta aos usuários com código 500 (ou seja) erro interno do servidor. Você criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** solicitações | em que resultCode = = "500"<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Valor do limite:** Maior que 0<br>

Em seguida, Alert executaria a consulta a cada 5 minutos, com 30 minutos de dados-para procurar qualquer registro em que o código de resultado fosse 500. Se até mesmo um desses registros for encontrado, ele acionará o alerta e disparará a ação configurada.

### <a name="metric-measurement-alert-rules"></a>Regras de alerta de medição métrica

As regras de alerta de **medição de métrica** criam um alerta para cada objeto em uma consulta com um valor que exceda um limite especificado e uma condição de gatilho especificada. Ao contrário **do número de resultados de regras de** alerta, as regras de alerta de **medição métrica** funcionam quando o resultado da análise fornece uma série temporal. Eles têm as seguintes diferenças distintas das regras de alerta de **número de resultados** .

- **Função de agregação**: Determina o cálculo que é executado e potencialmente um campo numérico a ser agregado.  Por exemplo, **Count ()** retorna o número de registros na consulta, **AVG (** CounterValue) retorna a média do campo Counter-Value ao longo do intervalo. A função de agregação na consulta deve ser nomeada/chamada: AggregatedValue e forneça um valor numérico. 

- **Campo de grupo**: Um registro com um valor agregado é criado para cada instância desse campo, e um alerta pode ser gerado para cada um.  Por exemplo, se você quisesse gerar um alerta para cada computador, usaria **por computador**. Caso haja vários campos de grupo especificados na consulta de alerta, o usuário pode especificar qual campo será usado para classificar os resultados usando o parâmetro **Aggregate on** (metricColumn)

    > [!NOTE]
    > A opção *Aggregate on* (metricColumn) está disponível para alertas de log do tipo de medição métrica para Application insights e alertas de log para [log Analytics configurados usando](../../azure-monitor/platform/alerts-log-api-switch.md) apenas a API do scheduledQueryRules.

- **Intervalo**:  Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se você especificou **cinco minutos**, um registro seria criado para cada instância do campo de grupo agregada em intervalos de 5 minutos ao longo do período especificado para o alerta.

    > [!NOTE]
    > A função bin deve ser usada na consulta para especificar o intervalo. As bin () podem resultar em intervalos de tempo desiguais-o alerta converterá automaticamente o comando bin no comando bin_at com o tempo apropriado em tempo de execução, para garantir os resultados com um ponto fixo. O tipo de medição métrica do alerta de log foi projetado para funcionar com consultas com até três instâncias do comando bin ()
    
- **Limite**: O limite para as regras de alerta de medição métrica é definido por um valor de agregação e um número de violações.  Se qualquer ponto de dados na pesquisa de log exceder esse valor, ele será considerado uma violação.  Se o número de violações em para qualquer objeto nos resultados exceder o valor especificado, um alerta será criado para esse objeto.

A configuração incorreta da opção *Aggregate on* ou *metricColumn* pode fazer com que as regras de alerta misfire. Para obter mais informações, consulte [Solucionando problemas quando a regra de alerta de medição de métrica está incorreta](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemplo de alerta de log do tipo de medida métrica

Considere um cenário em que você queria um alerta se algum computador excedesse a utilização do processador de 90% três vezes em 30 minutos.  Você criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** Perf | onde ObjectName = = "Processor" e CounterName = = "% Processor Time" | Resuma AggregatedValue = AVG (comvalue) por bin (TimeGenerated, 5 min), Computer<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Lógica de alerta-limite de & da condição:** Maior que 90<br>
- **Campo de grupo (agregação):** Computer
- **Disparar alerta com base em:** Total de violações maiores que 2<br>

A consulta criaria um valor médio para cada computador em intervalos de 5 minutos.  Essa consulta seria executada a cada 5 minutos para os dados coletados nos últimos 30 minutos. Como o campo de grupo (agregação) escolhido é colunar ' computador '-o AggregatedValue é dividido para vários valores de ' computador ' e a utilização média do processador para cada computador é determinada para uma hora de 5 minutos.  O resultado da consulta de exemplo para (digamos) três computadores é o seguinte.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se o resultado da consulta fosse plotado, ele será exibido como.

![Resultados da consulta de exemplo](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Neste exemplo, vemos em compartimentos de 5 minutos para cada um dos três computadores-utilização média do processador, conforme calculado por 5 minutos. Limite de 90 que está sendo violado por Srv01 apenas uma vez ao 1:25 bin. Em comparação, o srv02 excede o limite de 90 às 1:10, 1:15 e 1:25 compartimentos; enquanto o srv03 excede o limite de 90 às 1:10, 1:15, 1:20 e 1:30.
Como o alerta está configurado para disparar com base nas violações totais são mais de duas, vemos que srv02 e srv03 só atendem aos critérios. Portanto, os alertas separados seriam criados para srv02 e srv03, uma vez que eles violaram o limite de 90% duas vezes em vários compartimentos de tempo.  Se o *alerta de gatilho com base em:* o parâmetro foi configurado para a opção de *violações contínuas* , um alerta será disparado **apenas** para srv03, já que ele violou o limite de três bandejas de tempo consecutivas de 1:10 a 1:20. E **não** para srv02, pois ele violou o limite de dois compartimentos de tempo consecutivos de 1:10 a 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Regra de alerta de pesquisa de logs – acionamento e estado

A regra de alerta de pesquisa de log funciona na lógica preparada pelo usuário de acordo com a configuração e a consulta de análise personalizada usada. Como a lógica de monitoramento, incluindo a condição exata ou o motivo pelo qual a regra de alerta deve disparar, é encapsulada em uma consulta de análise, que pode ser diferente em cada regra de alerta de log. Os alertas do Azure têm informações escassa do cenário de causa raiz (ou) subjacente específico que está sendo avaliado quando a condição de limite da regra de alerta de pesquisa de logs é atendida ou excedida. Assim, os alertas de log são conhecidos como sem estado. E as regras de alerta de log continuarão sendo acionadas, desde que a condição de alerta seja atendida pelo resultado da consulta de análise personalizada fornecida. Sem o alerta a cada ser resolvido, como a lógica da causa raiz exata da falha de monitoramento é mascarada dentro da consulta de análise fornecida pelo usuário. Atualmente, não há nenhum mecanismo para alertas de Azure Monitor para deduzir conclusivamente a causa raiz que está sendo resolvida.

Vamos ver o mesmo com um exemplo prático. Suponha que tenhamos uma regra de alerta de log chamada *contoso-log-Alert*, de acordo com a configuração no [exemplo fornecido para o número de resultados de alerta de log](#example-of-number-of-records-type-log-alert) -em que a consulta de alerta personalizada foi criada para procurar o código de resultado 500 nos logs.

- Às 1:05 PM quando contoso-log-Alert foi executado pelos alertas do Azure, o resultado da pesquisa de log gerou zero registros com o código de resultado de 500. Como zero está abaixo do limite e o alerta não é acionado.
- Na próxima iteração às 1:10, quando contoso-log-Alert foi executado pelos alertas do Azure, o resultado da pesquisa de logs forneceu cinco registros com o código de resultado como 500. Como cinco excede o limite e o alerta é acionado com as ações associadas disparadas.
- Às 1:15 PM quando contoso-log-Alert foi executado por alertas do Azure, o resultado da pesquisa de logs forneceu dois registros com código de resultado 500. Como dois excedem o limite e o alerta é acionado com as ações associadas disparadas.
- Agora, na próxima iteração às 1:20, quando contoso-log-Alert foi executado pelo alerta do Azure, o resultado da pesquisa de log forneceu novamente zero registros com o código de resultado 500. Como zero está abaixo do limite e o alerta não é acionado.

Mas, no caso listado acima, às 1:15 PM-os alertas do Azure não podem determinar que os problemas subjacentes vistos em 1:10 persistam e se há novas falhas na rede. Como a consulta fornecida pelo usuário pode estar levando em conta registros anteriores – os alertas do Azure podem ter certeza. Como a lógica do alerta é encapsulada na consulta de alerta, portanto, os dois registros com o código de resultado 500 visto às 1:15 PM podem ou não ser vistos na 1:10 PM. Portanto, para o erro no lado do cuidado, quando contoso-log-Alert for executado às 1:15 PM, a ação configurada será disparada novamente. Agora às 1:20, quando zero registros são vistos com o código de resultado de 500, os alertas do Azure não podem ter certeza de que a causa do código de resultado 500 visto às 1:10 e a 1:15 PM agora está resolvida e Azure Monitor alertas pode deduzir com segurança que os problemas de erro de 500 não ocorrerão pelo mesmo motivo s novamente. Portanto, o contoso-log-Alert não será alterado para resolvido no painel de alerta do Azure e/ou as notificações enviadas informando a resolução do alerta. Em vez disso, o usuário que entende a condição ou o motivo exato da lógica incorporada na consulta de análise pode [marcar o alerta como fechado](alerts-managing-alert-states.md) , conforme necessário.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e cobrança de alertas de log

Os preços aplicáveis para alertas de log são indicados na página de [preços Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) . No Azure Bills, os alertas de log são representados como tipo `microsoft.insights/scheduledqueryrules` com:

- Alertas de log em Application Insights mostrados com o nome exato do alerta junto com as propriedades do grupo de recursos e do alerta
- Alertas de log em Log Analytics mostrados com o nome exato do alerta junto com as propriedades do grupo de recursos e do alerta; Quando criado usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

A [API de log Analytics herdada](../../azure-monitor/platform/api-alerts.md) tem ações de alerta e agendas como parte de log Analytics pesquisa salva e [recursos do Azure](../../azure-resource-manager/resource-group-overview.md)inadequados. Portanto, para habilitar a cobrança para esses alertas de log herdados criados para Log Analytics usando portal do Azure **sem** [alternar para a nova API](../../azure-monitor/platform/alerts-log-api-switch.md) ou por meio da [API de log Analytics herdada](../../azure-monitor/platform/api-alerts.md) , as regras de pseudodispositivo ocultas são criadas no `microsoft.insights/scheduledqueryrules` para cobrança no Azure. As regras de pseudo-alerta ocultas criadas para cobrança no `microsoft.insights/scheduledqueryrules`, conforme mostrado como `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`, juntamente com as propriedades do grupo de recursos e do alerta.

> [!NOTE]
> Se forem presentes caracteres inválidos, como `<, >, %, &, \, ?, /`, eles serão substituídos por `_` no nome da regra de pseudo-alerta oculto e, portanto, também na fatura do Azure.

Para remover os recursos de scheduleQueryRules ocultos criados para a cobrança de regras de alerta usando a [API de log Analytics herdada](api-alerts.md), o usuário pode fazer o seguinte:

- Qualquer usuário pode [alternar a preferência de API para as regras de alerta no espaço de trabalho log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) e sem perda de suas regras de alerta ou movimentação de monitoramento para Azure Resource Manager [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)compatível. Portanto, elimine a necessidade de tornar as regras de alerta pseudo-ocultas para cobrança.
- Ou, se o usuário não quiser alternar a preferência de API, o usuário precisará **excluir** a agenda original e a ação de alerta usando a [API de log Analytics herdada](api-alerts.md) ou excluir em [portal do Azure regra de alerta de log original](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Além disso, para os recursos ocultos do scheduleQueryRules criados para a cobrança de regras de alerta usando a [API de log Analytics herdada](api-alerts.md), qualquer operação de modificação como Put falhará. Como as pseudovariáveis de tipo `microsoft.insights/scheduledqueryrules` são para fins de cobrança das regras de alerta criadas usando a [API de log Analytics herdada](api-alerts.md). Qualquer modificação de regra de alerta deve ser feita usando a [API de log Analytics herdada](api-alerts.md) (ou) o usuário pode [alternar a preferência de API para que as regras de alerta usem a](../../azure-monitor/platform/alerts-log-api-switch.md) [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em vez disso.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [criar alertas de log no Azure](../../azure-monitor/platform/alerts-log.md).
* Entenda os [WebHooks em alertas de log no Azure](alerts-log-webhook.md).
* Saiba mais sobre os [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [Application insights](../../azure-monitor/app/analytics.md).
* Saiba mais sobre [log Analytics](../../azure-monitor/log-query/log-query-overview.md).