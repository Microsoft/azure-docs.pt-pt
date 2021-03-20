---
title: Classificar a transformação no fluxo de dados de mapeamento
description: Transformação do tipo de mapeamento de dados da fábrica de dados Azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81606328"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Classificar a transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação do tipo permite-lhe classificar as linhas de entrada no fluxo de dados atual. Pode escolher colunas individuais e serdená-las em ordem ascendente ou descendente.

> [!NOTE]
> Os fluxos de dados de mapeamento são executados em clusters de faíscas que distribuem dados através de múltiplos nós e divisórias. Se optar por repartir os seus dados numa transformação subsequente, poderá perder a sua triagem devido à remodelação de dados.

## <a name="configuration"></a>Configuração

![Configurações de classificação](media/data-flow/sort.png "Ordenar")

**Caso insensível:** Quer deseje ou não ignorar o caso ao separar os campos de cordas ou de texto

**Ordenar apenas dentro de divisórias:** À medida que os fluxos de dados são executados na faísca, cada fluxo de dados é dividido em divisórias. Esta definição classifica os dados apenas dentro das divisórias recebidas em vez de classificar todo o fluxo de dados. 

**Condições de classificação:** Escolha quais colunas está a selecionar e em que ordem o tipo acontece. A ordem determina a prioridade de triagem. Escolha se os nulos aparecerão ou não no início ou no fim do fluxo de dados.

### <a name="computed-columns"></a>Colunas computadas

Para modificar ou extrair um valor de coluna antes de aplicar o tipo, paire sobre a coluna e selecione "coluna computada". Isto abrirá o construtor de expressão para criar uma expressão para a operação tipo em vez de usar um valor de coluna.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Configurações de classificação](media/data-flow/sort.png "Ordenar")

O script de fluxo de dados para a configuração do tipo acima está no corte de código abaixo.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Passos seguintes

Após a triagem, pode querer usar a [Transformação Agregada](data-flow-aggregate.md)
