---
title: Parametrizar os fluxos de dados de mapeamento
description: Saiba como parametrizar um fluxo de dados de mapeamento a partir de oleodutos de fábrica de dados
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8e88e5e8a9fbe1881959c5183dc01b11ac681bdf
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780402"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizar os fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Os fluxos de dados de mapeamento na Azure Data Factory suportam a utilização de parâmetros. Defina os parâmetros dentro da definição de fluxo de dados e use-os ao longo das suas expressões. Os valores do parâmetro são definidos pelo pipeline de chamada através da atividade executar data flow. Tem três opções para definir os valores nas expressões de atividade de fluxo de dados:

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

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetros de um oleoduto

Uma vez criado um fluxo de dados com parâmetros, pode executá-lo a partir de um pipeline com a Executar Data Flow Activity. Depois de adicionar a atividade à sua tela de pipeline, será apresentado com os parâmetros de fluxo de dados disponíveis no separador **Parâmetros** da atividade.

Ao atribuir valores de parâmetros, pode utilizar a linguagem de expressão do [gasoduto](control-flow-expression-language-functions.md) ou a linguagem de expressão de fluxo de [dados](data-flow-expression-functions.md) com base em tipos de faíscas. Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de gasoduto e de fluxo de dados.

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "Definição de um parâmetro de fluxo de dados")

### <a name="pipeline-expression-parameters"></a>Parâmetros de expressão do gasoduto

Os parâmetros de expressão do gasoduto permitem-lhe referência a variáveis, funções, parâmetros de gasoduto e variáveis semelhantes a outras atividades do pipeline. Quando clicar na **expressão pipeline,** um navegação lateral abre-se permitindo-lhe introduzir uma expressão usando o construtor de expressão.

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-pipeline.png "Definição de um parâmetro de fluxo de dados")

Quando referenciados, os parâmetros do gasoduto são avaliados e, em seguida, o seu valor é usado na linguagem de expressão de fluxo de dados. O tipo de expressão do gasoduto não precisa de corresponder ao tipo de parâmetro de fluxo de dados. 

#### <a name="string-literals-vs-expressions"></a>Cordas literais vs expressões

Ao atribuir um parâmetro de expressão de gasoduto de cadeia de tipo, por padrão as cotações serão adicionadas e o valor será avaliado como um literal. Para ler o valor do parâmetro como expressão de fluxo de dados, verifique a caixa de expressão ao lado do parâmetro.

![Definição de um parâmetro de fluxo de dados](media/data-flow/string-parameter.png "Definição de um parâmetro de fluxo de dados")

Se o parâmetro `stringParam` de fluxo de dados `upper(column1)`se referir a um parâmetro de gasoduto com valor . 

- Se a expressão `$stringParam` for verificada, avalia o valor da coluna1 em todas as maiúsculas.
- Se a expressão não for `$stringParam` verificada (comportamento padrão), avalia para`'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Passando em selos temporais

Na linguagem de expressão do pipeline, variáveis do sistema `pipeline().TriggerTime` tais como e funções `utcNow()` como selos\'de\'tempo de retorno como cordas em formato 'yyy-MM-dd T HH:mm:ss. Sssssssz. Para convertê-los em parâmetros de fluxo de dados de carimbo de `toTimestamp()` tempo tipo, utilize a interpolação da cadeia para incluir a marca de tempo desejada numa função. Por exemplo, para converter o tempo de disparo do `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`gasoduto num parâmetro de fluxo de dados, pode utilizar . 

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-timestamp.png "Definição de um parâmetro de fluxo de dados")

> [!NOTE]
> Os Fluxos de Dados só podem suportar até 3 milissegundos de algarismo. A `left()` função é utilizada aparando os dígitos adicionais.

#### <a name="pipeline-parameter-example"></a>Exemplo de parâmetro de gasoduto

Digamos que tem um `intParam` parâmetro inteiro que está a fazer `@pipeline.parameters.pipelineParam`referência a um parâmetro de gasoduto de tipo String, . 

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-pipeline-2.png "Definição de um parâmetro de fluxo de dados")

`@pipeline.parameters.pipelineParam`é atribuído um `abs(1)` valor de em tempo de execução.

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-pipeline-4.png "Definição de um parâmetro de fluxo de dados")

Quando `$intParam` é referenciado numa expressão como uma coluna `abs(1)` derivada, avaliará o retorno `1`. 

![Definição de um parâmetro de fluxo de dados](media/data-flow/parameter-pipeline-3.png "Definição de um parâmetro de fluxo de dados")

### <a name="data-flow-expression-parameters"></a>Parâmetros de expressão de fluxo de dados

A expressão de fluxo de **dados** selecionada abrirá o construtor de expressão de fluxo de dados. Poderá fazer referência a funções, outros parâmetros e qualquer coluna de esquema definida ao longo do fluxo de dados. Esta expressão será avaliada como é quando referenciada.

> [!NOTE]
> Se passar numa expressão inválida ou se referir uma coluna de esquemas que não existe nessa transformação, o parâmetro avaliará a nula.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando em um nome de coluna como um parâmetro

Um padrão comum é passar em um nome de coluna como um valor de parâmetro. Se a coluna for definida no esquema de fluxo de dados, pode referenciar diretamente como expressão de cadeia. Se a coluna não estiver definida no esquema, utilize a `byName()` função. Lembre-se de lançar a coluna ao seu `toString()`tipo apropriado com uma função de função de fundição como .

Por exemplo, se quiser mapear uma coluna de `columnName`cordas baseada num parâmetro, pode `toString(byName($columnName))`adicionar uma transformação de coluna derivada igual a .

![Passando em um nome de coluna como um parâmetro](media/data-flow/parameterize-column-name.png "Passando em um nome de coluna como um parâmetro")

## <a name="next-steps"></a>Passos seguintes
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controlo](control-flow-expression-language-functions.md)
