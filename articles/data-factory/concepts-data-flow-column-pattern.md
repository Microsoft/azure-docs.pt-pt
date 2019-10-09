---
title: Padrões de coluna de fluxo de dados de mapeamento de Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando Azure Data Factory padrões de coluna no mapeamento de fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 535656f315f65ffb7aa241618fe9e73b8246b71f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027859"
---
# <a name="mapping-data-flows-column-patterns"></a>Mapeando padrões de coluna de fluxos de dados



Vários Azure Data Factory transformações de fluxo de dados dão suporte à ideia de "padrões de colunas" para que você possa criar colunas de modelo com base em padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no construtor de expressões para definir padrões para corresponder colunas para transformação em vez de exigir nomes de campo específicos e exatos. Padrões são úteis se os campos de origem de entrada são alterados com frequência, especialmente no caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Essa condição às vezes é chamada de "descompasso de esquema".

Essa manipulação de "esquema flexível" atualmente é encontrada na coluna derivada e nas transformações agregadas, bem como nas transformações Select e Sink como "mapeamento baseado em regras".

![](media/data-flow/columnpattern2.png "padrões de coluna") padrões de coluna

## <a name="column-patterns"></a>Padrões de coluna
Padrões de coluna são úteis para lidar com cenários de descompasso de esquema, bem como cenários gerais. É bom para condições em que você não possa saber totalmente cada nome de coluna. Você pode fazer a correspondência de padrão com o nome da coluna e o tipo de dados da coluna e criar uma expressão para transformação que executará essa operação em qualquer campo no fluxo de dados que corresponda aos padrões `name` @ no__t-1 @ no__t-2.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar padrão de coluna". Padrões de coluna permitem padrões de correspondência de coluna de descompasso de esquema.

Ao criar padrões de coluna de modelo, use `$$` na expressão para representar uma referência a cada campo correspondente do fluxo de dados de entrada.

Se você optar por usar uma das funções Regex do Expression Builder, poderá, posteriormente, usar $1, $2, $3... para fazer referência aos subpadrões correspondentes de sua expressão Regex.

Um exemplo de cenário de padrão de coluna é usar SUM com uma série de campos de entrada. Os cálculos da soma de agregação estão na transformação Agregação. Em seguida, você pode usar SUM em cada correspondência de tipos de campo que correspondem a "Integer" e, em seguida, use $ $ para fazer referência a cada correspondência em sua expressão.

## <a name="match-columns"></a>Colunas de correspondência
![](media/data-flow/pattern2.png "tipos") de padrões de coluna tipos padrão

Para criar padrões com base em colunas, você pode corresponder ao nome da coluna, ao tipo, ao fluxo ou à posição e usar qualquer combinação delas com funções de expressão e expressões regulares.

![](media/data-flow/position.png "posição da coluna") de posição da coluna

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao mapear colunas na origem e selecionar transformações, você terá a opção de escolher "mapeamento fixo" ou "mapeamento baseado em regras". Quando você conhece o esquema de seus dados e espera colunas específicas do conjunto do dados de origem que sempre correspondem a nomes estáticos específicos, você pode usar o mapeamento fixo. Mas quando você estiver trabalhando com esquemas flexíveis, use o mapeamento baseado em regras. Você poderá criar uma correspondência de padrões usando as regras descritas acima.

![](media/data-flow/rule2.png "mapeamento") baseado em regra de mapeamento baseado em regra

Crie suas regras usando o construtor de expressões. Suas expressões retornarão um valor booliano para corresponder colunas (true) ou excluir colunas (false).

## <a name="pattern-matching-special-columns"></a>Padrões de correspondência de colunas especiais

* `$$` será convertido para o nome de cada correspondência no tempo de design no modo de depuração e na execução em tempo de execução
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo ou transformação em seu fluxo
* `position` é a posição ordinal das colunas em seu fluxo de dados

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [linguagem de expressão](https://aka.ms/dataflowexpressions) de fluxo de dados de mapeamento do ADF para transformações de dados
* Usar padrões de coluna na [transformação do coletor](data-flow-sink.md) e [selecionar a transformação](data-flow-select.md) com mapeamento baseado em regras
