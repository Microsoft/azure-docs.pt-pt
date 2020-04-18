---
title: Mapeando fluxo de dados Select Transformation
description: Fluxo de dados de mapeamento da Fábrica de Dados azure Select Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: 71a7c812b71e67c7b729844d0672e520c88e2d08
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606355"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Selecione a transformação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação selecionada para mudar o nome, a queda ou a reencomenda das colunas. Esta transformação não altera os dados da linha, mas escolhe quais colunas são propagadas a jusante. 

Numa transformação selecionada, os utilizadores podem especificar mapeamentos fixos, usar padrões para fazer mapeamento baseado em regras ou ativar o mapeamento automático. Mapeamentos fixos e baseados em regras podem ser usados dentro da mesma transformação selecionada. Se uma coluna não corresponder a um dos mapeamentos definidos, será largada.

## <a name="fixed-mapping"></a>Mapeamento fixo

Se houver menos de 50 colunas definidas na sua projeção, todas as colunas definidas terão um mapeamento fixo por padrão. Um mapeamento fixo requer uma coluna definida e de entrada e mapeia-a com um nome exato.

![Mapeamento fixo](media/data-flow/fixedmapping.png "Mapeamento fixo")

> [!NOTE]
> Não se pode mapear ou renomear uma coluna à deriva usando um mapeamento fixo

### <a name="mapping-hierarchical-columns"></a>Mapeando colunas hierárquicas

Mapeamentos fixos podem ser usados para mapear uma subcoluna de uma coluna hierárquica para uma coluna de alto nível. Se tiver uma hierarquia definida, utilize a coluna para selecionar uma subcoluna. A transformação selecionada criará uma nova coluna com o valor e o tipo de dados da subcoluna.

![mapeamento hierárquico](media/data-flow/select-hierarchy.png "mapeamento hierárquico")

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras

Se desejar mapear muitas colunas ao mesmo tempo ou passar colunas derivadas a jusante, utilize mapeamento baseado em regras para definir os seus mapeamentos utilizando padrões de coluna. Jogo baseado `name` `type`no, `stream`, `position` e nas colunas. Você pode ter qualquer combinação de mapeamentos fixos e baseados em regras. Por padrão, todas as projeções com mais de 50 colunas serão indefinidas a um mapeamento baseado em regras que corresponde a cada coluna e saídas o nome inputado. 

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

Se tiver várias correspondências de regex no nome `$n` da coluna, pode consultar-se a partidas específicas utilizando onde 'n' se refere a qual corresponde. Por exemplo, '$2' refere-se ao segundo jogo dentro de um nome de coluna.

### <a name="rule-based-hierarchies"></a>Hierarquias baseadas em regras

Se a sua projeção definida tiver uma hierarquia, pode usar mapeamento baseado em regras para mapear as subcolunas das hierarquias. Especifique uma condição correspondente e a coluna complexa cujas subcolunas pretende mapear. Todas as subcolunas compatíveis serão saídas utilizando a regra "Nome as" especificada à direita.

![mapeamento baseado em regras](media/data-flow/rule-based-hierarchy.png "Mapeamento baseado em regras")

O exemplo acima coincide em todas `a`as subcolunas de coluna complexa . `a`contém duas subcolunas `b` e `c`. O esquema de saída incluirá `b` duas `c` colunas e como `$$`a condição "Nome as" é .

### <a name="parameterization"></a>Parametrização

Pode parametrizar nomes de colunas usando mapeamento baseado em regras. Use a ```name``` palavra-chave para combinar os nomes das colunas com um parâmetro. Por exemplo, se tiver um ```mycolumn```parâmetro de fluxo de dados, pode criar ```mycolumn```uma regra que corresponda a qualquer nome de coluna igual a . Pode renomear a coluna com um fio codificado como "chave de negócios" e remeti-la explicitamente. Neste exemplo, a condição ```name == $mycolumn``` correspondente é e a condição do nome é "chave de negócios". 

## <a name="auto-mapping"></a>Mapeamento automático

Ao adicionar uma transformação selecionada, o **mapeamento automático** pode ser ativado trocando o slider de mapeamento Automático. Com o mapeamento automático, a transformação selecionada mapeia todas as colunas de entrada, excluindo duplicados, com o mesmo nome que a sua entrada. Isto incluirá colunas derivadas, o que significa que os dados de saída podem conter colunas não definidas no seu esquema. Para obter mais informações sobre colunas derivadas, consulte a [deriva do esquema](concepts-data-flow-schema-drift.md).

![Mapeamento automático](media/data-flow/automap.png "Mapeamento automático")

Com o mapeamento automático ligado, a transformação selecionada irá honrar as configurações de skip duplicado e fornecer um novo pseudónimo para as colunas existentes. Aliasing é útil quando se fazem múltiplas juntas ou procurações no mesmo fluxo e em cenários de auto-adesão. 

## <a name="duplicate-columns"></a>Colunas duplicadas

Por predefinição, a transformação selecionada deixa cair colunas duplicadas tanto na projeção de entrada como de saída. As colunas de entrada duplicadas vêm frequentemente de transformações de adesão e de procura, onde os nomes das colunas são duplicados em cada lado da união. Podem ocorrer colunas de saída duplicadas se mapear duas colunas de entrada diferentes com o mesmo nome. Escolha se larga ou passa em colunas duplicadas toggling a caixa de verificação.

![Skip Duplicates](media/data-flow/select-skip-dup.png "Skip Duplicates")

## <a name="ordering-of-columns"></a>Encomenda de colunas

A ordem dos mapeamentos determina a ordem das colunas de saída. Se uma coluna de entrada for mapeada várias vezes, apenas o primeiro mapeamento será homenageado. Para qualquer coluna duplicada que caia, o primeiro jogo será mantido.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

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

Abaixo está um exemplo de um mapeamento selecionado e seu script de fluxo de dados:

![Selecione o exemplo do script](media/data-flow/select-script-example.png "Selecione o exemplo do script")

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
* Depois de utilizar o Select para mudar o nome, reencomendar e colunas de pseudónimos, utilize a [transformação do Sink](data-flow-sink.md) para aterrar os seus dados numa loja de dados.
