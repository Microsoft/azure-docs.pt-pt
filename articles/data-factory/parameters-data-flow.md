---
title: Parametrizar os fluxos de dados de mapeamento
description: Saiba como parametrizar um fluxo de dados de mapeamento a partir de oleodutos de fábrica de dados
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725715"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizar os fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Os fluxos de dados de mapeamento na Azure Data Factory e na Azure Synapse Analytics suportam a utilização de parâmetros. Defina parâmetros dentro da definição de fluxo de dados e use-os ao longo das suas expressões. Os valores dos parâmetros são definidos pelo pipeline de chamada através da atividade Executar fluxo de dados. Tem três opções para definir os valores nas expressões de atividade do fluxo de dados:

* Utilize a linguagem de expressão de fluxo de controlo do gasoduto para definir um valor dinâmico
* Utilize a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Use qualquer linguagem de expressão para definir um valor literal estático

Utilize esta capacidade para tornar os seus dados fluídos de uso geral, flexíveis e reutilizáveis. Pode parametrizar as definições e expressões do fluxo de dados com estes parâmetros.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Criar parâmetros num fluxo de dados de mapeamento

Para adicionar parâmetros ao fluxo de dados, clique na parte em branco da tela de fluxo de dados para ver as propriedades gerais. No painel de definições, verá um separador chamado **Parâmetro**. Selecione **Novo** para gerar um novo parâmetro. Para cada parâmetro, deve atribuir um nome, selecionar um tipo e definir opcionalmente um valor predefinido.

![Criar parâmetros de fluxo de dados](media/data-flow/create-params.png "Criar parâmetros de fluxo de dados")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Use parâmetros num fluxo de dados de mapeamento 

Os parâmetros podem ser referenciados em qualquer expressão de fluxo de dados. Os parâmetros começam com $$ são imutáveis. Encontrará a lista de parâmetros disponíveis no interior do Porta-voz do Construtor de Expressão no **separador Parâmetros.**

![A screenshot mostra os parâmetros disponíveis no separador Parâmetros.](media/data-flow/parameter-expression.png "Expressão do parâmetro do fluxo de dados")

Pode adicionar rapidamente parâmetros adicionais selecionando **novo parâmetro** e especificando o nome e o tipo.

![A screenshot mostra os parâmetros no separador Parâmetros com novos parâmetros adicionados.](media/data-flow/new-parameter-expression.png "Expressão do parâmetro do fluxo de dados")

## <a name="assign-parameter-values-from-a-pipeline"></a>Atribuir valores de parâmetros a partir de um oleoduto

Uma vez criado um fluxo de dados com parâmetros, pode executá-lo a partir de um pipeline com a Atividade de Fluxo de Dados executar. Depois de adicionar a atividade à sua tela de pipeline, será apresentado com os parâmetros de fluxo de dados disponíveis no **separador Parâmetros** da atividade.

Ao atribuir valores de parâmetros, pode utilizar a linguagem de expressão do [gasoduto](control-flow-expression-language-functions.md) ou a linguagem de expressão do [fluxo de dados](data-flow-expression-functions.md) com base em tipos de faíscas. Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de pipeline e fluxo de dados.

![O screenshot mostra o separador Parâmetros com a expressão Data Flow selecionada para o valor do myparam.](media/data-flow/parameter-assign.png "Definição de um parâmetro de fluxo de dados")

### <a name="pipeline-expression-parameters"></a>Parâmetros de expressão do gasoduto

Os parâmetros de expressão do gasoduto permitem-lhe referenciar variáveis, funções, parâmetros de pipeline e variáveis semelhantes a outras atividades de pipeline. Ao clicar na **expressão Pipeline,** abre-se um navegador lateral que lhe permite introduzir uma expressão utilizando o construtor de expressão.

![A screenshot mostra o painel de construtores de expressão.](media/data-flow/parameter-pipeline.png "Definição de um parâmetro de fluxo de dados")

Quando referenciados, os parâmetros do pipeline são avaliados e, em seguida, o seu valor é usado na linguagem de expressão do fluxo de dados. O tipo de expressão do gasoduto não precisa de corresponder ao tipo de parâmetro de fluxo de dados. 

