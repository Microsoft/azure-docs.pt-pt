---
title: Derivados a transformação de coluna no mapeamento de fluxo de dados - Azure Data Factory | Documentos da Microsoft
description: Saiba como transformar dados em escala no Azure Data Factory com a mapeamento de dados de fluxo transformação Derived Column.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312225"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de coluna derivada no mapeamento de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilize a transformação Derived Column para gerar novas colunas do fluxo de dados ou para modificar os campos existentes.

## <a name="derived-column-settings"></a>Definições de coluna derivadas

Para substituir uma coluna existente, selecione-o através da lista pendente de coluna. Caso contrário, utilize o campo de seleção de coluna como uma caixa de texto e escreva o nome da nova coluna. Para criar a expressão da coluna derivada, clique na caixa 'Introduzir a expressão' para abrir o [construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md).

![Derivado definições da coluna](media/data-flow/dc1.png "derivado de definições de coluna")

Para adicionar colunas derivadas adicionais, coloque o cursor sobre um existente derivada coluna e clique em "+". Em seguida, escolha 'Adicionar coluna' ou "Padrão da coluna de adicionar". Padrões de coluna podem ser útil se os nomes de coluna são variável a partir de origens. Para obter mais informações, consulte [coluna padrões](concepts-data-flow-column-pattern.md).

![Novo derivado a seleção da coluna](media/data-flow/columnpattern.png "New derivado a seleção de coluna")

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [fluxo de dados de mapeamento de linguagem de expressão](data-flow-expression-functions.md).
