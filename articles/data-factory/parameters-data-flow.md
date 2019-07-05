---
title: Mapeamento de parâmetros de fluxo de dados de fábrica de dados do Azure
description: Saiba como parametrizar um fluxo de dados de mapeamento de pipelines da fábrica de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a7140f70db78c8511f3c4da00b2f9c11c368163
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477716"
---
# <a name="mapping-data-flow-parameters"></a>Parâmetros de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapeamento de fluxos de dados do Azure Data Factory suporta a utilização de parâmetros. Pode definir parâmetros dentro de sua definição de fluxo de dados, que, em seguida, pode ser usado em todo o seu expressões. Os valores de parâmetro podem ser definidos pelo pipeline chamada por meio da atividade de executar o fluxo de dados. Tem três opções para definir os valores no fluxo de dados, expressões de atividade:

* Utilize a linguagem de expressão de fluxo de controle de pipeline para definir um valor dinâmico
* Utilize a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Utilize qualquer linguagem de expressão para definir um valor literal estático

Utilize esta capacidade para tornar seus fluxos de dados para fins gerais, flexíveis e reutilizáveis. É possível parametrizar a definições de fluxo de dados e expressões com estes parâmetros.

> [!NOTE]
> Para utilizar expressões de fluxo de controle de pipeline, o parâmetro de fluxo de dados tem de ser do tipo cadeia.

## <a name="create-parameters-in-mapping-data-flow"></a>Criar parâmetros no mapeamento de fluxo de dados

Para adicionar parâmetros ao seu fluxo de dados, clique na parte em branco da tela de fluxo de dados para ver as propriedades gerais. No painel de definições, verá uma guia chamada 'Parameters'. Clique no botão "Novo" para gerar um novo parâmetro. Para cada parâmetro, tem de atribuir um nome, selecionar um tipo e, se desejar, defina um valor predefinido.

![Parâmetros de fluxo de dados de criação](media/data-flow/create-params.png "parâmetros criar fluxo de dados")

Parâmetros podem ser utilizados em qualquer expressão de fluxo de dados. Parâmetros para começar, $ e são imutáveis. Encontrará a lista de parâmetros disponíveis dentro do construtor de expressões no separador "Parameters".

![Expressão de parâmetro de fluxo de dados](media/data-flow/parameter-expression.png "expressão de parâmetro de fluxo de dados")

## <a name="use-parameters-in-your-data-flow"></a>Utilizar parâmetros do fluxo de dados

* Pode utilizar valores de parâmetro dentro as expressões de transformação. Encontrará a lista de parâmetros na guia Parameters no construtor de expressões. ![Utilizar parâmetros de fluxo de dados](media/data-flow/params9.png "parâmetros de fluxo de dados de utilização")

* Parâmetros também são utilizados para configurar valores dinâmicos para a sua origem e Sink definições de transformação. Quando clica dentro de campos configuráveis, verá o link "Adicionar contect dinâmico" aparecem. Clicar aqui leva-o para um construtor de expressões onde pode utilizar os parâmetros a utilizar valores dinâmicos. ![Fluxo de dados de conteúdo dinâmico](media/data-flow/params6.png "conteúdo dinâmico de fluxo de dados")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Definir parâmetros de mapeamento de fluxo de dados a partir do pipeline

Depois de criar o fluxo de dados com parâmetros, pode executá-la a partir de um pipeline com a atividade de fluxo de dados executar. Depois de adicionar a atividade à tela de pipeline, verá os dados disponíveis parâmetros de fluxo no separador de 'Parameters' da atividade.

![Definir um parâmetro de fluxo de dados](media/data-flow/parameter-assign.png "definir um parâmetro de fluxo de dados")

Se o tipo de dados do parâmetro é a cadeia de caracteres, quando clica na caixa de texto para definir valores de parâmetros, pode optar por introduzir um pipeline ou uma expressão de fluxo de dados. Se optar por expressão de pipeline, ser-lhe-á apresentada o painel de expressões do pipeline. Certifique-se incluir as funções de pipeline dentro através da sintaxe de interpolação de cadeia de caracteres "@{<expression>}", por exemplo:

```'@{pipeline().RunId}'```

Se o parâmetro não é do tipo string, sempre será apresentada com o construtor de expressões de fluxo de dados. Aqui, pode introduzir qualquer expressão ou valores literais que desejar que corresponda ao tipo de dados do parâmetro. Seguem-se exemplos de expressão de fluxo de dados e uma cadeia literal do construtor de expressões:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Cada fluxo de dados de mapeamento pode ter qualquer combinação de parâmetros de expressão de fluxo de dados e pipeline. 

![Exemplo de parâmetros de fluxo de dados](media/data-flow/parameter-example.png "amostra de parâmetros de fluxo de dados")



## <a name="next-steps"></a>Passos Seguintes
* [Executar a atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
