---
title: Transformação de filtro no fluxo de dados de mapeamento
description: Filtrar as filas utilizando a transformação do filtro no fluxo de dados de mapeamento da Fábrica de Dados azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930358"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

A transformação do Filtro permite a filtragem da linha com base numa condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação do filtro é semelhante a uma cláusula WHERE no SQL.

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

O exemplo abaixo é `FilterBefore1960` uma transformação de `CleanData`filtro chamada que acolhe fluxo de entrada . A condição do `year <= 1960`filtro é a expressão .

Na Fábrica de Dados UX, esta transformação parece a imagem abaixo:

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

O script de fluxo de dados para esta transformação está no corte abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Passos seguintes

Filtrar colunas com a [transformação selecionada](data-flow-select.md)
