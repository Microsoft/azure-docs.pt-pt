---
title: Alertas de log no Monitor Azure
description: Desencadear e-mails, notificações, sites de chamadas URLs (webhooks) ou automação quando as condições de consulta analítica que especifica forem satisfeitas para alertas Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: a6abf4665c27771497037da35f85bb540e6e904e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665226"
---
# <a name="log-alerts-in-azure-monitor"></a>Alertas de log no Monitor Azure

Este artigo fornece detalhes dos alertas de Registo são um dos tipos de alertas suportados nos [Alertas Azure](../../azure-monitor/platform/alerts-overview.md) e permitem que os utilizadores utilizem a plataforma de análise do Azure como base para alertar.

O Alerta de Registo consiste em regras de pesquisa de log criadas para registos de [monitores do Azure](../../azure-monitor/learn/tutorial-viewdata.md) ou [insights de aplicação](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Para saber mais sobre o seu uso, consulte a criação de alertas de [registo em Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Os dados de registo populares do [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) também estão disponíveis na plataforma métrica do Monitor Azure. Para mais detalhes, [Alerta Métrico para Registos](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Regra de alerta de pesquisa de registo - definição e tipos

São criadas regras de pesquisa de registos pelos Alertas do Azure para executar automaticamente consultas de registos especificados a intervalos regulares.  Se os resultados da pesquisa de registos corresponderem a critérios específicos, é criado um registo de alerta. A regra pode executar automaticamente uma ou mais ações através dos [Grupos de Ações](../../azure-monitor/platform/action-groups.md). Pode ser necessário o papel do Colaborador de [Monitorização Azure](../../azure-monitor/platform/roles-permissions-security.md) para a criação, modificação e atualização de alertas de registo; juntamente com os direitos de acesso e consulta de execução para os alvos de análise em regra de alerta ou consulta de alerta. Caso a criação do utilizador não tenha acesso a todos os alvos de análise em regra de alerta ou consulta de alerta - a criação de regras pode falhar ou a regra de alerta de registo será executada com resultados parciais.

As regras de pesquisa de registo são definidas pelos seguintes detalhes:

- **Consulta de registo**.  A consulta que corre sempre que o alerta governa.  Os registos devolvidos por esta consulta são usados para determinar se um alerta deve ser desencadeado. A consulta de análise pode ser para um espaço de trabalho específico log Analytics ou app Application Insights e até mesmo abranger [vários recursos](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) de Log Analytics e Application Insights desde que o utilizador tenha acesso, bem como direitos de consulta a todos os recursos. 
    > [!IMPORTANT]
    > suporte [de consulta de recursos cruzados](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) em alertas de registo para Insights de aplicação e alertas de registo para Log Analytics [configurado apenas usando API agendadoQueryRules.](../../azure-monitor/platform/alerts-log-api-switch.md)

    Alguns comandos e combinações analíticos são incompatíveis com a utilização em alertas de registo; para mais detalhes, consultas de [alerta de registo no Monitor Azure](../../azure-monitor/platform/alerts-log-query.md).

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta devolve apenas os registos que foram criados neste intervalo da hora atual. O período de tempo restringe os dados recolhidos para consulta de registo para evitar abusos e contorna qualquer comando de tempo (como há pouco) utilizado na consulta de registo. <br>*Por exemplo, Se o período de tempo for definido para 60 minutos, e a consulta for executada às 13:15, apenas os registos criados entre as 12:15 e as 13:15 são devolvidos para executar consulta de registo. Agora, se a consulta de registo usar o comando de tempo como há algum tempo (7d), a consulta de registo seria executada apenas para dados entre as 12:15 e as 13:15 - como se os dados existissem apenas nos últimos 60 minutos. E não por sete dias de dados especificados na consulta de registo.*

- **Frequência.**  Especifica com que frequência a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou inferior ao período de tempo.  Se o valor for maior do que o período de tempo, arrisca-se a perder registos.<br>*Por exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada às 13:00, devolve os registos entre as 12:30 e as 13:00.  A próxima vez que a consulta correr é às 14:00, quando devolveria os registos entre 1:30 e 2:00.  Quaisquer registos criados entre 1:00 e 1:30 nunca seriam avaliados.*

- **Limiar**.  Os resultados da pesquisa de registo são avaliados para determinar se deve ser criado um alerta.  O limiar é diferente para os diferentes tipos de regras de alerta de pesquisa de registo.

As regras de pesquisa de registos são para Registos de [Monitores Azure](../../azure-monitor/learn/tutorial-viewdata.md) ou Insights de [Aplicação,](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)podem ser de dois tipos. Cada um destes tipos é descrito em detalhe nas secções que se seguem.

- **[Número de resultados.](#number-of-results-alert-rules)** O único alerta criado quando os registos de números devolvidos pela pesquisa de registo excedem um número especificado.
- **[Medição métrica](#metric-measurement-alert-rules)** .  Alerta criado para cada objeto nos resultados da pesquisa de registo com valores que excedem o limiar especificado.

As diferenças entre os tipos de regra de alerta são as seguintes.

- *O número de* regras de alerta de resultados cria sempre um único alerta, enquanto a regra de alerta de *medição métrica* cria um alerta para cada objeto que exceda o limiar.
- O número de regras de alerta *de resultados* cria um alerta quando o limiar é ultrapassado uma única vez. As regras de alerta de *medição métrica* podem criar um alerta quando o limiar é ultrapassado um determinado número de vezes durante um determinado intervalo de tempo.

### <a name="number-of-results-alert-rules"></a>Número de regras de alerta de resultados

O número de regras de alerta **de resultados** cria um único alerta quando o número de registos devolvidos pela consulta de pesquisa excede o limiar especificado. Este tipo de regra de alerta é ideal para trabalhar com eventos como registos de eventos do Windows, Syslog, WebApp Response e registos personalizados.  Pode querer criar um alerta quando um determinado erro é criado, ou quando vários erros são criados dentro de um determinado período de tempo.

**Limiar**: O limiar para uma série de regras de alerta de resultados é superior ou inferior a um valor específico.  Se o número de registos devolvidos pela pesquisa de registo corresponder a estes critérios, então é criado um alerta.

Para alertar num único evento, detete o número de resultados para um número superior a 0 e verifique a ocorrência de um único evento que foi criado desde a última vez que a consulta foi executada. Algumas aplicações podem registar um erro ocasional que não deve necessariamente levantar um alerta.  Por exemplo, a aplicação pode voltar a tentar o processo que criou o erro e, em seguida, ter sucesso da próxima vez.  Neste caso, pode não querer criar o alerta a menos que vários eventos sejam criados dentro de um determinado período de tempo.  

Em alguns casos, pode querer criar um alerta na ausência de um evento.  Por exemplo, um processo pode registar eventos regulares para indicar que está a funcionar corretamente.  Se não registar um destes eventos num determinado período de tempo, então deve ser criado um alerta.  Neste caso, fixaria o limiar para menos de **1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exemplo de número de registos tipo alerta

Considere um cenário em que deseja saber quando a sua App baseada na Web dá uma resposta aos utilizadores com código 500 (isto é) Erro do Servidor Interno. Criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** pedidos / onde resultadoCódigo == "500"<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Valor limiar:** Maior que 0<br>

Em seguida, o alerta seria executado a cada 5 minutos, com 30 minutos de dados - para procurar qualquer registo onde o código de resultados fosse 500. Se mesmo um desses registos for encontrado, dispara o alerta e despoleta a ação configurada.

### <a name="metric-measurement-alert-rules"></a>Regras de alerta de medição métrica

As regras de alerta de **medição métrica** criam um alerta para cada objeto numa consulta com um valor que excede um limiar especificado e condição especificada do gatilho. Ao contrário do número de regras de alerta **de resultados,** as regras de alerta de **medição métrica** funcionam quando o resultado da análise fornece uma série de tempo. Têm as seguintes diferenças distintas em relação ao número de regras de alerta **de resultados.**

- **Função agregada**: Determina o cálculo que é realizado e potencialmente um campo numérico agregar.  Por exemplo, **a contagem()** devolve o número de registos na consulta, **a avg(CounterValue)** devolve a média do campo CounterValue ao longo do intervalo. A função agregada na consulta deve ser nomeada/chamada: Valor Agregado e fornecer um valor numérico. 

- **Campo de Grupo**: É criado um registo com um valor agregado para cada instância deste campo, podendo ser gerado um alerta para cada um.  Por exemplo, se quisesse gerar um alerta para cada computador, utilizaria **por Computador**. No caso de existirem vários campos de grupo especificados em consulta de alerta, o utilizador pode especificar qual o campo a utilizar para classificar resultados utilizando o parâmetro **Agregado On** (metricColumn)

    > [!NOTE]
    > *A* opção Agregado On (metricColumn) está disponível para alertas de registo do tipo de medição métrica para insights de aplicação e alertas de registo para [Log Analytics configurado apenas usando API agendadaS.](../../azure-monitor/platform/alerts-log-api-switch.md)

- **Intervalo**: Define o intervalo de tempo sobre o qual os dados são agregados.  Por exemplo, se especificasse **cinco minutos,** seria criado um registo para cada instância do campo de grupo agregado em intervalos de 5 minutos durante o período de tempo especificado para o alerta.

    > [!NOTE]
    > A função do caixote do lixo deve ser utilizada em consulta para especificar o intervalo. Como o caixote do lixo pode resultar em intervalos de tempo desiguais - O alerta converterá automaticamente o comando do caixote do lixo para bin_at comando com o tempo de funcionar adequado, para garantir resultados com um ponto fixo. O tipo de alerta de registo de medição métrica foi concebido para funcionar com consultas com até três instâncias de comando de contentores
    
- **Limiar**: O limiar para as regras de alerta de medição métrica é definido por um valor agregado e por uma série de infrações.  Se qualquer ponto de dados na pesquisa de registo exceder este valor, é considerado uma violação.  Se o número de infrações em qualquer objeto nos resultados exceder o valor especificado, então é criado um alerta para esse objeto.

A configuração errada da opção *Agregado On* ou *metricColumn* pode causar regras de alerta a um erro de incêndio. Para obter mais informações, consulte a [resolução de problemas quando a regra](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)do alerta de medição métrica estiver incorreta .

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exemplo de alerta de registo do tipo de medição métrica

Considere um cenário em que quisesse um alerta se algum computador excedesse a utilização do processador de 90% três vezes em 30 minutos.  Criaria uma regra de alerta com os seguintes detalhes:  

- **Consulta:** Perf  Perf  onde o ObjectName == "Processador" e ContraNome == "% Tempo do Processador"  resumir AgregadaValue = avg(CounterValue) por bin (TimeGenerated, 5m), Computer<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Lógica de Alerta - Condição e Limiar:** Mais de 90<br>
- **Campo de Grupo (Agregado):** Computador
- **Alerta de disparo com base em:** Total de infrações Superiores a 2<br>

A consulta criaria um valor médio para cada computador em intervalos de 5 minutos.  Esta consulta seria executada a cada 5 minutos para obter dados recolhidos nos 30 minutos anteriores. Uma vez que o Campo de Grupo (Agregado)escolhido é colunaar 'Computer' - o Valor Agregado é dividido por vários valores de 'Computador' e a utilização média do processador para cada computador é determinada por um intervalo de tempo de 5 minutos.  O resultado da consulta de amostra para (por exemplo) três computadores, seria como abaixo.


|TimeGenerated [UTC] |Computador  |Valor Agregado  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Se o resultado da consulta fosse traçado, parece que.

![Resultados da consulta de amostra](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Neste exemplo, vemos em caixotes de 5 minutos para cada um dos três computadores - utilização média do processador como calculado durante 5 minutos. Limiar de 90 a ser violado por srv01 apenas uma vez às 1:25. Em comparação, o srv02 ultrapassa o limiar de 90 em 1:10, 1:15 e 1:25 caixotes; enquanto o srv03 excede o limiar de 90 às 1:10, 1:15, 1:20 e 1:30.
Uma vez que o alerta está configurado para desencadear com base em violações totais são mais de dois, vemos que srv02 e srv03 apenas cumprem os critérios. Assim, seriam criados alertas separados para srv02 e srv03, uma vez que violaram o limiar de 90% duas vezes através de vários caixotes do tempo.  Se o *alerta de gatilho se basear em:* parâmetro foi configurado para a opção de *infrações contínuas,* então um alerta seria disparado **apenas** para srv03 uma vez que violou o limiar de três caixas de tempo consecutivas de 1:10 a 1:20. E **não** para o Srv02, já que ultrapassou o limiar de dois caixotes do tempo consecutivos de 1:10 a 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Regra de alerta de pesquisa de log - disparo e estado

As regras de alerta de pesquisa de registo funcionam apenas na lógica que se constrói na consulta. O sistema de alerta não tem qualquer outro contexto do estado do sistema, da sua intenção, ou da causa principal implícita na consulta. Como tal, os alertas de registo são referidos como menos estado. As condições são avaliadas como "TRUE" ou "FALSE" sempre que são executadas.  Um alerta será dado sempre que a avaliação do estado de alerta for "VERDADE", independentemente de ser previamente disparada.    

Vamos ver este comportamento em ação com um exemplo prático. Assuma que temos uma regra de alerta de log chamada *Contoso-Log-Alert,* que está configurada como mostrado no [exemplo fornecido para o alerta de registo do tipo Número de Resultados](#example-of-number-of-records-type-log-alert). A condição é uma consulta de alerta personalizada projetada para procurar 500 código de resultados em registos. Se mais 500 códigos de resultados forem encontrados em registos, o estado do alerta é verdadeiro. 

Em cada intervalo abaixo, o sistema de alerta sinuoso do Azure avalia a condição para o *Alerta de Registo de Contoso*.


| Hora    | Num dos registos devolvidos por consulta de pesquisa de registo | Evalorização da condição de registo | Resultado 
| ------- | ----------| ----------| ------- 
| 13H05 | 0 registos | 0 não é > 0 tão FALSO |  O alerta não dispara. Nenhuma ação chamada.
| 13h10 | 2 registos | 2 > 0 tão VERDADEIRO  | Alerta de incêndios e grupos de ação chamados. Estado de alerta ATIVO.
| 13:15 | 5 registos | 5 > 0 tão VERDADEIRO  | Alerta de incêndios e grupos de ação chamados. Estado de alerta ATIVO.
| 13H20 | 0 registos | 0 não é > 0 tão FALSO |  O alerta não dispara. Nenhuma ação chamada. Estado de alerta deixado ATIVO.

Utilizando o caso anterior como exemplo:

Às 13:15, os alertas do Azure não conseguem determinar se as questões subjacentes observadas às 13:10 persistem e se os registos são novas falhas líquidas ou repetições de falhas mais antigas às 13:10. A consulta fornecida pelo utilizador pode ou não estar a ter em conta registos anteriores e o sistema não sabe. O sistema de alerta sintetizador do Azure foi construído para errar por precaução, e volta a disparar o alerta e as ações associadas às 13h15. 

Às 13:20, quando são vistos zero registos com código de resultados de 500, os alertas do Azure não podem ter a certeza de que a causa do código de resultados de 500 pessoas vista às 13:10 e 13:15 está agora resolvida. Não sabe se os 500 erros voltarão a acontecer pelas mesmas razões. Por isso, o *Alerta de Registo de Contoso* não se altera para **resolvido** no painel de alerta azure e/ou notificações não são enviadas indicando que o alerta está resolvido. Só você, que entende a condição exata ou a razão da lógica incorporada na consulta de análise, pode [marcar o alerta conforme](alerts-managing-alert-states.md) necessário.

## <a name="pricing-and-billing-of-log-alerts"></a>Preços e Faturação de Alertas de Registo

Os preços aplicáveis aos Alertas de Registo estão indicados na página de Preços do [Monitor Do Azure.](https://azure.microsoft.com/pricing/details/monitor/) Nas contas azure, os Alertas de Registo são representados como tipo `microsoft.insights/scheduledqueryrules` com:

- Alertas de log em Insights de Aplicação mostrados com nome de alerta exato juntamente com grupo de recursos e propriedades de alerta
- Alertas de log no Log Analytics mostrados com o nome de alerta exato juntamente com propriedades do grupo de recursos e alerta; quando criado usando [a API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

O [legado Log Analytics API](../../azure-monitor/platform/api-alerts.md) tem ações de alerta e horários como parte da Pesquisa Guardada de Log Analytics e não de recursos [próprios do Azure.](../../azure-resource-manager/management/overview.md) Assim, para permitir a faturação de tais alertas de registo sinuosos criados para log Analytics usando o portal Azure **sem** [mudar para novo API](../../azure-monitor/platform/alerts-log-api-switch.md) ou através do legado Log Analytics [API](../../azure-monitor/platform/api-alerts.md) - são criadas regras ocultas de alerta de pseudo-alerta em `microsoft.insights/scheduledqueryrules` para faturação em Azure. As regras ocultas de alerta de pseudo-alerta criadas para faturação em `microsoft.insights/scheduledqueryrules` como mostrado como `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` juntamente com o grupo de recursos e propriedades de alerta.

> [!NOTE]
> Se estiverem presentes personagens inválidos como `<, >, %, &, \, ?, /`, serão substituídos por `_` no nome da regra de pseudo alerta oculto e, portanto, também na conta Azure.

Para remover os horários ocultosOs recursos criados para faturação de regras de alerta utilizando a [API de Log Analytics,](api-alerts.md)o utilizador pode fazer qualquer um dos seguintes recursos:

- Qualquer utilizador pode mudar a preferência da [API pelas regras](../../azure-monitor/platform/alerts-log-api-switch.md) de alerta no espaço de trabalho do Log Analytics e sem perda das suas regras de alerta ou de monitorização para o Funcionamento do Gestor de Recursos Azure, [a API .](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Elimine assim a necessidade de fazer pseudo regras de alerta escondidas para a faturação.
- Ou se o utilizador não quiser mudar a preferência da API, o utilizador terá de **eliminar** a programação original e alertar a ação utilizando o [legacy Log Analytics API](api-alerts.md) ou eliminar no portal [Azure a regra original](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) de alerta de registo

Adicionalmente para os horários ocultosOs recursos criados para faturação de regras de alerta utilizando o [legado Log Analytics API,](api-alerts.md)qualquer operação de modificação como PUT falhará. Como as regras pseudo tipo `microsoft.insights/scheduledqueryrules` são para efeitos de faturação das regras de alerta criadas usando o [legado Log Analytics API](api-alerts.md). Qualquer modificação da regra de alerta deve ser feita utilizando o legado do [Log Analytics API](api-alerts.md) (ou) utilizador pode mudar a [preferência de API para as regras](../../azure-monitor/platform/alerts-log-api-switch.md) de alerta para utilizar a [API agendada.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="next-steps"></a>Passos seguintes

* Saiba sobre [a criação de alertas de registo em Azure.](../../azure-monitor/platform/alerts-log.md)
* Compreenda [os webhooks em alertas](alerts-log-webhook.md)de registo em Azure .
* Saiba mais sobre [os Alertas Azure.](../../azure-monitor/platform/alerts-overview.md)
* Saiba mais sobre [os Insights de Aplicação.](../../azure-monitor/app/analytics.md)
* Saiba mais sobre [o Log Analytics.](../../azure-monitor/log-query/log-query-overview.md)
