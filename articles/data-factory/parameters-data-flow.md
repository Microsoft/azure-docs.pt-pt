---
title: Parametrizar os fluxos de dados de mapeamento
description: Saiba como Parametrizar um fluxo de dados de mapeamento de pipelines data factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c717d9ab44493d15589030073cd2ab260ef52e1c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760220"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizar os fluxos de dados de mapeamento

O mapeamento de fluxos de dados no Azure Data Factory dá suporte ao uso de parâmetros. Você pode definir parâmetros dentro de sua definição de fluxo de dados, que você pode usar em todas as expressões. Os valores de parâmetro podem ser definidos pelo pipeline de chamada por meio da atividade executar fluxo de dados. Você tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

* Usar a linguagem de expressão do fluxo de controle de pipeline para definir um valor dinâmico
* Usar a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Use essa capacidade para tornar seus fluxos de dados de uso geral, flexíveis e reutilizáveis. Você pode parametrizar as configurações de fluxo de dados e expressões com esses parâmetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Criar parâmetros em um fluxo de dados de mapeamento

Para adicionar parâmetros ao fluxo de dados, clique na parte em branco da tela fluxo de dados para ver as propriedades gerais. No painel configurações, você verá uma guia chamada **parâmetro**. Selecione **novo** para gerar um novo parâmetro. Para cada parâmetro, você deve atribuir um nome, selecionar um tipo e, opcionalmente, definir um valor padrão.

![Criar parâmetros de fluxo de dados](media/data-flow/create-params.png "Criar parâmetros de fluxo de dados")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Usar parâmetros em um fluxo de dados de mapeamento 

Os parâmetros podem ser referenciados em qualquer expressão de fluxo de dados. Os parâmetros começam com $ e são imutáveis. Você encontrará a lista de parâmetros disponíveis no construtor de expressões, na guia **parâmetros** .

![Expressão de parâmetro de fluxo de dados](media/data-flow/parameter-expression.png "Expressão de parâmetro de fluxo de dados")

Você pode adicionar parâmetros adicionais rapidamente selecionando **novo parâmetro** e especificando o nome e o tipo.

![Expressão de parâmetro de fluxo de dados](media/data-flow/new-parameter-expression.png "Expressão de parâmetro de fluxo de dados")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando um nome de coluna como um parâmetro

Um padrão comum é passar um nome de coluna como um valor de parâmetro. Para fazer referência à coluna associada ao parâmetro, use a função `byName()`. Lembre-se de converter a coluna em seu tipo apropriado com uma função de conversão, como `toString()`.

Por exemplo, se você quisesse mapear uma coluna de cadeia de caracteres com base em um parâmetro `columnName`, poderá adicionar uma transformação coluna derivada igual a `toString(byName($columnName))`.

![Passando um nome de coluna como um parâmetro](media/data-flow/parameterize-column-name.png "Passando um nome de coluna como um paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetro de um pipeline

Depois de criar o fluxo de dados com parâmetros, você pode executá-lo de um pipeline com a atividade executar fluxo de dados. Depois de adicionar a atividade à tela do pipeline, você verá os parâmetros de fluxo de dados disponíveis na guia **parâmetros** da atividade.

![Definindo um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "Definindo um parâmetro de fluxo de dados")

Se o tipo de dados do parâmetro for cadeia de caracteres, quando você clicar na caixa de texto para definir valores de parâmetro, poderá optar por inserir um pipeline ou uma expressão de fluxo de dados. Se você escolher expressão de pipeline, verá o painel expressão de pipeline. Certifique-se de incluir funções de pipeline dentro da sintaxe de interpolação de cadeia de caracteres usando `'@{<expression>}'`, por exemplo:

```'@{pipeline().RunId}'```

Se o parâmetro não for do tipo cadeia de caracteres, você sempre será apresentado com o construtor de expressões de fluxo de dados. Aqui, você pode inserir qualquer expressão ou valores literais que deseja que correspondam ao tipo de dados do parâmetro. Abaixo estão exemplos de expressão de fluxo de dados e uma cadeia de caracteres literal do construtor de expressões:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de fluxo de dados e pipeline. 

![Exemplo de parâmetros de fluxo de dados](media/data-flow/parameter-example.png "Exemplo de parâmetros de fluxo de dados")



## <a name="next-steps"></a>Passos seguintes
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
