---
title: Transformação de divisão condicional no fluxo de dados de mapeamento
description: Divida dados em diferentes fluxos usando a transformação de divisão condicional no fluxo de dados de mapeamento da Fábrica de Dados azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606488"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformação de divisão condicional no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação de divisão condicional encaminha linhas de dados para diferentes fluxos com base em condições correspondentes. A transformação de divisão condicional é semelhante a uma estrutura de decisão do CASO numa linguagem de programação. A transformação avalia expressões e, com base nos resultados, direciona a linha de dados para o fluxo especificado.

## <a name="configuration"></a>Configuração

A **Divisão na** definição determina se a linha de dados flui para o primeiro fluxo correspondente ou para cada fluxo a que corresponde.

Utilize o construtor de expressão de fluxo de dados para introduzir uma expressão para a condição dividida. Para adicionar uma nova condição, clique no ícone plus numa linha existente. Um fluxo padrão também pode ser adicionado para linhas que não correspondem a qualquer condição.

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

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

O exemplo abaixo é uma `SplitByYear` transformação de `CleanData`divisão condicional chamada que acolhe o fluxo de entrada . Esta transformação tem `year < 1960` `year > 1980`duas condições divididas e. `disjoint`é falso porque os dados vão para a primeira condição correspondente. Cada linha correspondente à primeira `moviesBefore1960`condição vai para o fluxo de saída. Todas as linhas restantes correspondentes `moviesAFter1980`à segunda condição vão para o fluxo de saída . Todas as outras linhas `AllOtherMovies`fluem através do fluxo padrão .

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

O script de fluxo de dados para esta transformação está no corte abaixo:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Passos seguintes

As transformações comuns do fluxo de dados usadas com divisão condicional são a transformação de [join,](data-flow-join.md)a transformação de [lookup,](data-flow-lookup.md)e a [transformação selecionada](data-flow-select.md)
