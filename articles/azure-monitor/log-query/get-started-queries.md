---
title: Introdução às consultas de registo no Azure Monitor | Documentos da Microsoft
description: Este artigo fornece um tutorial para começar a trabalhar escrever consultas de registo no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: d9c94f7bd1a10e5db084e4541081998dc3de2f05
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749715"
---
# <a name="get-started-with-azure-monitor-log-queries"></a>Introdução às consultas de registo do Azure Monitor


> [!NOTE]
> Deve efetuar [introdução ao Log Analytics do Azure Monitor](get-started-portal.md) antes de concluir este tutorial.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Neste tutorial irá aprender a escrever consultas de registo do Azure LMonitor. Ele irá ensiná-lo como para:

- Compreender a estrutura dos consultas
- Ordenar os resultados de consulta
- Filtrar os resultados da consulta
- Especifique um intervalo de tempo
- Selecionar quais campos serão incluídos nos resultados
- Defina e utilize campos personalizados
- Resultados de agregação e de grupo


## <a name="writing-a-new-query"></a>Escrever uma nova consulta
Consultas podem começar com o nome de uma tabela ou o *pesquisa* comando. Deve começar com um nome de tabela, uma vez que define um âmbito claro para a consulta e melhora o desempenho da consulta e relevância dos resultados.

> [!NOTE]
> A linguagem de consulta de Kusto utilizada pelo Azure Monitor diferencia maiúsculas de minúsculas. Palavras-chave idiomáticas são normalmente escritas em minúsculas. Ao utilizar nomes de tabelas ou colunas numa consulta, certifique-se utilizar as maiúsculas e minúsculas corretas, conforme mostrado no painel de esquema.

### <a name="table-based-queries"></a>Consultas baseadas em tabela
Monitor do Azure organiza os dados de registo em tabelas, cada uma consistindo em várias colunas. Todas as tabelas e colunas são apresentadas no painel de esquema no Log Analytics no portal do Analytics. Identificar uma tabela que tem interesse e, em seguida, dê uma olhada num pouco de dados:

```Kusto
SecurityEvent
| take 10
```

A consulta mostrada acima devolve 10 resultados a partir da *SecurityEvent* tabela, sem nenhuma ordem específica. Essa é uma maneira muito comum usar rapidamente numa tabela e compreender a sua estrutura e conteúdo. Vamos examinar como é criada:

* A consulta começa com o nome da tabela *SecurityEvent* -esta parte define o âmbito da consulta.
* O caráter de pipe (|) separa comandos, por isso, o resultado do primeiro na entrada do comando seguinte. Pode adicionar qualquer número de elementos de pipe.
* Seguir o pipe é o **tirar** comando, que devolve um número específico de arbitrários registos da tabela.

Na verdade, executar a consulta, mesmo sem adicionar `| take 10` - ainda seria válido, mas ele poderia retornar resultados até 10 000.

### <a name="search-queries"></a>Consultas de pesquisa
Consultas de pesquisa são menos estruturado e, em geral mais adequado para localizar os registos, que incluem um valor específico em qualquer uma das respetivas colunas:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Essa consulta pesquisa o *SecurityEvent* tabela para registos que contêm a frase "Criptográficos utilizados". Esses registos, 10 registros serão retornados e apresentados. Se podemos omitir os `in (SecurityEvent)` parte e apenas execute `search "Cryptographic"`, a pesquisa abordará *todos os* tabelas, que poderiam demorar mais tempo e ser menos eficiente.

> [!NOTE]
> Por predefinição, um intervalo de tempo de _últimas 24 horas_ está definido. Para utilizar um intervalo diferente, utilize o Seletor de hora (junto à *ir* botão) ou adicionar um período de tempo explícito filtro de intervalo para a sua consulta.

## <a name="sort-and-top"></a>Ordenar e a parte superior
Embora **tirar** é útil para obter alguns registos, os resultados são selecionados e exibidos sem nenhuma ordem específica. Para obter uma vista ordenada, poderia **ordenação** pela coluna preferencial:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Isso poderia retornar demasiados resultados no entanto e também poderá demorar algum tempo. A consulta acima ordena *toda a* SecurityEvent tabela pela coluna TimeGenerated. O portal da análise, em seguida, limita a exibição para mostrar apenas 10 000 registos. Claro que essa abordagem não é ideal.

A melhor forma de obter apenas os registos de 10 mais recente é usar **superior**, que ordena a tabela inteira no lado do servidor e, em seguida, devolve os primeiros registos:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Descendente é a predefinição de ordenação, para que, normalmente, omitimos a **desc** argumento. A saída terá o seguinte aspeto:

