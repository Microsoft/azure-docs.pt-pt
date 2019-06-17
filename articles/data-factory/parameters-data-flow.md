---
title: Parâmetros de fluxo de dados de mapeamento de fábrica de dados do Azure
description: Saiba como parametrizar um fluxo de dados de mapeamento de pipelines da fábrica de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082888"
---
# <a name="mapping-data-flow-parameters"></a>Parâmetros de fluxo de dados de mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Dados de mapeamento fluxos no data factory suportam o uso de parâmetros. Pode definir parâmetros dentro de sua definição de fluxo de dados, que, em seguida, pode ser usado em todo o seu expressões. Os parâmetros, em seguida, podem ser definidos pelo pipeline chamada por meio da atividade de executar o fluxo de dados. Tem três opções para utilizar para definir os valores de fluxo de dados, expressões de atividade:

* Utilize a linguagem de expressão de fluxo de controle de pipeline para definir um valor dinâmico
* Utilize a linguagem de expressão de fluxo de dados para definir um valor dinâmico
* Utilize qualquer linguagem de expressão para definir um valor literal estático

Utilize esta capacidade para tornar seus fluxos de dados para fins gerais, flexíveis e reutilizáveis. É possível parametrizar a definições de fluxo de dados e expressões com estes parâmetros.

> [!NOTE]
> Para utilizar expressões de fluxo de controle de pipeline, o parâmetro de fluxo de dados tem de ser do tipo cadeia.

* Adicione uma atividade executar fluxo de dados para a tela de pipeline.
* Se o fluxo de dados tiver parâmetros, verá a lista de parâmetros disponíveis no parâmetros tab.* *, clique na caixa de texto junto a cada parâmetro para introduzir o valor do parâmetro.
* Pode optar por criar a sua expressão de parâmetro através da linguagem de expressão de fluxo de controle de pipeline ou expressões de fluxo de dados.

![3 de parâmetros de fluxo de dados](media/data-flow/params3.png "3 de parâmetros de fluxo de dados")

## <a name="create-parameters-in-data-flow"></a>Criar parâmetros no fluxo de dados

![Parâmetros de 1 de fluxo de dados](media/data-flow/params1.png "parâmetros de 1 de fluxo de dados")

Para adicionar parâmetros ao seu fluxo de dados, clique na parte em branco da tela de fluxo de dados para ver as propriedades gerais. No painel de definições, verá uma guia chamada parâmetros. Clique no botão New para gerar novos parâmetros, que, em seguida, podem ser definidos a partir do pipeline, transmitir os valores em seu fluxo de dados. Introduza um nome de parâmetro e selecione o tipo de dados para cada parâmetro.

Dentro as expressões de fluxo de dados, pode utilizar os parâmetros com os valores definidos do pipeline. Parâmetros para começar, $ e são imutáveis. Também encontrará a lista de seus parâmetros disponíveis dentro do construtor de expressões no separador de parâmetros. Pode utilizar estes valores nas suas expressões, apesar de não pode atribuir novos valores para os parâmetros.

![Parâmetros de 2 de fluxo de dados](media/data-flow/params2.png "2 de parâmetros de fluxo de dados")

## <a name="set-data-flow-parameters-from-pipeline"></a>Definir parâmetros de fluxo de dados do pipeline

Depois de criar o fluxo de dados com parâmetros, agora pode executar esse fluxo de dados de um pipeline com a atividade de fluxo de dados executar. Depois de adicionar essa atividade à tela de design de pipeline, verá os dados disponíveis parâmetros de fluxo no separador de definição de parâmetros da atividade.

![Linguagem de expressão de parâmetros de fluxo de dados](media/data-flow/params4.png "linguagem de expressão de parâmetros de fluxo de dados")

Ao clicar na caixa de texto para valores de parâmetro fill-in, ser-lhe-á apresentada o construtor de expressões de fluxo de dados. Aqui, pode introduzir qualquer expressão ou valores literais que desejar que correspondam ao tipo de dados do parâmetro. Seguem-se exemplos de expressão de fluxo de dados e uma cadeia literal do construtor de expressões:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Se o tipo de dados do parâmetro é uma cadeia de caracteres, em seguida, pode optar por introduzir um pipeline ou uma expressão de fluxo de dados. Se optar por expressão de pipeline, em vez disso, será apresentada com o painel de expressão do pipeline. Certifique-se incluir as funções de pipeline dentro através da sintaxe de interpolação de cadeia de caracteres "@{<expression>}", por exemplo:

```'@{pipeline().RunId}'```

![Exemplo de parâmetros de fluxo de dados](media/data-flow/params5.png "amostra de parâmetros de fluxo de dados")

## <a name="next-steps"></a>Passos Seguintes

* [Executar a atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
* [Expressões de fluxo de controle](control-flow-expression-language-functions.md)
