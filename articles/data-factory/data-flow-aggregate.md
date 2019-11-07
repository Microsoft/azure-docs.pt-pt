---
title: Transformação Agregação no fluxo de dados de mapeamento Azure Data Factory
description: Saiba como agregar dados em escala em Azure Data Factory com a transformação agregar fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f21367c36ae31aa2115c109933a581bef464baf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676902"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação Agregação no fluxo de dados de mapeamento 

A transformação Agregação define as agregações de colunas em seus fluxos de dados. Usando o construtor de expressões, você pode definir diferentes tipos de agregações como SUM, MIN, MAX e COUNT agrupados por colunas computadas ou existentes.

## <a name="group-by"></a>Agrupar por

Selecione uma coluna existente ou crie uma nova coluna computada para usar como uma cláusula Group by para sua agregação. Para usar uma coluna existente, selecione-a na lista suspensa. Para criar uma nova coluna computada, passe o mouse sobre a cláusula e clique em **coluna computada**. Isso abre o [Construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md). Depois de criar a coluna computada, insira o nome da coluna de saída sob o campo **nome como** . Se você quiser adicionar uma cláusula Group by adicional, passe o mouse sobre uma cláusula existente e clique no ícone de adição.

![Grupos de transformação agregados por configurações](media/data-flow/agg.png "Grupos de transformação agregados por configurações")

Uma cláusula Group by é opcional em uma transformação Agregação.

## <a name="aggregate-column"></a>Coluna de agregação 

Vá para a guia **agregações** para criar expressões de agregação. Você pode substituir uma coluna existente por uma agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa à direita ao lado do seletor de nome de coluna. Para editar a expressão, clique na caixa de texto para abrir o construtor de expressões. Para adicionar agregações adicionais, passe o mouse sobre uma expressão existente e clique no ícone de adição para criar uma nova coluna de agregação ou [padrão de coluna](concepts-data-flow-column-pattern.md).

Cada expressão de agregação deve conter pelo menos uma função de agregação.

![Configurações de agregação de transformação Agregação](media/data-flow/agg2.png "Configurações de agregação de transformação Agregação")


> [!NOTE]
> No modo de depuração, o construtor de expressões não pode produzir visualizações de dados com funções de agregação. Para exibir visualizações de dados para transformações agregadas, feche o construtor de expressões e exiba os dados por meio da guia ' Data preview '.

## <a name="reconnect-rows-and-columns"></a>Reconectar linhas e colunas

As transformações agregadas são semelhantes às consultas SELECT de agregação do SQL. As colunas que não estão incluídas em sua cláusula Group by ou funções de agregação não fluirão para a saída da transformação Agregação. Se você quiser incluir outras colunas em sua saída agregada, execute um dos seguintes métodos:

* Use uma função de agregação como `last()` ou `first()` para incluir essa coluna adicional.
* Reingresse as colunas no fluxo de saída usando o [padrão de autojunção](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo usa um fluxo de entrada `MoviesYear` e agrupa linhas por coluna `year`. A transformação cria uma coluna de agregação `avgrating` que é avaliada como a média de `Rating`de coluna. Essa transformação agregada é nomeada `AvgComedyRatingsByYear`.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Agrupar por exemplo](media/data-flow/agg-script1.png "Agrupar por exemplo")

![Exemplo de agregação](media/data-flow/agg-script2.png "Exemplo de agregação")

O script de fluxo de dados para essa transformação está no trecho de código abaixo.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Passos seguintes

* Definir a agregação baseada em janela usando a [transformação janela](data-flow-window.md)
