---
title: Começar com consultas de log no Monitor Azure / Microsoft Docs
description: Este artigo fornece um tutorial para começar a escrever consultas de registo no Azure Monitor.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: f56abe2bf6ccea1f55f9b3fe94b75016d449b46b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670184"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Começar com consultas de log no Monitor Azure

> [!NOTE]
> Pode trabalhar através deste exercício no seu próprio ambiente se estiver a recolher dados de pelo menos uma máquina virtual. Se não utilizar o nosso [ambiente de demonstração,](https://portal.loganalytics.io/demo)que inclui muitos dados da amostra.

Neste tutorial você aprenderá a escrever consultas de log no Monitor Azure. Vai ensinar-te a:

- Compreender a estrutura da consulta
- Ordenar resultados de consulta
- Resultados da consulta de filtro
- Especificar um intervalo de tempo
- Selecione quais os campos a incluir nos resultados
- Defina e use campos personalizados
- Resultados agregados e de grupo

Para um tutorial sobre a utilização do Log Analytics no portal Azure, consulte [Iniciar-se com o Azure Monitor Log Analytics](get-started-portal.md).<br>
Para mais detalhes sobre consultas de log no Monitor Azure, consulte a [visão geral das consultas de registo no Monitor Azure](log-query-overview.md).

Acompanhe com uma versão em vídeo deste tutorial abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Escrever uma nova consulta
As consultas podem começar com um nome de mesa ou com o comando de *pesquisa.* Você deve começar com um nome de mesa, uma vez que define uma margem clara para a consulta e melhora tanto o desempenho da consulta como a relevância dos resultados.

> [!NOTE]
> A linguagem de consulta Kusto usada pelo Monitor Azure é sensível a casos. As palavras-chave linguísticas são tipicamente escritas em maiúsculas. Quando utilizar nomes de tabelas ou colunas numa consulta, certifique-se de que utiliza a caixa correta, como mostra o painel de esquemas.

### <a name="table-based-queries"></a>Consultas à base de mesa
O Azure Monitor organiza dados de registo em tabelas, cada uma composta por múltiplas colunas. Todas as tabelas e colunas são mostradas no painel de esquemas no Log Analytics no portal Analytics. Identifique uma tabela que lhe interesse e, em seguida, dê uma olhada em alguns dados:

```Kusto
SecurityEvent
| take 10
```

A consulta acima mostrada devolve 10 resultados da tabela *SecurityEvent,* em nenhuma ordem específica. Esta é uma forma muito comum de dar uma olhada numa mesa e entender a sua estrutura e conteúdo. Vamos examinar como é construído:

* A consulta começa com o nome de mesa *SecurityEvent* - esta parte define o âmbito da consulta.
* O tubo (/) caracteres separa os comandos, de modo que a saída do primeiro na entrada do comando seguinte. Pode adicionar qualquer número de elementos canalizados.
* Seguindo o tubo está o comando **take,** que devolve um número específico de registos arbitrários da mesa.

Poderíamos, de facto, fazer a consulta mesmo sem adicionar `| take 10` - que ainda seria válido, mas poderia devolver até 10.000 resultados.

### <a name="search-queries"></a>Consultas de pesquisa
As consultas de pesquisa são menos estruturadas e geralmente mais adequadas para encontrar registos que incluam um valor específico em qualquer uma das suas colunas:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Esta consulta procura na tabela *SecurityEvent* por registos que contenham a frase "Criptografia". Desses registos, 10 registos serão devolvidos e exibidos. Se omitirmos a parte `in (SecurityEvent)` e apenas corrermos `search "Cryptographic"`, a procura passará por *todas as* mesas, o que demoraria mais tempo e seria menos eficiente.

> [!WARNING]
> As consultas de pesquisa são tipicamente mais lentas do que consultas baseadas em mesaporque têm de processar mais dados. 

## <a name="sort-and-top"></a>Ordenar e completar
Embora **a tomada** seja útil para obter alguns registos, os resultados são selecionados e exibidos em nenhuma ordem particular. Para obter uma visão ordenada, você poderia **ordenar** pela coluna preferida:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Isso pode, no entanto, dar demasiados resultados e também levar algum tempo. A consulta acima classifica *toda a* tabela SecurityEvent pela coluna TimeGenerated. O portal Analytics limita então o ecrã para mostrar apenas 10.000 registos. Esta abordagem não é, naturalmente, a ideal.

A melhor maneira de obter apenas os últimos 10 registos é usar o **topo**, que classifica toda a tabela do lado do servidor e depois devolve os registos superiores:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Descendo é a ordem de triagem padrão, então nós tipicamente omitimos o argumento **de desc.** A saída será assim:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Onde: filtragem em uma condição
Os filtros, conforme indicado pelo seu nome, filtram os dados por uma condição específica. Esta é a forma mais comum de limitar os resultados da consulta a informações relevantes.

Para adicionar um filtro a uma consulta, utilize o **local onde** o operador é seguido por uma ou mais condições. Por exemplo, a seguinte consulta devolve apenas registos *securityEvent* onde _o Nível_ de Nível é igual a _8:_

```Kusto
SecurityEvent
| where Level == 8
```

Ao escrever as condições do filtro, pode utilizar as seguintes expressões:

| Expressão | Descrição | Exemplo |
|:---|:---|:---|
| == | Verificar igualdade<br>(sensível a casos) | `Level == 8` |
| =~ | Verificar igualdade<br>(insensíveis aos casos) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Verificar a desigualdade<br>(ambas as expressões são idênticas) | `Level != 4` |
| *e,* *ou,* | Obrigatório entre condições| `Level == 16 or CommandLine != ""` |

Para filtrar por múltiplas condições, pode utilizar **e:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

ou tubo múltiplo **quando** elementos um após o outro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Os valores podem ter diferentes tipos, por isso pode ser necessário lançá-los para realizar a comparação no tipo correto. Por exemplo, a coluna *SecurityEvent Level* é do tipo String, por isso deve lançá-la para um tipo numérico, como *int* ou *longo*, antes de poder utilizar operadores numéricos: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Especificar um intervalo de tempo

### <a name="time-picker"></a>Selecionador de tempo
O selecionador está ao lado do botão Executar e indica que estamos a consultar apenas registos das últimas 24 horas. Este é o intervalo de tempo padrão aplicado a todas as consultas. Para obter apenas registos da última hora, selecione _Last hour_ e faça a consulta novamente.

![Selecionador de hora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro de tempo na consulta
Também pode definir o seu próprio intervalo de tempo adicionando um filtro de tempo à consulta. É melhor colocar o filtro de tempo imediatamente após o nome da tabela: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

No filtro de tempo acima `ago(30m)` significa "há 30 minutos" por isso esta consulta só devolve registos dos últimos 30 minutos. Outras unidades de tempo incluem dias (2d), minutos (25m) e segundos (10s).


## <a name="project-and-extend-select-and-compute-columns"></a>Projeto e Extensão: selecione e computacolunas
Utilize **o projeto** para selecionar colunas específicas para incluir nos resultados:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

O exemplo anterior gera esta saída:

![Resultados do projeto de consulta](media/get-started-queries/project.png)

Também pode usar **o projeto** para mudar o nome das colunas e definir novas. O exemplo seguinte utiliza o projeto para fazer o seguinte:

* Selecione apenas as colunas originais *Computer* e *TimeGenerated.*
* Mude o nome da coluna *Atividade* para *EventDetails*.
* Crie uma nova coluna chamada *EventCode*. A função **substring()** é usada para obter apenas os primeiros quatro caracteres do campo Atividade.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**extensão** mantém todas as colunas originais no conjunto de resultados e define outras adicionais. As seguintes utilizações de consulta **estendem-se** para adicionar a coluna *EventCode.* Note que esta coluna pode não apresentar no final da tabela resultados, caso em que precisaria de expandir os detalhes de um registo para vê-lo.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Resumo: grupos agregados de linhas
Utilize resumos para identificar grupos de registos, de acordo com uma ou mais colunas, e aplique agregações neles. O uso mais comum de **resumo** é a *contagem*, que devolve o número de resultados em cada grupo.

A seguinte consulta analisa todos os registos *perf* da última hora, agrupa-os pelo *ObjectName*, e conta os registos em cada grupo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Às vezes faz sentido definir grupos por múltiplas dimensões. Cada combinação única destes valores define um grupo separado:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Outra utilização comum é realizar cálculos matemáticos ou estatísticos em cada grupo. Por exemplo, o seguinte calcula o *ContraValor* médio para cada computador:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Infelizmente, os resultados desta consulta não têm sentido, uma vez que juntámos diferentes contadores de desempenho. Para tornar isto mais significativo, devemos calcular a média separadamente para cada combinação de *Contranome* e *Computador:*

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Resumo por uma coluna do tempo
Os resultados de agrupamento também podem basear-se numa coluna de tempo, ou noutro valor contínuo. Resumindo `by TimeGenerated` criaria grupos para cada milésimo de segundo ao longo do intervalo de tempo, uma vez que estes são valores únicos. 

Para criar grupos baseados em valores contínuos, o melhor é quebrar o alcance em unidades manejáveis usando **o caixote**do lixo . A consulta seguinte analisa os registos *perf* que medem a memória gratuita *(MBytes disponíveis*) num computador específico. Calcula o valor médio de cada período de 1 hora nos últimos 7 dias:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Para tornar a saída mais clara, selecione-a como um gráfico de tempo, mostrando a memória disponível ao longo do tempo:

![Memória de consulta ao longo do tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a utilização de dados de cordas numa consulta de log com cordas em consultas de [registo do Monitor Azure](string-operations.md).
- Saiba mais sobre aagregação de dados numa consulta de log com [agregações avançadas em consultas](advanced-aggregations.md)de registo do Monitor Azure .
- Saiba como juntar dados de [várias tabelas](joins.md)com Joins em consultas de registo do Monitor Azure .
- Obtenha documentação sobre toda a linguagem de consulta kusto na [referência linguística KQL](/azure/kusto/query/).
