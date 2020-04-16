---
title: Parametrizar os fluxos de dados de mapeamento
description: Saiba como parametrizar um fluxo de dados de mapeamento a partir de oleodutos de fábrica de dados
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419175"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizar os fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Os fluxos de dados de mapeamento na Azure Data Factory suportam a utilização de parâmetros. Pode definir parâmetros dentro da definição de fluxo de dados, que pode utilizar ao longo das suas expressões. Os valores do parâmetro podem ser definidos pelo gasoduto de chamada através da atividade executar o Fluxo de Dados. Tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

* Utilize a linguagem de expressão de fluxo de controlo de gasodutos para definir um valor dinâmico
* Use a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Utilize esta capacidade para tornar os seus fluxos de dados de fins gerais, flexíveis e reutilizáveis. Pode parametrizar as definições e expressões de fluxo de dados com estes parâmetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Criar parâmetros num fluxo de dados de mapeamento

Para adicionar parâmetros ao fluxo de dados, clique na parte em branco da tela de fluxo de dados para ver as propriedades gerais. No painel de definições, verá um separador chamado **Parâmetro**. Selecione **Novo** para gerar um novo parâmetro. Para cada parâmetro, deve atribuir um nome, selecionar um tipo e definir opcionalmente um valor predefinido.

![Criar parâmetros de Fluxo de Dados](media/data-flow/create-params.png "Criar parâmetros de Fluxo de Dados")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Utilize parâmetros num fluxo de dados de mapeamento 

Os parâmetros podem ser referenciados em qualquer expressão de fluxo de dados. Os parâmetros começam com $ e são imutáveis. Encontrará a lista de parâmetros disponíveis dentro do Expression Builder sob o separador **Parâmetros.**

![Expressão do parâmetro do fluxo de dados](media/data-flow/parameter-expression.png "Expressão do parâmetro do fluxo de dados")

Pode adicionar rapidamente parâmetros adicionais selecionando **novo parâmetro** e especificando o nome e o tipo.

![Expressão do parâmetro do fluxo de dados](media/data-flow/new-parameter-expression.png "Expressão do parâmetro do fluxo de dados")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando em um nome de coluna como um parâmetro

Um padrão comum é passar em um nome de coluna como um valor de parâmetro. Para fazer referência à coluna associada ao `byName()` parâmetro, utilize a função. Lembre-se de lançar a coluna ao seu `toString()`tipo apropriado com uma função de função de fundição como .

Por exemplo, se quiser mapear uma coluna de `columnName`cordas baseada num parâmetro, pode `toString(byName($columnName))`adicionar uma transformação de coluna derivada igual a .

![Passando em um nome de coluna como um parâmetro](media/data-flow/parameterize-column-name.png "Passando em um nome de coluna como um paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetros de um oleoduto

Uma vez criado o seu fluxo de dados com parâmetros, pode executá-lo a partir de um pipeline com a Executar Data Flow Activity. Depois de adicionar a atividade à sua tela de pipeline, será apresentado com os parâmetros de fluxo de dados disponíveis no separador **Parâmetros** da atividade.

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "Definição de um parâmetro de fluxo de dados")

Se o seu tipo de dados de parâmetro for string, quando clicar na caixa de texto para definir valores de parâmetros, pode optar por introduzir um pipeline ou uma expressão de fluxo de dados. Se escolher a expressão do gasoduto, será apresentado com o painel de expressão do gasoduto. Certifique-se de incluir funções de gasoduto `'@{<expression>}'`dentro da sintaxe de interpolação de cordas utilizando, por exemplo:

```'@{pipeline().RunId}'```

Se o seu parâmetro não for de cadeia de tipo, será sempre apresentado ao Construtor de Expressão de Fluxo de Dados. Aqui, pode introduzir qualquer expressão ou valores literais que deseje que correspondam ao tipo de dados do parâmetro. Abaixo estão exemplos de expressão de fluxo de dados e uma corda literal do construtor de expressão:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de gasoduto e de fluxo de dados. 

![Amostra de parâmetros de fluxo de dados](media/data-flow/parameter-example.png "Amostra de parâmetros de fluxo de dados")



## <a name="next-steps"></a>Passos seguintes
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controlo](control-flow-expression-language-functions.md)
