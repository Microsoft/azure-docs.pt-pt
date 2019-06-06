---
title: Mapeamento de padrões de coluna de fluxo de dados de fábrica de dados do Azure
description: Saiba como utilizar padrões de coluna de fábrica de dados do Azure no mapeamento de fluxo de dados para criar padrões de modelo generalizado para transformar os campos num fluxo de dados independentemente dos metadados de esquema subjacente
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430765"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Padrões de coluna de fluxos de dados de mapeamento de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Várias transformações de fluxo de dados do Azure Data Factory suportam a idéia de "Padrões de colunas" para que possa criar colunas de modelo com base nos padrões em vez de nomes de coluna hard-coded. Pode utilizar esta funcionalidade dentro do construtor de expressões para definir padrões de acordo com colunas para transformação em vez de exigir que os nomes de campos exatos e específico. Padrões são úteis se a entrada campos de origem alterados com frequência, especialmente no caso de alterar as colunas nos ficheiros de texto ou bases de dados NoSQL. Esta condição é por vezes referida como "Descompassos de esquema".

![padrões de coluna](media/data-flow/columnpattern2.png "padrões de coluna")

Padrões de coluna são úteis para manipular os cenários de desvios de esquema, bem como cenários gerais. É bom para condições em que não é possível saber totalmente cada nome de coluna. Pode padrão de correspondência com o nome da coluna e dados da coluna, escreva e criar uma expressão de transformação que irá executar essa operação em relação a qualquer campo no fluxo de dados que corresponde à sua `name`  &  `type` padrões.

Ao adicionar uma expressão para uma transformação que aceita padrões, escolha "Adicionar coluna padrão". Coluna padrões permite que os padrões do esquema descompassos coluna correspondente.

Ao criar padrões de coluna de modelo, utilize `$$` na expressão para representar uma referência a cada campo de correspondência do fluxo de dados de entrada.

Se optar por utilizar uma das funções de regex Expression Builder, pode, em seguida, utilizar US $1, 2 $, US $3... para fazer referência as subpadrões combinados a partir de sua expressão regex.

Um exemplo de cenário de coluna padrão é através da soma com uma série de campos de entrada. Os cálculos de soma de agregação no agregado são transformação. Em seguida, pode usar soma em cada correspondência de tipos de campo que correspondem aos "integer" e, em seguida, utilize $$ para cada correspondência na sua expressão de referência.
