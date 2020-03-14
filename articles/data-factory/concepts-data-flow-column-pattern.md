---
title: Padrões de coluna sinuosos em Fluxo de dados de mapeamento da Fábrica de Dados azure
description: Criar padrões de transformação de dados generalizados usando padrões de coluna sinuosos em fluxos de dados de mapeamento da Fábrica de Dados azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243812"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna sinuosos no fluxo de dados de mapeamento

Várias transformações de fluxo de dados de mapeamento permitem-lhe referência a colunas de modelos com base em padrões em vez de nomes de colunas codificados. Esta correspondência é conhecida como padrões de *coluna.* Pode definir padrões para combinar colunas com base no nome, tipo de dados, fluxo ou posição em vez de exigir nomes de campo exatos. Há dois cenários em que os padrões de coluna são úteis:

* Se os campos de origem que entram mudarem frequentemente, como o caso de alterar colunas em ficheiros de texto ou bases de dados NoSQL. Este cenário é conhecido como [deriva de esquemas.](concepts-data-flow-schema-drift.md)
* Se desejar fazer uma operação comum num grande grupo de colunas. Por exemplo, querer lançar todas as colunas que têm "total" no seu nome de coluna em duplo.

Os padrões de coluna estão atualmente disponíveis na coluna derivada, agregado, selecionado e afundando transformações.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregado

Para adicionar um padrão de coluna numa coluna derivada ou no separador Agregados de uma transformação agregada, clique no ícone mais à direita de uma coluna existente. Selecione **Adicionar padrão de coluna**. 

![padrões de coluna](media/data-flow/columnpattern.png "Padrões de Coluna")

Utilize o construtor de [expressão](concepts-data-flow-expression-builder.md) para introduzir a condição de jogo. Crie uma expressão booleana que corresponda às colunas baseadas no `name`, `type`, `stream`e `position` da coluna. O padrão afetará qualquer coluna, derivada ou definida, onde a condição retorna verdadeira.

As duas caixas de expressão abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Utilize `$$` para fazer referência ao valor existente do campo combinado. A caixa de expressão esquerda define o nome e a caixa de expressão direita define o valor.

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de Coluna")

O padrão da coluna acima corresponde a cada coluna de tipo duplo e cria uma coluna agregada por correspondência. O nome da nova coluna é o nome da coluna comparada com '_total'. O valor da nova coluna é a soma arredondada e agregada do valor duplo existente.

Para verificar se a sua condição de correspondência está correta, pode validar o esquema de saída das colunas definidas no separador **Inspecionar** ou obter uma imagem instantânea dos dados no separador **de pré-visualização de Dados.** 

![padrões de coluna](media/data-flow/columnpattern3.png "Padrões de Coluna")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em select e afundar

Ao mapear colunas na fonte e selecionar transformações, pode adicionar mapeamento fixo ou mapeamentobaseado em regras. Se conhecer o esquema dos seus dados e esperar que colunas específicas do conjunto de dados de origem correspondam sempre a nomes estáticos específicos, utilize mapeamento fixo. Se estiver a trabalhar com esquemas flexíveis, use mapeamento baseado em regras para construir uma combinação de padrões baseada no `name`, `type`, `stream`e `position` de colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regras**.

![mapeamento baseado em regras](media/data-flow/rule2.png "Mapeamento baseado em regras")

Na caixa de expressão esquerda, introduza a sua condição de jogo booleana. Na caixa de expressão certa, especifique a que coluna será mapeada. Utilize `$$` para fazer referência ao nome existente do campo combinado.

Se clicar no ícone chevron descendente, pode especificar uma condição de mapeamento regex.

Clique no ícone dos óculos ao lado de um mapeamento baseado em regras para ver quais colunas definidas são correspondidas e a que estão mapeadas.

![mapeamento baseado em regras](media/data-flow/rule1.png "Mapeamento baseado em regras")

No exemplo acima, são criados dois mapeamentos baseados em regras. A primeira pega em todas as colunas não chamadas 'filme' e mapeia-as para os seus valores existentes. A segunda regra usa o regex para combinar com todas as colunas que começam com 'filme' e mapeia-as para coluna 'movieId'.

Se a sua regra resultar em mapeamentos idênticos múltiplos, ative a saída de **skip duplicado** ou ignore as **saídas duplicadas** para evitar duplicados.

## <a name="pattern-matching-expression-values"></a>Padrão combinando valores de expressão.

* `$$` traduz-se para o nome ou valor de cada jogo no tempo de execução
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo, ou transformação no seu fluxo
* `position` é a posição ordinal das colunas no seu fluxo de dados

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a linguagem de expressão de [fluxo](data-flow-expression-functions.md) de dados de mapeamento para transformações de dados
* Use padrões de coluna na [transformação](data-flow-sink.md) da pia e [selecione a transformação](data-flow-select.md) com mapeamento baseado em regras
