---
title: Mapeamento de fluxo de dados do Azure Data Factory derivados a transformação de coluna
description: Como transformar dados em escala com o Azure dados fábrica de mapeamento de fluxo derivado coluna transformação de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917568"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mapeamento de fluxo de dados derivados a transformação de coluna

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize a transformação Derived Column para gerar novas colunas do fluxo de dados ou para modificar os campos existentes.

![derivar coluna](media/data-flow/dc1.png "Derived Column")

Pode efetuar várias ações de coluna derivada numa transformação Derived Column única. Clique em "Adicionar coluna" para transformar a mais de 1 coluna no passo único de transformação.

No campo de coluna, selecione uma coluna existente para substituir com um novo valor derivado ou clique em "Criar nova coluna" para gerar uma nova coluna com o valor derivado recentemente.

A caixa de texto expressão irá abrir o construtor de expressões onde pode criar a expressão para colunas derivadas, com as funções de expressão.

## <a name="column-patterns"></a>Padrões de coluna

Se os nomes de coluna são variável a partir de origens, pode querer criar transformações dentro da coluna derivada usando padrões de coluna em vez de usar com o nome colunas. Consulte a [Descompassos de esquema](concepts-data-flow-schema-drift.md) artigo para obter mais detalhes.

![padrão da coluna](media/data-flow/columnpattern.png "padrões de coluna")

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [linguagem de expressão do Data Factory para transformações](https://aka.ms/dataflowexpressions) e o [construtor de expressões](concepts-data-flow-expression-builder.md)
