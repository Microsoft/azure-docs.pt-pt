---
title: Gravando consultas de log eficientes no Azure Monitor | Microsoft Docs
description: Referências a recursos para aprender a escrever consultas no Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894123"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Gravando consultas de log eficientes no Azure Monitor
Este artigo fornece recomendações para escrever consultas de log eficientes no Azure Monitor. Usando essas estratégias, você pode garantir que suas consultas serão executadas rapidamente e com um mínimo de ouvido.

## <a name="scope-your-query"></a>Escopo da consulta
Ter seu processo de consulta mais dados do que você realmente precisa pode levar a uma consulta de longa execução e, muitas vezes, resultar em muitos dados em seus resultados para analisar efetivamente. Em casos extremos, a consulta pode até atingir o tempo limite e falhar.

### <a name="specify-your-data-source"></a>Especificar sua fonte de dados
A primeira etapa na escrita de uma consulta eficiente é limitar seu escopo às suas fontes de dados necessárias. A especificação de uma tabela é sempre preferida na execução de uma pesquisa de texto larga, como `search *`. Para consultar uma tabela específica, inicie a consulta com o nome da tabela como no seguinte:

``` Kusto
requests | ...
```

Você pode usar a [pesquisa](/azure/kusto/query/searchoperator) para pesquisar um valor em várias colunas em tabelas específicas usando uma consulta semelhante à seguinte:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Use [Union](/azure/kusto/query/unionoperator) para consultar várias tabelas como as seguintes:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Especificar um intervalo de tempo
Você também deve limitar a consulta para o intervalo de tempo dos dados que você precisa. Por padrão, a consulta incluirá os dados coletados nas últimas 24 horas. Você pode alterar essa opção no [seletor de intervalo de tempo](get-started-portal.md#select-a-time-range) ou adicioná-la explicitamente à sua consulta. É melhor adicionar o filtro de tempo imediatamente após o nome da tabela para que o restante da consulta processe apenas os dados dentro desse intervalo:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Obter somente os registros mais recentes

Para retornar apenas os registros mais recentes, use o operador *Top* como na consulta a seguir, que retorna os 10 registros mais recentes registrados na tabela de *rastreamentos* :

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrar registros
Para examinar somente os logs que correspondem a uma determinada condição, use o operador *Where* como na consulta a seguir que retorna somente os registros nos quais o valor de _nível_ é maior que 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Comparações de cadeia de caracteres
Ao [avaliar cadeias de caracteres](/azure/kusto/query/datatypes-string-operators), geralmente você deve usar `has` em vez de `contains` ao procurar tokens completos. `has` é mais eficiente, pois não precisa procurar subcadeias de caracteres.

## <a name="returned-columns"></a>Colunas retornadas

Use o [projeto](/azure/kusto/query/projectoperator) para restringir o conjunto de colunas que estão sendo processadas somente para os que você precisa:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Embora você possa usar [Extend](/azure/kusto/query/extendoperator) para calcular valores e criar suas próprias colunas, normalmente será mais eficiente filtrar em uma coluna de tabela.

Por exemplo, a primeira consulta abaixo que filtra na _operação\_nome_ seria mais eficiente do que a segunda, que cria uma nova coluna de _assinatura_ e filtra:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Usando junções
Ao usar o operador de [junção](/azure/kusto/query/joinoperator) , escolha a tabela com menos linhas para o lado esquerdo da consulta.


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as práticas recomendadas de consulta, consulte [práticas recomendadas de consulta](/azure/kusto/query/best-practices).