---
title: Mapeamento de transformação classificação de fluxo de dados
description: Transformação de classificação de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930214"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory transformações de classificação de fluxo de dados



![Configurações de classificação](media/data-flow/sort.png "Ordenar")

A transformação classificação permite que você classifique as linhas de entrada no fluxo de dados atual. As linhas de saída da transformação classificação, subsequentemente, seguirão as regras de ordenação que você definiu. Você pode escolher colunas individuais e classificá-las ASC ou DEC, usando o indicador de seta ao lado de cada campo. Se você precisar modificar a coluna antes de aplicar a classificação, clique em "colunas computadas" para iniciar o editor de expressão. Isso fornecerá uma oportunidade para criar uma expressão para a operação de classificação em vez de simplesmente aplicar uma coluna para a classificação.

## <a name="case-insensitive"></a>Não sensível a maiúsculas e minúsculas
Você pode ativar "não diferencia maiúsculas de minúsculas" se desejar ignorar maiúsculas e minúsculas ao classificar campos de texto ou cadeia de caracteres.

"Classificar somente dentro de partições" aproveita o particionamento de dados do Spark. Ao classificar os dados de entrada somente dentro de cada partição, os fluxos de dados podem classificar dados particionados em vez de classificar todo o fluxo de dados.

Cada uma das condições de classificação na transformação classificação pode ser reorganizada. Portanto, se você precisar mover uma coluna para cima na precedência de classificação, pegue essa linha com o mouse e mova-a para mais ou para menos na lista de classificação.

Efeitos de particionamento na classificação

O fluxo de dados do ADF é executado em Big Data clusters Spark com dados distribuídos em vários nós e partições. É importante ter isso em mente ao arquitetar seu fluxo de dados se você estiver dependendo da transformação de classificação para manter os dados na mesma ordem. Se você optar por reparticionar seus dados em uma transformação subsequente, poderá perder a classificação devido a esse embaralhando de dados.

## <a name="next-steps"></a>Passos seguintes

Após a classificação, talvez você queira usar a [transformação Agregação](data-flow-aggregate.md)
