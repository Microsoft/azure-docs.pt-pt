---
title: Transformação de filtro no fluxo de dados de mapeamento
description: Filtrar as filas utilizando a transformação do filtro no fluxo de dados de mapeamento da Fábrica de Dados azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112816"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação do Filtro permite a filtragem da linha com base numa condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação do filtro é semelhante a uma cláusula WHERE no SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuração

Utilize o construtor de expressão de fluxo de dados para introduzir uma expressão para a condição do filtro. Para abrir o construtor de expressão, clique na caixa azul. A condição do filtro deve ser de tipo booleano. Para obter mais informações sobre como criar uma expressão, consulte a documentação do construtor de [expressão.](concepts-data-flow-expression-builder.md)

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

O exemplo abaixo é uma transformação de filtro chamada `FilterBefore1960` que acolhe fluxo de entrada `CleanData` . A condição do filtro é a expressão `year <= 1960` .

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

O script de fluxo de dados para esta transformação está no corte abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Próximos passos

Filtrar colunas com a [transformação selecionada](data-flow-select.md)
