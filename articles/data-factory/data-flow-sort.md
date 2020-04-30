---
title: Ordenar transformação no fluxo de dados de mapeamento
description: Fábrica de dados azure mapeia dados classificam a transformação
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606328"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Ordenar transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação de classificação permite-lhe classificar as linhas de entrada no fluxo de dados atuais. Pode escolher colunas individuais e separe-las em ordem ascendente ou descendente.

> [!NOTE]
> Os fluxos de dados de mapeamento são executados em clusters de faíscas que distribuem dados por vários nós e divisórias. Se optar por repartipartição dos seus dados numa transformação subsequente, poderá perder a sua triagem devido à remodelação de dados.

## <a name="configuration"></a>Configuração

![Ordenar configurações](media/data-flow/sort.png "Ordenar")

**Caso insensível:** Quer deseje ou não ignorar caso ao classificar cordas ou campos de texto

**Ordenar apenas dentro das divisórias:** À medida que os fluxos de dados são executados em faísca, cada fluxo de dados é dividido em divisórias. Esta definição classifica os dados apenas dentro das divisórias que chegam em vez de classificar todo o fluxo de dados. 

**Condições de classificação:** Escolha quais as colunas por que está a classificar e em que ordem o tipo acontece. A ordem determina a prioridade de classificação. Escolha se os nulos aparecerão ou não no início ou no fim do fluxo de dados.

### <a name="computed-columns"></a>Colunas computorizadas

Para modificar ou extrair um valor de coluna antes de aplicar o tipo, pairar sobre a coluna e selecionar "coluna computorizada". Isto abrirá o construtor de expressão para criar uma expressão para a operação de tipo em vez de usar um valor de coluna.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Ordenar configurações](media/data-flow/sort.png "Ordenar")

O script de fluxo de dados para a configuração de classificação acima está no código abaixo.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Passos seguintes

Após a triagem, pode querer usar a [Transformação Agregada](data-flow-aggregate.md)
