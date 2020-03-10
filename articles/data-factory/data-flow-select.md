---
title: Mapeando fluxo de dados Select Transformation
description: Fluxo de dados de mapeamento da Fábrica de Dados azure Select Transformation
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/08/2020
ms.openlocfilehash: 2d04de420f743e4fef4cff4bd2912559dae0886a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934182"
---
# <a name="mapping-data-flow-select-transformation"></a>Mapeando a transformação de fluxo de dados


Utilize esta transformação para seletividade de colunas (redução do número de colunas), colunas de pseudónimos e nomes de fluxo, e colunas de reencomenda.

## <a name="how-to-use-select-transformation"></a>Como utilizar a Transformação Select
A transformação Select permite-lhe alisar um fluxo inteiro, ou colunas nesse fluxo, atribuir nomes diferentes (pseudónimos) e, em seguida, referenciar esses novos nomes mais tarde no fluxo de dados. Esta transformação é útil para cenários de auto-adesão. A forma de implementar uma auto-adesão no ADF Data Flow é pegar num fluxo, ramicá-lo com "Novo Ramo", e imediatamente a seguir, adicionar uma transformação "Select". Esse fluxo terá agora um novo nome que pode usar para se juntar ao fluxo original, criando uma auto-adesão:

![Auto-juntar-se](media/data-flow/selfjoin.png "Auto-juntar-se")

No diagrama acima, a transformação Select está na parte superior. Isto é alisar o fluxo original para "OrigSourceBatting". Na em destaque, a Transform abaixo, pode ver que utilizamos este fluxo de pseudónimoS Select como a mão direita, permitindo-nos fazer referência à mesma tecla tanto no lado esquerdo como direito do Inner Join.

A seleção também pode ser usada como forma de desseleccionar colunas a partir do fluxo de dados. Por exemplo, se tiver 6 colunas definidas na pia, mas apenas deseja escolher um 3 específico para transformar e depois fluir para a pia, pode selecionar apenas as 3 utilizando a transformação selecionada.

![Selecione Transformação](media/data-flow/newselect1.png "Selecione Pseudónimos")

## <a name="options"></a>Opções
* A definição predefinida para "Select" é incluir todas as colunas de entrada e manter esses nomes originais. Pode alisar o fluxo definindo o nome da transformação Select.
* Para alisar colunas individuais, desmarque "Select All" e utilize o mapeamento da coluna na parte inferior.
* Escolha Skip Duplicates para eliminar colunas duplicadas de metadados de entrada ou saída.

![Skip Duplicates](media/data-flow/select-skip-dup.png "Skip Duplicates")

* Quando optar por saltar duplicados, os resultados serão visíveis no separador Inspecionar. ADF manterá a primeira ocorrência da coluna e verá que cada ocorrência subsequente dessa mesma coluna foi removida do seu fluxo.

> [!NOTE]
> Para limpar as regras de mapeamento, prima o botão **Reset.**

## <a name="mapping"></a>Mapear
Por predefinição, a transformação Select irá mapear automaticamente todas as colunas, que passarão por todas as colunas de entrada para o mesmo nome na saída. O nome do fluxo de saída definido em Definições Selecionadas definirá um novo nome de pseudónimo para o fluxo. Se mantiver o conjunto Select para o mapa automático, então pode alisar todo o fluxo com todas as colunas iguais.

![Selecione regras de transformação](media/data-flow/rule2.png "Mapeamento baseado em regras")

Se desejar alias, remover, mudar o nome ou reencomendar colunas, deve primeiro desligar o "auto-mapa". Por predefinição, verá uma regra predefinida introduzida para si chamada "Todas as colunas de entrada". Pode deixar esta regra no lugar se pretender sempre permitir que todas as colunas de entrada mapeiem o mesmo nome na sua saída.

No entanto, se desejar adicionar regras personalizadas, clicará em "Adicionar mapeamento". O mapeamento de campo fornecer-lhe-á uma lista de nomes de colunas recebidas e saídas para mapear e pseudónimos. Escolha "mapeamento baseado em regras" para criar regras de correspondência de padrões.

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao escolher o mapeamento baseado em regras, está a instruir a ADF a avaliar a sua expressão correspondente para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Pode adicionar qualquer combinação de mapeamentos baseados em regras. Os nomes de campo são então gerados em tempo de execução pela ADF com base em metadados de entrada a partir da fonte. Pode visualizar os nomes dos campos gerados durante o depurado e utilizar o painel de pré-visualização de dados.

Mais detalhes sobre a correspondência de padrões estão disponíveis na documentação do Padrão da [Coluna.](concepts-data-flow-column-pattern.md)

### <a name="use-rule-based-mapping-to-parameterize-the-select-transformation"></a>Utilize mapeamento baseado em regras para parametrizar a transformação Select
Pode parametrizar o mapeamento de campo na transformação Select utilizando mapeamento baseado em regras. Utilize a palavra-chave ```name``` para verificar os nomes das colunas de entrada num parâmetro. Por exemplo, se tiver um parâmetro de fluxo de dados chamado ```mycolumn``` pode criar uma única regra de transformação Select que mapeie sempre qualquer nome de coluna que ```mycolumn``` para um nome de campo desta forma:

```name == $mycolumn```

## <a name="next-steps"></a>Passos Seguintes
* Depois de utilizar o Select para mudar o nome, reencomendar e colunas de pseudónimos, utilize a [transformação do Sink](data-flow-sink.md) para aterrar os seus dados numa loja de dados.
