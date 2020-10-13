---
title: Padrões de coluna no fluxo de dados de mapeamento da Azure Data Factory
description: Criar padrões de transformação de dados generalizados utilizando padrões de coluna em fluxos de dados de mapeamento da Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 74656401d7b0ef12cf509674921a6a5153ce992d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282940"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Usando padrões de coluna no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Várias transformações de fluxo de dados de mapeamento permitem-lhe referenciar colunas de modelos com base em padrões em vez de nomes de colunas codificados. Esta correspondência é conhecida como *padrões de coluna.* Pode definir padrões para combinar colunas com base no nome, tipo de dados, fluxo, origem ou posição em vez de exigir nomes de campo exatos. Existem dois cenários em que os padrões das colunas são úteis:

* Se os campos de origem de entrada mudarem frequentemente, como o caso de alterar colunas em ficheiros de texto ou bases de dados NoSQL. Este cenário é conhecido como [deriva de esquema.](concepts-data-flow-schema-drift.md)
* Se quiser fazer uma operação comum num grande grupo de colunas. Por exemplo, querer lançar todas as colunas que têm o nome 'total' da sua coluna num duplo.

Os padrões de coluna estão atualmente disponíveis nas transformações de colunas derivadas, agregadas, selecionadas e afundadas.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Padrões de coluna em coluna derivada e agregado

Para adicionar um padrão de coluna numa coluna derivada, agregada ou transformação de janela, clique em **Adicionar** acima da lista de colunas ou do ícone mais ao lado de uma coluna derivada existente. Escolha **Adicionar o padrão da coluna**.

![padrões de coluna](media/data-flow/add-column-pattern.png "Padrões de Coluna")

Utilize o construtor de [expressão](concepts-data-flow-expression-builder.md) para introduzir a condição de correspondência. Crie uma expressão booleana que corresponda às colunas com base no `name` , , e da `type` `stream` `origin` `position` coluna. O padrão afetará qualquer coluna, derivada ou definida, onde a condição retorna verdadeiramente.

As duas caixas de expressão abaixo da condição de correspondência especificam os novos nomes e valores das colunas afetadas. Utilize `$$` para fazer referência ao valor existente do campo combinado. A caixa de expressão esquerda define o nome e a caixa de expressão certa define o valor.

![padrões de coluna](media/data-flow/edit-column-pattern.png "Padrões de Coluna")

O padrão de coluna acima corresponde a cada coluna do tipo duplo e cria uma coluna derivada por correspondência. Ao indicar `$$` como o campo de nome da coluna, cada coluna correspondeda é atualizada com o mesmo nome. O valor de cada coluna é o valor existente arredondado para dois pontos decimais.

Para verificar se a sua condição de correspondência está correta, pode validar o esquema de saída das colunas definidas no **separador Inspecionar** ou obter uma imagem instantânea dos dados no **separador de pré-visualização de Dados.** 

![padrões de coluna](media/data-flow/columnpattern3.png "Padrões de Coluna")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapeamento baseado em regras em selecionar e afundar

Ao mapear colunas na fonte e selecionar transformações, pode adicionar mapeamento fixo ou mapeamento baseado em regras. Corresponder com base no `name` , , , , e de `type` `stream` `origin` `position` colunas. Pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por predefinição, todas as projeções com mais de 50 colunas predefinirão num mapeamento baseado em regras que corresponda em cada coluna e produz o nome introduzido. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regras.**

![mapeamento baseado em regras](media/data-flow/rule2.png "Mapeamento baseado em regras")

Cada mapeamento baseado em regras requer duas entradas: a condição para combinar e o que nomear cada coluna mapeada. Ambos os valores são introduzidos através do construtor de [expressão.](concepts-data-flow-expression-builder.md) Na caixa de expressão esquerda, insira a sua condição de jogo boolean. Na caixa de expressão certa, especifique para que coluna correspondeda será mapeada.

![mapeamento baseado em regras](media/data-flow/rule-based-mapping.png "Mapeamento baseado em regras")

Utilize `$$` sintaxe para fazer referência ao nome de entrada de uma coluna compatível. Usando a imagem acima como exemplo, digamos que um utilizador quer combinar em todas as colunas de cordas cujos nomes são mais curtos do que seis caracteres. Se uma coluna recebida for `test` nomeada, a expressão `$$ + '_short'` mudará o nome da coluna `test_short` . Se for o único mapeamento que existe, todas as colunas que não satisfaçam a condição serão retiradas dos dados outputados.

Os padrões combinam com colunas derivadas e definidas. Para ver quais colunas definidas são mapeadas por uma regra, clique no ícone dos óculos ao lado da regra. Verifique a sua saída utilizando a pré-visualização de dados.

### <a name="regex-mapping"></a>Mapeamento regex

Se clicar no ícone chevron descendente, pode especificar uma condição de mapeamento regex. Uma condição de mapeamento regex corresponde a todos os nomes das colunas que correspondem à condição regex especificada. Isto pode ser usado em combinação com mapeamentos padrão baseados em regras.

![mapeamento baseado em regras](media/data-flow/regex-matching.png "Mapeamento baseado em regras")

O exemplo acima coincide com o padrão regex `(r)` ou qualquer nome de coluna que contenha um r de caixa inferior. Semelhante ao mapeamento padrão baseado em regras, todas as colunas correspondidas são alteradas pela condição no direito usando `$$` sintaxe.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a sua projeção definida tiver uma hierarquia, pode usar o mapeamento baseado em regras para mapear os subcolumns das hierarquias. Especifique uma condição correspondente e a coluna complexa cujos subcolumes deseja mapear. Todos os subcolumns combinados serão produzidos utilizando a regra "Nome como" especificada à direita.

![mapeamento baseado em regras](media/data-flow/rule-based-hierarchy.png "Mapeamento baseado em regras")

O exemplo acima coincide com todos os subcolumns de coluna `a` complexa. `a` contém dois subcolumns `b` e `c` . O esquema de saída incluirá duas colunas `b` e como é a condição `c` 'Nome `$$` as'.

## <a name="pattern-matching-expression-values"></a>Padrões correspondentes valores de expressão.

* `$$` traduz-se para o nome ou valor de cada jogo no tempo de execução
* `name` representa o nome de cada coluna de entrada
* `type` representa o tipo de dados de cada coluna de entrada
* `stream` representa o nome associado a cada fluxo, ou transformação no seu fluxo
* `position` é a posição ordinálca das colunas no fluxo de dados
* `origin` é a transformação onde uma coluna originou ou foi atualizada pela última vez

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a linguagem de [expressão](data-flow-expression-functions.md) de fluxo de dados de mapeamento para transformações de dados
* Use padrões de coluna na [transformação](data-flow-sink.md) do lavatório e [selecione a transformação](data-flow-select.md) com mapeamento baseado em regras
