---
title: Transformação de colunas derivadas no fluxo de dados de mapeamento
description: Aprenda a transformar dados em escala na Azure Data Factory com o fluxo de dados de mapeamento Transformação da Coluna Derivada.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81606492"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de colunas derivadas no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da coluna derivada para gerar novas colunas no fluxo de dados ou para modificar os campos existentes.

## <a name="derived-column-settings"></a>Definições de colunas derivadas

Para anular uma coluna existente, selecione-a através do dropdown da coluna. Caso contrário, utilize o campo de seleção da coluna como uma caixa de texto e escreva o nome da nova coluna. Para construir a expressão da coluna derivada, clique na caixa 'Inserir expressão' para abrir o [Data Flow Expression Builder](concepts-data-flow-expression-builder.md).

![Definições de colunas derivadas](media/data-flow/dc1.png "Definições de colunas derivadas")

Para adicionar colunas derivadas adicionais, paire sobre uma coluna derivada existente e clique no ícone plus. Escolha **adicionar a coluna** ou **adicionar o padrão de coluna**. Os padrões das colunas podem ser úteis se os nomes das suas colunas forem variáveis a partir das suas fontes. Para obter mais informações, consulte [Os Padrões de Coluna.](concepts-data-flow-column-pattern.md)

![Nova seleção de colunas derivadas](media/data-flow/columnpattern.png "Nova seleção de colunas derivadas")

## <a name="build-schemas-in-output-schema-pane"></a>Construa esquemas no painel de esquemas de saída

As colunas que está a modificar e a adicionar ao seu esquema estão listadas no painel de esquemas de saída, Você pode construir interativamente estruturas de dados simples e complexas aqui. Para adicionar campos adicionais, **selecione Adicionar coluna**. Para construir hierarquias, **selecione Adicionar subcolumn**.

![Adicionar subcolumn](media/data-flow/addsubcolumn.png "Adicionar Subcolumn")

Para obter mais informações sobre o manuseamento de tipos complexos no fluxo de dados, consulte [o tratamento JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar coluna complexa](media/data-flow/complexcolumn.png "Adicionar colunas")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

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

O exemplo abaixo é uma coluna derivada chamada `CleanData` que toma um fluxo de entrada e cria `MoviesYear` duas colunas derivadas. A primeira coluna derivada substitui a coluna `Rating` pelo valor de Rating como um tipo inteiro. A segunda coluna derivada é um padrão que combina com cada coluna cujo nome começa com "filmes". Para cada coluna combinada, cria uma coluna `movie` igual ao valor da coluna partida prefixada com 'movie_'. 

No Data Factory UX, esta transformação parece a imagem abaixo:

![Exemplo derivado](media/data-flow/derive-script1.png "Exemplo derivado")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [linguagem de expressão mapping Data Flow.](data-flow-expression-functions.md)
