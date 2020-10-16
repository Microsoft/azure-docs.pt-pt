---
title: Transformação agregada no fluxo de dados de mapeamento
description: Aprenda a agregar dados em escala na Azure Data Factory com o fluxo de dados de mapeamento Transformação agregada.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531941"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação agregada no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação agregada define agregações de colunas nos seus fluxos de dados. Utilizando o Expression Builder, pode definir diferentes tipos de agregações tais como SUM, MIN, MAX e COUNT agrupadas por colunas existentes ou computadas.

## <a name="group-by"></a>Agrupar por

Selecione uma coluna existente ou crie uma nova coluna computada para usar como grupo por cláusula para a sua agregação. Para utilizar uma coluna existente, selecione-a a partir do dropdown. Para criar uma nova coluna computada, paire sobre a cláusula e clique na **coluna Computada**. Isto abre o construtor de [expressão de fluxo de dados.](concepts-data-flow-expression-builder.md) Assim que criar a coluna computada, insira o nome da coluna de saída sob o **nome como** campo. Se desejar adicionar um grupo adicional por cláusula, sobressaia uma cláusula existente e clique no ícone plus.

![Grupo de transformação agregado por configurações](media/data-flow/agg.png "Grupo de transformação agregado por configurações")

Um grupo por cláusula é opcional numa transformação agregada.

## <a name="aggregate-columns"></a>Colunas agregadas

Vá ao **separador Agregados** para construir expressões de agregação. Pode substituir uma coluna existente com uma agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa direita ao lado do seletor de nomes da coluna. Para editar a expressão, clique na caixa de texto e abra o porta-expressão. Para adicionar mais colunas agregadas, clique em **Adicionar** acima da lista de colunas ou do ícone mais ao lado de uma coluna agregada existente. Escolha **adicionar a coluna** ou **adicionar o padrão de coluna**. Cada expressão de agregação deve conter pelo menos uma função agregada.

![Configurações agregadas](media/data-flow/aggregate-columns.png "Configurações agregadas")

> [!NOTE]
> No modo Debug, o construtor de expressão não pode produzir pré-visualizações de dados com funções agregadas. Para visualizar pré-visualizações de dados para transformações agregadas, feche o construtor de expressão e veja os dados através do separador 'Data Preview'.

### <a name="column-patterns"></a>Padrões de coluna

Utilize [padrões de coluna para](concepts-data-flow-column-pattern.md) aplicar a mesma agregação a um conjunto de colunas. Isto é útil se desejar persistir muitas colunas do esquema de entrada, uma vez que são deixadas por defeito. Utilize uma heurística para `first()` persistir nas colunas de entrada através da agregação.

## <a name="reconnect-rows-and-columns"></a>Reconectar linhas e colunas

As transformações agregadas são semelhantes às consultas selecionadas agregadas sql. Colunas que não estão incluídas no seu grupo por cláusula ou funções agregadas não fluirão para a saída da sua transformação agregada. Se desejar incluir outras colunas na sua saída agregada, faça um dos seguintes métodos:

* Utilize uma função agregada, como `last()` ou para incluir essa coluna `first()` adicional.
* Junte as colunas ao fluxo de saída utilizando o [padrão de união de si](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)mesmo .

## <a name="removing-duplicate-rows"></a>Remoção de linhas duplicadas

Um uso comum da transformação agregada é remover ou identificar entradas duplicadas em dados de origem. Este processo é conhecido como deduplicação. Baseado num conjunto de grupo por teclas, use uma heurística à sua escolha para determinar qual a linha duplicada a manter. Heurística comum `first()` `last()` são, `max()` e `min()` . Utilize [padrões de coluna para](concepts-data-flow-column-pattern.md) aplicar a regra a todas as colunas, exceto ao grupo por colunas.

![Eliminação de duplicados](media/data-flow/agg-dedupe.png "Eliminação de duplicados")

No exemplo acima, as colunas `ProductID` `Name` estão a ser utilizadas para o agrupamento. Se duas linhas têm os mesmos valores para estas duas colunas, são consideradas duplicadas. Nesta transformação agregada, os valores da primeira linha combinados serão mantidos e todos os outros serão eliminados. Utilizando a sintaxe do padrão da coluna, todas as colunas cujos nomes não são `ProductID` e `Name` são mapeadas para o seu nome de coluna existente e dado o valor das primeiras linhas combinadas. O esquema de saída é o mesmo que o esquema de entrada.

Para cenários de validação de dados, a `count()` função pode ser usada para contar quantas duplicações existem.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

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

O exemplo abaixo toma um fluxo de entrada `MoviesYear` e grupos linhas por coluna `year` . A transformação cria uma coluna agregada `avgrating` que avalia a média da `Rating` coluna. Esta transformação agregada é `AvgComedyRatingsByYear` nomeada.

No Data Factory UX, esta transformação parece a imagem abaixo:

![Grupo por exemplo](media/data-flow/agg-script1.png "Grupo por exemplo")

![Exemplo agregado](media/data-flow/agg-script2.png "Exemplo agregado")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script agregado de fluxo de dados](media/data-flow/aggdfs1.png "Script agregado de fluxo de dados")

```MoviesYear```: Coluna derivada que define o ano e as colunas de títulos ```AvgComedyRatingByYear``` : Transformação agregada para a classificação média das comédias agrupadas por ano ```avgrating``` : Nome da nova coluna criada para manter o valor agregado

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Passos seguintes

* Defina a agregação baseada em janelas utilizando a [transformação da janela](data-flow-window.md)