![Top 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Onde: filtragem numa condição
Filtros, conforme indicado pelo respetivo nome, filtrar os dados por uma condição específica. Esta é a forma mais comum para limitar os resultados da consulta para informações relevantes.

Para adicionar um filtro a uma consulta, utilize o **onde** operador seguido por uma ou mais condições. Por exemplo, a consulta seguinte devolve apenas *SecurityEvent* registos em que _nível_ é igual a _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Ao escrever as condições de filtro, pode usar as expressões seguintes:

| Expressão | Descrição | Exemplo |
|:---|:---|:---|
| == | Verificação de igualdade<br>(diferencia maiúsculas de minúsculas) | `Level == 8` |
| =~ | Verificação de igualdade<br>(maiúsculas de minúsculas) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Verificação de desigualdade<br>(ambas as expressões são idênticas) | `Level != 4` |
| *and*, *or* | Necessário entre as condições| `Level == 16 or CommandLine != ""` |

Para filtrar por várias condições, pode utilizar **e**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

ou encaminhar várias **onde** elementos um após o outro:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Valores podem ter diferentes tipos, pelo que poderá precisar para convertê-los para executar a comparação do tipo correto. Por exemplo, SecurityEvent *nível* coluna é do tipo cadeia de caracteres, pelo que tem de o transmitir para um tipo numérico, como *int* ou *longo*, antes de poder utilizar os operadores numéricos no mesmo: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Especifique um intervalo de tempo

### <a name="time-picker"></a>Selecionador de hora
O selecionador de hora é junto ao botão de execução e indica que estiver a consultar apenas os registos das últimas 24 horas. Este é o intervalo de tempo predefinido aplicado a todas as consultas. Para obter apenas os registos de última hora, selecione _última hora_ e execute a consulta novamente.

![Selecionador de hora](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtro de tempo na consulta
Também pode definir seu próprio intervalo de tempo ao adicionar um filtro de tempo para a consulta. É melhor colocar o filtro de tempo imediatamente após o nome da tabela: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

No filtro de hora acima `ago(30m)` significa "há 30 minutos", para que esta consulta devolve apenas registos dos últimos 30 minutos. Dias de incluir outras unidades de tempo (2d), minutos (25m) e segundos (10).


## <a name="project-and-extend-select-and-compute-columns"></a>Projeto e expandir: selecione e colunas de computação
Uso **projeto** para selecionar colunas específicas para incluir nos resultados:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

O exemplo anterior gera esta saída:

![Resultados do projeto de consulta](media/get-started-queries/project.png)

Também pode utilizar **projeto** para mudar o nome de colunas e definir novos. O exemplo seguinte utiliza o projeto para fazer o seguinte:

* Selecione apenas a *computador* e *TimeGenerated* colunas originais.
* Mudar o nome da *atividade* coluna *EventDetails*.
* Criar uma nova coluna chamada *EventCode*. O **substring()** função é usada para obter apenas os primeiros quatro carateres do campo de atividade.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

**expandir** mantém todas as colunas originais no conjunto de resultados e define as outras opções. A seguinte consulta utiliza **expandir** para adicionar um *localtime* coluna, que contém um valor de TimeGenerated localizado.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend localtime = TimeGenerated-8h
```

## <a name="summarize-aggregate-groups-of-rows"></a>Resumindo: agregar a grupos de linhas
Uso **resumir** para identificar os grupos de registos, de acordo com uma ou mais colunas e aplicar-lhes agregações. O uso mais comum de **resumir** é *contagem*, que retorna o número de resultados em cada grupo.

A seguinte consulta analisa todos os *Perf* registos da última hora, agrupa por *ObjectName*e conta os registos em cada grupo: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Por vezes, faz sentido definir grupos por várias dimensões. Cada combinação exclusiva destes valores define um grupo separado:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Outro uso comum é executar cálculos matemáticos ou estatísticos em cada grupo. Por exemplo, o seguinte calcula a média *CounterValue* para cada computador:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Infelizmente, os resultados desta consulta são sem sentido, uma vez que estamos juntos misto diferentes contadores de desempenho. Para tornar mais significativo, devemos calcular a média em separado para cada combinação desses *CounterName* e *computador*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Resumir por uma coluna de tempo
Agrupar os resultados também pode ser baseado numa coluna de tempo ou outro valor contínuo. Simplesmente resumir `by TimeGenerated` porém seria criar grupos para cada milissegundo único ao longo do intervalo de tempo, uma vez que estes são valores exclusivos. 

Para criar grupos com base nos valores contínuas, é melhor dividir o intervalo em unidades geríveis usando **bin**. A seguinte consulta analisa *Perf* registos que medem a memória livre (*MBytes disponíveis*) num computador específico. Ele calcula o valor médio para cada período se 1 hora, nos últimos 7 dias:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Para fazer com que a saída mais clara, selecione para exibi-lo como um gráfico de tempo, que mostra a memória disponível ao longo do tempo:

![Memória de consulta ao longo do tempo](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [escrever consultas de pesquisa](search-queries.md)
