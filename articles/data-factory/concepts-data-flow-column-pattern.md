---
title: Padrões de coluna sinuosos em Fluxo de dados de mapeamento da Fábrica de Dados azure
description: Criar padrões de transformação de dados generalizados usando padrões de coluna sinuosos em fluxos de dados de mapeamento da Fábrica de Dados azure
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065525"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna sinuosos no fluxo de dados de mapeamento

Várias transformações de fluxo de dados de mapeamento permitem-lhe referência a colunas de modelos com base em padrões em vez de nomes de colunas codificados. Esta correspondência é conhecida como padrões de *coluna.* Pode definir padrões para combinar colunas com base no nome, tipo de dados, fluxo ou posição em vez de exigir nomes de campo exatos. Há dois cenários em que os padrões de coluna são úteis:

* Se os campos de origem que entram mudarem frequentemente, como o caso de alterar colunas em ficheiros de texto ou bases de dados NoSQL. Este cenário é conhecido como [deriva de esquemas.](concepts-data-flow-schema-drift.md)
* Se desejar fazer uma operação comum num grande grupo de colunas. Por exemplo, querer lançar todas as colunas que têm "total" no seu nome de coluna em duplo.

Os padrões de coluna estão atualmente disponíveis na coluna derivada, agregado, selecionado e afundando transformações.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregado

Para adicionar um padrão de coluna numa coluna derivada ou no separador Agregados de uma transformação agregada, clique no ícone mais à direita de uma coluna existente. Selecione **Adicionar padrão de coluna**. 

![padrões de coluna](media/data-flow/columnpattern.png "Padrões de Coluna")

Utilize o construtor de [expressão](concepts-data-flow-expression-builder.md) para introduzir a condição de jogo. Crie uma expressão booleana que `name` `type`corresponda às colunas com base na, e `stream` `position` na coluna. O padrão afetará qualquer coluna, derivada ou definida, onde a condição retorna verdadeira.

As duas caixas de expressão abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Utilizar `$$` para fazer referência ao valor existente do campo combinado. A caixa de expressão esquerda define o nome e a caixa de expressão direita define o valor.

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de Coluna")

O padrão da coluna acima corresponde a cada coluna de tipo duplo e cria uma coluna agregada por correspondência. O nome da nova coluna é o nome da coluna comparada com '_total'. O valor da nova coluna é a soma arredondada e agregada do valor duplo existente.

Para verificar se a sua condição de correspondência está correta, pode validar o esquema de saída das colunas definidas no separador **Inspecionar** ou obter uma imagem instantânea dos dados no separador **de pré-visualização de Dados.** 

![padrões de coluna](media/data-flow/columnpattern3.png "Padrões de Coluna")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em select e afundar

Ao mapear colunas na fonte e selecionar transformações, pode adicionar mapeamento fixo ou mapeamentobaseado em regras. Jogo baseado `name` `type`no, `stream`, `position` e nas colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão indefinidas a um mapeamento baseado em regras que corresponde a cada coluna e saídas o nome inputado. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regras**.

![mapeamento baseado em regras](media/data-flow/rule2.png "Mapeamento baseado em regras")

Cada mapeamento baseado em regras requer duas inputs: a condição para combinar e o que nomear cada coluna mapeada. Ambos os valores são introduzidos através do construtor de [expressão.](concepts-data-flow-expression-builder.md) Na caixa de expressão esquerda, introduza a sua condição de jogo booleana. Na caixa de expressão certa, especifique a que coluna será mapeada.

![mapeamento baseado em regras](media/data-flow/rule-based-mapping.png "Mapeamento baseado em regras")

Utilize `$$` a sintaxe para fazer referência ao nome de entrada de uma coluna compatível. Usando a imagem acima como exemplo, diga que um utilizador quer combinar em todas as colunas de cordas cujos nomes são inferiores a seis caracteres. Se uma coluna de `test`entrada `$$ + '_short'` for nomeada, `test_short`a expressão mudará o nome da coluna . Se este for o único mapeamento que existe, todas as colunas que não satisfaçam a condição serão retiradas dos dados outputed.

Os padrões combinam com as colunas derivadas e definidas. Para ver quais as colunas definidas que são mapeadas por uma regra, clique no ícone dos óculos ao lado da regra. Verifique a sua saída utilizando a pré-visualização de dados.

### <a name="regex-mapping"></a>Mapeamento Regex

Se clicar no ícone chevron descendente, pode especificar uma condição de mapeamento regex. Uma condição de mapeamento regex corresponde a todos os nomes da coluna que correspondem à condição regex especificada. Isto pode ser usado em combinação com mapeamentos padrão baseados em regras.

![mapeamento baseado em regras](media/data-flow/regex-matching.png "Mapeamento baseado em regras")

O exemplo acima coincide `(r)` com o padrão regex ou qualquer nome de coluna que contenha um r maiúsculo inferior. Semelhante ao mapeamento padrão baseado em regras, todas as colunas `$$` correspondidas são alteradas pela condição à direita usando a sintaxe.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a sua projeção definida tiver uma hierarquia, pode usar mapeamento baseado em regras para mapear as subcolunas das hierarquias. Especifique uma condição correspondente e a coluna complexa cujas subcolunas pretende mapear. Todas as subcolunas compatíveis serão saídas utilizando a regra "Nome as" especificada à direita.

![mapeamento baseado em regras](media/data-flow/rule-based-hierarchy.png "Mapeamento baseado em regras")

O exemplo acima coincide em todas `a`as subcolunas de coluna complexa . `a`contém duas subcolunas `b` e `c`. O esquema de saída incluirá `b` duas `c` colunas e como `$$`a condição "Nome as" é .

## <a name="pattern-matching-expression-values"></a>Padrão combinando valores de expressão.

* `$$`traduz-se para o nome ou valor de cada jogo no tempo de execução
* `name`representa o nome de cada coluna de entrada
* `type`representa o tipo de dados de cada coluna de entrada
* `stream`representa o nome associado a cada fluxo, ou transformação no seu fluxo
* `position`é a posição ordinal de colunas no seu fluxo de dados

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a linguagem de expressão de [fluxo](data-flow-expression-functions.md) de dados de mapeamento para transformações de dados
* Use padrões de coluna na [transformação](data-flow-sink.md) da pia e [selecione a transformação](data-flow-select.md) com mapeamento baseado em regras
