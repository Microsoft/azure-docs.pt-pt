---
title: Escrever consultas de registo eficiente no Azure Monitor | Documentos da Microsoft
description: Referências a recursos para aprender a escrever consultas do Log Analytics.
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
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519376"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Escrever consultas de registo eficiente no Azure Monitor
Este artigo fornece recomendações para escrever consultas de registo eficiente no Azure Monitor. Com essas estratégias, pode garantir que suas consultas serão executadas rapidamente e com mínima sobrecarga.

## <a name="scope-your-query"></a>Definir o âmbito sua consulta
Ter a sua consulta processar mais dados do que realmente precisa pode levar a uma consulta de execução longa e, muitas vezes, resultar em demasiados dados nos seus resultados para analisar com eficiência. Em casos extremos, a consulta pode até mesmo o tempo limite e falhar.

### <a name="specify-your-data-source"></a>Especifique a sua origem de dados
A primeira etapa da criação de uma consulta eficiente esteja a limitar seu escopo de suas fontes de dados necessários. Especificar uma tabela será sempre preferencial ao longo de executar uma pesquisa de texto grande, como `search *`. Para consultar uma tabela específica, comece a sua consulta com o nome da tabela como a seguir:

``` Kusto
requests | ...
```

Pode usar [pesquisa](/azure/kusto/query/searchoperator) para procurar um valor em múltiplas colunas em tabelas específicas, usando uma consulta semelhante ao seguinte:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Uso [União](/azure/kusto/query/unionoperator) para consultar várias tabelas semelhante ao seguinte:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Especifique um intervalo de tempo
Também deve limitar sua consulta para o intervalo de tempo de dados de que necessita. Por predefinição, a sua consulta incluirá dados recolhidos nas últimas 24 horas. Pode alterar essa opção no [Seletor de intervalo de tempo](get-started-portal.md#select-a-time-range) ou adicioná-lo explicitamente à sua consulta. É melhor adicionar o filtro de tempo imediatamente após o nome da tabela para que o resto da sua consulta só processa os dados dentro do intervalo:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Obter apenas os registos mais recentes

Para devolver apenas os registos mais recente, utilize o *top* operador como a seguinte consulta que devolve os registos de 10 mais recentes com sessão iniciada *rastreios* tabela:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrar registos
Para rever apenas os registos que correspondem a uma determinada condição, utilize o *em que* operador como a seguinte consulta devolve apenas os registos em que o _severityLevel_ valor é superior a 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Comparações de seqüência de caracteres
Quando [avaliar as cadeias de caracteres](/azure/kusto/query/datatypes-string-operators), normalmente, deve utilizar `has` em vez de `contains` quando está à procura de tokens completas. `has` é mais eficiente, pois ele não precisa pesquisar para subcadeias de carateres.

## <a name="returned-columns"></a>Colunas retornadas

Uso [projeto](/azure/kusto/query/projectoperator) para restringir o conjunto de colunas a ser processado apenas àqueles que precisa:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Embora seja possível usar [expandir](/azure/kusto/query/extendoperator) para calcular valores e criar suas próprias colunas, normalmente é mais eficiente para filtrar numa coluna de tabela.

Por exemplo, a primeira consulta abaixo que filtra no _operação\_Name_ seria mais eficiente do que o segundo que cria um novo _subscrição_ coluna e os filtros no mesmo:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Usando junções
Ao utilizar o [associação](/azure/kusto/query/joinoperator) operador, selecione a tabela com menos linhas de estar no lado esquerdo da consulta.


## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre as melhores práticas de consulta, veja [consultar as melhores práticas](/azure/kusto/query/best-practices).