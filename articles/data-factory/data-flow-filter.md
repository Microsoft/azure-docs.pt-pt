---
title: Transformação de filtro no fluxo de dados de mapeamento
description: Filtrar linhas usando a transformação do filtro no fluxo de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84112816"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação do Filtro permite a filtragem de linha com base numa condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação do filtro é semelhante a uma cláusula WHERE em SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuração

Utilize o construtor de expressão de fluxo de dados para introduzir uma expressão para a condição do filtro. Para abrir o construtor de expressão, clique na caixa azul. A condição do filtro deve ser do tipo boolean. Para obter mais informações sobre como criar uma expressão, consulte a documentação do [construtor de expressão.](concepts-data-flow-expression-builder.md)

![Transformação de filtros](media/data-flow/filter1.png "Transformação de filtros")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação de filtro nomeada `FilterBefore1960` que toma em fluxo de entrada `CleanData` . A condição do filtro é a expressão `year <= 1960` .

No Data Factory UX, esta transformação parece a imagem abaixo:

![Transformação de filtros](media/data-flow/filter1.png "Transformação de filtros")

O roteiro do fluxo de dados para esta transformação está no snippet abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Passos seguintes

Filtrar colunas com a [transformação selecionada](data-flow-select.md)
