---
title: Transformação de divisão condicional no fluxo de dados de mapeamento
description: Divida dados em diferentes fluxos usando a transformação de divisão condicional no fluxo de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83800074"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformação de divisão condicional no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação de divisão condicional encaminha linhas de dados para diferentes fluxos com base em condições de correspondência. A transformação de divisão condicional é semelhante a uma estrutura de decisão case em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Configuração

A **Divisão na** definição determina se a linha de dados flui para o primeiro fluxo de correspondência ou para cada fluxo que corresponde.

Utilize o construtor de expressão de fluxo de dados para introduzir uma expressão para a condição de divisão. Para adicionar uma nova condição, clique no ícone plus em uma linha existente. Um fluxo predefinido também pode ser adicionado para linhas que não correspondem a qualquer condição.

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

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

O exemplo abaixo é uma transformação de divisão condicional nomeada `SplitByYear` que toma em fluxo de entrada `CleanData` . Esta transformação tem duas condições divididas `year < 1960` `year > 1980` e. `disjoint` é falso porque os dados vão para a primeira condição de correspondência. Cada linha correspondente à primeira condição vai para o fluxo de saída `moviesBefore1960` . Todas as linhas restantes correspondentes à segunda condição vão para o fluxo de saída `moviesAFter1980` . Todas as outras linhas fluem através do fluxo predefinido `AllOtherMovies` .

No Data Factory UX, esta transformação parece a imagem abaixo:

![divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Passos seguintes

As transformações comuns do fluxo de dados utilizadas com a divisão condicional são a transformação da [união,](data-flow-join.md) [a transformação da procura](data-flow-lookup.md)e a [transformação selecionada](data-flow-select.md)
