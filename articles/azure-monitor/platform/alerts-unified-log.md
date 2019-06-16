---
title: Alertas de registo no Azure Monitor
description: E-mails de Acionador, notificações, URLs de Web sites de chamada (webhooks) ou automatização quando estiverem reunidas as condições de consulta de análise que especificar para alertas do Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427423"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de registo no Azure Monitor

Este artigo fornece detalhes de alertas de registo são um dos tipos de alertas de suportam o [alertas do Azure](../../azure-monitor/platform/alerts-overview.md) e permitir que os utilizadores utilizem a plataforma de análise do Azure como base para alertas.

Alerta de registo é composta por regras de pesquisa de registos, criadas para [registos do Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Para saber mais sobre a utilização, consulte [criar alertas de registo no Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Dados de registos populares [registos do Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) também está agora disponível na plataforma de métrica no Azure Monitor. Para a vista de detalhes, [alerta de métrica para os registos](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Log search regra de alerta - definição e tipos

São criadas regras de pesquisa de registos pelos Alertas do Azure para executar automaticamente consultas de registos especificados a intervalos regulares.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, é criado um registo de alerta. A regra pode executar automaticamente uma ou mais ações através dos [Grupos de Ações](../../azure-monitor/platform/action-groups.md). [Contribuidor de monitorização do Azure](../../azure-monitor/platform/roles-permissions-security.md) função para a criação, modificação e alertas de registo de atualização poderão ser necessários; juntamente com direitos de execução de acesso e de consulta para o destino de análise (s) na regra de alerta ou consulta de alerta. No caso de criação de utilizador não tem acesso a todos os destinos de análise na regra de alerta ou consulta de alerta - a criação de regra poderá falhar ou a regra de alerta de registo será executada com resultados parciais.

Regras de pesquisa de registo são definidas pelos seguintes detalhes:

- **Consulta de registo**.  A consulta é executada sempre que a regra de alerta é acionado.  Os registos devolvidos por esta consulta são utilizados para determinar se um alerta é acionado. Consulta do Analytics pode ser para uma área de trabalho do Log Analytics específica ou uma aplicação do Application Insights e até mesmo abranger várias [vários recursos do Log Analytics e Application Insights](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) desde que o utilizador tem acesso, bem como consultar direitos a todos recursos. 
    > [!IMPORTANT]
    > [entre recursos consulta](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) suporte em alertas de registo para o Application Insights e log de alertas para [Log Analytics configurada utilizando scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) apenas.

    Alguns comandos de análise e combinações são incompatíveis com o uso em alertas de registo; Para ver mais detalhes, [consultas de alertas de registo no Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo da hora atual. Período de tempo restringe os dados obtidos para a consulta de registo evitar abusos e evita qualquer comando de tempo (como há) utilizado numa consulta de registo. <br>*Por exemplo, se o período de tempo é definido como 60 minutos, e a consulta é executada em 1 às 15:15, apenas os registos criados entre 12:15 e 1 às 15:15 é devolvido ao executar a consulta de registo. Agora, se a consulta de registo utiliza o comando como atrás de tempo (7D), a consulta de log deve ser executada apenas para dados entre 12:15 e 1 às 15:15 - como se existem dados para apenas os últimos 60 minutos. E não durante sete dias de dados conforme especificado na consulta de registo.*

- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou menor que o período de tempo.  Se o valor for maior do que o período de tempo, em seguida, corre o risco de registos a ser omitidos.<br>*Por exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta é executada às 9:00 com 1, ele devolve registos entre 12:30 e 1 21 horas.  Da próxima vez que a consulta executaria é 2:00 quando ele retornaria registos entre 1:30 e 2:00.  Quaisquer registos criados entre 1:00 e 1:30 nunca seriam avaliados.*

- **Limiar**.  Os resultados da pesquisa de registos são avaliados para determinar se deve ser criado um alerta.  O limiar é diferente para os diferentes tipos de regras de alerta de pesquisa de registo.

Regras de pesquisa de registo, sê-lo para [registos do Azure Monitor](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), podem ter dois tipos. Cada um desses tipos é descrita detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)** . Único alerta criada quando um número especificado de excedem o número de registos devolvido pela pesquisa de registo.
- **[Medida da métrica](#metric-measurement-alert-rules)** .  Alerta criado para cada objeto nos resultados da pesquisa de registo com valores que excedam o limiar especificado.

Seguem-se as diferenças entre os tipos de regra de alerta.

- *Número de resultados* regras de alerta cria sempre um tempo de alerta, único *medida da métrica* regra de alerta cria um alerta para cada objeto que é superior ao limiar.
- *Número de resultados* regras de alerta criar um alerta quando o limiar for excedido uma única vez. *Medida da métrica* regras de alerta podem criar um alerta quando o limiar for ultrapassado um determinado número de vezes ao longo de um intervalo de tempo específico.

### <a name="number-of-results-alert-rules"></a>Número de regras de alerta de resultados

**Número de resultados** regras de alerta criar um único alerta quando o número de registros retornados pela consulta de pesquisa exceder o limiar especificado. Este tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, Syslog, resposta de aplicação Web e os registos personalizados.  Pode querer criar um alerta quando é criado um evento de erro específico ou quando vários eventos de erro são criados num período de tempo específico.

**Limiar**: O limite para um número de regras de alerta de resultados é maior ou menor do que um determinado valor.  Se o número de registos devolvidos pela pesquisa de registos corresponderem este critério, em seguida, é criado um alerta.

Para alertar relativamente a um único evento, defina o número de resultados para maior que 0 e procurar a ocorrência de um único evento que foi criado desde a última vez que a consulta foi executada. Alguns aplicativos podem iniciar um erro de ocasional necessariamente não deve emitir um alerta.  Por exemplo, o aplicativo pode repetir o processo que o evento de erro de criar e, em seguida, ter êxito na próxima vez.  Neste caso, não poderá criar o alerta, a menos que vários eventos são criados num período de tempo específico.  

Em alguns casos, pode querer criar um alerta na ausência de um evento.  Por exemplo, um processo pode iniciar eventos regulares para indicar que está a funcionar corretamente.  Se não iniciar um dos seguintes eventos num período de tempo específico, em seguida, um alerta deve ser criado.  Neste caso, definir o limiar **menos de 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemplo de alerta de registo do tipo de número de registos

Considere um cenário onde deseja saber quando a sua aplicação baseada na web dá uma resposta para os utilizadores com o código de 500 Erro de servidor (ou seja) interno. Poderia criar uma regra de alerta com os seguintes detalhes:  

- **Consulta:** pedidos | onde resultCode = = "500"<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência do alerta:** cinco minutos<br>
- **Valor de limiar:** Maior que 0<br>

Em seguida, alerta seria executar a consulta a cada 5 minutos, com 30 minutos de dados, para procurar qualquer registo em que o código de resultado era 500. Se nem um registo é encontrado, ele é acionado o alerta e aciona a ação configurada.

### <a name="metric-measurement-alert-rules"></a>Regras de alerta de medida da métrica

**Medida da métrica** regras de alerta criar um alerta para cada objeto numa consulta com um valor que excede um limiar especificado e especificar a condição de acionamento. Ao contrário **número de resultados** regras, de alerta **medida da métrica** regras de alerta de trabalho quando o resultado do analytics fornece uma série de tempo. Eles têm as seguintes diferenças distintas de **número de resultados** regras de alerta.

- **Função de agregação**: Determina o cálculo que é executado e, potencialmente, com um numérico campo a agregar.  Por exemplo, **count ()** devolve o número de registos na consulta, **avg(CounterValue)** devolve a média do campo CounterValue ao longo do intervalo. Função de agregação na consulta tem de ser com o nome/chamado: AggregatedValue e fornecer um valor numérico. 

- **Campo de grupo**: É criado um registo com um valor agregado para cada instância deste campo e pode ser gerado um alerta para cada um.  Por exemplo, se quiser gerar um alerta para cada computador, usaria **por computador**. No caso, existem vários campos de grupo especificados na consulta de alerta, o utilizador pode especificar que campo a ser utilizado para ordenar resultados utilizando o **agregada no** parâmetro (metricColumn)

    > [!NOTE]
    > *No agregado* opção (metricColumn) está disponível para alertas de registo de tipo de medida da métrica do Application Insights e o registo de alertas para [configurados utilizando scheduledQueryRules API do Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) apenas.

- **Intervalo**:  Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se especificou **cinco minutos**, teria de ser criado um registo para cada instância do campo Grupo agregada em intervalos de 5 minutos ao longo do período de tempo especificado para o alerta.

    > [!NOTE]
    > Função de posição tem de ser utilizada numa consulta para especificar o intervalo. Como bin() pode resultar em intervalos de tempo desiguais - alerta converte automaticamente bin comando bin_at comando com um horário apropriado no tempo de execução, para garantir resultados com um ponto de fixo. Tipo de medida da métrica de alerta de registo foi concebido para trabalhar com consultas com até três instâncias do comando de bin()
    
- **Limiar**: O limiar para regras de alerta de medida da métrica é definido por um valor agregado e um número de violações.  Se qualquer ponto de dados na pesquisa de registos exceder este valor, ele é considerado uma violação.  Se o número de falhas para qualquer objeto nos resultados excede o valor especificado, é criado um alerta para esse objeto.

Uma configuração incorreta dos *agregada no* ou *metricColumn* opção pode fazer com que as regras de alerta para misfire. Para obter mais informações, consulte [resolução de problemas quando a regra de alerta de medida da métrica está incorreta](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemplo de alerta de registo do tipo de medida da métrica

Considere um cenário em que queria um alerta se a qualquer computador excedeu a utilização do processador de 90% três vezes mais de 30 minutos.  Poderia criar uma regra de alerta com os seguintes detalhes:  

- **Consulta:** Desempenho | onde ObjectName = = "Processador" e CounterName = = "% tempo do processador" | resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 5m), computador<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência do alerta:** cinco minutos<br>
- **Alert Logic - limiar de & condição:** Superior a 90<br>
- **Campo de grupo (agregado ativado):** Computador
- **Acionar alerta com base em:** Total de casos de violações de maior que 2<br>

A consulta deverá criar um valor médio para cada computador em intervalos de 5 minutos.  Esta consulta seria executada a cada 5 minutos para os dados recolhidos durante os 30 minutos anteriores. Uma vez que o campo de grupo (agregado-on) escolhido é em colunas "computador" - o AggregatedValue é dividido para vários valores de "Computador" e a utilização média do processador para cada computador é determinada para um contentor de tempo de 5 minutos.  Resultado da consulta de exemplo para (Digamos) três computadores, seria conforme mostrado abaixo.


|TimeGenerated [UTC] |Computador  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se o resultado da consulta ser desenhados, ela apareceria como.

![Resultados da consulta de exemplo](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Neste exemplo, podemos ver nas caixas de 5 minutos para cada um dos três computadores - utilização do processador médio como calculada para 5 minutos. Limiar de 90, que está a ser infringido, através da srv01 apenas uma vez em 1:25 bin. Em comparação, o srv02 excede o limiar de 90 1: dez anos, 1:15 e 1:25 discretizações; Embora srv03 excede o limiar de 90 em 1:10, 1:15, 1:20 e 1:30.
Uma vez que o alerta está configurado para acionador com base no total de falhas são mais do que dois, podemos ver que o srv02 e srv03 apenas cumprem os critérios. Por conseguinte, seriam possível criar alertas separadas para srv02 e srv03, uma vez que eles infringido o limiar de 90% duas vezes em várias caixas de tempo.  Se o *acionar alerta com base na:* em vez disso, configurado para o parâmetro *violações contínuas* opção, em seguida, poderia ser acionado um alerta **apenas** para srv03, uma vez que ele infringido o limiar para três caixas de tempo consecutivos de 1:10 a 1:20. E **não** para srv02, como ele infringido o limiar de duas caixas de tempo consecutivos de 1:10 a 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Log search regra de alerta - acionadas e Estado

Regra de alerta de pesquisa de registo funciona na lógica vinculada por utilizador de acordo com a configuração e a consulta de análise personalizada utilizada. Desde a lógica de monitorização incluindo a condição exata ou o motivo por que deve acionar a regra de alerta é encapsulado numa consulta do analytics - que pode ser diferente em cada regra de alerta de registo. Alertas do Azure tem informações escassos o cenário que está a ser avaliada quando a condição de limiar de regra de alerta de pesquisa de registo é atingida ou excedida específica subjacente causa (ou). Portanto, alertas de registo são referidas como sem estado. E regras de alerta de registo irão manter trabalharem, desde que a condição do alerta é cumprida ao resultado da consulta de análise personalizada fornecida. Sem o alerta cada introdução resolvido, como a lógica a causa exata da falha de monitorização está oculto dentro da consulta de análise fornecida pelo usuário. Daí que estão em nenhum mecanismo para alertas do Azure Monitor deduzir as consequências causa de raiz que está sendo resolvida.

Permite-na ver o mesmo com um exemplo prático. Vamos supor que temos uma regra de alerta de registo denominada *Contoso-alerta de registo*, de acordo com a configuração no [exemplo fornecido para alerta de registo do tipo de número de resultados](#example-of-number-of-records-type-log-alert) – onde a consulta de alerta personalizada é projetada para procurar por 500 código de resultado nos registos.

- Em 1 05 PM quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo gerou zero registos com o código de resultado ter 500. Uma vez que zero é inferior ao limiar, e não é acionado o alerta.
- Na próxima iteração às 1: 22:00 quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo fornecido cinco registos com o código de resultado como 500. Uma vez que cinco excede o limiar e o alerta é acionado com ações associadas obter acionada.
- Em 1 às 15:15 quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo fornecidos dois registos com o código de resultado 500. Uma vez que os dois é superior ao limiar e o alerta é acionado com ações associadas obter acionada.
- Agora na próxima iteração em 1:20 PM quando o alerta de registo de Contoso foi executada pelo alerta do Azure, o resultado da pesquisa de registo fornecido novamente zero registos com o código de resultado 500. Uma vez que zero é inferior ao limiar, e não é acionado o alerta.

Mas, no caso listado acima, em 1 às 15:15 - alertas do Azure não consegue determinar que os problemas subjacentes vistos em 1:10 manter e se houver falhas líquidas de novo. Como a consulta fornecida pelo utilizador pode ser tendo em conta registos anteriores - podem Certifique-se os alertas do Azure. Uma vez que a lógica para o alerta é encapsulada na consulta de alerta - portanto, dois registos com o código de resultado 500 visto em 1 às 15:15 podem ou não podem ser vistos já às 1: 22:00. Por conseguinte, para err com cautela, quando o alerta de registo Contoso é executado em 1 às 15:15, ação configurada é acionada novamente. Agora em 1:20 PM quando são vistos zero registos com o código de resultado 500 - alertas do Azure não podem ter a certeza de que a causa do código de resultado 500 visto em 1: 22:00 e 1 às 15:15 agora está resolvida e alertas do Azure Monitor com confiança podem deduzir os problemas de 500 Erro não acontecerá pela mesma razão s novamente. Por conseguinte, o alerta de registo de Contoso será não alterados como resolvido no dashboard de alerta do Azure e/ou notificações enviadas informando a resolução do alerta. Em vez disso, o utilizador que entenda a condição exata ou o motivo para a lógica incorporada na consulta do analytics, pode [marque o alerta como fechada](alerts-managing-alert-states.md) conforme necessário.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e faturação de alertas de registo

Preços aplicáveis para alertas de registo é mencionado no [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) página. No faturas do Azure, os alertas de registo são representadas como tipo `microsoft.insights/scheduledqueryrules` com:

- Alertas de registo no Application Insights apresentado com o nome exato do alerta, juntamente com o grupo de recursos e as propriedades do alerta
- Alertas de registos no Log Analytics mostrado com o nome exato do alerta, juntamente com o grupo de recursos e as propriedades do alerta; Quando criados com [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

O [API do Log Analytics legadas](../../azure-monitor/platform/api-alerts.md) tem ações de alerta e agendas como parte de pesquisa guardada do Log Analytics e não correta [recursos do Azure](../../azure-resource-manager/resource-group-overview.md). Por conseguinte, para ativar a faturação para esses alertas de registo legado criada para o Log Analytics através do portal do Azure **sem** [mudar para a nova API](../../azure-monitor/platform/alerts-log-api-switch.md) ou via [API do Log Analytics legadas](../../azure-monitor/platform/api-alerts.md) - regras de alerta de pseudo-autenticação ocultos são criadas no `microsoft.insights/scheduledqueryrules` para faturação no Azure. As regras de alerta de pseudo-autenticação ocultos criadas para ser `microsoft.insights/scheduledqueryrules` conforme mostrado como `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` juntamente com o grupo de recursos e as propriedades do alerta.

> [!NOTE]
> Se inválido carateres como `<, >, %, &, \, ?, /` estão presentes, serão substituídas por `_` na regra de alerta de pseudo-autenticação ocultos dê um nome e, por conseguinte, também no Azure são faturadas.

Para remover os recursos de scheduleQueryRules ocultos criados para uma faturação de regras de alerta usando [API do Log Analytics legadas](api-alerts.md), utilizador pode fazer qualquer um dos seguintes:

- De qualquer utilizador pode [mudar a preferência de API para as regras de alerta na área de trabalho do Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) e sem perda de suas regras de alerta ou a movimentação de monitorização para o Azure Resource Manager em conformidade [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Deste modo, elimina a necessidade de tornar as regras de alerta de pseudo-autenticação ocultada para faturação.
- Ou se o utilizador não quer alternar a preferência de API, o usuário precisará **eliminar** a agenda original e, em seguida, utilizar a ação do alerta [API do Log Analytics legada](api-alerts.md) ou eliminar no [portal do Azure a regra de alerta de registo original](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Além disso, para os recursos de scheduleQueryRules ocultos criados para uma faturação de regras de alerta com [API do Log Analytics legadas](api-alerts.md), qualquer operação de modificação como PUT irá falhar. Como o `microsoft.insights/scheduledqueryrules` são de tipo pseudo regras para fins de faturação as regras de alerta criadas utilizando [API do Log Analytics legadas](api-alerts.md). Qualquer modificação de regra de alerta deve ser feita utilizando [API do Log Analytics legadas](api-alerts.md) (ou) o utilizador pode [mudar a preferência de API para as regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md) utilizar [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em vez disso.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [criar nos alertas de registo no Azure](../../azure-monitor/platform/alerts-log.md).
* Compreender [webhooks em alertas de registo no Azure](alerts-log-webhook.md).
* Saiba mais sobre [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md).
* Saiba mais sobre [do Log Analytics](../../azure-monitor/log-query/log-query-overview.md).