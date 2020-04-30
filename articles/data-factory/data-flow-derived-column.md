---
title: Transformação de coluna derivada no fluxo de dados de mapeamento
description: Aprenda a transformar dados à escala na Azure Data Factory com o fluxo de dados de mapeamento Transformação de Coluna derivada.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606492"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de coluna derivada no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da coluna derivada para gerar novas colunas no fluxo de dados ou para modificar os campos existentes.

## <a name="derived-column-settings"></a>Definições de colunaderivadas

Para sobrepor uma coluna existente, selecione-a através da queda da coluna. Caso contrário, utilize o campo de seleção da coluna como caixa de texto e escreva o nome da nova coluna. Para construir a expressão da coluna derivada, clique na caixa 'Enter expression' para abrir o Builder de Expressão de Fluxo de [Dados](concepts-data-flow-expression-builder.md).

![Definições de colunaderivadas](media/data-flow/dc1.png "Definições de colunaderivadas")

Para adicionar colunas derivadas adicionais, paire sobre uma coluna derivada existente e clique no ícone plus. Escolha adicionar **coluna** ou **adicionar padrão**de coluna . Os padrões das colunas podem ser úteis se os nomes das colunas forem variáveis a partir das suas fontes. Para mais informações, consulte Os [Padrões](concepts-data-flow-column-pattern.md)da Coluna .

![Nova seleção de colunas derivadas](media/data-flow/columnpattern.png "Nova seleção de colunas derivadas")

## <a name="build-schemas-in-output-schema-pane"></a>Construa esquemia no painel de Schema de saída

As colunas que está a modificar e a adicionar ao seu esquema estão listadas no painel De saída schema,. Pode construir estruturas de dados simples e complexas aqui. Para adicionar campos adicionais, selecione **Adicionar coluna**. Para construir hierarquias, selecione **Adicionar subcoluna**.

![Adicionar subcoluna](media/data-flow/addsubcolumn.png "Adicionar Subcoluna")

Para obter mais informações sobre o manuseamento de tipos complexos no fluxo de dados, consulte o [manuseamento da JSON no fluxo](format-json.md#mapping-data-flow-properties)de dados de mapeamento .

![Adicionar coluna complexa](media/data-flow/complexcolumn.png "Adicionar colunas")

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

O exemplo abaixo é uma `CleanData` coluna derivada `MoviesYear` chamada que pega num fluxo de entrada e cria duas colunas derivadas. A primeira coluna derivada `Rating` substitui a coluna pelo valor do Rating como um tipo inteiro. A segunda coluna derivada é um padrão que combina com cada coluna cujo nome começa com 'filmes'. Para cada coluna compatível, cria `movie` uma coluna igual ao valor da coluna correspondida pré-fixada com 'movie_'. 

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Exemplo de derivar](media/data-flow/derive-script1.png "Exemplo de derivar")

O script de fluxo de dados para esta transformação está no corte abaixo:

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

- Saiba mais sobre a linguagem de expressão de fluxo de [dados de mapeamento.](data-flow-expression-functions.md)
