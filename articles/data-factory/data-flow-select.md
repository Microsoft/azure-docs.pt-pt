---
title: Selecione a transformação no fluxo de dados de mapeamento
description: Fluxo de dados de mapeamento da fábrica de dados Azure Data Factory Selecione Transformação
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040237"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selecione a transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação selecionada para renomear, largar ou reencomendar colunas. Esta transformação não altera os dados da linha, mas escolhe quais as colunas que são propagadas a jusante. 

Numa transformação selecionada, os utilizadores podem especificar mapeamentos fixos, usar padrões para fazer mapeamento baseado em regras ou permitir o mapeamento automático. Os mapeamentos fixos e baseados em regras podem ser utilizados dentro da mesma transformação selecionada. Se uma coluna não corresponder a um dos mapeamentos definidos, será largada.

## <a name="fixed-mapping"></a>Mapeamento fixo

Se houver menos de 50 colunas definidas na sua projeção, todas as colunas definidas terão um mapeamento fixo por predefinição. Um mapeamento fixo requer uma coluna definida e de entrada e mapeia-a com um nome exato.

![Mapeamento fixo](media/data-flow/fixedmapping.png "Mapeamento fixo")

> [!NOTE]
> Não é possível mapear ou mudar o nome de uma coluna à deriva usando um mapeamento fixo

### <a name="mapping-hierarchical-columns"></a>Mapeamento de colunas hierárquicas

Mapeamentos fixos podem ser usados para mapear um subcolumn de uma coluna hierárquica para uma coluna de nível superior. Se tiver uma hierarquia definida, utilize a coluna para selecionar um subcolumn. A transformação selecionada criará uma nova coluna com o valor e o tipo de dados do subcolumn.

![mapeamento hierárquico](media/data-flow/select-hierarchy.png "mapeamento hierárquico")

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Se desejar mapear muitas colunas ao mesmo tempo ou passar colunas derivadas a jusante, utilize mapeamento baseado em regras para definir os seus mapeamentos utilizando padrões de coluna. Corresponder com base no `name` , , e de `type` `stream` `position` colunas. Pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por predefinição, todas as projeções com mais de 50 colunas predefinirão num mapeamento baseado em regras que corresponda em cada coluna e produz o nome introduzido. 

Para adicionar um mapeamento baseado em regras, clique em **Adicionar mapeamento** e selecione **mapeamento baseado em regras.**

![A screenshot mostra mapeamento baseado em regras selecionado a partir do mapeamento Add.](media/data-flow/rule2.png "Mapeamento baseado em regras")

Cada mapeamento baseado em regras requer duas entradas: a condição para combinar e o que nomear cada coluna mapeada. Ambos os valores são introduzidos através do construtor de [expressão.](concepts-data-flow-expression-builder.md) Na caixa de expressão esquerda, insira a sua condição de jogo boolean. Na caixa de expressão certa, especifique para que coluna correspondeda será mapeada.

![A imagem mostra um mapeamento.](media/data-flow/rule-based-mapping.png "Mapeamento baseado em regras")

Utilize `$$` sintaxe para fazer referência ao nome de entrada de uma coluna compatível. Usando a imagem acima como exemplo, digamos que um utilizador quer combinar em todas as colunas de cordas cujos nomes são mais curtos do que seis caracteres. Se uma coluna recebida for `test` nomeada, a expressão `$$ + '_short'` mudará o nome da coluna `test_short` . Se for o único mapeamento que existe, todas as colunas que não satisfaçam a condição serão retiradas dos dados outputados.

Os padrões combinam com colunas derivadas e definidas. Para ver quais colunas definidas são mapeadas por uma regra, clique no ícone dos óculos ao lado da regra. Verifique a sua saída utilizando a pré-visualização de dados.

### <a name="regex-mapping"></a>Mapeamento regex

Se clicar no ícone chevron descendente, pode especificar uma condição de mapeamento regex. Uma condição de mapeamento regex corresponde a todos os nomes das colunas que correspondem à condição regex especificada. Isto pode ser usado em combinação com mapeamentos padrão baseados em regras.

![A screenshot mostra a condição de mapeamento regex com nível de hierarquia e correspondências de nome.](media/data-flow/regex-matching.png "Mapeamento baseado em regras")

