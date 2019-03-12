---
title: Mapeamento de transformação de dinâmica de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de dinâmica de fluxo de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569897"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Mapeamento de transformação de dinâmica de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize Pivot no fluxo de dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Essencialmente, pode dinamizar valores de linha em novas colunas (transformar dados em metadados).

![Dinamizar opções](media/data-flow/pivot1.png "dinamizar 1")

## <a name="group-by"></a>Agrupar Por

![Dinamizar opções](media/data-flow/pivot2.png "dinamizar 2")

Em primeiro lugar, defina as colunas que deseja agrupar por para a agregação de pivot. Pode definir mais de 1 coluna aqui com o + início de sessão junto à lista de colunas.

## <a name="pivot-key"></a>Chave de pivot

![Dinamizar opções](media/data-flow/pivot3.png "dinamizar 3")

A chave de pivô é a coluna que ADF será dinâmico da linha a coluna. Por predefinição, cada valor exclusivo no conjunto de dados para este campo será dinâmico a uma coluna. No entanto, pode, opcionalmente, introduza os valores do conjunto de dados que pretende passar para os valores de coluna.

## <a name="pivoted-columns"></a>Colunas articuladas

![Dinamizar opções](media/data-flow/pivot4.png "dinamizar 4")

Por último, irá escolher a agregação de que deseja usar para os valores articulados e como gostaria que as colunas a apresentar na nova projeção de saída da transformação.

(Opcional) Pode definir um padrão de nomenclatura com um prefixo, intermediária e sufixo para ser adicionado a cada novo nome de coluna de entre os valores de linha.

Por exemplo, dinamização "Vendas" por "Região" resultaria em novos valores de coluna de cada valor de vendas, ou seja "25", "50", "1000", etc. No entanto, se definir um valor de prefixo de "Vendas-", cada valor de coluna adicionaria "Vendas-" no início do valor.

![Dinamizar opções](media/data-flow/pivot5.png "dinamizar 5")

Definir a disposição de coluna como "Normal" será agrupar todas as colunas articuladas com os respetivos valores agregados. Definir a disposição de colunas para "Lateral" irá se alternam entre a coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação de que pretende utilizar para os valores de pivot, clique no campo na parte inferior do painel de colunas articuladas. Irá introduzir para o construtor de expressões o fluxo de dados do ADF onde pode criar uma expressão de agregação e forneça um nome de alias descritivo para seus novos valores agregados.

Utilizar a linguagem de expressão de fluxo do ADF dados para descrever as transformações de coluna articulada no construtor de expressões: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Como reintegrado campos originais
> [!NOTE]
> A transformação de Pivot será o projeto apenas as colunas utilizadas na agregação, o agrupamento e a ação de pivot. Se deseja incluir as outras colunas do passo anterior no seu fluxo, utilize um novo ramo do passo anterior e usar o padrão de associação automática para ligar o fluxo com os metadados do original.

## <a name="next-steps"></a>Passos Seguintes

Experimente o [anular dinamização de transformação](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
