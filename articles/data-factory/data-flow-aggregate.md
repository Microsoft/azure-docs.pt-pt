---
title: Transformação agregada no fluxo de dados de mapeamento
description: Saiba como agregar dados à escala na Azure Data Factory com o fluxo de dados de mapeamento Transformação agregada.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606540"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação agregada no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação agregada define agregações de colunas nos seus fluxos de dados. Utilizando o Expression Builder, pode definir diferentes tipos de agregados tais como SUM, MIN, MAX e COUNT agrupados por colunas existentes ou computadas.

## <a name="group-by"></a>Agrupar por

Selecione uma coluna existente ou crie uma nova coluna computorizada para usar como grupo por cláusula para a sua agregação. Para utilizar uma coluna existente, selecione-a a partir do dropdown. Para criar uma nova coluna computorizada, paire sobre a cláusula e clique na **coluna Computed**. Isto abre o construtor de expressão de fluxo de [dados.](concepts-data-flow-expression-builder.md) Assim que criar a sua coluna computorizada, introduza o nome da coluna de saída sob o **nome de campo.** Se desejar adicionar um grupo adicional por cláusula, paire sobre uma cláusula existente e clique no ícone plus.

![Grupo de transformação agregado por configurações](media/data-flow/agg.png "Grupo de transformação agregado por configurações")

Um grupo por cláusula é opcional numa transformação agregada.

## <a name="aggregate-column"></a>Coluna agregada 

Vá ao separador **Agregados** para construir expressões de agregação. Pode substituir uma coluna existente com uma agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa da direita ao lado do seletor de nome da coluna. Para editar a expressão, clique na caixa de texto para abrir o construtor de expressão. Para adicionar agregações adicionais, paire sobre uma expressão existente e clique mais ícone para criar uma nova coluna de agregação ou padrão de [coluna](concepts-data-flow-column-pattern.md).

Cada expressão de agregação deve conter pelo menos uma função agregada.

![Definições agregadas de transformação](media/data-flow/agg2.png "Definições agregadas de transformação")


> [!NOTE]
> No modo Debug, o construtor de expressão não pode produzir pré-visualizações de dados com funções agregadas. Para visualizar as pré-visualizações de dados para transformações agregadas, feche o construtor de expressão e veja os dados através do separador 'Data Preview'.

## <a name="reconnect-rows-and-columns"></a>Reconectar linhas e colunas

As transformações agregadas são semelhantes às consultas selecionadas agregadas sQL. Colunas que não estejam incluídas no seu grupo por cláusula ou funções agregadas não fluirão para a saída da sua transformação agregada. Se desejar incluir outras colunas na sua saída agregada, faça um dos seguintes métodos:

* Utilize uma função `last()` `first()` agregada como ou para incluir essa coluna adicional.
* Volte a juntar as colunas ao fluxo de saída utilizando o padrão de [auto-união](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Remoção de linhas duplicadas

Um uso comum da transformação agregada é remover ou identificar entradas duplicadas em dados de origem. Este processo é conhecido como desduplicação. Com base num conjunto de chaves de grupo, use um heurístico da sua escolha para determinar qual a linha duplicada a manter. Heurística comum `first()` `last()`é, `max()` `min()`e . Utilize padrões de [colunas](concepts-data-flow-column-pattern.md) para aplicar a regra a todas as colunas, exceto no grupo por colunas.

![Eliminação de duplicados](media/data-flow/agg-dedupe.png "Eliminação de duplicados")

No exemplo acima, `ProductID` as `Name` colunas e estão a ser utilizados para agrupar. Se duas linhas têm os mesmos valores para estas duas colunas, são consideradas duplicadas. Nesta transformação agregada, os valores da primeira linha combinados serão mantidos e todos os outros serão eliminados. Utilizando sintaxe de padrão de coluna, `ProductID` `Name` todas as colunas cujos nomes não são e estão mapeadas para o nome da coluna existente e dado o valor das primeiras linhas correspondidas. O esquema de saída é o mesmo que o esquema de entrada.

Para cenários de `count()` validação de dados, a função pode ser usada para contar quantos duplicados existem.

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

O exemplo abaixo leva `MoviesYear` um fluxo de `year`entrada e agrupa linhas por coluna . A transformação cria `avgrating` uma coluna agregada `Rating`que avalia a média da coluna. Esta transformação `AvgComedyRatingsByYear`agregada chama-se .

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Grupo por exemplo](media/data-flow/agg-script1.png "Grupo por exemplo")

![Exemplo agregado](media/data-flow/agg-script2.png "Exemplo agregado")

O script de fluxo de dados para esta transformação está no corte abaixo.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script de fluxo de dados agregado](media/data-flow/aggdfs1.png "Script de fluxo de dados agregado")

```MoviesYear```: Coluna derivada que define ```AvgComedyRatingByYear```ano e colunas de títulos : ```avgrating```Transformação agregada para classificação média de ededies agruparadas por ano : Nome da nova coluna criada para deter o valor agregado

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Passos seguintes

* Defina a gregação baseada em janelas utilizando a [transformação](data-flow-window.md) da janela
