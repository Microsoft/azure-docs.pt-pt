---
title: Padrões de coluna de fluxo de dados de mapeamento de Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando Azure Data Factory padrões de coluna no mapeamento de fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8f1fa6f7823c643278e52ffd0faa1c0ce4972ef8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640249"
---
# <a name="mapping-data-flows-column-patterns"></a>Mapeando padrões de coluna de fluxos de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Vários Azure Data Factory transformações de fluxo de dados dão suporte à ideia de "padrões de colunas" para que você possa criar colunas de modelo com base em padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no construtor de expressões para definir padrões para corresponder colunas para transformação em vez de exigir nomes de campo específicos e exatos. Padrões são úteis se os campos de origem de entrada são alterados com frequência, especialmente no caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Essa condição às vezes é chamada de "descompasso de esquema".

Essa manipulação de "esquema flexível" atualmente é encontrada na coluna derivada e nas transformações agregadas, bem como nas transformações Select e Sink como "mapeamento baseado em regras".

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

## <a name="column-patterns"></a>Padrões de coluna
Padrões de coluna são úteis para lidar com cenários de descompasso de esquema, bem como cenários gerais. É bom para condições em que você não possa saber totalmente cada nome de coluna. Você pode fazer a correspondência de padrão com o nome da coluna e o tipo de dados da coluna e criar uma expressão para transformação que executará essa operação em qualquer `name` campo no fluxo de dados que corresponda aos seus  &  `type` padrões.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar padrão de coluna". Padrões de coluna permitem padrões de correspondência de coluna de descompasso de esquema.

Ao criar padrões de coluna de modelo `$$` , use na expressão para representar uma referência a cada campo correspondente do fluxo de dados de entrada.

Se você optar por usar uma das funções Regex do Expression Builder, poderá, posteriormente, usar $1, $2, $3... para fazer referência aos subpadrões correspondentes de sua expressão Regex.

Um exemplo de cenário de padrão de coluna é usar SUM com uma série de campos de entrada. Os cálculos da soma de agregação estão na transformação Agregação. Em seguida, você pode usar SUM em cada correspondência de tipos de campo que correspondem a "Integer" e, em seguida, use $ $ para fazer referência a cada correspondência em sua expressão.

## <a name="match-columns"></a>Colunas de correspondência
![tipos de padrões de coluna](media/data-flow/pattern2.png "Tipos de padrões")

Para criar padrões com base em colunas, você pode corresponder ao nome da coluna, ao tipo, ao fluxo ou à posição e usar qualquer combinação delas com funções de expressão e expressões regulares.

![posição da coluna](media/data-flow/position.png "Posição da coluna")

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao mapear colunas na origem e selecionar transformações, você terá a opção de escolher "mapeamento fixo" ou "mapeamento baseado em regras". Quando você conhece o esquema de seus dados e espera colunas específicas do conjunto do dados de origem que sempre correspondem a nomes estáticos específicos, você pode usar o mapeamento fixo. Mas quando você estiver trabalhando com esquemas flexíveis, use o mapeamento baseado em regras. Você poderá criar uma correspondência de padrões usando as regras descritas acima.

![mapeamento baseado em regras](media/data-flow/rule2.png "Mapeamento baseado em regras")

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a [linguagem de expressão](http://aka.ms/dataflowexpressions) de fluxo de dados de mapeamento do ADF para transformações de dados
* Usar padrões de coluna na [transformação do coletor](data-flow-sink.md) e [selecionar a transformação](data-flow-select.md) com mapeamento baseado em regras