#### <a name="string-literals-vs-expressions"></a>Cadeias literais vs expressões

Ao atribuir um parâmetro de expressão de pipeline de cadeia tipo, por padrão serão adicionados as cotações e o valor será avaliado como literal. Para ler o valor do parâmetro como uma expressão de fluxo de dados, verifique a caixa de expressão ao lado do parâmetro.

![A screenshot mostra o painel de parâmetros de fluxo de dados Expressão selecionada para um parâmetro.](media/data-flow/string-parameter.png "Definição de um parâmetro de fluxo de dados")

Se o parâmetro de fluxo de `stringParam` dados referenciar um parâmetro de pipeline com valor `upper(column1)` . 

- Se a expressão for verificada, `$stringParam` avalia o valor da coluna1 em todos os maiúsculas.
- Se a expressão não for verificada (comportamento predefinido),  `$stringParam` avalia `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Passando em timetamps

Na linguagem de expressão do gasoduto, variáveis do sistema tais como `pipeline().TriggerTime` e funções como `utcNow()` os timetamps de retorno como cordas em formato 'yyyy-MM-dd \' T \' HH:mm:mms. Sssssz' Para convertê-los em parâmetros de fluxo de dados de tempos de tempo tipo, utilize a interpolação de cordas para incluir o tempotamp desejado numa `toTimestamp()` função. Por exemplo, para converter o tempo de disparo do gasoduto num parâmetro de fluxo de dados, pode utilizar `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![A screenshot mostra o separador Parâmetros onde pode introduzir um tempo de gatilho.](media/data-flow/parameter-timestamp.png "Definição de um parâmetro de fluxo de dados")

> [!NOTE]
> Os Fluxos de Dados só podem suportar até 3 milissegundos. A `left()` função é utilizada para cortar dígitos adicionais.

#### <a name="pipeline-parameter-example"></a>Exemplo do parâmetro do gasoduto

Digamos que tem um parâmetro inteiro `intParam` que faz referência a um parâmetro de pipeline do tipo String, `@pipeline.parameters.pipelineParam` . 

![A screenshot mostra o separador Parâmetros com parâmetros chamados stringParam e intParam.](media/data-flow/parameter-pipeline-2.png "Definição de um parâmetro de fluxo de dados")

`@pipeline.parameters.pipelineParam` é atribuído um valor de tempo de `abs(1)` execução.

![A screenshot mostra o separador Parâmetros com o valor de um b s (1) selecionado.](media/data-flow/parameter-pipeline-4.png "Definição de um parâmetro de fluxo de dados")

Quando `$intParam` é referenciado numa expressão como uma coluna derivada, avaliará `abs(1)` o retorno `1` . 

![A imagem mostra o valor das colunas.](media/data-flow/parameter-pipeline-3.png "Definição de um parâmetro de fluxo de dados")

### <a name="data-flow-expression-parameters"></a>Parâmetros de expressão de fluxo de dados

A **expressão de fluxo de dados** selecionado abrirá o construtor de expressão de fluxo de dados. Poderá referenciar funções, outros parâmetros e qualquer coluna de esquema definida ao longo do fluxo de dados. Esta expressão será avaliada como é quando referenciada.

> [!NOTE]
> Se passar numa expressão inválida ou referenciar uma coluna de esquemas que não exista nessa transformação, o parâmetro avaliará para nulo.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Passando um nome de coluna como parâmetro

Um padrão comum é passar em um nome de coluna como um valor de parâmetro. Se a coluna for definida no esquema de fluxo de dados, pode encaminhá-la diretamente como uma expressão de corda. Se a coluna não estiver definida no esquema, utilize a `byName()` função. Lembre-se de lançar a coluna ao seu tipo apropriado com uma função de fundição como `toString()` .

Por exemplo, se quiser mapear uma coluna de cordas com base num `columnName` parâmetro, pode adicionar uma transformação de coluna derivada igual a `toString(byName($columnName))` .

![Passando um nome de coluna como parâmetro](media/data-flow/parameterize-column-name.png "Passando um nome de coluna como parâmetro")

## <a name="next-steps"></a>Passos seguintes
* [Executar atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controlo](control-flow-expression-language-functions.md)
