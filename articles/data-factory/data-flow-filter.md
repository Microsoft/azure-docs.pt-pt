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
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413736"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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