O exemplo acima coincide com o padrão regex `(r)` ou qualquer nome de coluna que contenha um r de caixa inferior. Semelhante ao mapeamento padrão baseado em regras, todas as colunas correspondidas são alteradas pela condição no direito usando `$$` sintaxe.

Se tiver várias partidas regex no nome da sua coluna, pode consultar `$n` fósforos específicos utilizando o local onde 'n' se refere a que correspondência. Por exemplo, '$2' refere-se ao segundo jogo dentro de um nome de coluna.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a sua projeção definida tiver uma hierarquia, pode usar o mapeamento baseado em regras para mapear os subcolumns das hierarquias. Especifique uma condição correspondente e a coluna complexa cujos subcolumes deseja mapear. Todos os subcolumns combinados serão produzidos utilizando a regra "Nome como" especificada à direita.

![A screenshot mostra um mapeamento baseado em regras usando para uma hierarquia.](media/data-flow/rule-based-hierarchy.png "Mapeamento baseado em regras")

O exemplo acima coincide com todos os subcolumns de coluna `a` complexa. `a` contém dois subcolumns `b` e `c` . O esquema de saída incluirá duas colunas `b` e como é a condição `c` 'Nome `$$` as'.

### <a name="parameterization"></a>Parametização

Pode parametrizar os nomes das colunas utilizando mapeamento baseado em regras. Utilize a palavra-chave ```name``` para combinar os nomes das colunas recebidas com um parâmetro. Por exemplo, se tiver um parâmetro de fluxo de ```mycolumn``` dados, pode criar uma regra que corresponda a qualquer nome de coluna que seja igual a ```mycolumn``` . Pode renomear a coluna correspondeda a uma cadeia codificada dura, como "chave de negócios" e faz referência explicitamente. Neste exemplo, a condição de correspondência é ```name == $mycolumn``` e a condição do nome é "chave de negócio". 

## <a name="auto-mapping"></a>Mapeamento automático

Ao adicionar uma transformação selecionada, **o mapeamento automático** pode ser ativado com a mudança do slider de mapeamento automático. Com o mapeamento automático, a transformação selecionada mapeia todas as colunas de entrada, excluindo duplicados, com o mesmo nome que a sua entrada. Isto incluirá colunas derivadas, o que significa que os dados de saída podem conter colunas não definidas no seu esquema. Para obter mais informações sobre colunas à deriva, consulte [a deriva de esquema.](concepts-data-flow-schema-drift.md)

![Mapeamento automático](media/data-flow/automap.png "Mapeamento automático")

Com o mapeamento automático ligado, a transformação selecionada honrará as definições de duplicação de salto e fornecerá um novo pseudónimo para as colunas existentes. O aliasing é útil quando se faz múltiplas junções ou procuras no mesmo fluxo e em cenários de auto-junção. 

## <a name="duplicate-columns"></a>Colunas duplicadas

Por predefinição, a transformação selecionada deixa cair colunas duplicadas tanto na projeção de entrada como na projeção de saída. As colunas de entrada duplicadas vêm frequentemente de transformações de junção e procura onde os nomes das colunas são duplicados em cada lado da junta. Podem ocorrer colunas de saída duplicadas se mapear duas colunas de entrada diferentes com o mesmo nome. Escolha se deve cair ou passar colunas duplicadas toggling a caixa de verificação.

![Saltar duplicados](media/data-flow/select-skip-dup.png "Saltar duplicados")

## <a name="ordering-of-columns"></a>Ordenação de colunas

A ordem dos mapeamentos determina a ordem das colunas de saída. Se uma coluna de entrada for mapeada várias vezes, apenas o primeiro mapeamento será honrado. Para qualquer queda de coluna duplicada, a primeira partida será mantida.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Exemplo

Abaixo está um exemplo de um mapeamento selecionado e o seu script de fluxo de dados:

![Selecione exemplo de script](media/data-flow/select-script-example.png "Selecione exemplo de script")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Passos seguintes
* Depois de utilizar Select para renomear, reencomendar e colunas de pseudónimos, utilize a [transformação](data-flow-sink.md) do Sink para colocar os seus dados numa loja de dados.
