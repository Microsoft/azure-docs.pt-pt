---
title: Transformação coluna derivada no fluxo de dados de mapeamento-Azure Data Factory | Microsoft Docs
description: Saiba como transformar dados em escala em Azure Data Factory com a transformação coluna derivada de fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026843"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação coluna derivada no fluxo de dados de mapeamento

Use a transformação coluna derivada para gerar novas colunas no fluxo de dados ou para modificar os campos existentes.

## <a name="derived-column-settings"></a>Configurações de coluna derivadas

Para substituir uma coluna existente, selecione-a por meio da lista suspensa coluna. Caso contrário, use o campo seleção de coluna como uma caixa de texto e digite o nome da nova coluna. Para criar a expressão da coluna derivada, clique na caixa ' Inserir expressão ' para abrir o construtor de [expressões de fluxo de dados](concepts-data-flow-expression-builder.md).

Configurações da ![coluna derivada configurações]de(media/data-flow/dc1.png "coluna derivadas")

Para adicionar mais colunas derivadas, passe o mouse sobre uma coluna derivada existente e clique em ' + '. Em seguida, escolha ' adicionar coluna ' ou ' Adicionar padrão de coluna '. Padrões de coluna poderão ser úteis se os nomes de coluna forem variáveis de suas fontes. Para obter mais informações, consulte [padrões de coluna](concepts-data-flow-column-pattern.md).

![Nova]seleção de coluna derivada seleção de(media/data-flow/columnpattern.png "nova coluna derivada")

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [linguagem de expressão de fluxo de dados de mapeamento](data-flow-expression-functions.md).
