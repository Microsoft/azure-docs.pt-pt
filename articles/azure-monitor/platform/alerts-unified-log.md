---
title: Alertas de registo no Azure Monitor
description: E-mails de Acionador, notificações, URLs de Web sites de chamada (webhooks) ou automatização quando estiverem reunidas as condições de consulta de análise que especificar para alertas do Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1aeafa9fb2fc4d275e15d1a911adea7d3f3220dc
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467235"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de registo no Azure Monitor
Este artigo fornece detalhes de alertas de registo são um dos tipos de alertas de suportam o [alertas do Azure](../../azure-monitor/platform/alerts-overview.md) e permitir que os utilizadores utilizem a plataforma de análise do Azure como base para alertas.

Alerta de registo é composta por regras de pesquisa de registos, criadas para [do Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Para saber mais sobre a utilização, consulte [criar alertas de registo no Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Dados de registos populares [do Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) também está agora disponível na plataforma de métrica no Azure Monitor. Para a vista de detalhes, [alerta de métrica para os registos](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Log search regra de alerta - definição e tipos

São criadas regras de pesquisa de registos pelos Alertas do Azure para executar automaticamente consultas de registos especificados a intervalos regulares.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, é criado um registo de alerta. A regra pode executar automaticamente uma ou mais ações através dos [Grupos de Ações](../../azure-monitor/platform/action-groups.md). 

Regras de pesquisa de registo são definidas pelos seguintes detalhes:
- **Consulta de registo**.  A consulta é executada sempre que a regra de alerta é acionado.  Os registos devolvidos por esta consulta são utilizados para determinar se é criado um alerta. Consulta do Analytics também pode incluir [em várias aplicações chamadas](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), [entre chamadas de área de trabalho, e [chamadas entre recursos](../../azure-monitor/log-query/cross-workspace-query.md) desde que o utilizador tem direitos de acesso para os aplicativos externos. 

    > [!IMPORTANT]
    > Utilizador tem de ter [Contribuidor de monitorização do Azure](../../azure-monitor/platform/roles-permissions-security.md) função para a criação, modificação e a atualizar a alertas de registo no Azure Monitor; juntamente com acesso & direitos de execução para o destino de análise (s) na regra de alerta ou consulta de alerta de consulta. Se a criação de utilizador não tem acesso a todos os destinos de análise na regra de alerta ou consulta de alerta - a criação de regra poderá falhar ou a regra de alerta de registo será executada com resultados parciais.

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo da hora atual. Período de tempo restringe os dados obtidos para a consulta de registo evitar abusos e evita qualquer comando de tempo (como há) utilizado numa consulta de registo. <br>*Por exemplo, se o período de tempo é definido como 60 minutos, e a consulta é executada em 1 às 15:15, apenas os registos criados entre 12:15 e 1 às 15:15 é devolvido ao executar a consulta de registo. Agora, se a consulta de registo utiliza o comando como atrás de tempo (7D), a consulta de log deve ser executada apenas para dados entre 12:15 e 1 às 15:15 - como se existem dados para apenas os últimos 60 minutos. E não durante sete dias de dados conforme especificado na consulta de registo.*
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou menor que o período de tempo.  Se o valor for maior do que o período de tempo, em seguida, corre o risco de registos a ser omitidos.<br>*Por exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta é executada às 9:00 com 1, ele devolve registos entre 12:30 e 1 21 horas.  Da próxima vez que a consulta executaria é 2:00 quando ele retornaria registos entre 1:30 e 2:00.  Quaisquer registos criados entre 1:00 e 1:30 nunca seriam avaliados.*
- **Limiar**.  Os resultados da pesquisa de registos são avaliados para determinar se deve ser criado um alerta.  O limiar é diferente para os diferentes tipos de regras de alerta de pesquisa de registo.

Regras de pesquisa de registo, sê-lo para [do Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), podem ter dois tipos. Cada um desses tipos é descrita detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)**. Único alerta criada quando um número especificado de excedem o número de registos devolvido pela pesquisa de registo.
- **[Medida da métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de registo com valores que excedam o limiar especificado.

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

- **Medida da métrica** regras de alerta criar um alerta para cada objeto numa consulta com um valor que excede um limiar especificado.  Eles têm as seguintes diferenças distintas de **número de resultados** regras de alerta.
- **Função de agregação**: Determina o cálculo que é executado e, potencialmente, com um numérico campo a agregar.  Por exemplo, **count ()** devolve o número de registos na consulta, **avg(CounterValue)** devolve a média do campo CounterValue ao longo do intervalo. Função de agregação na consulta tem de ser com o nome/chamado: AggregatedValue e fornecer um valor numérico. 
- **Campo de grupo**: É criado um registo com um valor agregado para cada instância deste campo e pode ser gerado um alerta para cada um.  Por exemplo, se quiser gerar um alerta para cada computador, usaria **por computador** 

    > [!NOTE]
    > Medida da métrica regras de alerta que se baseiam no Application Insights, pode especificar o campo para agrupar os dados. Para tal, utilize o **agregada no** opção na definição da regra.   
    
- **Intervalo**:  Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se especificou **cinco minutos**, teria de ser criado um registo para cada instância do campo Grupo agregada em intervalos de 5 minutos ao longo do período de tempo especificado para o alerta.

    > [!NOTE]
    > Função de posição tem de ser utilizada numa consulta para especificar o intervalo. Como bin() pode resultar em intervalos de tempo desiguais - alerta converte automaticamente bin comando bin_at comando com um horário apropriado no tempo de execução, para garantir resultados com um ponto de fixo. Tipo de medida da métrica de alerta de registo foi concebido para trabalhar com consultas com o comando singular bin()
    
- **Limiar**: O limiar para regras de alerta de medida da métrica é definido por um valor agregado e um número de violações.  Se qualquer ponto de dados na pesquisa de registos exceder este valor, ele é considerado uma violação.  Se o número de falhas para qualquer objeto nos resultados excede o valor especificado, é criado um alerta para esse objeto.

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemplo de alerta de registo do tipo de medida da métrica
Considere um cenário em que queria um alerta se a qualquer computador excedeu a utilização do processador de 90% três vezes mais de 30 minutos.  Poderia criar uma regra de alerta com os seguintes detalhes:  

- **Consulta:** Desempenho | onde ObjectName = = "Processador" e CounterName = = "% tempo do processador" | resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 5m), computador<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência do alerta:** cinco minutos<br>
- **Valor agregado:** Superior a 90<br>
- **Acionar alerta com base em:** Total de casos de violações de maior que 2<br>

A consulta deverá criar um valor médio para cada computador em intervalos de 5 minutos.  Esta consulta seria executada a cada 5 minutos para os dados recolhidos durante os 30 minutos anteriores.  Dados de exemplo são mostrados abaixo para três computadores.

![Resultados da consulta de exemplo](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Neste exemplo, seriam possível criar alertas separadas para srv02 e srv03, uma vez que eles infringido o limiar de 90% três vezes ao longo do período de tempo.  Se o **acionar alerta com base em:** foram alterados para **Consecutive** , em seguida, seria possível criar um alerta apenas para srv03, uma vez que ele infringido o limiar para três amostras consecutivas.

## <a name="log-search-alert-rule---firing-and-state"></a>Log search regra de alerta - acionadas e Estado
Regra de alerta de pesquisa de registo funciona na lógica vinculada por utilizador de acordo com a configuração e a consulta de análise personalizada utilizada. Desde a lógica da condição exata ou motivo por que a regra de alerta deve o acionador é encapsulado numa consulta do Analytics - que pode ser diferente em cada regra de alerta de registo. Alertas do Azure tem informações escassos da específica subjacente causa dentro os resultados de registo quando a condição de limiar de regra de alerta de pesquisa de registo é atingida ou excedida. Assim, alertas de registo são referidas como como estado sem e irá disparar sempre que o resultado da pesquisa de registo é suficiente para exceder o limiar especificado nos alertas de registo de *número de resultados* ou *medida da métrica* tipo da condição. E regras de alerta de registo continuamente irão manter disparo, desde que a condição de alerta for cumprida, o resultado da consulta do analytics personalizado fornecido; sem o alerta resolvido cada introdução. Como a lógica a causa exata da falha de monitorização está oculto dentro da consulta de análise fornecida pelo usuário; não existe nenhuma forma dos alertas do Azure para deduzir as consequências se o resultado de pesquisa de registo não cumprir o limiar indica a resolução do problema.

Agora vamos supor que temos uma regra de alerta de registo denominada *Contoso-alerta de registo*, de acordo com a configuração no [exemplo fornecido para alerta de registo do tipo de número de resultados](#example-of-number-of-records-type-log-alert). 
- Em 1 05 PM quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo gerou 0 registros; abaixo do limiar e, por conseguinte, não disparando o alerta. 
- Na próxima iteração às 1: 22:00 quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo fornecido 5 registos; a exceder o limiar e disparando o alerta, logo após ao acionar a [grupo de ação](../../azure-monitor/platform/action-groups.md) associados. 
- Em 1 às 15:15 quando o alerta de registo de Contoso foi executada através de alertas do Azure, o resultado da pesquisa de registo fornecido 2 registos; a exceder o limiar e disparando o alerta, logo após ao acionar a [grupo de ação](../../azure-monitor/platform/action-groups.md) associados.
- Agora na próxima iteração em 1:20 PM quando o alerta de registo de Contoso foi executada pelo alerta do Azure, o resultado de pesquisa de registo fornecida novamente 0 registros; abaixo do limiar e, por conseguinte, não disparando o alerta.

Mas no caso listado acima, em 1 às 15:15 - alertas do Azure não consegue determinar que os problemas subjacentes vistos em 1:10 manter e se houver falhas novo net; como a consulta fornecida pelo utilizador pode ser tendo em conta registos anteriores - podem Certifique-se os alertas do Azure. Por conseguinte, para err no lado de advertência, quando o alerta de registo de Contoso é executado em 1 às 15:15, configurado [grupo de ação](../../azure-monitor/platform/action-groups.md) é acionado novamente. Agora em 1:20 PM quando não existem registos são vistos: alertas do Azure não podem ser-se de que foi resolvida a causa dos registos; Por conseguinte, o alerta de registo de Contoso será não alterados como resolvido no dashboard de alerta do Azure e/ou notificações enviadas informando a resolução do alerta.


## <a name="pricing-and-billing-of-log-alerts"></a>Preços e faturação de alertas de registo
Preços aplicáveis para alertas de registo é mencionado no [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) página. No faturas do Azure, os alertas de registo são representadas como tipo `microsoft.insights/scheduledqueryrules` com:
- Alertas de registo no Application Insights apresentado com o nome exato do alerta, juntamente com o grupo de recursos e as propriedades do alerta
- Alertas no Log Analytics mostrado com o nome do alerta de registo `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` juntamente com o grupo de recursos e as propriedades do alerta

    > [!NOTE]
    > O nome para pesquisas guardadas tudo, cronogramas e ações criadas com a API de análise de registo tem de ser em minúsculas. Se inválido carateres como `<, >, %, &, \, ?, /` são utilizados – eles serão substituídos por `_` na fatura.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [criar nos alertas de registo no Azure](../../azure-monitor/platform/alerts-log.md).
* Compreender [webhooks em alertas de registo no Azure](alerts-log-webhook.md).
* Saiba mais sobre [alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [Application Insights](../../azure-monitor/app/analytics.md).
* Saiba mais sobre [do Log Analytics](../../azure-monitor/log-query/log-query-overview.md).    

