---
title: Alertas de registo no Monitor Azure
description: Desencadeie e-mails, notificações, telefones com URLs (webhooks) ou automação quando as condições de consulta analítica especificarem são cumpridas para alertas Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 0e81d48f4e709a9a0bb8ebb33c7029d3841167b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609051"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de registo no Monitor Azure

Os alertas de registo são um dos tipos de alerta que são suportados em [Alertas Azure](../../azure-monitor/platform/alerts-overview.md). Os alertas de registo permitem que os utilizadores utilizem a plataforma Azure Analytics como base para alertar.

O Alerta de Registo consiste em regras de Pesquisa de Registos criadas para [Registos de Monitores Azure](../../azure-monitor/learn/tutorial-viewdata.md) ou [Insights de Aplicações](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Para saber mais sobre o seu uso, consulte [a criação de alertas de registo em Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Os dados de registo populares do [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) também estão agora disponíveis na plataforma métrica no Azure Monitor. Para visualização de detalhes, [Alerta métrico para Registos](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Regra de alerta de pesquisa de registo - definição e tipos

São criadas regras de pesquisa de registos pelos Alertas do Azure para executar automaticamente consultas de registos especificados a intervalos regulares.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, é criado um registo de alerta. A regra pode executar automaticamente uma ou mais ações através dos [Grupos de Ações](../../azure-monitor/platform/action-groups.md). Pode ser necessária uma função [de colaboradora de monitorização do Azure](../../azure-monitor/platform/roles-permissions-security.md) para a criação, modificação e atualização de alertas de registo; juntamente com o acesso & direitos de execução de consultas para os alvos de análise em regra de alerta ou consulta de alerta. Caso o utilizador que cria não tenha acesso a todos os alvos analíticos em consulta de regra de alerta ou alerta - a criação de regras pode falhar ou a regra de alerta de registo será executada com resultados parciais.

As regras de pesquisa de registo são definidas pelos seguintes detalhes:

- **Consulta de registo**.  a consulta que é executada sempre que a regra de alerta é acionada.  Os registos devolvidos por esta consulta são usados para determinar se um alerta deve ser desencadeado. A consulta de análise pode ser para um espaço de trabalho específico do Log Analytics ou app Application Insights e até mesmo abranger [vários recursos de Log Analytics e Application Insights,](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) desde que o utilizador tenha acesso, bem como direitos de consulta a todos os recursos. 
    > [!IMPORTANT]
    > suporte [de consulta de recursos cruzados](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) em alertas de registo para Insights de Aplicação e alertas de registo para Log Analytics [configurado usando apenas API de Regras DeQueryRules programados.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Alguns comandos e combinações analíticas são incompatíveis com a utilização em alertas de registo; para obter mais detalhes, [registar consultas de alerta no Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo da hora atual. O período de tempo restringe os dados recolhidos para consulta de registo para evitar abusos e contorna qualquer comando de tempo (como há pouco) utilizado na consulta de registo. <br>*Por exemplo, Se o período de tempo estiver definido para 60 minutos, e a consulta for executada às 13:15, apenas os registos criados entre as 12:15 e as 13:15 são devolvidos para executar consulta de registo. Agora, se a consulta de registo usar o comando do tempo como ago (7d), a consulta de registo seria executada apenas para dados entre as 12:15 e 1:15 PM - como se os dados existissem apenas nos últimos 60 minutos. E não por sete dias de dados como especificado na consulta de registo.*

- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou inferior ao período de tempo.  Se o valor for maior do que o período de tempo, arrisca-se a falhar registos.<br>*Por exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada às 13:00, retorna os registos entre as 12:30 e as 13:00.  A próxima vez que a consulta correr é às 14:00, quando devolveria os registos entre 1:30 e 2:00.  Quaisquer registos criados entre 1:00 e 1:30 nunca seriam avaliados.*

- **Limiar**.  Os resultados da pesquisa de registo são avaliados para determinar se deve ser criado um alerta.  O limiar é diferente para os diferentes tipos de regras de alerta de pesquisa de registo.

As regras de pesquisa de registo, seja para [Registos monitores Azure](../../azure-monitor/learn/tutorial-viewdata.md) ou Insights de [Aplicação,](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)podem ser de dois tipos. Cada um destes tipos é descrito em detalhe nas secções que se seguem.

- **[Número de resultados.](#number-of-results-alert-rules)** Único alerta criado quando os registos de números devolvidos pela pesquisa de registo excedem um número especificado.
- **[Medição métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de registo com valores que excedam o limiar especificado.

As diferenças entre os tipos de regras de alerta são as seguintes.

- *O número de* regras de alerta de resultados cria sempre um único alerta, enquanto a regra de alerta *de medição métrica* cria um alerta para cada objeto que excede o limiar.
- *O número de regras de* alerta de resultados cria um alerta quando o limiar é ultrapassado por uma única vez. As regras de alerta *de medição métrica* podem criar um alerta quando o limiar é ultrapassado um determinado número de vezes durante um determinado intervalo de tempo.

### <a name="number-of-results-alert-rules"></a>Número de regras de alerta de resultados

**O número de regras de** alerta de resultados cria um único alerta quando o número de registos devolvidos pela consulta de pesquisa excede o limiar especificado. Este tipo de regra de alerta é ideal para trabalhar com eventos como registos de eventos Windows, Syslog, WebApp Response e registos personalizados.  Pode querer criar um alerta quando um determinado evento de erro é criado, ou quando vários eventos de erro são criados dentro de um determinado período de tempo.

**Limiar**: O limiar para um número de regras de alerta de resultados é superior ou inferior a um valor específico.  Se o número de registos devolvidos pela pesquisa de registo corresponder a estes critérios, então é criado um alerta.

Para alertar num único evento, desa um número de resultados superior a 0 e verifique a ocorrência de um único evento que foi criado desde a última vez que a consulta foi executada. Algumas aplicações podem registar um erro ocasional que não deve necessariamente levantar um alerta.  Por exemplo, a aplicação pode voltar a tentar o processo que criou o evento de erro e, em seguida, ter sucesso da próxima vez.  Neste caso, pode não querer criar o alerta a menos que sejam criados vários eventos dentro de um determinado período de tempo.  

Em alguns casos, pode querer criar um alerta na ausência de um evento.  Por exemplo, um processo pode registar eventos regulares para indicar que está a funcionar corretamente.  Se não registar um destes eventos dentro de um determinado período de tempo, então deve ser criado um alerta.  Neste caso, fixaria o limiar para **menos de 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemplo do número de registos de registos

Considere um cenário em que pretende saber quando a sua App baseada na Web dá uma resposta aos utilizadores com o Código 500 (isto é) Erro do Servidor Interno. Criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** pedidos / onde resultados Código == "500"<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Valor limiar:** Maior que 0<br>

Em seguida, o alerta executaria a consulta a cada 5 minutos, com 30 minutos de dados - para procurar qualquer registo onde o código de resultados fosse 500. Se mesmo um desses registos for encontrado, dispara o alerta e despoleta a ação configurada.

### <a name="metric-measurement-alert-rules"></a>Regras de alerta de medição métrica

As regras de alerta **de medição métrica** criam um alerta para cada objeto numa consulta com um valor que excede um limiar especificado e uma condição de gatilho especificado. Ao contrário do número de regras de alerta **de resultados,** as regras de alerta **de medição métrica** funcionam quando o resultado da análise fornece uma série de tempo. Têm as seguintes diferenças distintas em termos de número de regras de alerta **de resultados.**

- **Função agregada**: Determina o cálculo que é realizado e potencialmente um campo numérico para agregar.  Por exemplo, **a contagem()** devolve o número de registos na consulta, **avg (CounterValue)** devolve a média do campo CounterValue durante o intervalo. A função agregada em consulta deve ser nomeada/chamada: AgregadaValue e fornecer um valor numérico. 

- **Campo do Grupo**: É criado um registo com um valor agregado para cada instância deste campo, podendo ser gerado um alerta para cada um.  Por exemplo, se quisesse gerar um alerta para cada computador, utilizaria **por Computador**. No caso de existirem vários campos de grupo especificados em consulta de alerta, o utilizador pode especificar qual o campo a utilizar para classificar resultados utilizando o parâmetro **Agregado On** (metricColumn)

    > [!NOTE]
    > A opção *Agregante On* (metricColumn) está disponível para alertas de registo tipo de medição métrica para Insights de Aplicação e alertas de registo para [Log Analytics configurado apenas usando a API de Regras De Recurso.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Intervalo**: Define o intervalo de tempo sobre o qual os dados são agregados.  Por exemplo, se especificasse **cinco minutos,** seria criado um registo para cada instância do campo de grupo agregado a intervalos de 5 minutos durante o período de tempo especificado para o alerta.

    > [!NOTE]
    > A função do recipiente deve ser utilizada em consulta para especificar o intervalo. Como o caixote do lixo pode resultar em intervalos de tempo desiguais - O alerta converterá automaticamente o comando do caixote do lixo para bin_at comando com tempo adequado no tempo de execução, para garantir resultados com um ponto fixo. O tipo de medição métrica do alerta de log é projetado para funcionar com consultas com até três instâncias de comando bin()
    
- **Limiar**: O limiar das regras de alerta de medição métrica é definido por um valor agregado e por uma série de infrações.  Se algum ponto de dados na pesquisa de registo exceder este valor, é considerado uma violação.  Se o número de infrações para qualquer objeto nos resultados exceder o valor especificado, então é criado um alerta para esse objeto.

A configuração errada da opção *Aggregate On* ou *metricColumn* pode causar erros de alerta. Para obter mais informações, consulte [a resolução de problemas quando a regra de alerta de medição métrica estiver incorreta](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemplo do alerta de registo do tipo de medição métrica

Considere um cenário em que desejasse um alerta se algum computador excedesse a utilização do processador de 90% três vezes em 30 minutos.  Criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** Perf ! onde ObjectName == "Processador" e ContraName == "% tempo do processador" resumir AgregadoValue = avg (CounterValue) por bin (TimeGenerated, 5m), Computador<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Lógica de Alerta - Limiar de & de Condição:** Maior que 90<br>
- **Campo de Grupo (Agregado-on):** Computador
- **Alerta de gatilho baseado em:** Total de infrações Superior a 2<br>

A consulta criaria um valor médio para cada computador em intervalos de 5 minutos.  Esta consulta seria executada a cada 5 minutos para os dados recolhidos nos 30 minutos anteriores. Uma vez que o Campo de Grupo (Agregado-on) escolhido é o columnar 'Computador' - o Agregadovalue é dividido por vários valores de 'Computador' e a utilização média do processador para cada computador é determinada por um intervalo de tempo de 5 minutos.  O resultado da consulta da amostra para (digamos) três computadores, seria o seguinte.


|TempoGerado [UTC] |Computador  |AgregadoValue  |
|---------|---------|---------|
|20xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se o resultado da consulta fosse traçado, parece que.

![Resultados da consulta de amostras](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Neste exemplo, vemos em caixas de 5 minutos para cada um dos três computadores - utilização média do processador como computada por 5 minutos. Limiar de 90 sendo violado por srv01 apenas uma vez às 1:25 bin. Em comparação, o SRV02 ultrapassa o limiar de 90 em 1:10, 1:15 e 1:25 contentores; enquanto srv03 excede 90 limiar em 1:10, 1:15, 1:20 e 1:30.
Uma vez que o alerta está configurado para desencadear com base em infrações totais são mais de dois, vemos que o SRV02 e o SRV03 apenas cumprem os critérios. Assim, seriam criados alertas separados para srv02 e srv03, uma vez que violaram o limiar de 90% duas vezes em vários caixotes do tempo.  Se o *alerta do Gatilho se baseasse em:* o parâmetro foi configurado para a opção de *infrações contínuas,* então um alerta seria disparado **apenas** para srv03 uma vez que ele violou o limiar por três caixotes de tempo consecutivos das 1:10 às 1:20. E **não** para o SRV02, já que ultrapassou o limiar por dois caixotes do tempo consecutivos de 1:10 para 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Regra de alerta de pesquisa de registo - disparo e estado

As regras de alerta de pesquisa funcionam apenas na lógica que constrói na consulta. O sistema de alerta não tem qualquer outro contexto do estado do sistema, da sua intenção ou da causa raiz implícita na consulta. Como tal, os alertas de registo são referidos como sem estado. As condições são avaliadas como "TRUE" ou "FALSE" cada vez que são executadas.  Um alerta disparará sempre que a avaliação da condição de alerta for "TRUE", independentemente de ser disparada anteriormente.    

Vamos ver este comportamento em ação com um exemplo prático. Assuma que temos uma regra de alerta de registo chamada *Contoso-Log-Alert,* que está configurada como mostrado no exemplo fornecido para o [número de resultados tipo alerta](#example-of-number-of-records-type-log-alert)de registo . A condição é uma consulta de alerta personalizada projetada para procurar 500 código de resultados em registos. Se mais um de 500 códigos de resultados forem encontrados em registos, o estado do alerta é verdadeiro. 

Em cada intervalo abaixo, o sistema de alerta Azure avalia a condição do *Alerta Contoso-Log*.


| Hora    | Num dos registos devolvidos por consulta de pesquisa de registo | Evação da condição de registo | Resultado 
| ------- | ----------| ----------| ------- 
| 13:05 | 0 recordes | 0 não é > 0 tão FALSO |  Alerta não dispara. Nenhuma ação chamada.
| 13h10 | 2 registos | 2 > 0 tão verdadeiro  | Alerta de incêndios e grupos de ação chamados. Estado de alerta ATIVE.
| 13:15 | 5 registos | 5 > 0 tão verdadeiro  | Alerta de incêndios e grupos de ação chamados. Estado de alerta ATIVE.
| 13:20 | 0 recordes | 0 não é > 0 tão FALSO |  Alerta não dispara. Nenhuma ação chamada. Estado de alerta deixado ATIVE.

Usando o caso anterior como exemplo:

Às 13:15, os alertas de Azure não podem determinar se os problemas subjacentes vistos às 13:10 persistem e se os registos são novas falhas ou repetições de falhas mais antigas às 13:10. A consulta fornecida pelo utilizador pode ou não estar a ter em conta registos anteriores e o sistema não sabe. O sistema de alerta Azure foi construído para errar por precaução, e dispara novamente o alerta e as ações associadas às 13:15. 

Às 13:20, quando zero registos são vistos com o código de resultados de 500, os alertas do Azure não podem ter a certeza de que a causa do código de resultados de 500 visto às 13:10 e 13:15 está agora resolvida. Não sabe se os 500 problemas de erro voltarão a acontecer pelas mesmas razões. Por *isso, o Alerta De Contoso-Log* não altera para **Resolvido** no painel de alerta Azure e/ou notificações não são enviadas indicando que o alerta está resolvido. Só você, que entende a condição exata ou razão para a lógica incorporada na consulta de análise, pode [marcar o alerta tão fechado](alerts-managing-alert-states.md) quanto necessário.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e Faturação de Alertas de Registo

Os preços aplicáveis aos Alertas de Registo estão indicados na página [de Preços do Monitor Azure.](https://azure.microsoft.com/pricing/details/monitor/) Nas notas do Azure, os Alertas de Registo são representados como tipo `microsoft.insights/scheduledqueryrules` com:

- Alertas de registo em Informações de aplicações mostrados com nome de alerta exato, juntamente com o grupo de recursos e propriedades de alerta
- Alertas de registo no Log Analytics mostrados com o nome de alerta exato, juntamente com as propriedades do grupo de recursos e de alerta; quando criado usando [a API deQueryRules programada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

O [legado Log Analytics API](../../azure-monitor/platform/api-alerts.md) tem ações e horários de alerta como parte da Pesquisa Salva de Log Analytics e não recursos [Azure adequados](../../azure-resource-manager/management/overview.md). Assim, para permitir a faturação de tais alertas de registo legado criados para o Log Analytics usando o portal Azure **sem** [mudar para a nova API](../../azure-monitor/platform/alerts-log-api-switch.md) ou através do [legado Log Analytics API](../../azure-monitor/platform/api-alerts.md) - são criadas regras de pseudo-alerta escondidas `microsoft.insights/scheduledqueryrules` para faturação no Azure. As regras de alerta pseudo-pseudo escondidas criadas para `microsoft.insights/scheduledqueryrules` faturação, conforme `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` mostrado, juntamente com o grupo de recursos e propriedades de alerta.

> [!NOTE]
> Se caracteres inválidos como `<, >, %, &, \, ?, /` estiverem presentes, serão substituídos `_` pelo nome da regra de alerta pseudo-oculto e, portanto, também na conta Azure.

Para remover o horário ocultoQueryRules recursos criados para faturação de regras de alerta usando [o legado Log Analytics API,](api-alerts.md)o utilizador pode fazer qualquer um dos seguintes:

- Qualquer utilizador pode [mudar a preferência da API pelas regras de alerta no espaço](../../azure-monitor/platform/alerts-log-api-switch.md) de trabalho do Log Analytics e sem perda das suas regras de alerta ou movimento de monitorização para a Azure Resource Manager em conformidade com a [API.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Assim, eliminar a necessidade de fazer pseudo regras de alerta escondido para a faturação.
- Ou se o utilizador não quiser mudar a preferência da API, o utilizador terá de **eliminar** o horário original e alertar a ação utilizando a [API do log api](api-alerts.md) legado ou eliminar no [portal Azure a regra original do alerta de registo](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Adicionalmente para o calendário ocultoQueryRules recursos criados para faturação de regras de alerta usando [o legado Log Analytics API](api-alerts.md), qualquer operação de modificação como PUT falhará. Como as `microsoft.insights/scheduledqueryrules` regras pseudo tipo destinam-se a faturar as regras de alerta criadas utilizando [o legado Log Analytics API](api-alerts.md). Qualquer modificação da regra de alerta deve ser feita usando [o legado Log Analytics API](api-alerts.md) (ou) o utilizador pode mudar a preferência da [API pelas regras de alerta](../../azure-monitor/platform/alerts-log-api-switch.md) para usar a [API de Regras Desígradas programadas.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="next-steps"></a>Próximos passos

* Saiba como [criar em alertas de registo em Azure](../../azure-monitor/platform/alerts-log.md).
* Compreenda [os webhooks em alertas de registo em Azure](alerts-log-webhook.md).
* Saiba mais sobre [alertas Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [a Aplicação Insights](../../azure-monitor/app/analytics.md).
* Saiba mais sobre [o Log Analytics.](../../azure-monitor/log-query/log-query-overview.md)
