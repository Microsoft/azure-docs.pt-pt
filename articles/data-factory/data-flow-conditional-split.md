---
title: Transformação de divisão condicional no fluxo de dados de mapeamento
description: Dividir dados em fluxos diferentes usando a transformação Divisão condicional no fluxo de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: d7e2af6c98951e685192656b37226716e4340bfe
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930435"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformação de divisão condicional no fluxo de dados de mapeamento

A transformação Divisão condicional roteia as linhas de dados para diferentes fluxos com base em condições de correspondência. A transformação Divisão condicional é semelhante a uma estrutura de decisão de caso em uma linguagem de programação. A transformação avalia as expressões e, com base nos resultados, direciona a linha de dados para o fluxo especificado.

## <a name="configuration"></a>Configuração

A configuração **Split on** determina se a linha de dados flui para o primeiro fluxo de correspondência ou para cada fluxo ao qual ela corresponde.

Use o construtor de expressões de fluxo de dados para inserir uma expressão para a condição de divisão. Para adicionar uma nova condição, clique no ícone de adição em uma linha existente. Um fluxo padrão também pode ser adicionado para linhas que não correspondem a nenhuma condição.

![Divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação de divisão condicional chamada `SplitByYear` que usa o fluxo de entrada `CleanData`. Essa transformação tem duas condições de divisão `year < 1960` e `year > 1980`. `disjoint` é false porque os dados vão para a primeira condição de correspondência. Cada linha que corresponde à primeira condição vai para o fluxo de saída `moviesBefore1960`. Todas as linhas restantes que correspondem à segunda condição vão para o fluxo de saída `moviesAFter1980`. Todas as outras linhas fluem por meio do `AllOtherMovies`de fluxo padrão.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Passos seguintes

As transformações de fluxo de dados comuns usadas com divisão condicional são a [transformação de junção](data-flow-join.md), a [transformação de pesquisa](data-flow-lookup.md)e a [transformação selecionar](data-flow-select.md)
