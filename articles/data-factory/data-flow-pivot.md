---
title: Transformação de pivô no fluxo de dados de mapeamento
description: Dados de pivô de linhas a colunas usando azure data factory mapeando dados fluxo de dados Transformação pivot
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a0084c3e8185f615e7ac2a2b8c212f1ebf022c08
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683298"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformação de pivô no fluxo de dados de mapeamento


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação do pivô para criar múltiplas colunas a partir dos valores únicos da linha de uma única coluna. O pivô é uma transformação de agregação onde seleciona grupo por colunas e gera colunas de pivô utilizando [funções agregadas](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Configuração

A transformação do pivô requer três inputs diferentes: grupo por colunas, a chave pivot, e como gerar as colunas pivotadas

### <a name="group-by"></a>Agrupar por

![Agrupar por opções](media/data-flow/pivot2.png "Agrupar por opções")

Selecione quais colunas para agregar as colunas mais giradas. Os dados de saída agruparão todas as linhas com o mesmo grupo por valores numa só linha. A agregação feita na coluna pivotada ocorrerá sobre cada grupo.

Esta secção é opcional. Se nenhum grupo por colunas for selecionado, todo o fluxo de dados será agregado e apenas uma linha será saída.

### <a name="pivot-key"></a>Chave de pivô

![Chave de pivô](media/data-flow/pivot3.png "Chave de pivô")

A chave de pivô é a coluna cujos valores de linha são transformados em novas colunas. Por padrão, a transformação do pivô criará uma nova coluna para cada valor único da linha.

Na secção **"Valor",** pode introduzir valores específicos de linha a serem pivôs. Apenas os valores de linha introduzidos nesta secção serão pivôs. Ativar o **valor Nulo** criará uma coluna pivotada para os valores nulos na coluna.

### <a name="pivoted-columns"></a>Colunas pivotadas

![Colunas pivotadas](media/data-flow/pivot4.png "Colunas pivotadas")

Para cada valor chave de pivô único que se torna uma coluna, gere um valor de linha agregado para cada grupo. Pode criar várias colunas por chave de pivô. Cada coluna de pivô deve conter pelo menos uma [função agregada](data-flow-expression-functions.md#aggregate-functions).

**Padrão de nome** da coluna: Selecione como formatar o nome da coluna de cada coluna de pivô. O nome da coluna output será uma combinação do valor da chave de pivô, prefixo da coluna e prefixo opcional, basta, caracteres médios. 

**Arranjo da coluna:** Se gerar mais de uma coluna de pivô por chave de pivô, escolha como pretende que as colunas sejam encomendadas. 

**Prefixo da coluna:** Se gerar mais de uma coluna de pivô por chave de pivô, introduza um prefixo de coluna para cada coluna. Esta definição é opcional se tiver apenas uma coluna pivotada.

## <a name="help-graphic"></a>Ajudar gráfico

O gráfico abaixo ajuda mostra como os diferentes componentes de pivô interagem uns com os outros

![Gráficos de ajuda de pivô](media/data-flow/pivot5.png "Gráfico de ajuda de pivô")

## <a name="pivot-metadata"></a>Metadados de pivô

Se não forem especificados valores na configuração da chave de rotação, as colunas dinâmicas serão geradas dinamicamente no tempo de execução. O número de colunas pivotadas equivalerá ao número de valores-chave únicos multiplicados pelo número de colunas de pivô. Como este pode ser um número de mudança, o UX não apresentará os metadados da coluna no separador **Inspecionar** e não haverá propagação da coluna. Para transformação destas colunas, utilize as capacidades de padrão da [coluna](concepts-data-flow-column-pattern.md) de mapeamento do fluxo de dados. 

Se forem definidos valores-chave de pivô específicos, as colunas dinâmicas aparecerão nos metadados. Os nomes das colunas estarão disponíveis para si no mapeamento de Inspeção e Pia.

### <a name="generate-metadata-from-drifted-columns"></a>Gerar metadados a partir de colunas derivadas

O pivô gera novos nomes de colunas dinamicamente baseados nos valores das linhas. Pode adicionar estas novas colunas aos metadados que podem ser referenciados mais tarde no fluxo de dados. Para isso, utilize o [mapa à deriva](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) na pré-visualização de dados. 

![Colunas Pivot](media/data-flow/newpivot1.png "Colunas de pivô derivadas do mapa")

### <a name="sinking-pivoted-columns"></a>Colunas afundadas

Embora as colunas dinâmicas sejam dinâmicas, ainda podem ser escritas na sua loja de dados de destino. Ativar **Permitir a deriva do esquema** nas definições da pia. Isto permitir-lhe-á escrever colunas que não estão incluídas em metadados. Não verá os novos nomes dinâmicos nos metadados da sua coluna, mas a opção de deriva de esquemas permitirá que você aterre os dados.

### <a name="rejoin-original-fields"></a>Voltar a juntar-se aos campos originais

A transformação do pivô só projetará o grupo por colunas e pivotadas. Se pretender que os seus dados de saída incluam outras colunas de entrada, utilize um padrão [de auto-adesão.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

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

Os ecrãs mostrados na secção de configuração têm o seguinte script de fluxo de dados:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Passos seguintes

Experimente a [transformação despivot](data-flow-unpivot.md) para transformar os valores da coluna em valores de linha. 
