---
title: Escrever consultas do Azure Data Explorer
description: Neste como-fazer, você aprende a realizar consultas básicas e mais avançadas para o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881188"
---
# <a name="write-queries-for-azure-data-explorer"></a>Escrever consultas do Azure Data Explorer

Neste artigo, aprende-se a usar a linguagem de consulta no Azure Data Explorer para realizar consultas básicas com os operadores mais comuns. Você também obtém exposição a algumas das características mais avançadas da linguagem.

## <a name="prerequisites"></a>Pré-requisitos

Você pode executar as consultas neste artigo de uma de duas maneiras:

- No Azure Data Explorer ajuda o *cluster* que estabelecemos para ajudar a aprendizagem.
    [Inscreva-se no cluster](https://dataexplorer.azure.com/clusters/help/databases/samples) com uma conta de e-mail organizacional que é membro do diretório Azure Ative.

- No seu próprio cluster que inclui os dados da amostra StormEvents. Para mais informações, consulte [Quickstart: Crie um cluster e base de dados do Azure Data Explorer e](create-cluster-database-portal.md) [os dados da amostra ingest no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Visão geral da linguagem de consulta

Uma linguagem de consulta no Azure Data Explorer é um pedido de leitura apenas para processar dados e devolver resultados. O pedido é indicado em texto simples, utilizando um modelo de fluxo de dados concebido para facilitar a leitura, autor e automatização da sintaxe. A consulta utiliza entidades de esquemas que são organizadas numa hierarquia semelhante à SQL: bases de dados, tabelas e colunas.

A consulta consiste numa sequência de declarações de consulta,`;`delimitadas por um ponto evícito , sendo pelo menos uma declaração uma declaração de expressão tabular, que é uma declaração que produz dados dispostos numa malha semelhante a uma mesa de colunas e linhas. As declarações de expressão tabular da consulta produzem os resultados da consulta.

A sintaxe da declaração de expressão tabular tem fluxo de dados tabular de um operador de consulta tabular para outro, começando pela fonte de dados (por exemplo, uma tabela numa`|`base de dados, ou um operador que produz dados) e, em seguida, fluiatravés de um conjunto de operadores de transformação de dados que estão ligados através da utilização do delimitador do tubo.

Por exemplo, a seguinte consulta tem uma única declaração, que é uma declaração de expressão tabular. A declaração começa com uma `StormEvents` referência a uma tabela chamada (a base de dados que acolhe esta tabela está implícita aqui, e parte da informação de ligação). Os dados (linhas) dessa tabela são depois filtrados `StartTime` pelo valor da coluna e `State` depois filtrados pelo valor da coluna. A consulta devolve então a contagem de linhas "sobreviventes".

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Neste caso, o resultado é:

|Contagem|
|-----|
|   23|
| |

Para mais informações consulte a [referência linguística da Consulta.](https://aka.ms/kustolangref)

## <a name="most-common-operators"></a>Operadores mais comuns

Os operadores abrangidos por esta secção são os blocos de construção para compreender as consultas no Azure Data Explorer. A maioria das consultas que escreve incluirá vários destes operadores.

Para executar consultas no cluster de ajuda: selecione **Clique para executar consulta** acima de cada consulta.

Para fazer consultas no seu próprio cluster:

1. Copie cada consulta na aplicação de consulta baseada na web e, em seguida, selecione a consulta ou coloque o cursor na consulta.

1. No topo da aplicação, selecione **Executar**.

### <a name="count"></a>count

[**contagem**](https://docs.microsoft.com/azure/kusto/query/countoperator): Devolve a contagem das linhas na tabela.

A seguinte consulta devolve a contagem de linhas na tabela StormEvents.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>tomar

[**tomar**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Devoluções até ao número especificado de linhas de dados.

A seguinte consulta retorna a cinco linhas da tabela StormEvents. O *limite* da palavra-chave é um pseudónimo para *tomar.*

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Não há garantias de que registos sejam devolvidos a menos que os dados de origem estejam classificados.

### <a name="project"></a>project

[**projeto**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Seleciona um subconjunto de colunas.

A consulta seguinte devolve um conjunto específico de colunas.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>em que

[**onde**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtra risa uma tabela para o subconjunto de linhas que satisfaçam um predicado.

A consulta seguinte filtra os `EventType` `State`dados e .

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>ordenar

[**classificação**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Ordenar as linhas da tabela de entrada em ordem por uma ou mais colunas.

A seguinte consulta classifica os dados `DamageProperty`em ordem descendente por .

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> A ordem de operações é importante. Tente `take 5` colocar `sort by`antes. Obtém resultados diferentes?

### <a name="top"></a>superior

[**superior:**](https://docs.microsoft.com/azure/kusto/query/topoperator)Devolve os primeiros registos *N* classificados pelas colunas especificadas.

A consulta seguinte devolve os mesmos resultados acima com um operador a menos.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>estender

[**extensão**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Colunas derivadas de computação.

A seguinte consulta cria uma nova coluna computando um valor em cada linha.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Expressões podem incluir todos os operadores habituais (+, *, *, /, %), e há uma gama de funções úteis que pode chamar.

### <a name="summarize"></a>resumo

[**resumo**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agrega grupos de linhas.

A seguinte consulta devolve a `State`contagem dos acontecimentos por .

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Os **summarize** grupos de operador resumem linhas que têm os mesmos valores na cláusula **por** cláusula, e depois utilizam a função de agregação (como **contagem)** para combinar cada grupo numa única linha. Então, neste caso, há uma linha para cada estado, e uma coluna para a contagem de linhas nesse estado.

Há uma gama de funções de agregação, e você pode usar várias delas em um **operador de resumo** para produzir várias colunas computorizadas. Por exemplo, você poderia obter a contagem de tempestades em cada estado e o número único de tempestades por estado, em seguida, usar **top** para obter os estados mais afetados pela tempestade.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

O resultado de uma operação **de resumo** tem:

- Cada coluna nomeada **por**

- Uma coluna para cada expressão computada

- Uma linha para cada combinação de valores

### <a name="render"></a>render

[**render:**](https://docs.microsoft.com/azure/kusto/query/renderoperator)Renderização resulta como uma saída gráfica.

A seguinte consulta exibe um gráfico de coluna.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

A seguinte consulta apresenta um simples gráfico de tempo.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

A seguinte consulta conta eventos na altura em que o modulo um dia, preso em horas, e exibe um gráfico de tempo.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

A consulta seguinte compara várias séries diárias num gráfico de tempo.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> O operador de **renderização** é uma funcionalidade do lado do cliente e não parte do motor. Está integrado na linguagem para facilitar o uso. A aplicação web suporta as seguintes opções: barchart, columnchart, piechart, timechart e linechart. 

## <a name="scalar-operators"></a>Operadores escalar

Esta secção abrange alguns dos operadores mais importantes.

### <a name="bin"></a>caixotes do lixo

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): As rondas descem para um múltiplo inteiro de um dado tamanho do caixote do lixo.

A seguinte consulta calcula a contagem com um balde tamanho de um dia.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>caso()

[**caso()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Avalia uma lista de predicados e devolve a expressão do primeiro resultado cujo predicado está satisfeito, ou a expressão final do **outro.** Pode utilizar este operador para categorizar ou classificar dados de grupo:

A seguinte consulta devolve `deaths_bucket` uma nova coluna e agrupa as mortes por número.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extrato()

[**extrato()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Obtém uma correspondência para uma expressão regular a partir de uma cadeia de texto.

A consulta seguinte extrai valores específicos de atributo saque.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Esta consulta utiliza uma declaração de **let,** que `MyData`liga um nome (neste caso) a uma expressão. Para o resto do âmbito, no qual a declaração de **leto** aparece (âmbito global ou em um âmbito de função do corpo), o nome pode ser usado para se referir ao seu valor vinculado.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpreta uma corda como um valor JSON e devolve o valor como dinâmico. É superior ao uso da função **extractjson()** quando é necessário extrair mais de um elemento de um objeto Composto JSON.

A seguinte consulta extrai os elementos JSON de uma matriz.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

A consulta seguinte extrai os elementos JSON.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

A consulta seguinte extrai os elementos JSON com um tipo de dados dinâmico.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>atrás()

[**atrás()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Subtrai a hora do tempo do relógio atual UTC.

A consulta seguinte devolve os dados das últimas 12 horas.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>início da semana()

[**início da semana::**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction)Devoluções do início da semana que contém a data, deslocados por uma compensação, se fornecido

A consulta seguinte devolve o início da semana com diferentes compensações.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Esta consulta utiliza o operador de **gama,** que gera uma tabela de valores de uma coluna única. Ver também: início [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction)da [**semana()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction)início da semana, [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction)início do [**mês()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)( [**início**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction)do [**dia), fim**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction) [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction) [**de semana()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction)

### <a name="between"></a>entre()

[**entre()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Corresponde à entrada que se encontra dentro do intervalo inclusivo.

A consulta seguinte filtra os dados por uma determinada gama de datas.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

A consulta seguinte filtra os dados por uma determinada gama de`3d`datas, com uma ligeira variação de três dias () a partir da data de início.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Operadores tabular

Kusto tem muitos operadores tabular, alguns dos quais estão cobertos por outras secções deste artigo. Aqui vamos focar-nos na **análise.** 

### <a name="parse"></a>parse

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Avalia uma expressão de cadeia e analisa o seu valor numa ou mais colunas calculadas. Há três formas de analisar: simples (o padrão), regex e relaxado.

A consulta que se segue analisa um traço e extrai os valores relevantes, utilizando um padrão de simples análise. A expressão (referida como StringConstant) é um valor de corda regular e a correspondência é rigorosa: colunas estendidas devem corresponder aos tipos exigidos.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

A consulta que se segue analisa um traço `kind = regex`e extrai os valores relevantes, utilizando . O StringConstant pode ser uma expressão regular.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

A consulta que se segue analisa um traço `kind = relaxed`e extrai os valores relevantes, utilizando . O StringConstant é um valor de cadeia regular e a partida é descontraída: colunas estendidas podem coincidir parcialmente com os tipos necessários.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Análise de série de tempo

### <a name="make-series"></a>séries de faz-séries

[**séries de make-**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator)agregados grupos de linhas como [resumo,](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)mas gera um vetor de série (time) por cada combinação de valores.

A seguinte consulta devolve um conjunto de séries temporais para a contagem de eventos de tempestade por dia. A consulta abrange um período de três meses para cada estado, enchendo os caixotes em falta com o constante 0:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Uma vez criado um conjunto de séries (time), pode aplicar funções de série para detetar formas anómalas, padrões sazonais e muito mais.

A seguinte consulta extrai os três estados que tiveram mais eventos em dias específicos:

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Para mais informações, reveja a lista completa das [funções](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)da série .

## <a name="advanced-aggregations"></a>Agregações avançadas

Cobrimos agregações básicas, como **contar** e **resumir,** no início deste artigo. Esta secção introduz opções mais avançadas.

### <a name="top-nested"></a>topo aninhada

[**topo aninhada**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Produz resultados hierárquicos superiores, onde cada nível é uma broca baseada em valores de nível anteriores.

Este operador é útil para cenários de visualização do dashboard, ou quando é necessário responder a uma pergunta como a seguinte: "Encontre os valores topo-N de K1 (usando alguma agregação); para cada um deles, descubra quais são os valores topo-M de K2 (usando outra agregação); ..."

A seguinte consulta devolve uma tabela `State` hierárquica `Sources`com ao nível superior, seguida por .

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() plugin

[**ficha() plugin:**](https://docs.microsoft.com/azure/kusto/query/pivotplugin)Roda uma tabela transformando os valores únicos de uma coluna na tabela de entrada em várias colunas na tabela de saída. O operador realiza agregações sempre que sejam exigidos sobre os valores restantes da coluna na saída final.

A seguinte consulta aplica um filtro e gira as linhas em colunas.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>contagem decrescentes()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Devolve uma estimativa do número de valores distintos de expressão no grupo. Utilizar [**a contagem()**](https://docs.microsoft.com/azure/kusto/query/countoperator) para contar todos os valores.

A seguinte consulta `Source` conta `State`distinta por .

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Devolve uma estimativa do número de valores distintos da expressão para as linhas para as quais o predicado avalia verdadeiramente.

A seguinte consulta conta os `Source` `DamageProperty < 5000`valores distintos de onde.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Calcula a **contagem** dos resultados do HyperLogLog (gerado por [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) ou [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

A seguinte consulta usa o algoritmo HLL para gerar a contagem.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Encontra uma linha no grupo que maximiza uma expressão, e devolve o valor de outra expressão (ou * para devolver toda a linha).

A seguinte consulta devolve a hora do último relatório de inundação em cada estado.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Devolve uma gama dinâmica (JSON) do conjunto de valores distintos que uma expressão leva no grupo.

A seguinte consulta retorna todas as vezes quando uma inundação foi relatada por cada estado e cria uma matriz a partir do conjunto de valores distintos.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expandir

[**mv-expande**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Expande a coleção de vários valores a partir de uma coluna de tipo dinâmico para que cada valor da coleção obtenha uma linha separada. Todas as outras colunas numa fila expandida são duplicadas. É o oposto da lista.

A seguinte consulta gera dados de amostra, criando um conjunto e, em seguida, usando-os para demonstrar as capacidades **de expansão de mv.**

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Devolve uma estimativa para o percentil de [**nível mais próximo**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) especificado da população definida por uma expressão. A precisão depende da densidade populacional na região do percentil. Só pode ser utilizado no contexto da agregação no interior da [**sumução.**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)

A seguinte consulta calcula percentiles para a duração da tempestade.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

A consulta seguinte calcula percentiles para a duração da tempestade por estado`5m`e normaliza os dados em caixotes de cinco minutos ( ).

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Conjunto de Dados Cruzados

Esta secção abrange elementos que lhe permitem criar consultas mais complexas, juntar dados através das tabelas e consultar através de bases de dados e clusters.

### <a name="let"></a>deixe

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Melhora a modularidade e a reutilização. A declaração **de letização** permite-lhe quebrar uma expressão potencialmente complexa em várias partes, cada uma ligada a um nome, e compor essas partes em conjunto. Uma declaração de **let** também pode ser usada para criar funções e pontos de vista definidos pelo utilizador (expressões sobre tabelas cujos resultados parecem uma nova tabela). As expressões ligadas por uma declaração de **leto** podem ser de tipo escalar, de tipo tabular, ou função definida pelo utilizador (lambdas).

O exemplo seguinte cria uma variável tabular e usa-a numa expressão subsequente.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>aderir

[**aderirão:**](https://docs.microsoft.com/azure/kusto/query/joinoperator)Fundir as linhas de duas tabelas para formar um novo quadro, combinando valores das colunas especificadas de cada tabela. Kusto suporta uma gama completa de tipos de união: **fullouter,** **interior,** **innerunique**, **leftanti, leftantisemi,** **leftouter,** **leftanti** **leftsemi,** **rightanti, rightantisemi,** **rightouter,** **rightsemi**. **rightantisemi**

O exemplo seguinte junta duas mesas com uma união interior.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Utilize os **operadores** **onde** e projetar para reduzir o número de linhas e colunas nas tabelas de entrada, antes da adesão. Se uma mesa for sempre mais pequena do que a outra, use-a como o lado esquerdo (canalizado) da união. As colunas para a partida de adesão devem ter o mesmo nome. Utilize o operador do **projeto,** se necessário, para renomear uma coluna numa das tabelas.

### <a name="serialize"></a>serializar

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serialize o conjunto de linhas para que possa utilizar funções que exijam dados serializados, como **row_number()**.

A consulta seguinte é bem sucedida porque os dados são serializados.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

O conjunto de linhas também é considerado como serializado se for resultado de: **classificar,** **topo,** ou operadores de **gama,** opcionalmente seguido por **projeto,** **project-away,** **estender,** **onde,** **parse,** **mv-expand,** ou **levar** operadores.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Consultas de base de dados cruzadas e de clusters

Consulta de [base de dados cruzada e cross-cluster:](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries)Pode consultar uma `database("MyDatabase").MyTable`base de dados sobre o mesmo cluster, remetendo-a como . Pode consultar uma base de dados num cluster `cluster("MyCluster").database("MyDatabase").MyTable`remoto, referindo-se a ela como .

A seguinte consulta é chamada a partir `MyCluster` de um cluster e consulta dados do cluster. Para espaçar esta consulta, use o seu próprio nome de cluster e nome de base de dados.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Análise do Utilizador

Esta secção inclui elementos e consultas que demonstram como é fácil realizar análises dos comportamentos dos utilizadores em Kusto.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

[**activity_counts_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Calcula métricas de atividade úteis (valores de contagem total, valores de contagem distintos, contagem distinta de novos valores e contagem distinta agregada). As métricas são calculadas para cada janela de tempo, então são comparadas, e agregadas a e com todas as janelas de tempo anteriores.

A seguinte consulta analisa a adoção do utilizador calculando as contagens diárias de atividade.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>activity_engagement plugin

[**activity_engagement plugin**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Calcula a relação de envolvimento da atividade com base na coluna de ID sobre uma janela de linha do tempo deslizante. **activity_engagement plugin** pode ser usado para calcular DAU, WAU e MAU (utilizadores ativos diários, semanais e mensais).

A consulta seguinte devolve a relação de utilizadores totalmente distintos usando uma aplicação diariamente em comparação com o total de utilizadores distintos que usam a aplicação semanalmente, numa janela móvel de sete dias.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Ao calcular a DAU/MAU, altere os dados finais e o período da janela em movimento (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics plugin

[**plugin activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Calcula métricas de atividade úteis (valores de contagem distintos, contagem distinta de novos valores, taxa de retenção e taxa de churn) com base na janela do período atual vs. janela do período anterior.

A seguinte consulta calcula a taxa de retenção e o retenção para um determinado conjunto de dados.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>plugin new_activity_metrics

[**new_activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Calcula métricas de atividade úteis (valores de contagem distintos, contagem distinta de novos valores, taxa de retenção e taxa de churn) para a coorte de novos utilizadores. O conceito deste plugin é semelhante ao [**plugin activity_metrics,**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)mas foca-se em novos utilizadores.

A consulta seguinte calcula uma taxa de retenção e agitação com uma janela semanal para o coorte de novos utilizadores (utilizadores que chegaram na primeira semana).

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>session_count plugin

[**session_count plugin**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Calcula a contagem de sessões com base na coluna id numa linha do tempo.

A seguinte consulta devolve a contagem de sessões. Uma sessão é considerada ativa se um ID do utilizador aparecer pelo menos uma vez num prazo de 100 slots, enquanto a janela de retrospetiva da sessão é de 41 slots.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

[**funnel_sequence plugin**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Calcula a contagem distinta de utilizadores que tomaram uma sequência de estados; mostra a distribuição de estados anteriores e próximos que conduziram ou foram seguidos pela sequência.

A seguinte consulta mostra o que acontece antes e depois de todos os eventos tornados em 2007.

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>plugin funnel_sequence_completion

[**funnel_sequence_completion plugin**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Calcula o funil dos passos de sequência concluídos em diferentes períodos de tempo.

A seguinte consulta verifica o funil `Hail -> Tornado -> Thunderstorm -> Wind` de conclusão da sequência: em tempos`[1h, 4h, 1d]`"globais" de uma hora, quatro horas e um dia ().

**\[**[**Clique para executar consulta**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funções

Esta secção cobre [**funções**](https://docs.microsoft.com/azure/kusto/query/functions): consultas reutilizáveis que são armazenadas no servidor. As funções podem ser invocadas por consultas e outras funções (funções recursivas não são suportadas).

> [!NOTE]
> Não é possível criar funções no cluster de ajuda, que é apenas leitura. Use o seu próprio cluster de teste para esta parte.

O exemplo seguinte cria uma função`MyState`que toma um nome de estado como argumento.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

O exemplo que se segue chama uma função, que obtém dados para o estado do Texas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

O exemplo seguinte elimina a função que foi criada no primeiro passo.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Passos seguintes

[Referência da linguagem da consulta de Kusto](https://aka.ms/kustolangref)
