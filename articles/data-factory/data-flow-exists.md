---
title: Transformação do fluxo de dados de mapeamento de Azure Data Factory existe
description: Como verificar as linhas existentes usando o mapeamento de data factory fluxos de dados com a transformação Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029397"
---
# <a name="mapping-data-flow-exists-transformation"></a>Transformação de fluxo de dados de mapeamento existe



A transformação Exists é uma transformação de filtragem de linha que interrompe ou permite que as linhas em seus dados fluam. A transformação Exists é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Após a transformação Exists, as linhas resultantes do fluxo de dados incluirão todas as linhas em que os valores de coluna da origem 1 existem na origem 2 ou não existem na origem 2.

![As configurações existentes](media/data-flow/exists.png "existem 1")

Escolha a segunda fonte para o seu Exists para que o fluxo de dados possa comparar valores do fluxo 1 com o fluxo 2.

Selecione a coluna da origem 1 e da origem 2 cujos valores você deseja verificar para que exista ou não exista.

## <a name="multiple-exists-conditions"></a>Várias condições existentes

Ao lado de cada linha em suas condições de coluna para existir, você encontrará um sinal de + disponível quando passar o mouse sobre a linha de alcance. Isso permitirá que você adicione várias linhas para as condições existentes. Cada condição adicional é um "e".

## <a name="custom-expression"></a>Expressão personalizada

![Existem configurações personalizadas](media/data-flow/exists1.png "existentes")

Você pode clicar em "expressão personalizada" para, em vez disso, criar uma expressão de forma livre como sua condição EXISTS ou NOT-EXISTS. Marcar essa caixa permitirá que você digite sua própria expressão como uma condição.

## <a name="next-steps"></a>Passos seguintes

As transformações semelhantes são [Lookup](data-flow-lookup.md) e [Join](data-flow-join.md).
