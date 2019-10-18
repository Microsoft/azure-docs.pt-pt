---
title: Transformação de coluna derivada no fluxo de dados de mapeamento de Azure Data Factory | Microsoft Docs
description: Saiba como transformar dados em escala em Azure Data Factory com a transformação coluna derivada de fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 60451fa6152590ed0fde51be436c867f39906acf
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514810"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação coluna derivada no fluxo de dados de mapeamento

Use a transformação coluna derivada para gerar novas colunas no fluxo de dados ou para modificar os campos existentes.

## <a name="derived-column-settings"></a>Configurações de coluna derivadas

Para substituir uma coluna existente, selecione-a por meio da lista suspensa coluna. Caso contrário, use o campo seleção de coluna como uma caixa de texto e digite o nome da nova coluna. Para criar a expressão da coluna derivada, clique na caixa ' Inserir expressão ' para abrir o construtor de [expressões de fluxo de dados](concepts-data-flow-expression-builder.md).

![Configurações de coluna derivadas](media/data-flow/dc1.png "Configurações de coluna derivadas")

Para adicionar mais colunas derivadas, passe o mouse sobre uma coluna derivada existente e clique no ícone de adição. Escolha o padrão **adicionar coluna** ou **adicionar coluna**. Padrões de coluna poderão ser úteis se os nomes de coluna forem variáveis de suas fontes. Para obter mais informações, consulte [padrões de coluna](concepts-data-flow-column-pattern.md).

![Nova seleção de coluna derivada](media/data-flow/columnpattern.png "Nova seleção de coluna derivada")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma coluna derivada chamada `CleanData` que usa um fluxo de entrada `MoviesYear` e cria duas colunas derivadas. A primeira coluna derivada substitui a coluna `Rating` com o valor da classificação como um tipo inteiro. A segunda coluna derivada é um padrão que corresponde a cada coluna cujo nome começa com ' filmes '. Para cada coluna correspondente, ele cria uma coluna `movie` que é igual ao valor da coluna correspondente prefixada com ' movie_ '. 

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Obter exemplo](media/data-flow/derive-script1.png "Obter exemplo")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [linguagem de expressão de fluxo de dados de mapeamento](data-flow-expression-functions.md).
