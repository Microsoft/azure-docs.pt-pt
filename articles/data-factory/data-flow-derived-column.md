---
title: Transformação de colunas derivadas no fluxo de dados de mapeamento
description: Aprenda a transformar dados em escala na Azure Data Factory com o fluxo de dados de mapeamento Transformação da Coluna Derivada.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532025"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de colunas derivadas no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da coluna derivada para gerar novas colunas no fluxo de dados ou para modificar os campos existentes.

## <a name="create-and-update-columns"></a>Criar e atualizar colunas

Ao criar uma coluna derivada, pode gerar uma nova coluna ou atualizar uma existente. Na caixa de texto da **Coluna,** introduza na coluna que está a criar. Para anular uma coluna existente no seu esquema, pode utilizar a coluna de retirada. Para construir a expressão da coluna derivada, clique na caixa de texto de **expressão Enter.** Pode começar a escrever a sua expressão ou abrir o construtor de expressão para construir a sua lógica.

![Definições de colunas derivadas](media/data-flow/create-derive-column.png "Definições de colunas derivadas")

Para adicionar mais colunas derivadas, clique em **Adicionar** acima da lista de colunas ou do ícone mais ao lado de uma coluna derivada existente. Escolha **adicionar a coluna** ou **adicionar o padrão de coluna**.

![Nova seleção de colunas derivadas](media/data-flow/add-derived-column.png "Nova seleção de colunas derivadas")

### <a name="column-patterns"></a>Padrões de coluna

Nos casos em que o seu esquema não esteja explicitamente definido ou se pretender atualizar um conjunto de colunas a granel, irá querer criar colunas. Os padrões de coluna permitem-lhe combinar colunas utilizando regras baseadas nos metadados da coluna e criar colunas derivadas para cada coluna correspondida. Para obter mais informações, [aprenda a construir padrões](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) de colunas na transformação da coluna derivada.

![Padrões de coluna](media/data-flow/column-pattern-derive.png "Padrões de coluna")

## <a name="building-schemas-using-the-expression-builder"></a>Esquemas de construção usando o construtor de expressão

Ao utilizar o [construtor](concepts-data-flow-expression-builder.md)de expressão de fluxo de dados de mapeamento, pode criar, editar e gerir as suas colunas derivadas na secção **Colunas Derivadas.** Todas as colunas que são criadas ou alteradas na transformação são listadas. Escolha interativamente qual coluna ou padrão está a editar clicando no nome da coluna. Para adicionar uma coluna adicional **selecione Criar novo** e escolher se deseja adicionar uma única coluna ou um padrão.

![Criar nova coluna](media/data-flow/derive-add-column.png "Criar nova coluna")

Ao trabalhar com colunas complexas, pode criar subcolumns. Para isso, clique no ícone mais ao lado de qualquer coluna e **selecione Adicionar subcolumn**. Para obter mais informações sobre o manuseamento de tipos complexos no fluxo de dados, consulte [o tratamento JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar subcolumn](media/data-flow/derive-add-subcolumn.png "Adicionar Subcolumn")

Para obter mais informações sobre o manuseamento de tipos complexos no fluxo de dados, consulte [o tratamento JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar coluna complexa](media/data-flow/derive-complex-column.png "Adicionar colunas")

### <a name="locals"></a>Locais

Se estiver a partilhar lógicas em várias colunas ou quiser compartimentar a sua lógica, pode criar um local dentro de uma transformação de colunas derivadas. Um local é um conjunto de lógica que não se propaga rio abaixo para a seguinte transformação. Os locais podem ser criados dentro do construtor de expressão indo para **elementos de expressão** e selecionando **locais.** Criar um novo selecionando **Criar novo**.

![Criar local](media/data-flow/create-local.png "Criar local")

Os locais podem referenciar qualquer elemento de expressão de uma coluna derivada, incluindo funções, esquema de entrada, parâmetros e outros locais. Quando se refere a outros locais, a ordem importa, uma vez que o local referenciado tem de estar "acima" do atual.

![Criar local 2](media/data-flow/create-local-2.png "Criar local 2")

Para fazer referência a um local numa coluna derivada, clique no local a partir da vista de **elementos de expressão** ou referencia-lo com um cólon na frente do seu nome. Por exemplo, um local chamado local1 seria referenciado por `:local1` . Para editar uma definição local, paire sobre ela na visualização de elementos de expressão e clique no ícone do lápis.

![Usando os locais](media/data-flow/using-locals.png "Usando os locais")

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

![Exemplo derivado](media/data-flow/derive-script.png "Exemplo derivado")

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

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [linguagem de expressão mapping Data Flow.](data-flow-expression-functions.md)
