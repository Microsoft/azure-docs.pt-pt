---
title: Mapeamento de padrões de coluna de fluxo de dados de fábrica de dados do Azure
description: Saiba como utilizar padrões de coluna de fábrica de dados do Azure no mapeamento de fluxo de dados para criar padrões de modelo generalizado para transformar os campos num fluxo de dados independentemente dos metadados de esquema subjacente
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5131329f6675bc86374f5a5c081e0aaa7d36c0fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155245"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Padrões de coluna de fluxos de dados de mapeamento de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Várias transformações de fluxo de dados do Azure Data Factory suportam a idéia de "Padrões de colunas" para que possa criar colunas de modelo com base nos padrões em vez de nomes de coluna hard-coded. Pode utilizar esta funcionalidade dentro do construtor de expressões para definir padrões de acordo com colunas para transformação em vez de exigir xact, nomes de campos específicos. Padrões são úteis se a entrada campos de origem alterados com frequência, especialmente no caso de alterar as colunas nos ficheiros de texto ou bases de dados NoSQL. Esta condição é por vezes referida como "Descompassos de esquema".

![padrões de coluna](media/data-flow/columnpattern2.png "padrões de coluna")

Padrões de coluna são úteis para manipular os cenários de desvios de esquema, bem como cenários gerais. É bom para condições em que não é possível saber totalmente cada nome de coluna. Pode padrão de correspondência com o nome da coluna e dados da coluna, escreva e criar uma expressão de transformação que irá executar essa operação em relação a qualquer campo no fluxo de dados que corresponde à sua `name`  &  `type` padrões.

Ao adicionar uma expressão para uma transformação que aceita padrões, escolha "Adicionar coluna padrão". Coluna padrões permite que os padrões do esquema descompassos coluna correspondente.

Ao criar padrões de coluna de modelo, utilize `$$` na expressão para representar uma referência a cada campo de correspondência do fluxo de dados de entrada.

Se optar por utilizar uma das funções de regex Expression Builder, pode, em seguida, utilizar US $1, 2 $, US $3 para fazer referência as subpatterns combinados a partir de sua expressão regex.

Um exemplo de cenário de coluna padrão é através da soma com uma série de campos de entrada. Os cálculos de soma de agregação no agregado são transformação. Pode somar, em seguida, em cada correspondência de tipos de campo essa correspondência "integer" e, em seguida, utilize $$ para cada correspondência na sua expressão de referência.
