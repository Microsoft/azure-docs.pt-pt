---
title: Transformação do pivô no fluxo de dados de mapeamento
description: Dados de rotação de linhas para colunas usando Azure Data Factory mapeamento de dados de rotação Transformação de Pivô
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87086664"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformação do pivô no fluxo de dados de mapeamento


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilize a transformação do pivô para criar várias colunas a partir dos valores de linha únicos de uma única coluna. O pivot é uma transformação de agregação onde seleciona grupo por colunas e gera colunas de pivô utilizando [funções agregadas.](data-flow-expression-functions.md#aggregate-functions)

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Configuração

A transformação do pivô requer três entradas diferentes: grupo por colunas, a chave de rotação, e como gerar as colunas pivotadas

### <a name="group-by"></a>Agrupar por

![Agrupar por opções](media/data-flow/pivot2.png "Agrupar por opções")

Selecione quais colunas para agregar as colunas mais votadas. Os dados de saída irão agrupar todas as linhas com o mesmo grupo por valores em uma linha. A agregação feita na coluna pivotada ocorrerá sobre cada grupo.

Esta secção é opcional. Se nenhum grupo por colunas for selecionado, todo o fluxo de dados será agregado e apenas uma linha será outputada.

### <a name="pivot-key"></a>Chave de pivô

![Chave de pivô](media/data-flow/pivot3.png "Chave de pivô")

A chave de rotação é a coluna cujos valores de linha são apostados em novas colunas. Por padrão, a transformação do pivô criará uma nova coluna para cada valor de linha único.

Na secção **"Valor"** marcado, pode introduzir valores de linha específicos a serem apostados. Apenas os valores de linha introduzidos nesta secção serão apostados. Ativar **o valor nulo** criará uma coluna pivotada para os valores nulos na coluna.

### <a name="pivoted-columns"></a>Colunas de formação

![Colunas de formação](media/data-flow/pivot4.png "Colunas de formação")

Para cada valor chave de pivô único que se torna uma coluna, gere um valor de linha agregado para cada grupo. Pode criar várias colunas por tecla de rotação. Cada coluna de pivô deve conter pelo menos uma [função agregada](data-flow-expression-functions.md#aggregate-functions).

**Padrão de nome da coluna:** Selecione como formatar o nome da coluna de cada coluna de pivô. O nome da coluna descodada será uma combinação do valor da chave de rotação, prefixo da coluna e prefixo opcional, suficiente, caracteres médios. 

**Arranjo de colunas:** Se gerar mais de uma coluna de pivô por tecla de rotação, escolha como pretende que as colunas sejam encomendadas. 

**Prefixo da coluna:** Se gerar mais de uma coluna de pivô por tecla de rotação, introduza um prefixo de coluna para cada coluna. Esta definição é opcional se tiver apenas uma coluna pivotada.

## <a name="help-graphic"></a>Ajudar gráfico

O gráfico de ajuda abaixo mostra como os diferentes componentes do pivô interagem uns com os outros

![Pivô ajuda gráficos](media/data-flow/pivot5.png "Pivô ajuda gráfico")

## <a name="pivot-metadata"></a>Metadados de pivô

Se não forem especificados valores na configuração da chave de rotação, as colunas de rotação serão geradas dinamicamente no tempo de execução. O número de colunas de voto equivalerá ao número de valores-chave de rotação único multiplicados pelo número de colunas de pivô. Como este pode ser um número de mudança, o UX não apresentará os metadados da coluna no **separador Inspect** e não haverá propagação de colunas. Para transformar estas colunas, use as capacidades de [padrão](concepts-data-flow-column-pattern.md) de coluna de mapeamento do fluxo de dados. 

Se forem definidos valores-chave de rotação específicas, as colunas pivotadas aparecerão nos metadados. Os nomes das colunas estarão disponíveis no mapeamento de Inspeção e Pia.

### <a name="generate-metadata-from-drifted-columns"></a>Gerar metadados a partir de colunas derivadas

O pivô gera novos nomes de colunas dinamicamente baseados nos valores da linha. Pode adicionar estas novas colunas aos metadados que podem ser referenciados mais tarde no fluxo de dados. Para isso, utilize o [mapa de](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) ação rápida na pré-visualização de dados. 

![Colunas dinâmicas](media/data-flow/newpivot1.png "Mapa de colunas de pivô à deriva")

### <a name="sinking-pivoted-columns"></a>Afundar colunas de formação

Embora as colunas de funções sejam dinâmicas, ainda podem ser escritas na sua loja de dados de destino. Ativar **Deixe o esquema flutuar** nas definições da pia. Isto permitir-lhe-á escrever colunas que não estão incluídas nos metadados. Não verá os novos nomes dinâmicos nos metadados da coluna, mas a opção de deriva de esquema permitir-lhe-á aterrar os dados.

### <a name="rejoin-original-fields"></a>Junte-se aos campos originais

A transformação do pivô só projetará o grupo por colunas e pivotadas. Se quiser que os seus dados de saída incluam outras colunas de entrada, utilize um padrão [de união de si](data-flow-join.md#self-join) mesmo.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Exemplo

Os ecrãs apresentados na secção de configuração têm o seguinte script de fluxo de dados:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Passos seguintes

Experimente a [transformação não-bevorta](data-flow-unpivot.md) para transformar os valores das colunas em valores de linha. 
