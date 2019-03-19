---
title: Mapeamento de transformação de classificação de fluxo de dados de fábrica de dados do Azure
description: Mapeamento de transformação de classificação de dados de fábrica de dados do Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898759"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Transformações de tipo de fluxo de dados de fábrica de dados do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Definições de ordenar](media/data-flow/sort.png "ordenação")

A transformação Sort permite-lhe classificar as linhas de entrada no fluxo de dados atual. As linhas de saída da transformação Sort, em seguida, seguir as regras de ordenação que definir. Pode escolher colunas individuais e classificá-los ASC ou DEC, usando o indicador de seta junto a cada campo. Se precisar de modificar a coluna antes de aplicar a classificação, clique em "Colunas calculadas" para iniciar o editor de expressões. Isso fornecerá a oportunidade de criar uma expressão para a operação de ordenação em vez de simplesmente aplicar uma coluna para a classificação.

## <a name="case-insensitive"></a>Não sensível a maiúsculas e minúsculas
Pode ativar o "Sensível a maiúsculas e minúsculas" se desejar ignorar maiúsculas / minúsculas ao ordenar os campos de cadeia de caracteres ou texto.

"Ordena apenas dentro de partições" tira partido da criação de partições de dados do Spark. A ordenação de dados de entrada apenas dentro de cada partição, os dados fluem pode ordenar os dados particionados em vez de classificação de fluxo de dados inteiro.

Todas as condições de classificação na transformação Sort podem ser reorganizadas. Então, se precisar de mover uma coluna maior na precedência de ordenação, pegar essa linha com o mouse e mova-superior ou inferior na lista de classificação.

Os efeitos exercidos sobre ordenação de criação de partições

O fluxo de dados do ADF é executado em grandes volumes de dados os clusters do Spark com dados distribuídos em vários nós e partições. É importante manter isso em mente ao desenvolver a arquitetura de seu fluxo de dados se dependem de transformação Sort para manter os dados nessa mesma ordem. Se optar por criar novas partições de seus dados numa transformação subsequente, pode perder a sua classificação devido a essa reshuffling de dados.

## <a name="next-steps"></a>Passos Seguintes

Após a classificação, talvez queira usar o [transformação de agregação](data-flow-aggregate.md)
