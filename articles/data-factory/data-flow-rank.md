---
title: Classificação transformação no fluxo de dados de mapeamento
description: Como utilizar a transformação do fluxo de fluxo de dados de mapeamento da Azure Data Factory gera uma coluna de ranking
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: 8584d1b64191cc5258c6eeeef9ae4125bf1a2c65
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044739"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Classificação transformação no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação da classificação para gerar um ranking ordenado com base em condições de classificação especificadas pelo utilizador. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuração

![Definições de classificação](media/data-flow/rank-configuration.png "Definições de classificação")

**Caso insensível:** Se uma coluna de tipo for de tipo de cadeia, o caso será contabilizado no ranking. 

**Denso:** Se ativado, a coluna de classificação será densa classificada. Cada contagem de classificações será um número consecutivo e os valores de classificação não serão ignorados após o empate.

**Coluna de classificação:** O nome da coluna de patente gerada. Esta coluna será do tipo longo.

**Condições de classificação:** Escolha quais colunas está a selecionar e em que ordem o tipo acontece. A ordem determina a prioridade de triagem.

A configuração acima pega nos dados do basquetebol e cria uma coluna de classificação chamada 'pointsRanking'. A linha com o valor mais elevado da coluna *PTS* terá um valor de *pontosSo de* 1.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Definições de classificação](media/data-flow/rank-configuration.png "Definições de classificação")

O script de fluxo de dados para a configuração do ranking acima está no seguinte corte de código.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Passos seguintes

Filtrar linhas com base nos valores de classificação utilizando a [transformação](data-flow-filter.md)do filtro .
