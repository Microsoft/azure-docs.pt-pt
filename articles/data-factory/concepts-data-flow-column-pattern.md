---
title: Padrões de coluna no fluxo de dados de mapeamento Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando padrões de coluna em fluxos de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789860"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna no fluxo de dados de mapeamento

Várias transformações de fluxo de dados de mapeamento permitem que você referencie colunas de modelo com base em padrões em vez de nomes de coluna embutidos em código. Essa correspondência é conhecida como *padrões de coluna*. Você pode definir padrões para corresponder colunas com base no nome, no tipo de dados, no fluxo ou na posição, em vez de exigir nomes de campo exatos. Há dois cenários em que os padrões de coluna são úteis:

* Se os campos de origem de entrada forem alterados com frequência, como o caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Esse cenário é conhecido como [descompasso de esquema](concepts-data-flow-schema-drift.md).
* Se você quiser fazer uma operação comum em um grande grupo de colunas. Por exemplo, deseja converter cada coluna que tem ' total ' em seu nome de coluna em um duplo.

Atualmente, os padrões de coluna estão disponíveis nas transformações coluna derivada, agregar, selecionar e coletor.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregação

Para adicionar um padrão de coluna em uma coluna derivada ou na guia agregações de uma transformação Agregação, clique no ícone de adição à direita de uma coluna existente. Selecione **Adicionar padrão de coluna**. 

![padrões de coluna](media/data-flow/columnpattern.png "Padrões de Coluna")

Use o [Construtor de expressões](concepts-data-flow-expression-builder.md) para inserir a condição de correspondência. Crie uma expressão booliana que corresponda a colunas com base no `name`, `type`, `stream`e `position` da coluna. O padrão afetará qualquer coluna, descompasso ou definida, em que a condição retorna true.

As duas caixas de expressões abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Use `$$` para fazer referência ao valor existente do campo correspondente. A caixa expressão à esquerda define o nome e a caixa de expressão direita define o valor.

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de Coluna")

O padrão de coluna acima corresponde a cada coluna do tipo Double e cria uma coluna de agregação por correspondência. O nome da nova coluna é o nome da coluna correspondente concatenado com ' _ total '. O valor da nova coluna é a soma arredondada, agregada do valor Double existente.

Para verificar se a condição de correspondência está correta, você pode validar o esquema de saída das colunas definidas na guia **inspecionar** ou obter um instantâneo dos dados na guia **visualização de dados** . 

![padrões de coluna](media/data-flow/columnpattern3.png "Padrões de Coluna")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em Select e Sink

Ao mapear colunas na origem e selecionar transformações, você pode adicionar mapeamento fixo ou mapeamentos baseados em regras. Se você souber o esquema dos seus dados e esperar que colunas específicas do conjunto de dados de origem sempre correspondam a nomes estáticos específicos, use o mapeamento fixo. Se você estiver trabalhando com esquemas flexíveis, use o mapeamento baseado em regras para criar uma correspondência de padrões com base no `name`, `type`, `stream`e `position` de colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regra**.

![mapeamento baseado em regras](media/data-flow/rule2.png "Mapeamento baseado em regras")

Na caixa expressão à esquerda, insira sua condição de correspondência booliana. Na caixa expressão à direita, especifique a qual a coluna correspondente será mapeada. Use `$$` para fazer referência ao nome existente do campo correspondente.

Se você clicar no ícone de divisa para baixo, poderá especificar uma condição de mapeamento de Regex.

Clique no ícone de óculos ao lado de um mapeamento baseado em regras para exibir quais colunas definidas são correspondidas e para que elas estão mapeadas.

![mapeamento baseado em regras](media/data-flow/rule1.png "Mapeamento baseado em regras")

No exemplo acima, são criados dois mapeamentos baseados em regras. A primeira pega todas as colunas não nomeadas como ' filme ' e as mapeia para seus valores existentes. A segunda regra usa Regex para corresponder a todas as colunas que começam com ' Movie ' e as mapeia para a coluna ' MovieID '.

Se a regra resultar em vários mapeamentos idênticos, habilite **ignorar entradas duplicadas** ou **ignorar saídas duplicadas** para evitar duplicatas.

## <a name="pattern-matching-expression-values"></a>Valores de expressão correspondentes de padrões.

* `$$` se traduz no nome ou valor de cada correspondência em tempo de execução
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo ou transformação em seu fluxo
* `position` é a posição ordinal das colunas em seu fluxo de dados

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [linguagem de expressão](data-flow-expression-functions.md) de fluxo de dados de mapeamento para transformações de dados
* Usar padrões de coluna na [transformação do coletor](data-flow-sink.md) e [selecionar a transformação](data-flow-select.md) com mapeamento baseado em regras
