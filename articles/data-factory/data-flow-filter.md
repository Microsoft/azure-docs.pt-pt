---
title: Transformação de filtro no fluxo de dados de mapeamento Azure Data Factory
description: Filtrar linhas usando a transformação de filtro em Azure Data Factory fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676165"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

As transformações de filtro permitem a filtragem de linha com base em uma condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação de filtro é semelhante a uma cláusula WHERE no SQL.

## <a name="configuration"></a>Configuração

Use o construtor de expressões de fluxo de dados para inserir uma expressão para a condição de filtro. Para abrir o construtor de expressões, clique na caixa azul. A condição de filtro deve ser do tipo booliano. Para obter mais informações sobre como criar uma expressão, consulte a documentação do [Construtor de expressões](concepts-data-flow-expression-builder.md) .

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação de divisão condicional chamada `FilterBefore1960` que usa o fluxo de entrada `CleanData`. A condição de filtro é a `year <= 1960`de expressão.

No Data Factory UX, essa transformação é semelhante à imagem abaixo:

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Passos seguintes

Filtrar colunas com a [transformação selecionar](data-flow-select.md)
