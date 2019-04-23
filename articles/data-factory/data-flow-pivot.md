---
title: Mapeamento de transformação de dinâmica de fluxo de dados de fábrica de dados do Azure
description: Dados de dinâmica de linhas para colunas com o Azure Data Factory mapeamento fluxo Pivot transformação de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794368"
---
# <a name="azure-data-factory-pivot-transformation"></a>Transformação de dinâmica de fábrica de dados do Azure
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize Pivot no fluxo de dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Essencialmente, pode dinamizar valores de linha em novas colunas (transformar dados em metadados).

![Dinamizar opções](media/data-flow/pivot1.png "dinamizar 1")

## <a name="group-by"></a>Agrupar por

![Dinamizar opções](media/data-flow/pivot2.png "dinamizar 2")

Em primeiro lugar, defina as colunas que deseja agrupar por para a agregação de pivot. Pode definir mais de 1 coluna aqui com o + início de sessão junto à lista de colunas.

## <a name="pivot-key"></a>Chave de pivot

![Dinamizar opções](media/data-flow/pivot3.png "dinamizar 3")

A chave de pivô é a coluna que ADF será dinâmico da linha a coluna. Por predefinição, cada valor exclusivo no conjunto de dados para este campo será dinâmico a uma coluna. No entanto, pode, opcionalmente, introduza os valores do conjunto de dados que pretende passar para os valores de coluna. Esta é a coluna que irão determinar as novas colunas que serão criadas.

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

## <a name="pivot-metadata"></a>Metadados de pivot

A transformação de Pivot produzirá novos nomes de coluna são dinâmicos com base nos seus dados de entrada. A chave de Pivot produz os valores para cada novo nome de coluna. Se não especificar valores individuais e deseja criar nomes de coluna dinâmica para cada valor exclusivo na sua chave de Pivot, em seguida, a interface do Usuário não serão apresentados os metadados no Inspect e não haverá nenhuma propagação de coluna para a transformação de Sink. Se definir valores para a chave de Pivot, em seguida, ADF pode determinar os novos nomes de coluna e esses nomes de coluna serão disponível no Inspect e mapeamento de Sink.

### <a name="landing-new-columns-in-sink"></a>Novas colunas de destino no Sink

Mesmo com nomes de coluna dinâmica no Pivot, ainda pode sink seus novos nomes das colunas e os valores em seu arquivo de destino. Basta defina "Permitir Descompassos de esquema" como nas definições do Sink. Não verá os novos nomes dinâmicos em seus metadados da coluna, mas a opção de desvios de esquema permite-lhe direcionado para os dados.

### <a name="view-metadata-in-design-mode"></a>Ver metadados no modo de design

Se quer ver os novos nomes de coluna como metadados no Inspect e quiser ver as colunas propagar explicitamente para a transformação de Sink, em seguida, defina os valores explícitos no separador Pivot chave.

### <a name="how-to-rejoin-original-fields"></a>Como reintegrado campos originais
A transformação de Pivot será o projeto apenas as colunas utilizadas na agregação, o agrupamento e a ação de pivot. Se deseja incluir as outras colunas do passo anterior no seu fluxo, utilize um novo ramo do passo anterior e usar o padrão de associação automática para ligar o fluxo com os metadados do original.

## <a name="next-steps"></a>Passos Seguintes

Experimente o [anular dinamização de transformação](data-flow-unpivot.md) para transformar valores de coluna em valores de linha. 
