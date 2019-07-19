---
title: Padrões de coluna de fluxo de dados de mapeamento de Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando Azure Data Factory padrões de coluna no mapeamento de fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: d24988dfd5cbaf20e92c5afbbc39dc0c78e3ef6a
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314879"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Padrões de coluna de fluxos de dados de mapeamento de data factory do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Vários Azure Data Factory transformações de fluxo de dados dão suporte à ideia de "padrões de colunas" para que você possa criar colunas de modelo com base em padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no construtor de expressões para definir padrões para corresponder colunas para transformação em vez de exigir nomes de campo específicos e exatos. Padrões são úteis se os campos de origem de entrada são alterados com frequência, especialmente no caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Essa condição às vezes é chamada de "descompasso de esquema".

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

Padrões de coluna são úteis para lidar com cenários de descompasso de esquema, bem como cenários gerais. É bom para condições em que você não possa saber totalmente cada nome de coluna. Você pode fazer a correspondência de padrão com o nome da coluna e o tipo de dados da coluna e criar uma expressão para transformação que executará essa operação em qualquer `name` campo no fluxo de dados que corresponda aos seus  &  `type` padrões.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar padrão de coluna". Padrões de coluna permitem padrões de correspondência de coluna de descompasso de esquema.

Ao criar padrões de coluna de modelo `$$` , use na expressão para representar uma referência a cada campo correspondente do fluxo de dados de entrada.

Se você optar por usar uma das funções Regex do Expression Builder, poderá, posteriormente, usar $1, $2, $3... para fazer referência aos subpadrões correspondentes de sua expressão Regex.

Um exemplo de cenário de padrão de coluna é usar SUM com uma série de campos de entrada. Os cálculos da soma de agregação estão na transformação Agregação. Em seguida, você pode usar SUM em cada correspondência de tipos de campo que correspondem a "Integer" e, em seguida, use $ $ para fazer referência a cada correspondência em sua expressão.

## <a name="match-columns"></a>Colunas de correspondência
![tipos de padrões de coluna](media/data-flow/pattern2.png "Tipos de padrões")

Para criar padrões com base em colunas, você pode corresponder ao nome da coluna, ao tipo, ao fluxo ou à posição e usar qualquer combinação delas com funções de expressão e expressões regulares.

![posição da coluna](media/data-flow/position.png "Posição da coluna")

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [linguagem de expressão](http://aka.ms/dataflowexpressions) de fluxo de dados de mapeamento do ADF para transformações de dados
